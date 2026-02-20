# Churn by Segment Page – Measures and Suggested Visuals

Use this guide to build the **Churn by segment** page in Power BI Desktop. The page answers "who churns?" by segmenting churn rate and volume by region, age band, policy type, and tenure.

---

## New measures (ChurnMeasures table)

All are in the **ChurnMeasures** table. These support segment-level comparison and "share of total" views.

| Measure | Description | Format |
|--------|--------------|--------|
| **Customers % of Total** | This segment's customer count as % of grand total (ignores segment dimensions). | 0.00 % |
| **Churned % of Total Churned** | This segment's churned count as % of total churned. | 0.00 % |
| **Premium % of Total Premium** | This segment's premium (current_premium sum) as % of total premium. | 0.00 % |

Existing measures used on this page: **Total Customers**, **Churned Customers**, **Churn Rate %**, **Avg Premium**, **Short/Medium/Long Tenure Customers**, **Churn Rate Short/Medium/Long Tenure %**.

---

## Suggested layout and visuals

### 1. Slicers (top or side)

- **DimDate** – Date or Date Hierarchy (single snapshot).
- **DimRegion[region_name]** – single or multi-select.
- **FactChurnSnapshot[age_band]** or **DimCustomer[age_band]** – if available in the model for slicing.
- **DimPolicyType[policy_type]** – single or multi-select.

*Purpose:* Filter the whole page to one snapshot and focus on one or more segments.

---

### 2. KPI row (optional)

**Cards:** **Total Customers**, **Churn Rate %**, **Churned Customers** (and optionally **Avg Premium**). These show the filtered totals for the current segment selection.

---

### 3. Churn rate by region

- **Visual:** **Clustered bar chart** or **Clustered column chart**.
- **Axis:** **DimRegion[region_name]**.
- **Values:** **Churn Rate %**. Optionally add **Total Customers** or **Churned Customers** as a second series or tooltip.
- **Purpose:** Compare churn rate across regions; sort by **Churn Rate %** to see worst regions first.

*Tip:* Add **Customers % of Total** or **Total Customers** in the tooltip to show segment size.

---

### 4. Churn rate by age band

- **Visual:** **Clustered bar chart** or **Clustered column chart**.
- **Axis:** **FactChurnSnapshot[age_band]** (or **DimCustomer[age_band]** if you use that).
- **Values:** **Churn Rate %** (and optionally **Total Customers**).
- **Purpose:** See which age bands churn most; control sort order (e.g. by age or by churn rate).

---

### 5. Churn rate by policy type

- **Visual:** **Clustered bar chart** or **Clustered column chart**.
- **Axis:** **DimPolicyType[policy_type]**.
- **Values:** **Churn Rate %** (and optionally **Total Customers**).
- **Purpose:** Compare churn by product/policy type.

---

### 6. Segment matrix (table)

- **Visual:** **Matrix** or **Table**.
- **Rows:** **DimRegion[region_name]** and/or **DimPolicyType[policy_type]** and/or **FactChurnSnapshot[age_band]** (e.g. Region on rows, Policy Type on columns, or two-level rows: Region then Policy Type).
- **Values:** **Total Customers**, **Churned Customers**, **Churn Rate %**, **Customers % of Total**, **Churned % of Total Churned** (and optionally **Avg Premium**, **Premium % of Total Premium**).
- **Purpose:** One view of volume, churn count, churn rate, and share of total for each segment or segment combination.

---

### 7. Churn by tenure band (segment view)

- **Visual:** **Clustered column chart** or **Table**.
- **Axis / Rows:** Tenure bands: use **Short Tenure Customers**, **Medium Tenure Customers**, **Long Tenure Customers** as three columns, or use **FactChurnSnapshot[customer_tenure_months]** with bins (e.g. 12).
- **Values:** **Churn Rate Short Tenure %**, **Churn Rate Medium Tenure %**, **Churn Rate Long Tenure %** (or **Churn Rate %** if using binned tenure on axis).
- **Purpose:** Compare churn rate by tenure band; optionally add **DimRegion** or **DimPolicyType** in legend to see tenure × segment.

---

### 8. Top N segments by churn rate (optional)

- **Visual:** **Clustered bar chart**.
- **Axis:** **DimRegion[region_name]** (or **DimPolicyType[policy_type]**).
- **Value:** **Churn Rate %**.
- **Filter:** **Top N** by **Churn Rate %** (e.g. Top 10) so the chart shows the worst segments only.
- **Purpose:** Focus on the highest-churn regions or policy types.

---

### 9. Segment contribution – donut or pie (optional)

- **Visual:** **Donut chart** or **Pie chart**.
- **Legend:** **DimRegion[region_name]** (or **DimPolicyType[policy_type]** or **age_band**).
- **Values:** **Churned Customers** or **Churned % of Total Churned**.
- **Purpose:** Show which segments contribute most to total churn by volume.

---

## Quick checklist

1. Add **Slicers** for **DimDate**, **DimRegion**, **DimPolicyType**, and **age_band** (if available).
2. Add **Clustered bar/column**: **DimRegion[region_name]** on axis, **Churn Rate %** (and **Total Customers**) on value.
3. Add **Clustered bar/column**: **age_band** on axis, **Churn Rate %** on value.
4. Add **Clustered bar/column**: **DimPolicyType[policy_type]** on axis, **Churn Rate %** on value.
5. Add **Matrix** or **Table** with **Region** (and/or **Policy Type**, **age_band**) on rows; **Total Customers**, **Churned Customers**, **Churn Rate %**, **Customers % of Total**, **Churned % of Total Churned** in values.
6. Add **Column chart** or **Table** for churn rate by tenure band (Short/Medium/Long or binned tenure).
7. Optional: **Top N** bar chart by **Churn Rate %**; **Donut** by **Churned Customers** per segment.

All fields reference **FactChurnSnapshot**, **DimDate**, **DimRegion**, **DimPolicyType**, **DimCustomer**, and **ChurnMeasures**.
