# Churn Dashboard – Build Summary

This document summarizes what was implemented according to the plan.

## Phase 0: Star schema in Power Query (M)

The single table `insurance_policyholder_churn_synthetic` was replaced with a star schema:

- **FactChurnSnapshot** – One row per customer per `as_of_date`; all 40 columns (measures and dimension keys). Partition M loads the CSV once, applies types, and replaces null `churn_type` with `"No Churn"`.
- **DimDate** – Distinct dates from the fact with columns: Date, Year, MonthNo, Month, QuarterNo, Quarter, Day, and a **Date Hierarchy** (Year → Quarter → Month → Day). Built in M from the same CSV.
- **DimRegion** – Distinct `region_name`.
- **DimPolicyType** – Distinct `policy_type`.
- **DimChurnType** – Distinct `churn_type` (nulls replaced by `"No Churn"` in M).
- **DimCustomer** – One row per `customer_id` (latest snapshot by `as_of_date`): customer_id, region_name, age_band, marital_status, age. Built in M (sort by date desc, then distinct by customer_id).

Relationships (in `ChurnModel_Cursor.SemanticModel/definition/relationships.tmdl`):

- FactChurnSnapshot[as_of_date] → DimDate[Date] (date part only)
- FactChurnSnapshot[region_name] → DimRegion[region_name]
- FactChurnSnapshot[policy_type] → DimPolicyType[policy_type]
- FactChurnSnapshot[churn_type] → DimChurnType[churn_type]
- FactChurnSnapshot[customer_id] → DimCustomer[customer_id]

The old table and auto date tables were removed.

## Phase 1: DAX measures

A **ChurnMeasures** table was added with these measures (in `ChurnModel_Cursor.SemanticModel/definition/tables/ChurnMeasures.tmdl`). (Power BI reserves the name "Measures", so the table is named ChurnMeasures.)

| Measure | DAX |
|--------|-----|
| Total Customers | COUNTROWS(FactChurnSnapshot) |
| Churned Customers | SUM(FactChurnSnapshot[churn_flag]) |
| Churn Rate % | DIVIDE([Churned Customers], [Total Customers], 0) * 100 |
| Revenue at Risk | Premium sum for churned or churn_probability > 0.7 |
| Avg Churn Probability | AVERAGE(churn_probability_true) |
| Avg Premium | AVERAGE(current_premium) |
| Avg Tenure months | AVERAGE(customer_tenure_months) |
| High Risk Customers / High Risk % / Medium/Low Risk / Premium at High Risk / Max Churn Probability | For Risk page; see [RISK_PAGE_VISUALS.md](RISK_PAGE_VISUALS.md). |
| Engagement: Customers with Complaint, Churn Rate Complainers %, Customers Autopay, Missed Payment, Avg Contacts 12m. Claims: Total Claims 12m, Customers with Claims, Avg Claim Amount, Total Payout 12m, Avg Settlement Days, Churn Rate With Claims % | For Engagement & claims page; see [ENGAGEMENT_CLAIMS_PAGE_VISUALS.md](ENGAGEMENT_CLAIMS_PAGE_VISUALS.md). |
| Premium: Total Premium, Avg Premium Change %, Customers with/No Premium Increase, Churn Rate Premium Increase % / No Increase %, Churned Premium, Avg Premium to Coverage. Tenure: Short/Medium/Long Tenure Customers, Churn Rate Short/Medium/Long Tenure % | For Premium & tenure page; see [PREMIUM_TENURE_PAGE_VISUALS.md](PREMIUM_TENURE_PAGE_VISUALS.md). |
| Segment: Customers % of Total, Churned % of Total Churned, Premium % of Total Premium | For Churn by segment page; use with Churn Rate %, Total/Churned Customers; see [CHURN_BY_SEGMENT_PAGE_VISUALS.md](CHURN_BY_SEGMENT_PAGE_VISUALS.md). |

## Phase 2/3: Report

- The only report page was renamed from **"Page 1"** to **"Overview"** (`ChurnModel_Cursor.Report/definition/pages/.../page.json`).
- **Adding visuals** (KPI cards, line charts, slicers, etc.) is best done in **Power BI Desktop**:
  1. Open `ChurnModel_Cursor.pbip` in Power BI Desktop.
  2. Ensure the CSV path in the semantic model matches your machine:  
     `C:\Users\Andrew\OneDrive\Documents\portfolio\PowerBI-MCP_experiment\data\insurance_policyholder_churn_synthetic.csv`
  3. On the Overview page, use the **Field list** to add visuals per [OVERVIEW_PAGE_VISUALS.md](OVERVIEW_PAGE_VISUALS.md): **Slicer** (DimDate), **Cards** (Total Customers, Churn Rate %, Revenue at Risk, Avg Churn Probability), **Line/area chart** (churn over time), **Gauge** (Avg Churn Probability), and optional bar/table.
  4. Add more pages (Churn by segment, Premium & tenure, Engagement & claims, Risk) and reuse the same fields/dimensions as in the plan.

## Data source path

All M partitions use this path (one per table; no shared query in TMDL):

`C:\Users\Andrew\OneDrive\Documents\portfolio\PowerBI-MCP_experiment\data\insurance_policyholder_churn_synthetic.csv`

If your data lives elsewhere, update the `File.Contents("...")` path in each of:

- `FactChurnSnapshot.tmdl`
- `DimDate.tmdl`
- `DimRegion.tmdl`
- `DimPolicyType.tmdl`
- `DimChurnType.tmdl`
- `DimCustomer.tmdl`

Then refresh the semantic model in Power BI Desktop.
