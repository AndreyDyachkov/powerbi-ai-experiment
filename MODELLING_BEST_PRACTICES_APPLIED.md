# Modeling Best Practices – Applied

This document summarizes the Power BI modeling best practices that were evaluated and implemented in the churn dashboard data model.

---

## 1. Summarization (Default Aggregation)

**Issue:** Columns had incorrect default summarization, leading to wrong aggregations when users drag columns into visuals.

**Fixed:**

### FactChurnSnapshot
- **age**: Changed from `sum` → `average` (age should be averaged, not summed)
- **customer_tenure_months**: Changed from `sum` → `average` (tenure should be averaged)
- **premium_change_pct**: Changed from `sum` → `average` (percentage change should be averaged)

### DimDate
- **Year, MonthNo, QuarterNo, Day**: Changed from `sum` → `none` (date parts should not be aggregated)

### DimCustomer
- **age**: Changed from `sum` → `average`

**Impact:** When users drag these columns into visuals, Power BI will suggest the correct aggregation (average for metrics, none for date parts).

---

## 2. Hidden Columns

**Best Practice:** Hide foreign key columns from fact tables and internal/technical columns from end users.

**Fixed:**

### FactChurnSnapshot
- **region_name**: Added `isHidden` (users should use DimRegion[region_name] instead)
- **policy_type**: Added `isHidden` (users should use DimPolicyType[policy_type] instead)
- **churn_type**: Added `isHidden` (users should use DimChurnType[churn_type] instead)

### ChurnMeasures
- **Placeholder**: Added `isHidden` (technical column for calculated table structure)

**Impact:** Field list is cleaner; users see dimension tables for filtering/grouping, not fact table foreign keys.

---

## 3. Relationships

**Best Practice:** Explicitly define cardinality and cross-filter direction for clarity and performance.

**Fixed:**

All relationships in `relationships.tmdl` now specify:
- **fromCardinality: many** (fact table side)
- **toCardinality: one** (dimension table side)
- **crossFilteringBehavior: oneDirection** (filters flow from dimension to fact, not reverse)

**Relationships updated:**
- FactChurnSnapshot[as_of_date] → DimDate[Date]
- FactChurnSnapshot[region_name] → DimRegion[region_name]
- FactChurnSnapshot[policy_type] → DimPolicyType[policy_type]
- FactChurnSnapshot[churn_type] → DimChurnType[churn_type]
- FactChurnSnapshot[customer_id] → DimCustomer[customer_id]

**Impact:** Clear relationship behavior, better performance, prevents accidental bidirectional filtering.

---

## 4. Date Table

**Best Practice:** Use a dedicated date table (DimDate) instead of auto date tables.

**Fixed:**

- **Removed:** `LocalDateTable_2ba3bdce-9b7e-49b3-87fb-d9c1919fee65.tmdl` (auto date table)
- **Removed:** `DateTableTemplate_15349067-ee71-4e37-98f8-ca667cb32819.tmdl` (template)
- **Removed:** References to auto date tables from `model.tmdl`
- **Fixed:** DimDate variation now correctly points to DimDate.'Date Hierarchy' instead of LocalDateTable
- **Updated:** DimDate.Date format from "Long Date" → "Short Date" (better UX)

**Impact:** Cleaner model, explicit date table control, no hidden auto date tables.

---

## 5. Key Columns

**Best Practice:** Mark primary key columns in dimension tables for referential integrity and performance.

**Fixed:**

- **DimRegion[region_name]**: Added `isKey`
- **DimPolicyType[policy_type]**: Added `isKey`
- **DimChurnType[churn_type]**: Added `isKey`
- **DimDate[Date]**: Added `isKey`
- **DimCustomer[customer_id]**: Added `isKey`

**Impact:** Power BI can optimize joins and validate referential integrity.

---

## 6. Format Strings

**Best Practice:** Use appropriate date formats for better UX.

**Fixed:**

- **FactChurnSnapshot[as_of_date]**: Changed from "Long Date" → "Short Date"
- **DimDate[Date]**: Changed from "Long Date" → "Short Date"

**Impact:** Dates display more compactly in visuals (e.g., "1/15/2024" instead of "Monday, January 15, 2024").

---

## 7. SummarizationSetBy Annotation

**Best Practice:** When summarization is set by user (not automatic), use `annotation SummarizationSetBy = User`.

**Fixed:**

- All columns where `summarizeBy` was changed from automatic to user-defined now have `annotation SummarizationSetBy = User` instead of `Automatic`.

**Impact:** Clear documentation that these aggregations were intentionally set.

---

## Summary of Changes

| Category | Count | Files Modified |
|----------|-------|----------------|
| Summarization fixes | 7 columns | FactChurnSnapshot, DimDate, DimCustomer |
| Hidden columns | 4 columns | FactChurnSnapshot (3), ChurnMeasures (1) |
| Relationships | 5 relationships | relationships.tmdl |
| Key columns | 5 columns | All dimension tables |
| Date format | 2 columns | FactChurnSnapshot, DimDate |
| Removed tables | 2 tables | LocalDateTable, DateTableTemplate |
| Model references | 1 file | model.tmdl |

---

## Remaining Best Practices (Optional)

These are already in good shape or optional:

- ✅ **Star schema structure** – Already implemented (fact + dimensions)
- ✅ **Measure organization** – Measures are in a dedicated ChurnMeasures table
- ✅ **Naming conventions** – Clear Dim/Fact prefixes
- ✅ **Date hierarchy** – Proper hierarchy in DimDate
- ⚠️ **Data categories** – Could add data categories (e.g., Address, Image URL) if needed for specific columns
- ⚠️ **Sort columns** – Already set for Month and Quarter (sortByColumn)
- ⚠️ **Descriptions** – Could add descriptions to tables/columns for documentation (using /// syntax in TMDL)

The model now follows Power BI best practices for star schema design, relationships, summarization, and user experience.
