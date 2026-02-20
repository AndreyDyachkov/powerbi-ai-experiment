# Engagement & Claims Page – Measures and Suggested Visuals

Use this guide to build the **Engagement & claims** page in Power BI Desktop. The page focuses on complaints, payment behavior, contacts, and claims as signals for churn.

---

## New measures (ChurnMeasures table)

All are in the **ChurnMeasures** table.

### Engagement

| Measure | Description | Format |
|--------|--------------|--------|
| **Customers with Complaint** | Count where `complaint_flag` = 1 | #,##0 |
| **Churned with Complaint** | Churned customers who had a complaint | #,##0 |
| **Churn Rate Complainers %** | Churned with Complaint / Customers with Complaint × 100 | 0.00 % |
| **Customers Autopay** | Count where `autopay_enabled` = 1 | #,##0 |
| **Customers with Missed Payment** | Count where `missed_payment_flag` = 1 | #,##0 |
| **Avg Contacts 12m** | AVERAGE(num_contacts_12m) | #,##0.00 |

### Claims

| Measure | Description | Format |
|--------|--------------|--------|
| **Total Claims 12m** | SUM(num_claims_12m) | #,##0 |
| **Customers with Claims** | Count where num_claims_12m > 0 | #,##0 |
| **Avg Claim Amount** | AVERAGE(avg_claim_amount) | $ #,##0.00 |
| **Total Payout 12m** | SUM(total_payout_amount_12m) | $ #,##0.00 |
| **Avg Settlement Days** | AVERAGE(avg_settlement_time_days) | #,##0 |
| **Churned with Claims** | Churned customers who had at least one claim | #,##0 |
| **Churn Rate With Claims %** | Churn rate % in the subset of customers with claims | 0.00 % |

Existing measures useful here: **Total Customers**, **Churn Rate %**, **Churned Customers**.

---

## Suggested layout and visuals

### 1. Slicers (top or side)

- **DimDate** – Date or Date Hierarchy (single snapshot).
- Optional: **DimRegion**, **DimPolicyType**.

---

### 2. KPI row – engagement & claims summary

**Cards (4–6):**

- **Customers with Complaint** and **Churn Rate Complainers %**
- **Customers Autopay** (and optionally **Customers with Missed Payment**)
- **Customers with Claims** and **Churn Rate With Claims %**
- **Avg Claim Amount** or **Total Payout 12m**

*Purpose:* Quick read on complaint volume, autopay adoption, missed payments, claims volume, and churn rates for complainers and customers with claims.

---

### 3. Churn rate by complaint (engagement signal)

- **Visual:** **Clustered column chart** or **Bar chart**.
- **Axis:** **complaint_flag** from FactChurnSnapshot (0 vs 1; you can add a label column in PQ or use “0” / “1” and label in the visual).
- **Value:** **Churn Rate %** (or **Churned Customers** and **Total Customers** as two series).
- **Purpose:** Compare churn rate for customers with vs without a complaint.

*Alternative:* **Table** – Rows: No Complaint / Had Complaint (or 0 / 1). Columns: Total Customers, Churned Customers, Churn Rate %. Use **Table** with **complaint_flag** as row and the three measures.

---

### 4. Churn rate by payment behavior

- **Visual:** **Clustered column chart**.
- **Axis:** **autopay_enabled** (0 vs 1) or **payment_frequency** (from FactChurnSnapshot).
- **Values:** **Churn Rate %** (and optionally **Total Customers**).
- **Purpose:** Compare churn by autopay and by payment frequency.

*Optional:* Second chart or table with **payment_frequency** on axis and **Churn Rate %** + **Total Customers**.

---

### 5. Churn rate by claims (claims bands)

- **Visual:** **Clustered column chart** or **Table**.
- **Option A – Bands:** Create a **calculated column** on FactChurnSnapshot (or in Power Query) for claim bands, e.g. "0 claims", "1–2", "3+", then use that as **Axis** with **Churn Rate %** and **Total Customers**.
- **Option B – No calculated column:** Use **num_claims_12m** on the axis (if few distinct values) or a **Table** with **num_claims_12m** as rows and **Churn Rate %**, **Total Customers**, **Churned with Claims** as columns.
- **Purpose:** See how churn rate varies by number of claims.

---

### 6. Claims metrics – amount and settlement

- **Visual:** **Clustered column** or **Table**.
- **Axis / Rows:** **DimRegion** or **DimPolicyType** (or leave as single KPI).
- **Values:** **Avg Claim Amount**, **Total Payout 12m**, **Avg Settlement Days**, **Total Claims 12m**.
- **Purpose:** Compare claims cost and settlement speed by region or product.

---

### 7. Engagement vs churn – matrix (optional)

- **Visual:** **Matrix** or **Table**.
- **Rows:** **complaint_flag** (or Complainer Yes/No), **autopay_enabled** (or **payment_frequency**).
- **Values:** **Total Customers**, **Churned Customers**, **Churn Rate %**.
- **Purpose:** Two-way view of engagement (complaint, payment) vs churn.

---

### 8. Top / bottom by contacts or claims (optional)

- **Visual:** **Table** or **Clustered bar**.
- **Rows:** **DimCustomer[customer_id]** or **DimRegion** (or **DimPolicyType**).
- **Values:** **Avg Contacts 12m**, **Total Claims 12m** (from fact), **Churn Rate %**.
- **Filter:** Top N by contacts or by claims (filter on visual or Top N).
- **Purpose:** Identify high-contact or high-claim segments.

---

## Quick checklist

1. Add **Slicer** for **DimDate** (and optionally Region / Policy type).
2. Add **Cards** for Customers with Complaint, Churn Rate Complainers %, Customers Autopay, Customers with Claims, Churn Rate With Claims %, Avg Claim Amount (or Total Payout 12m).
3. Add **Column/bar chart**: **complaint_flag** (or Yes/No) on axis, **Churn Rate %** (and **Total Customers**) on value.
4. Add **Column/bar chart**: **autopay_enabled** or **payment_frequency** on axis, **Churn Rate %** on value.
5. Add **Column/bar chart** or **Table**: claims band or **num_claims_12m** on axis/rows, **Churn Rate %** and **Total Customers**.
6. Add **Chart** or **Table** by **DimRegion** or **DimPolicyType** with **Avg Claim Amount**, **Total Payout 12m**, **Avg Settlement Days**.
7. Optional: **Matrix** (complaint × autopay or payment_frequency) with **Churn Rate %** and counts.

All fields reference **FactChurnSnapshot**, **DimDate**, **DimRegion**, **DimPolicyType**, **DimChurnType**, **DimCustomer**, and **ChurnMeasures**.
