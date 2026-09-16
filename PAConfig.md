下面是不依赖AI、纯用 Power Automate 表达式+循环实现的方案。这套逻辑比表面看起来复杂一些，因为要在流里重现三件事：①固定的BU/指标/渠道显示顺序，②IWD和PKG在"Order Received/Committed Order"层要把渠道明细**合并求和**（这是你截图里能对上数字的关键，我在上一轮已经验证过），③交替底色和合计行加粗。

## 整体嵌套结构

```
Run a query against a dataset
└─ Compose: AllRows / BUConfig / MetricOrder / ChannelOrder / TableHeaderHtml（静态，见下）
└─ Initialize variable: varRowsHtml(string) / varFirstMetricRow(bool) / varSumLY / varSumLD / varSumMTD (float)
└─ Apply to each: AT_BU  ← 遍历 BUConfig（并发控制设为1，见下方提醒）
     └─ Apply to each: AT_Metric  ← 遍历 MetricOrder（并发控制也设为1）
          └─ Set variable varFirstMetricRow = true
          └─ Filter array: Filter_BU_Metric_Rows（从 AllRows 里筛当前BU+当前指标）
          └─ Condition: Is_Shipped?
               ├─ 是 → Condition: Has_Channel_Breakdown?
               │        ├─ 是 → Apply to each: AT_Channel（遍历 ChannelOrder，逐条渠道append一行）
               │        └─ 否 → Append 单行（GI/Coil/PM/AR/CCA 的 Shipped）
               └─ 否 → Condition: Has_Multiple_Rows(length>1)?
                        ├─ 是 → Apply to each: AT_AggRow（累加求和后append一行，IWD/PKG的Order Received/Committed Order）
                        └─ 否 → Append 单行（GI/Coil/PM/AR/CCA 的 Order Received/Committed Order，Total行也走这里）
└─ Compose: FinalHtml = concat(TableHeaderHtml, varRowsHtml, '</table>')
└─ Send an Email (V2): Body = outputs('FinalHtml')，Is HTML = Yes
```

⚠️ **关键坑**：AT_BU 和 AT_Metric 这两层循环内部要 `Set variable`（改 varRowsHtml/varFirstMetricRow等），Power Automate 默认对 Apply to each 是**20路并发**执行，并发环境下多个迭代同时改同一个变量会互相覆盖，导致行顺序错乱或数据丢失。必须在这两个 Apply to each 的"设置(⚙)"里打开**并发控制**，把"程度"改成 **1**。

## 静态数据（用 Compose 建好，供后面引用）

| Compose 名称 | 内容 |
|---|---|
| `BUConfig` | `[{"bu":"IWD","bg":"#FFFFFF","fw":"normal"},{"bu":"PKG","bg":"#F2F2F2","fw":"normal"},{"bu":"GI","bg":"#FFFFFF","fw":"normal"},{"bu":"Coil","bg":"#F2F2F2","fw":"normal"},{"bu":"PM","bg":"#FFFFFF","fw":"normal"},{"bu":"AR","bg":"#F2F2F2","fw":"normal"},{"bu":"CCA","bg":"#FFFFFF","fw":"normal"},{"bu":"Total","bg":"#D9D9D9","fw":"bold"}]` |
| `MetricOrder` | `["Order Received","Committed Order","Shipped"]` |
| `ChannelOrder` | `["SW China","SW Korea","SW SEA"]` |
| `TableHeaderHtml` | 表头+标题的静态HTML字符串（BU/Metric/Channel/Plan/.../MTD vs LY 那一行），直接照抄上次生成文件里 `<table>...</tr>` 那部分，写死即可，不用表达式 |

`AllRows` 的 Compose 内容：
```
body('Run_a_query_against_a_dataset')?['body']?['results'][0]?['tables'][0]?['rows']
```

## Filter_BU_Metric_Rows（切到"高级模式"填表达式）

```
@and(
  equals(item()?['Dim_BU_Channel[REPORT_BU]'], items('AT_BU')?['bu']),
  equals(item()?['SW_APAC_SALES_FACT[METRIC_TYPE]'], items('AT_Metric'))
)
```
From 选 `outputs('AllRows')`。

## Is_Shipped 条件
```
@equals(items('AT_Metric'), 'Shipped')
```

## Has_Channel_Breakdown 条件（判断这个BU的Shipped是否有渠道明细）
```
@greater(
  length(filter(body('Filter_BU_Metric_Rows'), not(equals(item()?['Dim_BU_Channel[STANDARD_CHANNEL]'], null)))),
  0
)
```

## AT_Channel 循环内：Filter_Channel_Row
From 选 `body('Filter_BU_Metric_Rows')`，条件：
```
@equals(item()?['Dim_BU_Channel[STANDARD_CHANNEL]'], items('AT_Channel'))
```
再套一层 Condition：`@greater(length(body('Filter_Channel_Row')), 0)`，为真时才 append（因为IWD没有Korea，过滤结果会是空数组，要跳过）。

## Has_Multiple_Rows 条件（判断Order Received/Committed Order要不要聚合）
```
@greater(length(body('Filter_BU_Metric_Rows')), 1)
```

## AT_AggRow 循环（在 Has_Multiple_Rows=是 的分支里，先各Set variable重置为0，再进循环）
```
Set variable varSumLY  = @add(variables('varSumLY'),  items('AT_AggRow')?['[Last Year]'])
Set variable varSumLD  = @add(variables('varSumLD'),  items('AT_AggRow')?['[Last Day]'])
Set variable varSumMTD = @add(variables('varSumMTD'), items('AT_AggRow')?['[MTD]'])
```
循环结束后，Committed Order 的 MTD vs LY 要用合计值重算（不能直接平均两个渠道的比率）：
```
@if(equals(variables('varSumLY'), 0), '', div(variables('varSumMTD'), variables('varSumLY')))
```

## 拼一行 `<tr>` 的通用表达式模板

四个分支（渠道单行 / 渠道循环单条 / 聚合单行 / 直接单行）最终都调用同一套 **Append to string variable** → `varRowsHtml`，只是"数据来源"不同（渠道分支用 `first(body('Filter_Channel_Row'))`，聚合分支用 `variables('varSumLY')` 等变量，其余两种直接用 `first(body('Filter_BU_Metric_Rows'))`）。以"直接单行"为例，Value 表达式：

```
concat(
  '<tr>',
  '<td style="border:1px solid #DDDDDD;font-family:Segoe UI,Arial,sans-serif;font-size:12px;padding:6px 10px;text-align:left;font-weight:bold;background-color:', items('AT_BU')?['bg'], ';">',
    if(equals(items('AT_Metric'), 'Order Received'), items('AT_BU')?['bu'], ''),
  '</td>',
  '<td style="border:1px solid #DDDDDD;font-family:Segoe UI,Arial,sans-serif;font-size:12px;padding:6px 10px;text-align:left;font-weight:bold;background-color:', items('AT_BU')?['bg'], ';">',
    if(equals(variables('varFirstMetricRow'), true), items('AT_Metric'), ''),
  '</td>',
  '<td style="border:1px solid #DDDDDD;font-family:Segoe UI,Arial,sans-serif;font-size:12px;padding:6px 10px;text-align:left;background-color:', items('AT_BU')?['bg'], ';">',
    coalesce(first(body('Filter_BU_Metric_Rows'))?['Dim_BU_Channel[STANDARD_CHANNEL]'], ''),
  '</td>',
  '<td style="border:1px solid #DDDDDD;font-family:Segoe UI,Arial,sans-serif;font-size:12px;padding:6px 10px;text-align:right;font-weight:', items('AT_BU')?['fw'], ';background-color:', items('AT_BU')?['bg'], ';">',
    if(equals(first(body('Filter_BU_Metric_Rows'))?['[Plan]'], null), '', formatNumber(first(body('Filter_BU_Metric_Rows'))?['[Plan]'], '#,##0.00')),
  '</td>'
  /* Forecast / Last Year / Last Day / MTD 四列同理，把字段名换成 [Forecast]/[Last Year]/[Last Day]/[MTD] */
  /* MTD vs FCST / MTD vs LY 两列格式不同：*/
  ,'<td style="...同上num样式...">',
    if(equals(first(body('Filter_BU_Metric_Rows'))?['[MTD vs FCST]'], null), '', concat(formatNumber(mul(first(body('Filter_BU_Metric_Rows'))?['[MTD vs FCST]'], 100), '#,##0.00'), '%')),
  '</td>'
  ,'</tr>'
)
```
每次 Append 完成后紧跟一步 `Set variable varFirstMetricRow = false`。

数字格式化用的是 Power Automate 内置的 `formatNumber(数值, '#,##0.00')`，百分比就是先 `mul(值,100)` 再套同一个格式再拼上 `%`。四个分支只需要把 `first(body('Filter_BU_Metric_Rows'))?['...']` 换成对应来源（渠道行/聚合变量）即可，结构完全一样。

## 收尾
```
Compose FinalHtml = concat(outputs('TableHeaderHtml'), variables('varRowsHtml'), '</table>')
```
Send an Email (V2) → Body 填 `outputs('FinalHtml')`，Is HTML 打开。

## 一个可选的简化建议
上面"拼一行"的 concat 表达式要在4个分支里各写一遍（只是数据来源不同），维护起来有点啰嗦。如果你们环境支持**子流程（设置里勾选"此流可作为子流运行"，用"运行子流程"动作调用）**，可以把这段拼行逻辑单独做成一个子流，输入参数是 bu/metric/channel/bg/fw/plan/forecast/lastYear/lastDay/mtd/mtdVsFcst/mtdVsLy，输出一个 `<tr>` 字符串，四个分支都去调用它，改起来只用改一处。要不要我再把这个子流的输入输出结构也列一版？