Physician Service Utilization in Canada, 2015–2016 to 2024–2025
An interactive Power BI dashboard and analysis of ten years of national physician billing and service-utilization data from the Canadian Institute for Health Information (CIHI). It looks at how physician services respond to population growth, how care is shifting between consultations and procedures and between family medicine and specialists, and where regional access is weakest.
![Executive overview](National-Physician-Historical-Utilization/dashboard/screenshots/02_overview.png)
Business questions
How have physician service volumes and per-capita rates changed from 2015–16 to 2024–25, and did they keep pace with population growth?
How is care split between consultations/visits and surgical or diagnostic procedures, and how is that changing?
How does activity differ across family medicine, medical specialties and surgical specialties (services per physician, follow-up intensity, cost per service)?
Which provinces and territories show bottlenecks in physician supply or primary-care delivery?
Key findings
Indicator	Result
Total physician services, 2024–25	301.1 million
Services per 100K population, 2015–16 → 2024–25	720,713 → 728,605 (+1.1%)
Services index vs population index (2015 = 100)	116.6 vs 115.4 – services only matched population growth; 96.7 in 2020–21 (COVID-19)
Family medicine services per 100K	−6.1% (medical specialties +11.7%, surgical +6.2%); fell in 9 of 11 jurisdictions
Services per family physician	3,574 → 3,224 (−10%) while family physicians grew 20% (38,908 → 46,713)
Procedures share of all services	26.7% → 33.9%; consultations/visits per 100K −8.8%
Cost per service	$66.39 → $81.13 (+22%)
Lowest physician supply, 2024–25	Sask. 156, N.S. 158, N.L. 162 per 100K (national 213)
Recommendations
Protect primary-care access. Prioritize family-medicine capacity in N.S., P.E.I., Sask., N.L. and N.B.; expand team-based and blended-capitation models.
Plan on workload, not headcount. Use services per physician and age-adjusted per-capita rates for workforce targets.
Target recruitment to low-supply provinces. Focus on Sask., N.S. and N.L.
Close the data gap on alternative payments. National shadow billing would make non-fee-for-service primary care visible.
Monitor procedure growth and value. Track the 28% rise in procedures per capita and the 22% rise in cost per service against outcomes.
Dashboard
Page	Contents
Home	Landing page with navigation tiles
Overview	KPIs: total services, average services per physician, services per 100K, per-capita growth; 10-year trend; visits vs procedures; ranking by jurisdiction
Type of Care	Consultations vs procedures over time and per capita; jurisdiction × year heatmap; top 10 procedures
Specialty Activity	Family medicine : specialist ratio, follow-up visit ratio, cost per service; trends by specialty group; specialty summary table
Regional Access	Physician supply vs service delivery scatter; population vs services index; per-capita growth by jurisdiction and specialty group
Summary	Key findings, recommendations and data limitations
References	Literature and data sources
Slicers (synced across pages): Jurisdiction, Specialty Group, Fiscal Year, Service Category.
	
![Type of care](National-Physician-Historical-Utilization/dashboard/screenshots/03_type_of_care.png)	![Specialty activity](National-Physician-Historical-Utilization/dashboard/screenshots/04_specialty_activity.png)
![Regional access](National-Physician-Historical-Utilization/dashboard/screenshots/05_regional_access.png)	![Summary](National-Physician-Historical-Utilization/dashboard/screenshots/06_summary.png)
The full dashboard export is in `dashboard/Physician_Utilization_Dashboard.pdf`.
Data
Source: CIHI, National Physician Database Historical Utilization, 2015–2016 to 2024–2025 — Data Tables (Table 1: utilization by specialty and National Grouping System category; Table 3: Statistics Canada net population). See `data/README.md` for how to obtain it.
Scope: fee-for-service physician billings for 10 provinces and Yukon (N.W.T. and Nunavut not reported); 19 specialties; 103 NGS service categories.
Method
Power Query cleaning – removed title rows and line breaks in headers, kept CIHI suppression codes (`x`, `**`, `n/a`) in a `DataStatus` column and converted them to blanks (never zeros), and removed subtotal rows (NGS code 999, specialty subtotals, the national "Total" row) to prevent double counting. Steps: `docs/data_cleaning_steps.md`.
Star-schema model – three fact tables (services, head count, population) and four dimensions (fiscal year, jurisdiction, specialty, service category). Diagram and relationships: `docs/data_model.md`.
DAX measures – 19 measures, including subspecialty-safe totals, per-capita rates, year-over-year and since-2015 growth, and index series. Full list: `docs/dax_measures.md`.
Validation – model totals reproduce CIHI's published figures (2024–25: 301,069,612 services, $81.13 per service, 728,605 per 100K).
Limitations
Fee-for-service billing only; salary, capitation and other alternative payments are excluded, so jurisdictions with more physicians on alternative plans (e.g. N.S., P.E.I.) show lower service rates.
No patient-level counts: "repeat visit frequency" and "patient volume ratio" are proxies (follow-up visit ratio; family medicine : specialist services ratio).
Suppressed cells are excluded from totals; Yukon has many suppressed values.
Head counts summed across provinces are about 1.8% above CIHI's national count because physicians billing in more than one province are counted in each.
2020–21 reflects COVID-19 disruption.
References
Barer ML, Evans RG, McGrail KM, et al. Beneath the calm surface: the changing face of physician-service use in British Columbia, 1985/86 versus 1996/97. CMAJ. 2004;170(5):803–807. doi:10.1503/cmaj.1020460
McGrail KM, Evans RG, Barer ML, et al. Diagnosing senescence: contributions to physician expenditure increases in British Columbia, 1996/97 to 2005/06. Healthcare Policy. 2011;7(1):41–54. doi:10.12927/hcpol.2011.22527
Islam R, Kralj B, Sweetman A. Physician workforce planning in Canada: the importance of accounting for population aging and changing physician hours of work. CMAJ. 2023;195(9):E335–E340. doi:10.1503/cmaj.221239
McDonald T, Schultz SE, Green LA, et al. Family physician count and service provision in Ontario and Alberta between 2005/06 and 2017/18: a cross-sectional study. CMAJ Open. 2023;11(6):E1102. doi:10.9778/cmajo.20220201
Lavergne R, Peterson S, Rudoler D, et al. Productivity decline or administrative avalanche? Examining factors that shape changing workloads in primary care. Healthcare Policy. 2023;19(1):114–129. doi:10.12927/hcpol.2023.27152
Tools
Power BI Desktop · Power Query · DAX
Repository structure
```
Power-BI-Projects/
├── README.md
└── National-Physician-Historical-Utilization/
    ├── physician.pbix
    ├── dashboard/
    │   ├── Physician_Utilization_Dashboard.pdf
    │   ├── screenshots/        page images
    │   └── backgrounds/        canvas background images
    ├── docs/
    │   ├── data_cleaning_steps.md
    │   ├── data_model.md
    │   └── dax_measures.md
    ├── report/
    │   └── Report_Cover_Page.pdf / .png
    └── data/
        └── README.md           how to get the CIHI workbook
```
