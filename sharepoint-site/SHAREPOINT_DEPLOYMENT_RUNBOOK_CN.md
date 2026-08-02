# SW Data Mart — SharePoint 部署详细操作手册

> 适用对象：拥有 SharePoint Site Owner 权限的 PMO Manager 或 Technical Analyst。
> 目标：在公司 Microsoft 365 租户中，用标准 Modern Pages 和 Web Parts 建立 SW Data Mart | DRIVE Workspace。
> 原型来源：本仓库的 `sharepoint-site/` 文件夹。它只用作内容与版式蓝图，不直接上传为正式页面。

## 0. 开始前确认

在开始前，先确认以下事项：

- 你可以创建 Communication Site，或已获得现有 Site 的 Site Owner 权限。
- 已从 GitHub 下载最新版项目，并能打开本地 [index.html](index.html) 作为参考。
- 已确认 Site 的默认语言。SharePoint Site 创建后通常不能直接更改默认语言，因此此处建议按公司的英文协作规范创建 English Site。
- 已确定至少一名 Site Owner（建议 PMO Manager）和一名日常内容维护人（建议 Technical Analyst）。
- 已准备好上传的文件：`deliverables/documentation/`、`deliverables/template-library/` 及相应最终交付证据。

### 本手册的三个概念

| 名称 | 在本项目中的含义 |
|---|---|
| Local prototype | 仓库中的 HTML 页面，用于在本地浏览与复制英文内容。 |
| Modern Page | SharePoint 中通过 **New → Page** 创建的 Site Page；使用 Web Parts 拼装。 |
| Document Library | SharePoint 中独立的文件库，可管理权限、版本、元数据和视图。 |

## 1. 创建 Communication Site

1. 在公司电脑登录 Microsoft 365。
2. 从 App Launcher 打开 **SharePoint**。
3. 在 SharePoint 起始页选择 **Create site**。
4. 选择 **Communication site**，不要选择 Team site。
5. 选择最接近公司品牌的模板；如果没有统一要求，选择 Blank。
6. 输入 Site Name：`SW Data Mart | DRIVE Workspace`。
7. Site Description 建议填写：`Governed APAC analytics workspace consuming Global EDI Gold View for dashboards, reports and extensions.`
8. 按公司规范选择 Sensitivity label、语言和其他必填项，然后选择 **Create site**。
9. Site 创建完成后选择 **Go to site**。
10. 打开右上角 Settings，进入 **Site permissions**：
   - 将负责发布和权限管理的人员放入 Owners。
   - 将需要维护页面和文件的人放入 Members（是否允许编辑取决于组织权限设计）。
   - 大部分业务浏览者只应作为 Visitors（Read）。

> 如果无法看到 Create site、Communication site 或 New → Page，通常是公司管理员关闭了自助创建或页面创建权限。此时请让 SharePoint 管理员创建 Site 或授予相应权限。

## 2. 创建独立的交付资产 Document Library

**关键点：不要在默认 Documents 里面建立这些文件夹。**

1. 在新 Site 的顶部或左侧选择 **New**。
2. 选择 **Document library**。
3. 选择 Blank library。
4. Name 填写：`SW Data Mart Delivery Assets`。
5. Description 建议填写：`Controlled delivery specifications, templates, data foundations and release evidence for SW Data Mart.`
6. 选择 **Create**。
7. 打开 **Site contents**，确认现在有两个同级的 Document Library：
   - 默认的 `Documents`
   - 新建的 `SW Data Mart Delivery Assets`
8. 打开 `SW Data Mart Delivery Assets`，选择 **New → Folder**，依次建立：

```text
01 Active Specifications/
02 Staff Templates/
03 Reference Standards/
04 Delivery Evidence/
05 Archive/
```

### 无法创建新 Document Library 时的替代方案

只有公司权限限制无法创建 Library 时，才在默认 `Documents` 内新建顶层文件夹 `SW Data Mart Delivery Assets`，并在 DRIVE register 中注明这是替代方案。这样会降低权限与版本治理的独立性，因此不是首选。

## 3. 上传文件并建立可下载资产

打开本地项目根目录，将以下文件上传到新创建的 `SW Data Mart Delivery Assets`：

| 目标文件夹 | 上传内容 |
|---|---|
| `01 Active Specifications/` | `deliverables/documentation/DRIVE-001_Integrated_Technical_Design_Specification.docx` |
| `02 Staff Templates/` | DRIVE、DA、IV、RP、EX 的五个 `.docx` 模板 |
| `02 Staff Templates/` | `DM_Data_Model_Template.xlsx`、`DL_Data_Lineage_Template.xlsx`、`DD_Data_Dictionary_Template.xlsx` |
| `02 Staff Templates/` | `DRIVE_Delivery_Register_Template.csv` |
| `03 Reference Standards/` | `deliverables/archive/component-standards/` 中的历史参考内容 |
| `04 Delivery Evidence/` | 已完成 enhancement 的 UAT、对账、发布记录、最终截图（按 DRIVE ID 建立子文件夹） |
| `05 Archive/` | 退役模板、旧版本规格或历史材料 |

上传操作可使用拖拽，或在文档库选择 **Upload → Files / Folder**。上传完成后，逐一打开关键文件，确认下载和预览正常。

## 4. 配置推荐 metadata 与视图

这一步不是页面上线的阻塞条件，但建议在正式推广前完成。

1. 在 `SW Data Mart Delivery Assets` 选择右上角 Settings → **Library settings**。
2. 在 Columns 区域依次选择 **Create column**，建立以下列：

| 列名 | 类型 | 建议值 / 用途 |
|---|---|---|
| Asset Type | Choice | DRIVE / DA / IV / RP / EX / DM / DL / DD / Evidence / Standard |
| DRIVE ID | Single line of text | 例如 `DRIVE-001` |
| End User | Person or Group | 业务验收联系人 |
| PMO Manager | Person or Group | 范围、优先级与发布协调责任人 |
| Technical Analyst | Person or Group | 技术设计、实现和证据责任人 |
| ERP Consultant | Person or Group | 业务规则、字段和验收场景责任人 |
| Status | Choice | Draft / In Review / Approved / Released / Retired |
| Effective Date | Date and time | 生效日 |
| Review Date | Date and time | 下次复核日 |
| Confidentiality | Choice | Internal / Confidential / Restricted |

3. 建立至少两个 View：
   - **Current Templates**：过滤 `02 Staff Templates/`，按 Asset Type 排序。
   - **Active Specifications**：过滤 `01 Active Specifications/`，按 Status 和 Review Date 排序。
4. 可选：启用 Major versioning，使模板和已批准设计保留版本记录。

## 5. 创建 Home Modern Page

Communication Site 通常已经有一个 Home Page。选择右上角 **Edit** 编辑它；如果没有，选择 **New → Page** 创建名为 `Home` 的空白页面，并设置为主页。

### 5.1 页头

1. 选择页头标题，输入：`SW Data Mart`。
2. 如公司允许，在标题上方或下方添加：`DRIVE Workspace`。
3. 使用简洁的业务图片或公司认可的主题色，不要使用会使文字难以阅读的背景图。

### 5.2 推荐页面结构与 Web Parts

按从上到下的顺序增加 Section 和 Web Part：

| 顺序 | Section / Web Part | 操作内容 |
|---|---|---|
| 1 | Hero 或 Text | 放置一句核心价值：`Turn trusted data into faster, consistent business decisions.` |
| 2 | Text + 四栏 Section | 填写 `index.html` 中 “What SW Data Mart helps you do” 的业务价值说明。 |
| 3 | Text + Quick Links | 建立三个入口：Describe the business question、Choose the delivery type、Use the shared template。 |
| 4 | Text + 五栏 Section | 展示 DRIVE 生命周期：Register → Define → Design → Validate → Release & operate。 |
| 5 | News 或 Text | 复制本地首页的三张 latest update 卡片；没有正式更新时可隐藏此区。 |
| 6 | Text + 五栏 Section | 放置 Global EDI ownership 和 Coat 说明。 |
| 7 | Quick Links | 指向 How Data Is Prepared、Trust & Governance、Template Library。 |

### 5.3 必须使用的责任边界文案

在 Home Page 的 Global EDI 区域，保留以下含义：

- Global EDI owns the Medallion 3-tier warehouse, Gold View and upstream ETL.
- APAC consumes the published Gold View and maintains local consumption objects through Refined Coat (RC), Sealer Coat (SC) and Top Coat (TC).
- APAC does not duplicate Global EDI upstream ETL.

不要将 RC / SC / TC 描述为一个新的 Global EDI 数据仓库或企业级上游数据工程。

### 5.4 保存与发布

1. 编辑期间选择 **Save as draft**（或组织显示的 Save and close）。
2. 请 PMO Manager、ERP Consultant 和 Technical Analyst 预览内容。
3. 确认页面链接和英文措辞后选择 **Publish**。
4. 若需要，将页面 Promote / Add to navigation。

## 6. 创建四个 Detail Modern Pages

每个页面都通过 **New → Page** 创建。选择 Blank 或组织规定的页面模板，输入页面名称，添加下列 Web Parts 后 Publish。

### 6.1 Request & Delivery

- **Page name：**`Request & Delivery`
- **本地内容来源：**`pages/drive-workspace.html`
- **建议 Web Parts：**Text、Quick Links、Document Library、Microsoft Lists（如组织允许）。
- **内容顺序：**业务问题 → DRIVE enhancement 说明 → DA / IV / RP / EX 的适用条件 → 当前 DRIVE 规格链接 → delivery register 链接。
- **链接：**把 active DRIVE specification 链接到 `01 Active Specifications/`；把 register 链接到 `02 Staff Templates/` 或正式 Microsoft List。

### 6.2 How Data Is Prepared

- **Page name：**`How Data Is Prepared`
- **本地内容来源：**`pages/architecture.html`
- **建议 Web Parts：**Text、Image、Quick Links；可用多个 Text Section 表达流程。
- **内容顺序：**Global EDI Gold View → Refined Coat (RC) → Sealer Coat (SC) → Top Coat (TC) → business output。
- **必须写明：**Gold View 和上游 ETL 由 Global EDI 负责；APAC 的文档记录的是 Gold View 之后的消费过程。
- **可选链接：**Data Model、Data Lineage、Data Dictionary，以及 DA 模板。

### 6.3 Trust & Governance

- **Page name：**`Trust & Governance`
- **本地内容来源：**`pages/governance.html`
- **建议 Web Parts：**Text、Quick Links；角色表可直接用 Text Web Part 的表格功能。
- **仅保留四个角色：**End User、PMO Manager、Technical Analyst、ERP Consultant。
- **内容顺序：**角色职责 → 决策边界 → 设计/测试/发布 gates → 文档、证据与复核规则。

### 6.4 Template Library

- **Page name：**`Template Library`
- **本地内容来源：**`pages/template-library.html`
- **建议 Web Parts：**Document Library（必须）、Text、Quick Links。
- **Document Library Web Part 配置：**
  1. 编辑页面，在所需位置选择 **+**。
  2. 搜索并选择 **Document Library** Web Part。
  3. 选择 `SW Data Mart Delivery Assets`。
  4. 在 Web Part 设置中选择 `Current Templates` view，或指定 `02 Staff Templates/` 文件夹。
  5. 选择 Apply；完成页面后选择 Publish。
- **页面说明：**先写 DRIVE 是 overall enhancement design，然后简要说明 DA、IV、RP、EX、DM、DL、DD 的用途。

## 7. 配置顶部导航

顶部导航是 Site 级别导航，不是单独的 Modern Page。

1. 在 Site 顶部导航区域选择 **Edit**。
2. 逐项选择 **Add link**，添加以下已发布 Page：
   1. Home
   2. Request & Delivery
   3. How Data Is Prepared
   4. Trust & Governance
   5. Template Library
3. 调整顺序使其与上表一致。
4. 选择 Save。
5. 在非编辑模式中逐个点击，确认没有指向本地 `file:///` 链接或 GitHub 临时链接。

## 8. 权限与发布控制

建议将 Site 和交付资产库的权限分开审视：

| 范围 | 建议权限 |
|---|---|
| Visitors | 阅读 Site Pages 和下载 approved templates。 |
| Members | 可维护允许的页面和工作文档；是否编辑模板取决于组织安排。 |
| Owners | 可发布页面、管理导航、管理文件库、权限与版本。 |
| `01 Active Specifications/` | 仅授权 Owner / Technical Analyst 修改；其他用户只读。 |
| `02 Staff Templates/` | 团队可读/下载；模板主文件的编辑权应受控制。 |
| `04 Delivery Evidence/` | 依项目敏感度限制访问，并按 DRIVE ID 管理。 |

如果需要对某个文件夹设置独立权限，请由 Site Owner 在该文件夹的 **Manage access** 中执行，并记录原因。不要无理由打断权限继承。

## 9. 上线验收清单

### 页面与导航

- [ ] Home、Request & Delivery、How Data Is Prepared、Trust & Governance、Template Library 已 Publish。
- [ ] 顶部导航顺序正确，所有链接可打开。
- [ ] 页面中没有 `file:///` 本地链接、无效 GitHub 临时链接或内部草稿说明。
- [ ] Home Page 在桌面和移动端可读。

### 文档与下载

- [ ] `SW Data Mart Delivery Assets` 是独立 Library，与 Documents 同级。
- [ ] 五个资产文件夹均已创建。
- [ ] DRIVE、DA、IV、RP、EX、DM、DL、DD 模板可下载。
- [ ] Template Library 页面中的 Document Library Web Part 显示正确 View/Folder。
- [ ] active specification、register 与证据文件有正确的 DRIVE ID。

### 治理与内容

- [ ] Global EDI Gold View 的责任边界表达正确。
- [ ] 对外内容统一使用 Refined Coat / Sealer Coat / Top Coat；没有将其描述为 Global EDI 的重复上游工作。
- [ ] 角色仅为 End User、PMO Manager、Technical Analyst、ERP Consultant。
- [ ] 文档库 Owners、Members、Visitors 权限已检查。
- [ ] 正式 Site URL 已记录在 DRIVE register。

## 10. 常见问题

| 情况 | 处理方式 |
|---|---|
| 没有 Create site | 联系 Microsoft 365 / SharePoint 管理员；自助建站可能已被关闭。 |
| 没有 New → Page | 确认自己是 Site Owner 或具有页面编辑权限；也可能被管理员策略限制。 |
| 不能创建 Document Library | 使用默认 Documents 下的替代顶层文件夹，并在 DRIVE register 记录例外；同时向管理员申请独立 Library。 |
| Document Library Web Part 看不到指定文件夹 | 先确认文件夹存在、当前用户有权限；在 Web Part 属性中选择正确 Library、View 或输入文件夹路径。 |
| 员工可以修改 master template | 检查 Library 或文件夹的 Manage access；缩小 Edit 权限并保留 Owners 的管理权。 |
| 页面不能被普通用户看到 | 确认页面已 Publish，且 Visitor 组具有 Read 权限。 |

## 官方操作参考

- [Create a communication site in SharePoint](https://support.microsoft.com/en-US/SharePoint/sites-in-sharepoint/create-a-communication-site-in-sharepoint)
- [Create a document library in SharePoint](https://support.microsoft.com/en-us/sharepoint/documents-and-library/create-a-document-library-in-sharepoint)
- [Create and use modern pages on a SharePoint site](https://support.microsoft.com/en-us/sharepoint/pages-in-sharepoint/create-and-use-modern-pages-on-a-sharepoint-site)
- [Use the Document Library web part](https://support.microsoft.com/en-us/sharepoint/web-parts-and-apps-in-sharepoint/use-the-document-library-web-part)

> Microsoft 365 的界面和可用功能可能因公司 Tenant 设置、权限、语言和 Targeted Release 状态而略有不同；如按钮名称不同，请以同义的 New / Create / Edit / Publish 操作入口完成相同步骤。
