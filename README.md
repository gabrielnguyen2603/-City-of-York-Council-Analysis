# City of York Council — Spend Analysis Dashboard

**Executive financial analytics for spend visibility, trend detection, and savings opportunities.**

---

## 1. Executive Summary

This dashboard provides **senior-level data analysis** of City of York Council payment data. It supports:

- **Spend visibility** — Total expenditure, category mix, and vendor concentration at a glance.
- **Trend and anomaly detection** — Time-series view to spot unusual spikes or drops for investigation.
- **Actionable savings** — A quantified saving model with low/base/high scenarios for strategic planning.
- **Accuracy and drill-down** — All metrics and visuals are driven by the same underlying data and filters, so figures are consistent and auditable.

The design follows a **KPI → trends → breakdowns → levers** flow: summary metrics first, then time and category analysis, then vendor concentration, and finally prescriptive saving opportunities.

---

## 2. Data Foundation & Accuracy

### 2.1 Source and scope

| Attribute | Detail |
| --------- | ------ |
| **Source file** | `data/clean/City of York Council.csv` |
| **Granularity** | One row per payment transaction |
| **Primary measure** | **Net_Amount** (£) — used for all spend, averages, and shares |
| **Time dimension** | **Payment_Date** — used for Year/Month filters and trend charts |

All dashboard numbers (KPIs, charts, and saving baselines) are **derived from this single source**. There are no separate manual inputs for the core metrics, which keeps the dashboard consistent and auditable.

### 2.2 Column reference (data dictionary)

| Column | Description | Dashboard use |
| ------ | ----------- | -------------- |
| **Organisation_Name** | Paying body (e.g. City of York Council) | Context / scope |
| **Directorate** | High-level area (Economy and Place, Health Housing & Adult So Care, etc.) | Optional slicing |
| **Department** | Department within directorate | Optional slicing |
| **Service_Plan** | Service or plan (Transport, Commercial property, Operations, etc.) | Optional slicing |
| **Creditor_Name** | Supplier or payee | Vendor rankings, Top 20, saving levers |
| **Payment_Date** | Payment date | Year/Month filters, trend chart, time grouping |
| **Card_Transaction** | Card payment flag (often blank) | Optional analysis |
| **Transaction_No** | Unique transaction ID | Counts, deduplication |
| **Net_Amount** | Payment amount (£) | **All spend calculations** |
| **Irrecoverable_VAT** | Non-recoverable VAT (often blank) | Optional |
| **Subjective_Group** | **Spend category** (Supplies And Services, Premises, etc.) | Category breakdown, heat table, top category |
| **Subjective_Subgroup** | Finer category | Optional drill-down |
| **Subjective_Detail** | Most detailed category | Optional drill-down |

**Calculation rule:** All dashboard metrics aggregate **Net_Amount** and group by **Payment_Date**, **Creditor_Name**, and/or **Subjective_Group** as specified per section below. Filters (Year, Month) apply to the entire dashboard.

---

## 3. Key Performance Indicators (KPIs)

Six KPIs give an **executive summary** of scale, mix, concentration, and savings. They are recalculated for the selected Year and Month.

| KPI | Business meaning | Calculation |
| --- | ----------------- | ----------- |
| **Total Spend (FY)** | Total expenditure in the selected period | Sum of **Net_Amount** over filtered transactions (e.g. £1,307,153,399.64) |
| **Top category (by spend)** | Largest spend category and its total | Group by **Subjective_Group**, sum **Net_Amount**; max category and value (e.g. Supplies And Services, £687,819,858.51) |
| **Base Saving** | Total baseline savings from the saving model | Sum of “Base” scenario savings across all levers in the Saving model table (e.g. £5,840,028.71) |
| **Top 20 vendors' share of spend (%)** | Supplier concentration | Sum **Net_Amount** per **Creditor_Name**; top 20 vendors’ total ÷ total spend × 100 (e.g. 30.07%) |
| **Average transaction value** | Mean payment size | Total **Net_Amount** ÷ count of transactions (e.g. £4,347.45) |
| **Total transaction count** | Volume of payments | Row count in filtered data (e.g. 300,671) |

**Insight:** High “Top 20 vendors’ share” suggests negotiation or consolidation opportunities; “Base Saving” links directly to the Saving model table for accountability.

---

## 4. Dashboard Components & Insights
<img width="1850" height="515" alt="Screenshot 2026-03-01 040629" src="https://github.com/user-attachments/assets/696395ed-24be-4463-9f50-cd08a6194dd9" />

### 4.1 Trend Anomalies (line chart)

- **Axis:** Time (e.g. years 2019–2025 or year-quarter) on X; **Sum of Net_Amount** on Y.
- **Purpose:** See how total spend evolves and **identify anomalies** (spikes or drops) that may need explanation (one-off projects, policy changes, or data issues).
- **Use:** Select a period with Year/Month filters; investigate any sharp changes with further drill-down by category or vendor.

### 4.2 Category vs Year heat table

- **Layout:** Rows = **Subjective_Group** (e.g. Capital Fees, Employees, Supplies And Services, Transport Costs); columns = years (e.g. 2020–2025) + **Total**.
- **Cells:** Total **Net_Amount** for that category in that year (with optional heat formatting for high/low).
- **Purpose:** Compare **category spend across years** and see shifts in mix (e.g. Supplies And Services dominance, or changes in Capital vs revenue spend).
- **Use:** Spot categories growing or shrinking over time for business review.

### 4.3 Top 20 Vendors (bar + line)

- **X-axis:** **Creditor_Name** (top 20 by total **Net_Amount** in the filter).
- **Left Y (bars):** **Net Amount** per vendor.
- **Right Y (line):** **% of total** spend per vendor.
- **Purpose:** Show **who gets the most spend** and how concentrated it is; supports procurement and contract discussions.
- **Use:** Align with “Top 20 vendors’ share” KPI and with the “Consolidate top 5 vendors” lever in the Saving model.

### 4.4 Saving model table

- **Columns:** Lever name, **Baseline Spend**, then **Saving Low**, **Saving Base**, **Saving High** (as % and/or £).
- **Purpose:** Turn analysis into **actionable savings** with low/base/high scenarios for planning and accountability.

**Example levers:**

| Lever | Rationale |
| ----- | --------- |
| Consolidate top 5 vendors | Reduce duplication and improve terms with largest **Creditor_Name** payees |
| Reduce fuel price by 3% | Apply assumed price reduction to fuel-related spend |
| Renegotiate insurance | Savings from contract renegotiation |
| Remove duplicate spend | Eliminate overlapping or redundant payments |
| Reduce discretionary spend | Cut non-essential expenditure |

**Base Saving** KPI = sum of the “Base” column in this table. Baseline spend per lever should be traceable to the same CSV (by category/vendor/service) so the dashboard stays accurate and auditable.

### 4.5 Category Breakdown (horizontal bar chart)

- **Axis:** **Subjective_Group** on Y; **Sum of Net_Amount** on X.
- **Purpose:** Rank categories by spend for the selected period; reinforces “Top category” KPI and supports prioritisation.
- **Use:** Quick check of where spend sits (e.g. Supplies And Services as largest) before drilling into heat table or saving levers.

---

## 5. Filters & Interactivity

- **Year:** Restricts all metrics and visuals to transactions in the selected year(s) (e.g. 2020–2025).
- **Month:** Further restricts to selected month(s) (1–12) for monthly or quarterly analysis.

**Accuracy note:** KPIs, trend chart, heat table, Top 20 vendors, category breakdown, and saving model baselines all use the **same filtered dataset**, so period comparisons and drill-downs stay consistent.

---

## 6. How to Use This Dashboard Professionally

1. **Set period** — Choose Year (and Month if needed) so every number refers to the same timeframe.
2. **Read KPIs** — Check Total Spend, Top category, Base Saving, and Top 20 share to gauge scale and concentration.
3. **Check trends** — Use the trend chart to spot anomalies, then narrow by year/month and, if possible, by category or vendor elsewhere.
4. **Compare categories** — Use the heat table and category breakdown to see where spend grew or shrank.
5. **Link vendors to savings** — Use Top 20 vendors with the Saving model (e.g. “Consolidate top 5 vendors”) to tie analysis to actions.
6. **Track savings** — Use the Saving model table and Base Saving KPI for targets and reporting; ensure baseline spend for each lever is defined from the same data for accuracy.

---

## 7. Summary

| Aspect | Detail |
| ------ | ------ |
| **Data** | `data/clean/City of York Council.csv`; **Net_Amount** = measure; **Payment_Date** = time; **Creditor_Name** = vendor; **Subjective_Group** = category. |
| **Structure** | KPIs → Trend → Category vs Year → Top 20 Vendors → Saving model → Category breakdown; filters apply globally. |
| **Accuracy** | Single source; one measure (Net_Amount); same filters for all components; saving baselines derived from same data. |
| **Insights** | Spend level and mix, trend anomalies, vendor concentration, category evolution, and quantified saving levers with scenarios. |

This README describes the **structure, definitions, and use** of the City of York Council Analysis dashboard so senior analysts and stakeholders can interpret it correctly and use it for accurate, insight-driven decisions.
