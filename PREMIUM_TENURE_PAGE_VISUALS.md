# Premium & Tenure Page – Measures and Suggested Visuals

Use this guide to build the **Premium & tenure** page in Power BI Desktop. The page focuses on premium levels, premium changes, tenure bands, and how they relate to churn (value and loyalty).

---

## New measures (ChurnMeasures table)

All are in the **ChurnMeasures** table.

### Premium

| Measure | Description | Format |
|--------|--------------|--------|
| **Total Premium** | SUM(current_premium) | $ #,##0.00 |
| **Avg Premium Change %** | AVERAGE(premium_change_pct) | 0.00 % |
| **Customers with Premium Increase** | Count where premium_change_pct > 0 | #,##0 |
| **Churn Rate Premium Increase %** | Churn rate among customers who had a premium increase | 0.00 % |
| **Churn Rate No Premium Increase %** | Churn rate among customers with no increase (or decrease) | 0.00 % |
| **Churned Premium** | Sum of current_premium for churned customers | $ #,##0.00 |
| **Avg Premium to Coverage Ratio** | AVERAGE(premium_to_coverage_ratio) | #,##0.0000 |

### Tenure (bands: Short < 12m, Medium 12–36m, Long > 36m)

| Measure | Description | Format |
|--------|--------------|--------|
| **Short Tenure Customers** | Count where customer_tenure_months < 12 | #,##0 |
| **Medium Tenure Customers** | Count where tenure 12–36 months | #,##0 |
| **Long Tenure Customers** | Count where customer_tenure_months > 36 | #,##0 |
| **Churn Rate Short Tenure %** | Churn rate among short-tenure customers | 0.00 % |
| **Churn Rate Medium Tenure %** | Churn rate among medium-tenure customers | 0.00 % |
| **Churn Rate Long Tenure %** | Churn rate among long-tenure customers | 0.00 % |

Existing measures useful here: **Avg Premium**, **Avg Tenure months**, **Total Customers**, **Churn Rate %**, **Churned Customers**.

---

## Suggested layout and visuals

### 1. Slicers (top or side)

- **DimDate** – Date or Date Hierarchy (single snapshot).
- Optional: **DimRegion**, **DimPolicyType**.

---

### 2. KPI row – premium and tenure summary

**Cards (5–7):**

- **Total Premium**, **Avg Premium**, **Churned Premium**
- **Avg Premium Change %**, **Customers with Premium Increase**, **Churn Rate Premium Increase %**
- **Avg Tenure months**; optional: **Short Tenure Customers**, **Medium Tenure Customers**, **Long Tenure Customers** (or one card per band)

*Purpose:* Premium revenue, average premium, premium lost to churn, impact of premium increases, and tenure mix.

---

### 3. Churn rate by tenure band (loyalty)

- **Visual:** **Clustered column chart** or **Table**.
- **Option A – Table:** Build a **Table** with columns: **Tenure Band** (typed: "Short (<12m)", "Medium (12–36m)", "Long (>36m)"), **Customers** (use **Short Tenure Customers**, **Medium Tenure Customers**, **Long Tenure Customers** in three rows or one measure with SWITCH), **Churn Rate %** (use **Churn Rate Short Tenure %**, **Churn Rate Medium Tenure %**, **Churn Rate Long Tenure %**). You can type the band names in a table and use measures that return the corresponding value based on row context, or use three separate columns with the three measures.
- **Option B – Column chart with bins:** **X-axis:** **FactChurnSnapshot[customer_tenure_months]** with **bins** (e.g. 12-month bins: 0–12, 12–24, 24–36, 36+). **Y-axis:** **Churn Rate %**. This shows churn rate by tenure bucket without a calculated table.
- **Option C – Three cards:** Three **Card** visuals in a row: **Churn Rate Short Tenure %** (label "Short <12m"), **Churn Rate Medium Tenure %** ("Medium 12–36m"), **Churn Rate Long Tenure %** ("Long >36m").
- **Purpose:** Compare churn across tenure bands (short vs medium vs long).

---

### 4. Churn rate: premium increase vs no increase (price sensitivity)

- **Visual:** **Clustered column chart** or **Table**.
- **Axis / Rows:** Two categories: "No premium increase" and "Had premium increase". Use a **Table** with two rows and columns: **Churn Rate No Premium Increase %**, **Churn Rate Premium Increase %**, and optionally **Total Customers** for each (use **Customers with Premium Increase** and a measure for customers with no increase: **Customers No Premium Increase** = COUNTROWS(FILTER(FactChurnSnapshot, FactChurnSnapshot[premium_change_pct] <= 0))).
- **Column chart:** Create a small **calculated table** (e.g. PremiumChangeSegment) with values "No increase" and "Had increase". Add a measure **Churn Rate by Premium Change** = SWITCH(SELECTEDVALUE(PremiumChangeSegment[Segment]), "Had increase", [Churn Rate Premium Increase %], "No increase", [Churn Rate No Premium Increase %], BLANK()). Use Segment on axis and this measure on value. If you prefer no calculated table, use a **Table** with two rows and one column "Segment" and one column with the two churn rate measures; then use that table for a column chart.
- **Purpose:** Compare churn for customers who had a premium increase vs those who did not.

---

### 5. Premium vs churn – scatter or by segment

- **Visual:** **Scatter chart** (if you have one row per customer or aggregate by segment).
- **X-axis:** **Avg Premium** (or **current_premium** from fact if by customer). **Y-axis:** **Churn Rate %**. **Legend or Details:** **DimRegion** or **DimPolicyType**.
- **Alternative:** **Clustered bar chart** – **Axis:** **DimRegion** or **DimPolicyType**. **Values:** **Avg Premium**, **Churn Rate %** (dual axis or two series). **Purpose:** Compare average premium and churn rate by region or product.

---

### 6. Tenure distribution and churn (optional)

- **Visual:** **Line chart** or **Area chart**.
- **X-axis:** **customer_tenure_months** (binned, e.g. 6- or 12-month bins) from FactChurnSnapshot.
- **Y-axis:** **Total Customers** (count) and optionally **Churn Rate %** (if available at bin level). Or use **Clustered column**: bins on X, **Total Customers** on Y, with **Churn Rate %** as a line on secondary axis if supported.
- **Purpose:** Show how many customers sit in each tenure bucket and how churn rate varies by tenure.

---

### 7. Premium change % distribution (optional)

- **Visual:** **Histogram** or **Clustered column** with **premium_change_pct** binned (e.g. decrease / 0 / small increase / large increase).
- **X-axis:** Bins of **FactChurnSnapshot[premium_change_pct]**.
- **Y-axis:** **Total Customers** or count of rows.
- **Purpose:** Distribution of premium changes; overlay or compare with churn rate by bin if possible.

---

## Quick checklist

1. Add **Slicer** for **DimDate** (and optionally Region / Policy type).
2. Add **Cards** for Total Premium, Avg Premium, Churned Premium, Avg Premium Change %, Customers with Premium Increase, Churn Rate Premium Increase %, Avg Tenure months (and optionally Short/Medium/Long Tenure Customers).
3. Add **Table** or **Column chart** (tenure bins) for **Churn rate by tenure band** (Short / Medium / Long or binned customer_tenure_months).
4. Add **Table** or **Column chart** for **Churn rate: No premium increase vs Had premium increase** (Churn Rate No Premium Increase %, Churn Rate Premium Increase %).
5. Add **Scatter** or **Bar chart** (Avg Premium vs Churn Rate % by Region or Policy type).
6. Optional: **Column/line** for tenure distribution and churn by tenure bin; **Histogram** for premium_change_pct.

All fields reference **FactChurnSnapshot**, **DimDate**, **DimRegion**, **DimPolicyType**, **DimCustomer**, and **ChurnMeasures**.
