# Data Cleaning Guide: Handling Missing Values

This document describes a standard approach to identifying, assessing, and cleaning missing values in project datasets (e.g. `over250payments_2020_2024_combined.csv`) so that analyses remain valid and reproducible.

---

## 1. Overview

**Missing values** are empty cells, placeholders (e.g. "N/A", "—"), or values that clearly do not represent real data. Cleaning them involves:

1. **Identifying** where and how many missing values occur
2. **Classifying** the type of missingness (e.g. missing at random vs. structural)
3. **Deciding** whether to impute, recode, or exclude
4. **Documenting** all choices for reproducibility

This guide is written so you can apply it in **Excel** or adapt it in **Python** or other tools.

---

## 2. Identifying Missing Values

### 2.1 What Counts as “Missing”?


| Type                      | Examples                          | Typical columns                                            |
| ------------------------- | --------------------------------- | ---------------------------------------------------------- |
| **Empty**                 | Blank cell                        | `Card_Transaction`, `Irrecoverable_VAT`                    |
| **Placeholder text**      | "N/A", "—", "Unknown"             | Any text column                                            |
| **Redacted / anonymised** | "REDACTED - PERSONAL DATA"        | `Creditor_Name` (treat as missing for name-based analysis) |
| **Invalid numbers**       | Text in a numeric column, #DIV/0! | `Irrecoverable_VAT`                                        |


Treat a value as missing if it cannot be used meaningfully in your analysis (e.g. sums, filters, or grouping).

### 2.2 Finding Missing Values in Excel

**For many columns**

1. Use **Ctrl+F** → Find what: leave empty → **Find All** to see blank cells.
2. Or use **Conditional Formatting**: select the data range → **Home → Conditional Formatting → New Rule → Format only cells that contain → Blanks** → choose a format.

**Placeholder text**

1. **Ctrl+H** (Find & Replace).
2. Find: `N/A` (or `—`, `Unknown`, etc.).
3. Review **Find All** to see how many cells and which columns are affected before replacing.

Document the counts (e.g. “Column X: 1,234 blanks; Column Y: 56 ‘N/A’”) so you can report what was cleaned.

---

## 3. Classifying Missingness

Understanding *why* data are missing helps you choose a safe cleaning strategy.


| Kind of missingness                     | Meaning                                                               | Example                                                          |
| --------------------------------------- | --------------------------------------------------------------------- | ---------------------------------------------------------------- |
| **Missing completely at random (MCAR)** | Missingness unrelated to any variable                                 | Random data-entry skips                                          |
| **Missing at random (MAR)**             | Missingness related to other columns, not to the missing value itself | `Irrecoverable_VAT` blank when `Net_Amount` is below a threshold |
| **Missing not at random (MNAR)**        | Missingness related to the true value                                 | People opt out of reporting high amounts                         |


- **MCAR / MAR**: Imputation or “treat as zero” can be reasonable, depending on analysis.  
- **MNAR**: Imputation can bias results; often better to exclude or to analyse separately and document.

For payment data, blanks in **Irrecoverable_VAT** or **Card_Transaction** are often structural (e.g. “not applicable”) rather than random; document your assumption (e.g. “Blanks in Irrecoverable_VAT treated as 0”).

---

## 4. Cleaning Strategies

Choose one approach per column (or per analysis) and apply it consistently.

### 4.3 Fill with a Constant (e.g. Zero)

- **When**: Missing means “none” or “not applicable” in a **numeric** column (e.g. no VAT).  
- **Excel**:  
  1. Select the column.
  2. **Ctrl+G → Special → Blanks → OK**.
  3. Type `0` (or the constant) and press **Ctrl+Enter**.
- **Document**: “Blanks in [e.g. Irrecoverable_VAT] filled with 0 (no VAT).”

### 4.4 Imputation (Simple)

- **When**: You need a single “best guess” for analysis and missingness is not MNAR.  
- **Common methods**:  
  - **Numeric**: Mean, median, or mode of the column.  
  - **Categorical**: Mode (most frequent category).
- **Excel (numeric example)**:  
  1. Compute **MEDIAN(column range)** in a separate cell (ignoring blanks).
  2. Select the column → **Ctrl+G → Special → Blanks → OK**.
  3. Type `=MEDIAN($A$2:$A$10000)` (adjust range) and press **Ctrl+Enter**.
  4. Copy the column → **Paste Special → Values** to fix the imputed values.
- **Document**: “Missing [Column] imputed with column median (value: X).”

### 4.5 Exclude Rows (Listwise Deletion)

- **When**: You need a dataset with no missing values in key columns for a specific analysis.  
- **Excel**:  
  1. Add a **Filter** to the table.
  2. For each key column, filter out blanks (or “Missing”).
  3. Copy the visible rows to a new sheet/workbook and use that for the analysis.
- **Document**: “Analysis uses N rows with complete data in [list columns]. M rows excluded due to missing values.”

### 4.6 Redacted / Sensitive Placeholders

- **When**: Values like “REDACTED - PERSONAL DATA” represent missing information for privacy.  
- **Options**:  
  - **Exclude**: Filter out these rows for analyses that need real names.  
  - **Recode**: Replace with a single label (e.g. `(Redacted)`) for consistent reporting.
- **Document**: “Creditor names redacted for privacy; [N] rows labelled ‘(Redacted)’ and [excluded/included] in [analysis].”

---

## 5. Recommended Workflow

1. **Back up** the original file (e.g. keep `over250payments_2020_2024_combined.csv` unchanged).
2. **Create a working copy** (e.g. `over250payments_2020_2024_cleaned.csv` or a new Excel workbook).
3. **Count missing values per column** and note results (e.g. in a “Data diary” or sheet).
4. **Choose one strategy per column** (leave blank, recode, fill with constant, impute, or exclude).
5. **Apply cleaning** in a consistent order (e.g. trim text, then fix numbers, then handle blanks).
6. **Document** in the README or a separate “Cleaning log”: what was done, which columns, and how many rows affected.
7. **Validate**: spot-check sums, counts, and a few rows after cleaning to ensure nothing is broken.

---

## 6. Documentation Template

Keep a short log (e.g. in this README or in `data/README.md`) so others can reproduce your steps:

```text
## Missing value cleaning log – [Date]

| Column               | Missing count | Strategy        | Notes                    |
|----------------------|---------------|-----------------|--------------------------|
| Irrecoverable_VAT    | 301,000       | Fill with 0     | No VAT when blank        |
| Card_Transaction     | 298,000       | Fill with No    | Optional field           |
| Creditor_Name        | 0             | —               | Redacted rows recoded    |
```

---

## 7. References and Further Reading

- **Excel**: Microsoft support – “Find and replace” and “Filter for unique values or remove duplicate values”.  
- **Missing data**: Little & Rubin, *Statistical Analysis with Missing Data* (for formal treatment of MCAR/MAR/MNAR and imputation).  
- **Reproducibility**: Always record the exact steps and file versions used when cleaning missing values.

---

*Last updated: February 2025*