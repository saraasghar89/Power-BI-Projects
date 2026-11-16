# Data cleaning steps (Power Query)

## Issues found in the source workbook

| # | Issue | Fix |
| --- | --- | --- |
| 1 | Numbers mixed with CIHI codes: `x` suppressed (23,655 rows), `**` anesthesia suppressed (4,158), `n/a` (22,729) | Record the code in `DataStatus`, then convert to number and replace errors with `null` (never 0) |
| 2 | Service subtotal rows (NGS code 999: "Total consultations", "Total procedures", "Total services", …) | Remove; rebuilt through the Service Category dimension |
| 3 | Specialty subtotal rows (Medical specialists, Surgical specialists, Total specialists, Total physicians) | Remove; rebuilt through the Specialty dimension |
| 4 | Cardiology and Gastroenterology already counted in Internal medicine | Flag `IsSubspecialty = Yes` and exclude from totals in DAX |
| 5 | Jurisdiction "Total" row | Remove; Power BI sums provinces |
| 6 | Ratio columns (cost per service, services per 100K) | Drop; recalculated in DAX |
| 7 | Head count not additive across service categories | Separate fact table from "Total services" rows only |
| 8 | Population is wide and in calendar years | Unpivot; map year Y to fiscal year "Y–(Y+1)" (en dash) |

## Steps

**stg_Table1** (load disabled)
1. Load the Excel table `Table1`; rename columns (HeadCount, Services, Payments, CostPerService, ServicesPer100K, NGSCode, NGSCategory).
2. Trim text columns.
3. Add conditional column `DataStatus` (x → Suppressed, ** → Suppressed (anesthesia), n/a → Not applicable, else Reported).
4. Set measure columns to Decimal Number; Replace Errors → `null`. NGSCode → Whole Number.

**FactServices** (reference of stg_Table1) – filter NGSCode ≠ 999, Jurisdiction ≠ Total, remove the 4 specialty subtotals; drop HeadCount, CostPerService, ServicesPer100K. 106,558 rows.

**FactHeadCount** (reference of stg_Table1) – keep NGSCategory = Total services, same Jurisdiction/Specialty filters; keep Fiscal year, Jurisdiction, Specialty, HeadCount, DataStatus. 2,090 rows.

**FactPopulation** (Table3) – Unpivot Other Columns on Year; remove Total, N.W.T., Nun.; add `FiscalYear = Text.From([Year]) & "–" & Text.From([Year] + 1)`. 110 rows.

**Dimensions**
- `DimFiscalYear`: distinct FiscalYear + StartYear (sort FiscalYear by StartYear).
- `DimJurisdiction`: 11 distinct jurisdictions.
- `DimSpecialty`: 19 specialties; SpecialtyGroup (Family medicine / Medical specialties = sort ≤ 11 / Surgical specialties); IsSubspecialty.
- `DimServiceCategory`: 103 NGS codes; ServiceCategory = Consultations and visits (codes 1–24) or Procedures (25+).

## Validation (2024–25, no filters)

| Check | Model | CIHI published |
| --- | --- | --- |
| Total services | 301,069,612 | 301,069,599 (rounding) |
| Cost per service | $81.13 | $81.13 |
| Services per 100K | 728,605 | 728,605 |
