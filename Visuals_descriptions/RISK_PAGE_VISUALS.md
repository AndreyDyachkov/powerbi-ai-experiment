# Risk Page – Measures and Suggested Visuals

Use this guide to build the **Risk** page in Power BI Desktop. The page focuses on churn probability and high-risk customers.

---

## New measures (ChurnMeasures table)

These measures were added for the Risk page. All are in the **ChurnMeasures** table.

| Measure | Description | Format |
|--------|--------------|--------|
| **High Risk Customers** | Count where `churn_probability_true` > 0.7 | #,##0 |
| **High Risk %** | High Risk Customers / Total Customers × 100 | 0.00 % |
| **Medium Risk Customers** | Count where probability > 0.4 and ≤ 0.7 | #,##0 |
| **Low Risk Customers** | Count where probability ≤ 0.4 | #,##0 |
| **Premium at High Risk** | Sum of `current_premium` where probability > 0.7 | $ #,##0.00 |
| **Max Churn Probability** | Maximum `churn_probability_true` in context | 0.00% |

Existing measures useful on Risk: **Total Customers**, **Avg Churn Probability**, **Revenue at Risk**.

---

## Suggested layout and visuals

### 1. Top row – KPI cards (4–5 cards)

- **High Risk Customers** (card)
- **High Risk %** (card)
- **Premium at High Risk** (card)
- **Avg Churn Probability** (card)
- Optional: **Revenue at Risk** (card)

*Fields:* Drag each measure into its own **Card** visual. Optionally add a **Slicer** for **DimDate** (Date or Date Hierarchy) so the whole page respects a single date/snapshot.

---

### 2. Distribution of churn probability – histogram

- **Visual:** **Clustered column chart** (or **Histogram** if available).
- **X-axis:** Create **bins** from `FactChurnSnapshot[churn_probability_true]` (e.g. 0–0.2, 0.2–0.4, 0.4–0.6, 0.6–0.8, 0.8–1.0), or use **Histogram** with bin count (e.g. 10).
- **Y-axis:** **Total Customers** (or **Count of customer_id** from FactChurnSnapshot).
- **Purpose:** Show how many customers fall in each probability band.

*Alternative:* Use **Line chart** with **churn_probability_true** on X (continuous or binned) and **Total Customers** on Y.

---

### 3. Risk segments – stacked bar or pie

- **Visual:** **Stacked bar chart** or **Pie/donut chart**.
- **Legend / Category:** Use a **calculated column** or **DAX** risk band if you add one (e.g. "High", "Medium", "Low" based on 0.7 and 0.4 thresholds).  
  If you prefer **measures only**, use **Legend**: add a **Table** with one row per segment and measures:
  - **High Risk Customers**
  - **Medium Risk Customers**
  - **Low Risk Customers**  
  Then use **Stacked bar**: X = "Segment" (High/Medium/Low), Y = the three measures, or use **Pie** with the same three measures and a legend.
- **Purpose:** Compare volume in High / Medium / Low risk.

*Simple option:* **Clustered column chart** – Axis: a **Segment** column (you can create in Power Query or use a table with three rows High/Medium/Low and use **High Risk Customers**, **Medium Risk Customers**, **Low Risk Customers** as values). If you don’t have a Segment dimension, use a **Table** visual with columns: Segment (typed), Count; rows: High / Medium / Low with the three measures.

---

### 4. High-risk list – table

- **Visual:** **Table**.
- **Columns:**  
  **DimCustomer[customer_id]**, **FactChurnSnapshot[churn_probability_true]**, **FactChurnSnapshot[current_premium]**, **DimCustomer[region_name]**, **DimChurnType[churn_type]** (optional).
- **Filter:** Add a **Filter on this visual** (or page filter): **churn_probability_true** > 0.7 (or use **Top N** by churn_probability_true, e.g. Top 50).
- **Purpose:** List of high-risk customers for action.

---

### 5. Risk by churn type (optional)

- **Visual:** **Clustered bar chart** or **Table**.
- **Axis / Rows:** **DimChurnType[churn_type]**.
- **Values:** **Avg Churn Probability**, **High Risk Customers**, or **Premium at High Risk**.
- **Purpose:** See which churn types have highest probability or exposure.

---

### 6. Slicers (page-level)

- **DimDate** – Date or Date Hierarchy (single select or range) so all visuals use the same snapshot.
- **DimRegion** or **DimPolicyType** – optional, to focus on a region or product.

---

## Quick checklist

1. Add **Slicer** for **DimDate** (and optionally Region / Policy type).
2. Add **4–5 Card** visuals for High Risk Customers, High Risk %, Premium at High Risk, Avg Churn Probability (and optionally Revenue at Risk).
3. Add **Column chart** or **Histogram** for distribution of **churn_probability_true** with **Total Customers** (or count).
4. Add **Stacked bar** or **Pie** for High / Medium / Low risk counts (using the three risk measures).
5. Add **Table** filtered to **churn_probability_true** > 0.7 with customer_id, probability, premium, region, churn_type.
6. Optionally add **Bar chart** or **Table** by **churn_type** with **Avg Churn Probability** or **High Risk Customers**.

All measures and fields above reference the existing star schema (FactChurnSnapshot, DimDate, DimRegion, DimPolicyType, DimChurnType, DimCustomer, ChurnMeasures).
