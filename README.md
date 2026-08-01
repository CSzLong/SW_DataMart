# SW Data Mart

## Tools

| Tool Name      | Usage                                       |
|----------------|---------------------------------------------|
| Dataiku        | ETL, Data Cleanup/Massage, Machine Learning |
| Snowflake      | Data Warehouse                              |
| Power BI       | Dashboard, Report                           |
| Tableau        | Dashboard                                   |
| Uipath         | Automation Process                          |
| Power Automate | Automation Process                          |

## Technology

| Tech Name | Usage |
|-----------|-------|
| Python    |       |
| SQL       |       |
| DAX       |       |
| M语言       |       |

## ETL

- Source: Oracle EBS, Manual, Boundary Systems
- Module: OM, PO, MFG, ASCP, AR, COSTING
- Schema: Star Schema

## Data Cleanup/Massage Process

- Dataiku recipe
- Snowflake Views

#### 星型模型（Star Schema）

结构特点：一个 Fact 表处于中心，直接与多个 Dimension
表相连，Dimension
表本身不再继续拆分，所有相关属性都平铺在同一张维度表里。画出来的关系图从中心向四周辐射，形状像一颗星星。

举例：销售场景

```text
                Dim_Customer
                     |
Dim_Product ---- Fact_Sales ---- Dim_Date
                     |
                  Dim_Store
```

Dim_Store
这张表里会直接包含：门店名称、门店地址、城市、省份、国家、门店经理姓名……所有跟"
门店"相关的属性全部平铺在一张表里，即使"城市→省份→国家"
这种层级关系存在，也不会拆成三张单独的表。

优点

结构简单直观，Join 层级少（只需 Fact 和 Dimension 之间一层
Join）
查询性能好，尤其在 Snowflake、BigQuery 这类列式存储的云数仓上，因为
Join 越少，查询越快
业务人员容易理解，做 Power BI/Tableau 报表时对应关系一目了然

缺点

数据冗余较多（比如 1000 家门店里如果有 50 家都在"上海"，"上海"
这两个字就重复存了 50 次）
维度表更新稍微麻烦一点，冗余字段一致性需要注意

#### 雪花模型（Snowflake Schema）

结构特点：把 Dimension
表按照层级关系进一步规范化拆分，一张维度表里如果存在"一对多"
的层级属性，就拆成多张关联表，减少冗余。画出来的图从中心向外分叉，一层套一层x，像雪花的结晶形状。

同样是门店维度，雪花模型会拆成

```text
Dim_Store ---- Dim_City ---- Dim_Province ---- Dim_Country
```

Dim_Store 只保留门店自身的属性（门店名、经理），"城市""省份""
国家"这些字段被拆到独立的表里，Dim_Store 通过外键关联到
Dim_City。

优点

数据冗余更少，节省存储空间（尤其在早期磁盘昂贵的年代很重要）
维度层级关系更清晰，符合数据库范式化设计
更新某个层级属性（比如某省改名）只需要改一处，不会有一致性问题

缺点

Join 层级变多，查询变复杂（业务人员写 SQL 或者用 BI 工具建模时体验更差）
在现代列式存储的云数仓（Snowflake、Redshift、BigQuery）上，多余的
Join 反而拖慢查询性能——这也是为什么现在业界普遍更推崇星型模型

| 维度         | 星型模型       | 雪花模型           |
|------------|------------|----------------|
| 维度表规范化程度   | 不规范化（宽表）   | 规范化（拆分成多张关联表）  |
| Join 数量    | 少          | 多              |
| 查询性能       | 更快（尤其列式数仓） | 相对较慢           |
| 存储冗余       | 较多         | 较少             |
| 易理解性       | 高，业务人员友好   | 较低，需要理解表间层级关系  |
| 现代云数仓场景推荐度 | 高          | 较少用，除非有明确规范化需求 |

现在业界（尤其是用 Snowflake 这类云数仓）的主流做法是：优先用星型模型，因为：

存储成本在云时代已经不是核心痛点（Snowflake 存储很便宜）
查询性能才是关键，Join 越少越快
BI 工具（Power BI、Tableau）对星型模型的支持也更友好，做
RLS、关系建模时更简单

- Table:

| 类型                           | 作用            | 特点                             | 举例             |
|------------------------------|---------------|--------------------------------|----------------
| Fact Table（事实表）              | 存储业务度量值/发生的事件 | 体积大、增长快，通常包含大量外键 + 数值型度量字段     | 销售订单、库存变动、生产记录 |
| Dimension Table（维度表）         | 存储描述性上下文信息    | 相对稳定、变化慢，字段以文本/属性为主            | 客户、产品、时间、地区    |
| Bridge Table（桥接表）            | 解决多对多关系       | 连接一个 Fact 和多个Dimension 之间的多值关系 | 一个订单对应多个促销活动   |
| Aggregate/Summary Table（汇总表） | 预先计算好的聚合结果    | 提升查询性能，牺牲一定实时性                 | 月度销售汇总、季度 KPI  |

Dimension Table 的经典变体
Slowly Changing Dimension (SCD)：处理维度属性随时间变化的问题
Type 1：直接覆盖旧值，不保留历史（比如改个错别字）
Type 2：新增一行记录历史版本，保留完整变更轨迹（最常用，比如客户改了地址）
Type 3：用新增列保留"上一个值"，只保留有限历史
Conformed Dimension（一致性维度）：跨多个 Fact Table
共用的标准维度，比如"时间维度"可以同时服务销售、库存、财务等不同主题域
Junk Dimension（垃圾维度）：把一堆低基数的标志位/枚举字段打包成一张小维度表，避免
Fact 表里塞满零散字段

按细分类型（Fact Table 的三种常见形态）
Transaction Fact（事务型事实表）：记录单次事件发生时的快照，粒度最细，比如每一笔订单
Periodic Snapshot Fact（周期性快照事实表）：按固定时间间隔（每天/每月）记录状态，比如每日库存余额
Accumulating Snapshot
Fact（累积快照事实表）：跟踪一个流程从开始到结束的多个里程碑，比如订单从下单→发货→签收的全过程，会随流程推进不断更新同一行


## Architecture Design

### DA Refinery: Data & Analytics Refinery

Three Tier:

| Tier | Name | Physical Form | Responsibility |
|---|---|---|---|
| First Tier | Refined Coat | Physical Table | Ingest from EDI Gold View + join/integrate across tables |
| Second Tier | Sealer Coat | Physical Table / View | Additional processing, business rule correction, and enrichment as needed |
| Third Tier | Top Coat | View | Final business modeling, serving BI consumption |




| 传统概念 | 建议名称 | 作用 | 特点 | 举例 |
|---|---|---|---|---|
| Dimension | **Reference**(参考表) | 存储描述性上下文信息 | 相对稳定、变化慢，字段以文本/属性为主 | 客户、产品、时间、地区 |
| Fact | **Metric**(指标表) | 存储业务度量值/发生的事件 | 体积大、增长快，通常包含大量外键 + 数值型度量字段 | 销售订单、库存变动、生产记录 |
| Bridge | **Link**(关联表) | 解决多对多关系 | 连接一个 Metric 和多个 Reference 之间的多值关系 | 一个订单对应多个促销活动 |
| Aggregate/Summary | **Rollup**(汇总表) | 预先计算好的聚合结果 | 提升查询性能，牺牲一定实时性 | 月度销售汇总、季度 KPI |


各层职责与对应表类型

| Tier	         | 物理形式	       | Reference  | 	Metric	   | Link	   | Rollup |
|---------------|-------------|------------|------------|---------|--------|
| Refined Coat	 | Table	      | ✅ 需要	      | ✅ 需要	      | ❌ 不需要	  | ❌ 不需要  |
| Sealer Coat	  | Table/View	 | ✅ 需要(修正后)	 | ✅ 需要(修正后)	 | ⚠️ 视情况	 | ❌ 不需要  |
| Top Coat      | 	View	      | ✅ 需要	      | ✅ 需要	      | ✅ 需要    | 	✅ 需要  |


OBJECT 名字前缀叫 `SW_APAC_RC` \ `SW_APAC_SC` \ `SW_APAC_TC`

后缀

| 类型	       | 简写   |
|-----------|------|
| Reference | 	REF |
| Metric    | 	MET |
| Link	     | LNK  |
| Rollup	   | RUP  |