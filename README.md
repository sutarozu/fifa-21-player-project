# FIFA 21 Player Dataset — Cleaned

Cleaned version of the FIFA 21 player dataset (source: sofifa.com) with all messy columns parsed into analysis-ready formats.

## Dataset Overview

- Rows: 18,979 players
- Raw columns: 78
- Cleaned columns: 90 (+12 engineered)
- Size: ~18 MB CSV
- Source: sofifa.com (FIFA 21, data cutoff ~Oct 2020)

## Cleaning Tasks Completed

All 5 questions from the original brief addressed:

| # | Original Problem | Solution | New Columns |
|---|------------------|----------|-------------|
| 1 | Height "5'7"", Weight "159lbs" | Parse to metric integers | height_cm (int), weight_kg (int) |
| 2 | Joined "Jul 1, 2004" | Split to year/month/day | joined_year, joined_month, joined_day |
| 3 | Value/Wage/Release Clause "€67.5M" | Parse K/M/B suffixes to EUR | value_eur, wage_eur, release_clause_eur |
| 4 | Hits contains newline chars | Strip whitespace to int | hits (int) |
| 5 | Team & Contract multi-line | Parse 3 formats (regular/loan/free) | team, contract_start, contract_end |

## Repository Structure

```
fifa21-player-dataset/
├── .gitignore
├── requirements.txt
├── README.md
├── data/
│   ├── raw/
│   │   └── fifa21_raw_data.csv
│   └── processed/
│       └── fifa21_clean.csv
├── notebooks/
│   └── 01_eda_cleaning.ipynb
```

## Quick Start

```bash
git clone https://github.com/<USERNAME>/fifa21-player-dataset.git
cd fifa21-player-dataset

python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

pip install -r requirements.txt
```

```python
import pandas as pd
df = pd.read_csv('data/processed/fifa21_clean.csv')
print(df.shape)  # (18979, 90)
```

## Key Cleaned Columns

| Column | Type | Description |
|--------|------|-------------|
| height_cm | int64 | Height in centimeters (155-206) |
| weight_kg | int64 | Weight in kilograms (50-110) |
| joined_year | int64 | Year player joined club (1998-2020) |
| joined_month | int64 | Month joined (1-12) |
| joined_day | int64 | Day joined (1-31) |
| value_eur | int64 | Market value in EUR (0-105.5M) |
| wage_eur | int64 | Weekly wage in EUR (0-560K) |
| release_clause_eur | int64 | Release clause in EUR (0-203.1M) |
| hits | int64 | Player search popularity (0-984) |
| team | object | Clean club name (714 unique) |
| contract_start | float64 | Contract start year (NaN for loans/free) |
| contract_end | float64 | Contract end year (NaN for free agents) |

## Contract Types (3 formats parsed)

| Type | Count | contract_start | contract_end | Logic |
|------|-------|----------------|--------------|-------|
| Regular | 17,728 (93.4%) | Year | Year | "2004 ~ 2021" |
| Loan | 1,013 (5.3%) | NaN | Loan expiry | "Jun 30, 2021 On Loan" |
| Free Agent | 238 (1.3%) | NaN | NaN | "Free" |

Missing contract_start/end is intentional — reflects real contract status.

## Reproduce Cleaning

The notebook `notebooks/01_eda_cleaning.ipynb` contains the complete reproducible pipeline:
1. EDA — info, describe, missing analysis, format inspection
2. Height/Weight — regex parse ft/in & lbs to cm/kg
3. Joined Date — pd.to_datetime to year/month/day
4. Monetary — custom parse_eur handles € + K/M/B
5. Hits — str.strip to pd.to_numeric to int
6. Team & Contract — multi-format regex parser
7. Export — fifa21_clean.csv

Run all cells top-to-bottom to regenerate.

## License

MIT License — free to use, modify, distribute.

Data source: sofifa.com — used for educational/research purposes.