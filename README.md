# Global Freelancers Dataset: Data Cleaning & EDA

**Prepared by:** Bareera Maryam

A data cleaning and exploratory data analysis (EDA) project on a deliberately messy freelancers dataset (`global_freelancers_raw.csv`). The goal is to turn raw, inconsistent data into a clean, analysis-ready dataset and document every decision along the way.

## Dataset Overview

| Item | Value |
|---|---|
| Rows | 1,000 |
| Columns | 12 |
| Duplicate rows | 0 |
| Total null values (raw) | 541 |
| Columns needing type fixes | 5 |

**Columns:** `Freelancer_ID`, `Name`, `Gender`, `Age`, `Country`, `Language`, `Primary_Skill`, `Years_of_Experience`, `Hourly_Rate (USD)`, `Rating`, `Active_Status` (renamed from `is_Active`), `Client_Satisfaction`

## Data Quality Issues Found

| Column | Issue | Nulls |
|---|---|---|
| Name | Titles (Mr., Ms., Dr.) and suffixes (DDS, MD, PhD) mixed in | 0 |
| Gender | 10 different spellings (`f`, `F`, `FEMALE`, `male`, ...) | 0 |
| Age | Missing values, stored as float | 30 |
| Years_of_Experience | Missing values, stored as float | 51 |
| Hourly_Rate (USD) | Mixed formats (`USD 100`, `$40`, `50`), stored as text | 94 |
| Rating | Missing values | 101 |
| Active_Status | Mixed formats (`0`, `1`, `N`, `TRUE`, `FALSE`) | 89 |
| Client_Satisfaction | Percent sign text (`84%`) | 176 |

## Cleaning Steps

1. **Rename** `is_Active` to `Active_Status`.
2. **Duplicates check:** none found.
3. **Names:** removed titles and professional suffixes, then trimmed whitespace.
4. **Gender:** standardized all variants to `Male` / `Female`.
5. **Age & Years_of_Experience:** filled nulls with the median, converted to `int`.
6. **Hourly_Rate (USD):** stripped `$` / `USD` text, converted to numeric, filled nulls with `0`, converted to `int`.
7. **Rating:** filled nulls with the median.
8. **Active_Status:** mapped `0/1/N/Y/yes/no/true/false` to `True` / `False`, filled nulls with the mode.
9. **Client_Satisfaction:** removed `%`, converted to numeric, filled nulls with the median, converted to `int`.

**Result:** 0 null values across all 12 columns.

### Things to be aware of

- Missing hourly rates were filled with `0`. This pulls the average rate down (min = 0, mean = 47.53). Treating them as missing (`NaN`) or using the median would give higher, more realistic averages.
- The name cleaning regex `'Mr.|Ms.|Mrs.|Dr.|Miss'` treats `.` as "any character", so it can accidentally remove matching letter patterns inside names. Using `r'\b(Mr|Ms|Mrs|Dr|Miss)\.?\s'` is safer.
- Median imputation fills about 17.6% of Client_Satisfaction and about 10% of Rating, which reduces variance.

## Key Findings

- **Top paying skills:** Data Analysis ($50.00/hr), Cybersecurity ($49.94), Web Development ($49.42). Lowest: Graphic Design ($41.24).
- **Most freelancers by country:** South Korea (68), Canada (65), Germany (52).
- **Highest average rate by country:** Argentina ($55.00), Indonesia ($54.78), China ($52.24).
- **Correlations:** Age and Years_of_Experience are moderately correlated (0.60). Hourly rate, rating and client satisfaction show almost no relationship with each other (all |r| < 0.09).

See [`FINDINGS.md`](FINDINGS.md) for the short report.

## Charts

| Chart | File |
|---|---|
| Average hourly rate by skill | `charts/01_avg_rate_by_skill.png` |
| Top countries by freelancer count | `charts/02_top_countries_by_count.png` |
| Top 10 countries by average rate | `charts/03_top_countries_by_rate.png` |
| Correlation heatmap | `charts/04_correlation_heatmap.png` |
| Nulls before vs after cleaning | `charts/05_nulls_before_after.png` |
| Average values after cleaning | `charts/06_mean_summary.png` |

## Project Structure

```
freelancers_project/
├── README.md
├── FINDINGS.md
├── data_analysis.ipynb
└── charts/
    ├── 01_avg_rate_by_skill.png
    ├── 02_top_countries_by_count.png
    ├── 03_top_countries_by_rate.png
    ├── 04_correlation_heatmap.png
    ├── 05_nulls_before_after.png
    └── 06_mean_summary.png
```

## How to Run

1. Install dependencies:
   ```bash
   pip install pandas numpy matplotlib jupyter
   ```
2. Place `global_freelancers_raw.csv` somewhere on your machine.
3. In the notebook, update the path in the load cell, for example:
   ```python
   df = pd.read_csv("global_freelancers_raw.csv")
   ```
4. Open and run all cells:
   ```bash
   jupyter notebook data_analysis.ipynb
   ```

## Tools Used

- **pandas**: data handling and cleaning
- **numpy**: numerical computing
- **matplotlib**: visualization
