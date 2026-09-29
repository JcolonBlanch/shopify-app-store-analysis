# Shopify App Store Analysis

## Analyst Memo — Shopify App Store Insights

**Dashboard:** Shopify App Store Analysis  
**Reporting period:** July 11, 2018–December 30, 2024  
**Data:** 500 apps and 7,980 valid, deduplicated reviews

### Key insight

Marketplace review activity is growing quickly, but developer engagement has not kept pace. Review volume reached 3,804 in 2024, up 76.8% from 2,151 in 2023. Only 24.8% of valid reviews received a developer reply.

SEO is the highest-volume category with 1,145 reviews. Reviews and Ratings follows with 1,071, and Sales and Conversion has 916. BrilliantPilot Pro is the most-reviewed individual app with 244 reviews. The overall average rating is 4.19 out of 5.

### Business impact

The marketplace is attracting substantially more customer feedback each year, which provides a stronger signal for product quality and category demand. The low reply rate creates a service gap: roughly three out of four reviews receive no visible developer response. This can weaken merchant confidence, especially when feedback is negative or concerns a high-volume category.

SEO and Reviews and Ratings account for the largest review volumes, so improvements in those categories can affect the greatest number of merchant interactions. The strongest apps also provide practical benchmarks for positioning, onboarding, and customer support.

### Recommendation

1. Establish a developer-response target for high-volume apps and reviews rated three stars or below.
2. Prioritize marketplace quality work in SEO, Reviews and Ratings, and Sales and Conversion.
3. Review BrilliantPilot Pro and other high-volume apps to identify repeatable product and support practices.
4. Monitor monthly review volume, year-over-year growth, average rating, and developer reply rate together. Rising volume without a matching response rate should trigger outreach to developers.

## Dashboard design

### Overview

- KPI cards: Total Apps, Total Reviews, Average Rating, Developer Reply %
- Slicers: Category, Year, Has Free Plan
- Monthly review trend
- Reviews by category
- Top-app table
- Page navigator to Trend Analysis

### Trend Analysis

- Monthly review volume
- Reviews by year
- Year-over-year review change
- Accumulated reviews (YTD)
- Page navigator to Overview

## Data model

- `apps[app_id]` (one) → `reviews[app_id]` (many)
- `dim_date[Date]` (one) → `reviews[posted_at]` (many)
- `dim_date` is marked as the report's date table.

See `screenshots/model_view.png` for the relationship diagram.

## Data preparation

The model applies these Power Query transformations:

- Trim `app_name` and `developer`.
- Replace blank or null developer names with `Unknown Developer`.
- Capitalize category names consistently.
- Remove duplicate reviews by `review_id`.
- Keep only ratings from 1 through 5.
- Parse `posted_at` using locale-aware date conversion.
- Convert `has_developer_reply` from Yes/No to 1/0.
- Set identifiers, dates, whole numbers, and decimal values to appropriate types.

Exact Power Query steps and DAX measures are documented in `POWER_BI_BUILD_GUIDE.md` and `measures.dax`.

## Repository structure

```text
shopify-app-store-analysis/
├── README.md
├── report.pbix
├── POWER_BI_BUILD_GUIDE.md
├── measures.dax
├── shopify_analysis.xlsx
├── data/
│   ├── apps.csv
│   └── reviews.csv
└── screenshots/
    ├── overview_page.png
    ├── trend_analysis_page.png
    └── model_view.png
```

## Supporting analysis

`shopify_analysis.xlsx` contains the reconciled KPI summary, category performance, monthly and annual trends, top apps, source URLs, and DAX definitions used to validate the report.

## Data sources

- apps.csv: https://practicum-content.s3.us-west-1.amazonaws.com/datasets/apps.csv
- reviews.csv: https://practicum-content.s3.us-west-1.amazonaws.com/datasets/reviews.csv

