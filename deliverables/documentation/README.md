# SW Data Mart — Documentation Pack

This pack turns the README architecture into four controlled, English delivery standards. It is written for managers and directors, ERP functional consultants, and implementation teams.

## Catalogue

| Code | Document | Use it when |
|---|---|---|
| DA-001 | Data Architecture Blueprint | Creating or changing data lineage, Top Coat objects, semantic model, or data dictionaries |
| VI-001 | Dashboard Design Standard | Delivering a Power BI or Tableau dashboard |
| RP-001 | Structured Report Design Standard | Delivering scheduled/historical reports or Finance overrides |
| EX-001 | Extension Design Standard | Delivering Excel outputs, RLS, DAX libraries, sync or automation |

## Classification convention

The README's DRIVE framework remains the strategic mnemonic (Data, Report, Insight, Validation, Extension). For the working delivery catalogue, use **DA**, **VI**, **RP**, and **EX** IDs. Validation is a mandatory control plane across all four classes rather than a separate output class.

## Object naming

`SW_APAC_<RC|SC|TC>_<SUBJECT>_<OBJECT>_<REF|MET|LNK|RUP>`

Only Top Coat objects are permitted as standard BI/report/extension inputs.

## Core universal metadata

Identity: surrogate key, natural key, source system, record hash.  WHO: created by, updated by, owner organization, data steward.  WHEN: effective dates, source extract time, load time, last modified time, as-of date.  Control: current/deleted flags, data-quality status, exception code, batch ID.  Security: legal entity, region, security group, sensitivity class.
