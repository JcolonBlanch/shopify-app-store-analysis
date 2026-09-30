# Power BI build guide

## 1. Import the files

Use Import mode to load `data/apps.csv` and `data/reviews.csv`.

## 2. Clean `apps`

In Power Query:

1. Set `app_id` to Whole Number.
2. Apply **Trim** to `app_name` and `developer`.
3. Replace nulls and empty strings in `developer` with `Unknown Developer`.
4. Apply **Capitalize Each Word** to `category_name`; standardize `Seo` as `SEO`.
5. Set `launch_date` to Date using locale **English (United States)**.
6. Set `has_free_plan` to Text.
7. Set `monthly_price_usd` to Decimal Number.

## 3. Clean `reviews`

In Power Query:

1. Set `review_id`, `app_id`, `rating`, and `helpful_count` to Whole Number.
2. Remove duplicates using `review_id`.
3. Filter `rating` to values from 1 through 5.
4. Set `posted_at` to Date using locale **English (United States)**.
5. Replace `Yes` with `1` and `No` with `0` in `has_developer_reply`, then set it to Whole Number.

After these steps, the expected review count is **7,980**.

## 4. Create the date table

```DAX
dim_date =
ADDCOLUMNS(
    CALENDAR(MIN(reviews[posted_at]), MAX(reviews[posted_at])),
    "Year", YEAR([Date]),
    "Quarter", "Q" & FORMAT([Date], "Q"),
    "Month", FORMAT([Date], "MMMM"),
    "Month Number", MONTH([Date]),
    "Year Month", FORMAT([Date], "YYYY-MM")
)
```

Sort `dim_date[Month]` by `dim_date[Month Number]`, then mark `dim_date` as the date table using `dim_date[Date]`.

## 5. Create relationships

- `apps[app_id]` 1 → * `reviews[app_id]`, single-direction filtering.
- `dim_date[Date]` 1 → * `reviews[posted_at]`, single-direction filtering.

## 6. Add measures

Create the measures listed in `measures.dax`. Format `Developer Reply %` and `Reviews YoY %` as percentages with one decimal place. Format all review and app counts as whole numbers.

Expected KPI results:

- Total Apps: **500**
- Total Reviews: **7,980**
- Average Rating: **4.19**
- Developer Reply %: **25.6%**

## 7. Build the Overview page

Use a 16:9 canvas and an F-pattern layout.

1. Add four cards across the top: Total Apps, Total Reviews, Average Rating, Developer Reply %.
2. Add slicers for `category_name`, `dim_date[Year]`, and `has_free_plan`.
3. Add a horizontal bar chart with `category_name` and Total Reviews, sorted descending.
4. Add a line chart with `dim_date[Year Month]` and Total Reviews.
5. Add a horizontal bar chart with `app_name` and Total Reviews, sorted by Total Reviews descending.
6. Add a Page Navigator to navigate to Trend Analysis.

## 8. Build the Trend Analysis page

1. Add cards for Total Reviews, Reviews Previous Year, Reviews YoY %, and Reviews YTD.
2. Add a slicer for `dim_date[Year]`.
3. Add a monthly line chart using Total Reviews.
4. Add an annual column chart using Total Reviews.
5. Add a line chart for Reviews YTD by month.
6. Add a line chart for Reviews YoY % by year.
7. Add a Page Navigator to navigate back to Overview.

## 9. Validate and export screenshots

Confirm that the unfiltered cards match the expected KPI values above. Capture:

- `screenshots/overview_page.png`
- `screenshots/trend_analysis_page.png`
- `screenshots/model_view.png`

Save the Power BI Desktop file as `report.pbix` in the repository root.
