# SW Data Mart — Portable SharePoint Home Page Package

This package is designed to travel with the repository. On a company laptop, use it as the approved content and layout blueprint for a SharePoint Communication Site; no company account is required to review it locally.

Two ways to use it:

1. **Review the finished prototype locally.** Open `index.html` in a browser. It is a portable, multi-page Home Page prototype with working local navigation and template downloads.
2. **Recreate it in SharePoint.** Follow [SHAREPOINT_MIGRATION_GUIDE.md](SHAREPOINT_MIGRATION_GUIDE.md). It maps every page section to standard SharePoint web parts and includes the recommended document-library structure.

> SharePoint Modern pages do not import a standalone HTML file as a native page. The `index.html` file is the visual/content source of truth; recreate the same structure with standard web parts after uploading the documents to the destination site.

Pages:

- Home — starting point and quick links
- DRIVE Workspace — RICE-to-DRIVE terminology and request requirements
- Architecture — lineage, RC/SC/TC and data objects
- Governance — roles and release gates
- Template Library — download location for staff templates

The site is static and uses relative links, so it can be opened locally or adapted into a SharePoint communication site. The native template downloads resolve to `deliverables/template-library/`.
