# DAX measures

All measures live in the `_Measures` table. The `IsSubspecialty = "No"` filter keeps Cardiology and Gastroenterology from being counted twice (they are already inside Internal medicine).

## Base measures

```dax
Total Services = CALCULATE ( SUM ( FactServices[Services] ), KEEPFILTERS ( DimSpecialty[IsSubspecialty] = "No" ) )
Total Payments = CALCULATE ( SUM ( FactServices[Payments] ), KEEPFILTERS ( DimSpecialty[IsSubspecialty] = "No" ) )
Physicians = CALCULATE ( SUM ( FactHeadCount[HeadCount] ), KEEPFILTERS ( DimSpecialty[IsSubspecialty] = "No" ) )
Population = SUM ( FactPopulation[Population] )
```

## KPIs

```dax
Cost per Service = DIVIDE ( [Total Payments], [Total Services] )
Avg Services per Physician = DIVIDE ( [Total Services], [Physicians] )
Services per 100K = DIVIDE ( [Total Services], [Population] ) * 100000
Physicians per 100K = DIVIDE ( [Physicians], [Population] ) * 100000
```

## Growth and indexes

```dax
Services per 100K PY = CALCULATE ( [Services per 100K], REMOVEFILTERS ( DimFiscalYear ), DimFiscalYear[StartYear] = MAX ( DimFiscalYear[StartYear] ) - 1 )
Per-Capita Growth % = DIVIDE ( [Services per 100K] - [Services per 100K PY], [Services per 100K PY] )
Services per 100K 2015 = CALCULATE ( [Services per 100K], REMOVEFILTERS ( DimFiscalYear ), DimFiscalYear[StartYear] = 2015 )
Growth since 2015 % = DIVIDE ( [Services per 100K] - [Services per 100K 2015], [Services per 100K 2015] )
Population Index = DIVIDE ( [Population], CALCULATE ( [Population], REMOVEFILTERS ( DimFiscalYear ), DimFiscalYear[StartYear] = 2015 ) ) * 100
Services Index = DIVIDE ( [Total Services], CALCULATE ( [Total Services], REMOVEFILTERS ( DimFiscalYear ), DimFiscalYear[StartYear] = 2015 ) ) * 100
```

## Type of care and specialty activity

```dax
Consultations and Visits = CALCULATE ( [Total Services], DimServiceCategory[ServiceCategory] = "Consultations and visits" )
Procedures = CALCULATE ( [Total Services], DimServiceCategory[ServiceCategory] = "Procedures" )
Procedure Share % = DIVIDE ( [Procedures], [Total Services] )
FM to Specialist Ratio = DIVIDE ( CALCULATE ( [Total Services], DimSpecialty[SpecialtyGroup] = "Family medicine" ), CALCULATE ( [Total Services], DimSpecialty[SpecialtyGroup] <> "Family medicine" ) )
Follow-up Visit Ratio = DIVIDE ( CALCULATE ( [Total Services], DimServiceCategory[NGSCode] >= 10, DimServiceCategory[NGSCode] <= 16 ), CALCULATE ( [Total Services], DimServiceCategory[NGSCode] <= 9 ) )
```

`Follow-up Visit Ratio` compares follow-up assessments (NGS 10–16) with initial consultations and major assessments (NGS 1–9). It is a proxy for repeat-visit frequency, because the data has no patient-level counts.
