# City of York Council Analysis Dashboard

This document describes the **City of York Council Analysis** dashboard and the underlying data it is built from.

---

## Data source

The dashboard is based on the payment data file:

- **File:** `data/clean/City of York Council.csv`
- **Content:** Individual payment transactions made by City of York Council, with one row per transaction.

### CSV columns


| Column                  | Description                                                                                                                |
| ----------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| **Organisation_Name**   | Paying body (e.g. City of York Council)                                                                                    |
| **Directorate**         | High-level organisational area (e.g. Economy and Place, Health Housing & Adult So Care, Customer & Corporate Services)     |
| **Department**          | Department within the directorate                                                                                          |
| **Service_Plan**        | Service or plan the spend relates to (e.g. Transport, Commercial property, Operations)                                     |
| **Creditor_Name**       | Supplier or payee name                                                                                                     |
| **Payment_Date**        | Date of the payment (used for year/month filters and trend charts)                                                         |
| **Card_Transaction**    | Indicates if the payment was by card (often blank)                                                                         |
| **Transaction_No**      | Unique transaction identifier (e.g. 202021CR00000001)                                                                      |
| **Net_Amount**          | Payment amount in pounds (£) — **main measure used for all spend calculations**                                            |
| **Irrecoverable_VAT**   | VAT that cannot be recovered (often blank)                                                                                 |
| **Subjective_Group**    | **Spend category** (e.g. Supplies And Services, Premises, Capital Purchases) — used for category breakdowns and heat table |
| **Subjective_Subgroup** | Finer category (e.g. Repairs and Maintenance, Grants and Subscriptions)                                                    |
| **Subjective_Detail**   | Most detailed category (e.g. Repairs and Maintenance (Reactive), Subscriptions)                                            |


All dashboard metrics and charts are derived from this CSV by aggregating **Net_Amount** and grouping by **Payment_Date**, **Creditor_Name**, and **Subjective_Group** as needed.

---

## Dashboard overview

The dashboard provides a financial analysis view of the council’s spending: total spend, trends, category and vendor breakdowns, and a simple savings model. Users can filter by **Year** and **Month** (from **Payment_Date**) so all visuals and KPIs update for the selected period.

<img width="1850" height="515" alt="image" src="https://github.com/user-attachments/assets/1a96b301-9f23-4f3d-8ad2-2d1a160e6f8c" />



---

## 1. Key performance indicators (KPIs) — top row

Six summary metrics sit at the top. They are calculated from the filtered data (according to the selected year/month).


| KPI                                    | What it shows                                                              | How it’s derived from the data                                                                                                          |
| -------------------------------------- | -------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| **Total Spend (FY)**                   | Total expenditure in the selected financial year / period                  | Sum of **Net_Amount** for all transactions in the filter (e.g. £1,307,153,399.64)                                                       |
| **Top category (by spend)**            | The single **Subjective_Group** with the highest total spend and its total | Group by **Subjective_Group**, sum **Net_Amount**, take the category with the largest sum (e.g. Supplies And Services, £687,819,858.51) |
| **Base Saving**                        | Baseline total savings from the savings model (see section 5)              | Sum of the “Base %” savings across all levers in the Saving model table                                                                 |
| **Top 20 vendors' share of spend (%)** | Concentration of spend in the largest suppliers                            | Sum **Net_Amount** per **Creditor_Name**, take top 20, sum their spend, divide by total spend × 100 (e.g. 30.07%)                       |
| **Average transaction value**          | Mean payment size                                                          | Total **Net_Amount** ÷ number of transactions (e.g. £4,347.45)                                                                          |
| **Total transaction count**            | Number of payment records                                                  | Count of rows in the filtered data (e.g. 300,671)                                                                                       |


These KPIs give a quick view of scale, category mix, vendor concentration, and transaction volume.

---

## 2. Trend Anomalies chart

- **Type:** Line chart.
- **X-axis:** Time (e.g. years 2020–2025), often at year-quarter level if a “Year Quarter” control is used.
- **Y-axis:** **Sum of Net_Amount** (total spend) for each period.

**Purpose:** Shows how total spend changes over time and helps spot unusual spikes or drops (“anomalies”). The data comes from grouping by the time part of **Payment_Date** and summing **Net_Amount**. A relatively stable line suggests consistent spending; big jumps or falls may need further investigation.

---

## 3. Category vs Year heat table

- **Type:** Pivot-style table (heat table).
- **Rows:** **Subjective_Group** (spend categories), e.g. Capital Fees, Capital Financing, Capital Purchases, Employees, Miscellaneous Expenses, Other Capital, Premises, Supplies And Services, Transport Costs.
- **Columns:** Years (e.g. 2020–2025) plus a **Total** column per category.
- **Cells:** Total **Net_Amount** for that category in that year (or total across years).

**Purpose:** Lets you see how much the council spent in each category in each year and how the mix changes over time. For example, “Supplies And Services” might peak in 2021 (£814,060,378.07) while other categories stay flatter. The table is built by grouping the CSV by **Subjective_Group** and the year from **Payment_Date**, then summing **Net_Amount**.

---

## 4. Top 20 Vendors chart

- **Type:** Combined bar and line chart.
- **X-axis:** **Creditor_Name** — the top 20 vendors by total **Net_Amount** in the filtered period.
- **Left Y-axis (bars):** **Net Amount** — total spend with each vendor (e.g. blue bars).
- **Right Y-axis (line):** **Percent of total** — each vendor’s share of total spend (e.g. orange line).

**Purpose:** Shows which suppliers receive the most money and how concentrated spend is. It is built by summing **Net_Amount** per **Creditor_Name**, ranking by that sum, taking the top 20, and then computing each vendor’s share of total spend. Names like “City of York”, “North”, “United” (and others) are the **Creditor_Name** values from the CSV.

---

## 5. Saving model table

- **Type:** Table of saving “levers” and estimated savings.
- **Columns:** Lever name, **Baseline Spend**, and three saving scenarios — **Saving Low %**, **Saving Base %**, **Saving High %** — often shown as **Saving Low**, **Saving Base**, **Saving High** in currency.

**Example levers (as in the dashboard):**


| Lever                      | What it represents                                                                                                                            |
| -------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| Consolidate top 5 vendors  | Potential savings from reducing duplication and renegotiating with the five largest **Creditor_Name** payees (Baseline Spend e.g. £1,207,072) |
| Reduce fuel price by 3%    | Savings from a 3% reduction in fuel-related spend (identified from categories/vendors)                                                        |
| Renegotiate insurance      | Savings from renegotiating insurance contracts                                                                                                |
| Remove obsolete spend      | Savings from cutting spend identified as obsolete or redundant                                                                                |
| Reduce discretionary spend | Savings from reducing non-essential or discretionary expenditure                                                                              |


**Purpose:** Illustrates potential savings under low, base, and high assumptions. **Baseline Spend** for each lever is derived from the underlying data (e.g. by category or vendor); the **Base Saving** KPI is the sum of the “Base %” savings across these levers.

---

## 6. Category Breakdown chart

- **Type:** Horizontal bar chart.
- **Y-axis:** **Subjective_Group** (spend categories), e.g. Supplies And Services, Capital Purchases, Miscellaneous Expenses, Premises, Employees, Capital Fees, Transport Costs, Other Capital, Capital Financing.
- **X-axis:** **Sum of Net Amount** (£) for each category.

**Purpose:** Shows the relative size of each spend category at a glance. “Supplies And Services” is typically the largest; “Capital Purchases” and “Miscellaneous Expenses” are also large. Built by grouping the CSV by **Subjective_Group** and summing **Net_Amount** (within the current year/month filter).

---

## 7. Filters (Year and Month)

- **Year filter:** List of years (e.g. 2020–2025) from **Payment_Date**. Selecting one or more years restricts all dashboard calculations and visuals to transactions in those years.
- **Month filter:** Months 1–12 from **Payment_Date**. Selecting one or more months further narrows the period (e.g. to a specific quarter or month).

All KPIs, the trend chart, heat table, top 20 vendors, category breakdown, and the baseline figures in the saving model use only the filtered transactions.

---

## Summary

- **Data:** The dashboard is based on `data/clean/City of York Council.csv`, with **Net_Amount** as the main measure, **Payment_Date** for time and filters, **Creditor_Name** for vendors, and **Subjective_Group** for categories.
- **KPIs:** Total spend, top category, base saving, top 20 vendors’ share, average transaction value, and transaction count.
- **Charts/tables:** Trend over time, category-by-year heat table, top 20 vendors (amount and %), category breakdown bars, and a saving model table.
- **Interactivity:** Year and month filters drive the entire dashboard so you can analyse spending and savings by period.

