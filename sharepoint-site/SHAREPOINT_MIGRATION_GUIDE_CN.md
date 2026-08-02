# SW Data Mart — SharePoint Home Page 中文迁移指南

## 用途与前提

本指南用于在公司电脑上，把仓库中的本地原型迁移为 SharePoint **Communication Site**。本地页面 [index.html](index.html) 是内容和版式蓝图；正式 Site 应使用 Microsoft 365 的标准 Web Part，不建议嵌入或直接发布自定义 HTML。

如需逐点击执行，请使用 [SHAREPOINT_DEPLOYMENT_RUNBOOK_CN.md](SHAREPOINT_DEPLOYMENT_RUNBOOK_CN.md)。

## 最重要的文件库说明

请在 SharePoint 的 **Site contents（站点内容）** 中选择 **New → Document library**，创建一个新的文档库：

**SW Data Mart Delivery Assets**

它与 SharePoint 默认自动生成的 **Documents** 文档库是**同一级别的两个独立 Document Library**，不是在 Documents 内新建一个文件夹。

这样做的好处是：可以对交付资产单独设置权限、版本、元数据、视图和保留策略，也不会与站点的日常通用文件混在一起。

只有在公司政策不允许创建新 Document Library 时，才使用默认 Documents 作为替代；此时在 Documents 下新建一个名为 `SW Data Mart Delivery Assets` 的顶层文件夹，并在 DRIVE 中注明这是受限制条件下的替代方案。

## 建议的 Site 结构

创建一个名为 **SW Data Mart | DRIVE Workspace** 的 Communication Site，并按以下顺序创建页面和顶部导航：

| 导航名称 | SharePoint 页面名称 | 对应本地原型 |
|---|---|---|
| Home | Home | `index.html` |
| Request & Delivery | Request & Delivery | `pages/drive-workspace.html` |
| How Data Is Prepared | How Data Is Prepared | `pages/architecture.html` |
| Trust & Governance | Trust & Governance | `pages/governance.html` |
| Template Library | Template Library | `pages/template-library.html` |

## SW Data Mart Delivery Assets 内部文件夹

在新创建的 Document Library 内建立以下文件夹：

```text
01 Active Specifications/
02 Staff Templates/
03 Reference Standards/
04 Delivery Evidence/
05 Archive/
```

建议上传的仓库内容如下：

| SharePoint 位置 | 仓库来源 | 用途 |
|---|---|---|
| `01 Active Specifications/` | 每个相关 `DRIVE-###` 对应的已批准 DA-100 / TD-200 实例 | 受控的 enhancement 设计记录 |
| `02 Staff Templates/` | `deliverables/template-library/DA-100_Data_Architecture_Design_Template.docx` | Data Architecture Design 模板 |
| `02 Staff Templates/` | `deliverables/template-library/TD-200_Technical_Design_Template.docx` | 一体化技术设计模板：业务逻辑、Top Coat、Report/Insight/Validation/Extension、UAT 与发布 |
| `02 Staff Templates/` | `deliverables/template-library/DM_Data_Model_Template.xlsx` | Data Model Excel 模板 |
| `02 Staff Templates/` | `deliverables/template-library/DL_Data_Lineage_Template.xlsx` | Data Lineage Excel 模板 |
| `02 Staff Templates/` | `deliverables/template-library/DD_Data_Dictionary_Template.xlsx` | Data Dictionary Excel 模板 |
| `02 Staff Templates/` | `deliverables/template-library/DRIVE_Delivery_Register_Template.csv` | 初始 DRIVE register / List 结构 |
| `03 Reference Standards/` | `deliverables/archive/component-standards/` | 历史组件标准，仅作参考 |

DRIVE 既是 enhancement 编号，也是完整交付框架：**D — Data Architecture**（受治理的消费数据契约与数据血缘）、**R — Report**（结构化、周期性业务报表）、**I — Insight**（交互式 Power BI / Tableau Dashboard）、**V — Validation**（防止上游变更影响的数据质量与 schema 验证）及 **E — Extension**（Excel 自动化、RLS、同步管道等技术组件）。建议建立的 Document Library metadata：**Asset Type、DRIVE ID、End User、PMO Manager、Technical Analyst、ERP Consultant、Status、Effective Date、Review Date、Confidentiality**。

## Home Page 搭建顺序

英文正文已经在 `index.html` 中准备好。建议依以下顺序建立 Home Page：

| 页面区域 | 标准 SharePoint Web Part | 配置建议 |
|---|---|---|
| Site banner | Hero | 标题：*Turn trusted data into faster, consistent business decisions.*；两个入口：Template Library 和 delivery journey。 |
| What SW Data Mart helps you do | Text + 四栏布局 | 用业务语言解释：可信答案、清晰责任、减少手工工作、安全变更。 |
| Start here | Text + Quick Links | 三个链接：描述业务问题、选择交付类型、使用共享模板。 |
| DRIVE delivery lifecycle | Text + 五栏布局 | Register → Define → Design → Validate → Release & operate。 |
| Latest workspace updates | News 或 Text | 使用本地 Home Page 的三个更新卡片。 |
| Global EDI ownership and APAC Coat terminology | Text + 五栏布局 | 说明 Global EDI 的 Medallion 三段数据仓库和 Gold View；再说明 Refined Coat (RC)、Sealer Coat (SC)、Top Coat (TC) 与业务输出。 |
| Quick access | Quick Links | 链接至 How Data Is Prepared、Trust & Governance、Template Library。 |

Site 标题使用 **SW Data Mart**，副标题使用 **DRIVE Workspace**。对团队的页面和模板维持英文，以便与英文设计规格保持一致。

## Detail Pages 搭建建议

- **Request & Delivery：**使用 `pages/drive-workspace.html`。加入 active specification 链接和 delivery register 的 Quick Link。
- **How Data Is Prepared：**使用 `pages/architecture.html`。可用 Process Web Part 表达 Global EDI Gold View → RC → SC → TC → business output，并注明 Global EDI 的责任边界。
- **Trust & Governance：**使用 `pages/governance.html`。使用表格说明四个角色和 delivery gates。
- **Template Library：**使用 `pages/template-library.html`。加入 **Document Library** Web Part，并过滤显示 `02 Staff Templates/` 和 `01 Active Specifications/`，让员工可直接下载最新文件。

## 上线与交接检查清单

- [ ] 确认 Communication Site 的 Owner 和 Members。
- [ ] 创建独立的 **SW Data Mart Delivery Assets** Document Library，并建立五个文件夹。
- [ ] 上传五类资产并完成 metadata。
- [ ] 创建五个页面和顶部导航。
- [ ] 将本地原型的文件链接替换为正式 Document Library 链接。
- [ ] 确认员工可以下载模板，但只有授权 Owner 可以编辑 master specification。
- [ ] 确认 Home Page 在桌面和手机端显示正常。
- [ ] 在 DRIVE delivery register 中记录正式 Site URL。

## 重要说明

仓库中的 `sharepoint-site/` 文件夹继续作为离线预览和设计参考。正式 SharePoint Home Page 应通过标准 Web Part 搭建，以符合公司 Microsoft 365 的治理和后续维护要求。
