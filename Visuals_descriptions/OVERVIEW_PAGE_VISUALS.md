# Overview Page – Suggested Visuals

Use this guide to build the **Overview** page in Power BI Desktop. The page is the executive summary: key churn KPIs at a glance and trends over time.

**Measures used:** All from the **ChurnMeasures** table: **Total Customers**, **Churned Customers**, **Churn Rate %**, **Revenue at Risk**, **Avg Churn Probability**, **Avg Premium** (and optionally **Churned Premium**, **High Risk Customers**, **High Risk %**).

---

## 1. Slicer (top)

- **Visual:** **Slicer**.
- **Field:** **DimDate[Date]** or **DimDate** → **Date Hierarchy** (Year → Quarter → Month).
- **Mode:** Single select (one snapshot) or date range, depending on your data grain.
- **Purpose:** All visuals on the page respect this date so the Overview shows one point in time or one period.

---

## 2. KPI cards (top row)

**Visual:** **Card** (one per measure).

| Card 1       | Card 2           | Card 3             | Card 4                | Card 5 (optional)   |
|--------------|------------------|--------------------|------------------------|----------------------|
| Total Customers | Churn Rate %     | Revenue at Risk    | Avg Churn Probability | Churned Customers    |

- **Total Customers** – total in scope for the selected date.
- **Churn Rate %** – main headline churn metric.
- **Revenue at Risk** – premium at risk (churned + high probability).
- **Avg Churn Probability** – average predicted churn risk.
- **Churned Customers** – optional; raw count of churned.

*Tip:* Use **Format** → **Callout value** to emphasize the number; add a short **Title** per card (e.g. "Churn Rate", "Revenue at Risk").

---

## 3. Churn over time (main chart)

- **Visual:** **Line chart** or **Area chart** (stacked or single series).
- **X-axis:** **DimDate[Date]** (or **Year**, **Month** from DimDate if you have multiple dates).
- **Y-axis:** **Churn Rate %** and/or **Churned Customers** (and optionally **Total Customers**).
- **Legend:** Optional second series (e.g. **Churned Customers** vs **Total Customers**).
- **Purpose:** Show trend of churn rate or churn count over time. If you have one row per customer per snapshot, use **Churn Rate %**; if one row per period, **Churned Customers** and **Total Customers** work too.

*Alternative:* **Clustered column chart** – **DimDate** (e.g. Month) on axis, **Churned Customers** and **Total Customers** as columns, or **Churn Rate %** as a line on secondary axis.

---

## 4. Avg churn probability (gauge or card)

- **Visual:** **Gauge** (if available) or a second **Card**.
- **Value:** **Avg Churn Probability**.
- **Gauge:** Set min = 0, max = 1 (or 0–100%); optional target line (e.g. 0.2 or 20%).
- **Purpose:** Single number or gauge for “average risk” in the current selection.

---

## 5. Revenue at risk by segment (optional)

- **Visual:** **Clustered bar chart** (horizontal).
- **Axis:** **DimRegion[region_name]** or **DimPolicyType[policy_type]**.
- **Value:** **Revenue at Risk** (or **Churned Premium**).
- **Purpose:** Which regions or products contribute most to revenue at risk. Keep to one dimension (e.g. top 5 regions) so the Overview stays simple.

---

## 6. Small multi or summary table (optional)

- **Visual:** **Table**.
- **Columns:** **DimRegion[region_name]** (or **DimPolicyType[policy_type]**), **Total Customers**, **Churned Customers**, **Churn Rate %**.
- **Purpose:** Quick scan of churn by one segment without leaving the Overview. Limit rows (e.g. Top 5 by Churn Rate %) to avoid clutter.

---

## Suggested layout (wireframe)

```
[ Date Slicer – full width ]

[ Card: Total Customers ] [ Card: Churn Rate % ] [ Card: Revenue at Risk ] [ Card: Avg Churn Probability ]

[ Line/Area chart: Churn Rate % or Churned Customers over time – main area ]

[ Gauge: Avg Churn Probability ]   [ Bar chart: Revenue at Risk by Region (optional) ]
```

---

## Quick checklist

1. Add **Slicer** for **DimDate** (Date or Date Hierarchy).
2. Add **4–5 Cards**: Total Customers, Churn Rate %, Revenue at Risk, Avg Churn Probability (and optionally Churned Customers).
3. Add **Line chart** or **Area chart**: **DimDate** on X, **Churn Rate %** and/or **Churned Customers** on Y.
4. Add **Gauge** or **Card** for **Avg Churn Probability** (with 0–100% scale if gauge).
5. Optional: **Bar chart** – **DimRegion** or **DimPolicyType** on axis, **Revenue at Risk** on value; **Table** with Region/Policy Type, Total Customers, Churned Customers, Churn Rate %.

All fields reference **FactChurnSnapshot**, **DimDate**, **DimRegion**, **DimPolicyType**, and **ChurnMeasures**. No new measures are required for the Overview page.
