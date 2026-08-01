# SW Data Mart — SharePoint Home Page Migration Guide

## Purpose

Use this guide on a company laptop to recreate the repository's local prototype as a SharePoint **Communication Site**. The local source page is [index.html](index.html). It is a content and layout blueprint; the SharePoint version should use standard Microsoft 365 web parts, not embedded custom HTML.

## Recommended site structure

Create one Communication Site named **SW Data Mart | DRIVE Workspace**. Create the following pages and add them to the top navigation in this order:

| Navigation label | SharePoint page name | Source prototype |
|---|---|---|
| Home | Home | `index.html` |
| DRIVE Workspace | DRIVE Workspace | `pages/drive-workspace.html` |
| Architecture | Architecture | `pages/architecture.html` |
| Governance | Governance | `pages/governance.html` |
| Template Library | Template Library | `pages/template-library.html` |

## Document library

Create a document library called **SW Data Mart Delivery Assets**. Create these folders:

```text
01 Active Specifications/
02 Staff Templates/
03 Reference Standards/
04 Delivery Evidence/
05 Archive/
```

Upload the following repository content:

| SharePoint location | Repository source | Purpose |
|---|---|---|
| `01 Active Specifications/` | `deliverables/documentation/DRIVE-001_Integrated_Technical_Design_Specification.docx` | Current governed master specification |
| `02 Staff Templates/` | `deliverables/template-library/DRIVE_Integrated_Technical_Design_Template.docx` | Staff authoring template |
| `02 Staff Templates/` | `deliverables/template-library/DRIVE_Delivery_Register_Template.csv` | Initial register/list structure |
| `03 Reference Standards/` | `deliverables/archive/component-standards/` | Historical DA / VI / RP / EX component standards |

Recommended library metadata: **Asset Type**, **DRIVE ID**, **Business Owner**, **Technical Owner**, **Status**, **Effective Date**, **Review Date**, and **Confidentiality**.

## Home Page build sheet

Build the Home page in the following order. The wording is already available in `index.html`.

| Page section | Standard SharePoint web part | Configuration |
|---|---|---|
| Site banner | Hero | Large heading: *Build analytics as a controlled business capability.* Two calls-to-action: Template Library and Start a DRIVE request. |
| Start here | Text + Quick Links | Three links: Register the business requirement, Use the integrated specification, Validate and release. |
| DRIVE delivery lifecycle | Text + five-column section | Register → Define → Design → Validate → Release & operate. |
| Latest workspace updates | News or Text | Add the three current update cards from the local Home page. |
| Quick access | Quick Links | DA Refinery architecture, Roles and approvals, Template Library. |
| RICE terminology note | Call to Action or Text | Explain the replacement of RICE wording with DRIVE terms. |

Use the title **SW Data Mart** and subtitle **DRIVE Workspace**. Keep the language in English for consistency with the design specification.

## Detail pages

- **DRIVE Workspace:** use the `pages/drive-workspace.html` content. Add a prominent link to the active specification and a Quick Link to the delivery register.
- **Architecture:** use `pages/architecture.html`. Present the EDI Gold View → Refined Coat → Sealer Coat → Top Coat lineage as a Process web part or simple text diagram.
- **Governance:** use `pages/governance.html`. Use a table for role ownership and delivery gates.
- **Template Library:** use `pages/template-library.html`. Add a **Document Library** web part filtered to `02 Staff Templates/` and `01 Active Specifications/`; this makes files downloadable without maintaining individual links.

## Build and handover checklist

- [ ] Confirm the Communication Site owner and members.
- [ ] Upload the four asset groups and apply metadata.
- [ ] Create the five pages and top navigation.
- [ ] Replace prototype file links with document-library links.
- [ ] Verify that staff can download templates but only authorised owners can edit master specifications.
- [ ] Confirm the Home page renders correctly on desktop and mobile.
- [ ] Record the live site URL in the DRIVE delivery register.

## Important note

The repository's `sharepoint-site/` folder remains useful as an offline preview and design reference. The destination SharePoint Home Page should be built from standard web parts so it remains supportable under normal corporate Microsoft 365 governance.
