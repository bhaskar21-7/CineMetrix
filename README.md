# CineMetrix

A data project on Netflix's content catalog — and, more honestly, a project about catching my own mistakes in it.

## What this actually is

I started this as a fairly standard Netflix EDA + Power BI dashboard. Partway through, I found that my own cleaning step had a real bug in it, fixed it, and traced the fix all the way through five notebooks to the dashboard. That fix — and what it changed — is the most interesting part of this repo, more than any chart.

## Dataset

[Netflix Movies and TV Shows](https://www.kaggle.com/datasets/shivamb/netflix-shows) by shivamb on Kaggle. 7,787 titles, 12 columns.

## The bug

The `country` column stores co-productions as one string — `"United Kingdom, United States"` is a single value, not two. Counting that field directly, like most notebooks on this dataset do, means every co-produced title gets bucketed into its own category instead of crediting the countries actually involved.

I didn't catch this until I'd already written the EDA notebook and the numbers looked slightly off. Here's the before and after:

| | Before (raw field) | After (properly exploded) |
|---|---|---|
| Unique countries | 681 | 121 |
| United Kingdom's title count | 397 | 722 |
| 4th largest contributor | Japan | Canada |
| 5th largest contributor | South Korea | France |

The UK's real contribution was basically double what the naive count showed. And the story changes, not just the numbers — Canada and France actually outrank Japan and South Korea once this is fixed, which means the "Netflix invests heavily in Asian content" narrative I'd originally written doesn't really hold up. `duration` had a similar issue — it means minutes for movies and seasons for TV shows, same column, two units — so that got split too.

## Other things worth knowing

- Catalog growth looks like it drops off after 2020 — that's just where the dataset's collection window ends (January 2021), not a real decline.
- Top 10 countries make up 76.1% of the catalog. Netflix's sourcing is concentrated, not evenly global.
- Country and content type (Movie vs. TV Show) are statistically related (χ² = 214.67, p < 0.001) but the relationship is weak (Cramér's V = 0.193) — country shifts the mix a bit, it doesn't drive it.
- TV-MA and TV-14 alone make up over 60% of the catalog.

## Structure

```
CineMetrix/
├── data/
│   ├── raw/                  # Original file, never touched
│   ├── processed/            # Cleaned + exploded datasets
│   └── dashboard/            # Aggregated tables for Power BI
├── notebooks/
│   ├── 01_Data_Understanding.ipynb
│   ├── 02_Data_Cleaning.ipynb
│   ├── 03_Exploratory_Data_Analysis.ipynb
│   ├── 04_Dashboard_Preparation.ipynb
│   └── 05_Dashboard_Planning.ipynb
├── images/                   # Chart exports
├── dashboard/                 # Power BI file + screenshots
├── reports/
├── src/
├── requirements.txt
└── .gitignore
```

## How the notebooks fit together

01 is read-only — just figuring out what's wrong with the data, nothing gets changed. 02 is where everything gets fixed: dates converted, missingness checked (it's not random — director is missing almost exclusively for TV shows, for instance), country and genre split properly, duration split by type. 03 asks seven business questions of the cleaned data and tests the ones that can actually be tested instead of just eyeballing a chart. 04 turns all of that into flat tables Power BI can read. 05 is the dashboard plan — pages, KPIs, what each visual needs to say.

## Running it

```bash
python -m venv venv
venv\Scripts\activate
pip install -r requirements.txt
```

Then run the notebooks 01 through 05, in order — each one depends on the last one's output.

## Dashboard

Power BI, built from the eight tables in `data/dashboard/`. File and screenshots in `dashboard/`.

## Author

Bhaskar — B.Sc. Statistics, MSU Baroda