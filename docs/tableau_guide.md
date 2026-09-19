# Tableau build guide (advanced: formulas and table relationships)

> **First time in Tableau? Use [tableau_quickstart.md](tableau_quickstart.md) instead.** It builds the same three dashboards from ready-made summary files, with no formulas. This guide is the harder alternative.

Goal: a 3-dashboard workbook that answers the client question: **"Are our promotions actually increasing sales, in which stores and situations, and which should we keep, change or stop?"**

Tableau Public (free, works on Mac) is enough. Download it from public.tableau.com. Realistic time for a first build: **30-45 minutes**. Data files come from `notebooks/04_dashboard_data.ipynb`.

Saving: File -> Save to Tableau Public As... (needs a free account, and publishes the workbook to the public web; the Rossmann data is already public). If your version can also save a `.twbx` file to your computer, save one into `tableau/`. Either way, export a screenshot of each dashboard (Dashboard -> Export Image) into `tableau/`.

## 1. Connect the data (5 min)

**Data source 1: "Promotion Effectiveness"** (three tables, related on Store)
1. Connect -> Text file -> `data/analytics/fact_sales.csv`.
2. Drag `dim_store.csv` and `fact_forecast.csv` next to it in the top pane.
3. Make sure each new table is joined to the model with a **relationship** (a soft line between boxes, not a join diagram) on `Store`:
   - `fact_sales.Store` = `dim_store.Store`
   - `fact_forecast.Store` = `dim_store.Store`

   Use relationships, not joins. Relationships keep each table at its own level of detail, so a store's uplift is not counted once per day of sales.
4. In the Data pane, set types: `Date` (fact_sales) and `ForecastDate` (fact_forecast) -> **Date**; `Store` -> right-click -> **Convert to Dimension**.

**Data source 2: "Segments"** (Data -> New Data Source -> Text file): `uplift_by_segment.csv`. It is stand-alone on purpose: its rows are already summaries from the model stage.

Field names: Tableau shows the CSV column names as written (it may add spaces where there were underscores, for example `Segment Type`). In every formula below, type the field name **exactly as it appears in your Data pane**; drag the field into the formula instead of typing it if in doubt.

## 2. Calculated fields (10 min)

Analysis -> Create Calculated Field. Use these exact names; the checks in section 5 refer to them.

**Why Mon-Fri?** Promotions never run at weekends, so comparing all days would flatter the promo effect. Every promo vs no-promo calculation includes `[DayOfWeek] <= 5`.

In "Promotion Effectiveness":

```
Avg Sales Promo
AVG(IF [Promo] = 1 AND [DayOfWeek] <= 5 THEN [Sales] END)

Avg Sales No Promo
AVG(IF [Promo] = 0 AND [DayOfWeek] <= 5 THEN [Sales] END)

Promo Uplift %
[Avg Sales Promo] / [Avg Sales No Promo] - 1

Avg Customers Promo
AVG(IF [Promo] = 1 AND [DayOfWeek] <= 5 THEN [Customers] END)

Avg Customers No Promo
AVG(IF [Promo] = 0 AND [DayOfWeek] <= 5 THEN [Customers] END)

Spend Per Customer Promo
AVG(IF [Promo] = 1 AND [DayOfWeek] <= 5 THEN [Sales] / [Customers] END)

Spend Per Customer No Promo
AVG(IF [Promo] = 0 AND [DayOfWeek] <= 5 THEN [Sales] / [Customers] END)

Avg Store Uplift %
AVG([UpliftPct]) / 100

Stores With Positive Uplift
COUNTD(IF [UpliftPct] > 0 THEN [Store] END)

Forecast Bias %
SUM([Forecast]) / SUM([ActualSales]) - 1

Forecast MAPE %
AVG([AbsPctError]) / 100

Forecast MAE
AVG(ABS([Error]))
```

In "Segments":

```
Extra Sales From Promos (6 wks)
SUM(IF [segment_type] = "store_type" THEN [total_extra_sales_holdout] END)
```
(The two `segment_type` groups are two cuts of the same sales, so summing both would double count; keep the `store_type` condition.)

Two different uplift numbers, on purpose:
- `Promo Uplift %` is the **pooled** figure (promo-day average vs non-promo-day average across everything in view): 37.7% overall.
- `Avg Store Uplift %` is the **average of each store's own uplift** (already worked out per store in `dim_store[UpliftPct]`): 39.5%. Use it for by-type and by-distance charts, because it matches the notebook charts.

Formatting: right-click each uplift, bias and MAPE field -> Default Properties -> Number Format -> **Percentage, 1 decimal**. Sales fields and MAE -> Number (Custom), 0 decimals, thousands separator.

## 3. Sheets and dashboards (15-20 min)

Colour rule everywhere: **blue = promo / uplift, grey = no promo / baseline, orange = forecast.** Set colours via the Color card -> Edit Colors -> select the item -> More Colors -> HTML: blue `#2a78d6`, grey `#a9a8a1`, orange `#eb6834`.
Titles say the finding, not the field names. Turn off gridlines you do not need (Format -> Lines) and show data labels on bars.

### Dashboard 1 - "Promotions lift sales in every store"

| Sheet | How to build it |
|---|---|
| KPI: uplift | Drag `Promo Uplift %` to Text. Title: "Promo uplift, Mon-Fri". |
| KPI: stores | `Stores With Positive Uplift` to Text. Title: "stores gain (of 50)". |
| KPI: extra sales | In "Segments": `Extra Sales From Promos (6 wks)` to Text. Title: "extra sales in last 6 weeks (revenue, not profit)". |
| Promo vs no promo | Columns: Measure Names; Rows: Measure Values. Keep only `Avg Sales Promo` and `Avg Sales No Promo` on the Measure Values shelf; colour by Measure Names (blue / grey); labels on. |
| Uplift by store | Columns: `StoreLabel`, sorted descending by `AVG(UpliftPct)`; Rows: `AVG(UpliftPct)`. Title: "All 50 stores sell more on promo days". |
| Filter | Right-click `StoreType` -> Show Filter -> on the filter card: Apply to Worksheets -> **All Using This Data Source**. |

### Dashboard 2 - "Type b gains least; competitor distance makes no difference"

| Sheet | How to build it |
|---|---|
| Uplift by store type | Columns: `StoreType`; Rows: `Avg Store Uplift %`; labels on. |
| Uplift by competitor distance | Columns: `DistanceBand`; Rows: `Avg Store Uplift %`. Sort `DistanceBand` by field `DistanceBandOrder` (minimum, ascending) so it reads Closest -> Farthest. |
| Customers | Columns: `StoreType`; Rows: Measure Values with `Avg Customers Promo` and `Avg Customers No Promo` only. |
| Spend per customer | Same, with `Spend Per Customer Promo` and `Spend Per Customer No Promo`. |
| Segment table | In "Segments": Rows: `segment_type`, `segment`; Text: Measure Values with `stores`, `model_uplift_pct`, `eda_uplift_pct`, `avg_extra_sales_per_promo_day`, `total_extra_sales_holdout`. |
| Text box | "Type b = only 4 of the 50 sample stores (17 in the chain). Treat as a signal to verify." |

The Segments table is a separate source, so the store filter does not change it; that is intended (it holds the model's held-out-weeks numbers).

### Dashboard 3 - "The forecast tracks sales but runs about 6% low"

| Sheet | How to build it |
|---|---|
| Forecast vs actual | Columns: `ForecastDate` (continuous **Day**); Rows: Measure Values with `SUM(ActualSales)` and `SUM(Forecast)` only; colour actual blue, forecast orange; add a legend. |
| KPI: MAPE | `Forecast MAPE %` to Text. |
| KPI: bias | `Forecast Bias %` to Text. |
| KPI: MAE | `Forecast MAE` to Text. Title: "euros per store-day". |
| Filters | `StoreType` and `Store` (dropdown), applied to all worksheets using this data source. |
| Text box | "Accuracy is comparable to a simple store x weekday x promo average (10.6% vs 10.1% MAPE). The model's value is the promo uplift estimate, not forecasting accuracy." |

Dashboard build (all three): New Dashboard -> Size: Fixed, e.g. 1200 x 800 -> drag sheets in with Tiled layout (KPIs in a top row) -> add a footer text box: "Sample: 50 Rossmann stores. Revenue, not profit. Association, not proven cause."

## 4. Formatting checklist
- One story per dashboard; the title is the takeaway.
- Blue / grey / orange only, as in the notebook charts; no rainbow palettes.
- Data labels on the main bars; axis titles carry units (EUR, %).
- Keep the default tooltips; do not add extra sheets beyond the tables above.

## 5. Check your numbers (no filters applied)

If a number differs, the calculation or a relationship is wrong; the data is right. Values marked "by ..." need that field on the sheet.

| Calculated field | Expected |
|---|---|
| Avg Sales Promo | 8,242 |
| Avg Sales No Promo | 5,987 |
| Promo Uplift % | 37.7% |
| Avg Store Uplift % (nothing else in view) | 39.5% |
| Stores With Positive Uplift | 50 |
| Avg Customers Promo / No Promo | 864 / 727 |
| Spend Per Customer Promo / No Promo | 10.07 / 8.76 |
| Avg Store Uplift % by StoreType a / b / c / d | 40.8% / 20.5% / 46.1% / 38.9% |
| Avg Store Uplift % by DistanceBand Closest / Near / Far / Farthest | 39.3% / 41.6% / 38.4% / 38.8% |
| SUM(ActualSales) | 13,201,465 |
| SUM(Forecast) | 12,364,218 |
| Forecast Bias % | -6.3% |
| Forecast MAPE % | 10.6% |
| Forecast MAE | 769 |
| Extra Sales From Promos (6 wks) | 1,653,977 |

Segment table (from "Segments"): store type a 37.2 / b 19.6 / c 38.0 / d 35.3 and distance Closest 32.9 / Near 36.5 / Far 36.3 / Farthest 36.0, in `model_uplift_pct`.

## 6. Common problems
- **Store shows as a sum or as a green pill:** right-click the field -> Convert to Dimension.
- **Promo uplift shows about 35% instead of 37.7%:** the `[DayOfWeek] <= 5` condition is missing or misspelled, so weekends are counted.
- **"Cannot mix aggregate and non-aggregate arguments":** the `IF ... END` must sit inside `AVG( )` or `SUM( )`, as written above.
- **Store-level numbers look inflated or repeated:** the tables were joined instead of related. Remove the join and use a relationship on `Store`.
- **A date axis shows text or single labels:** change the field type to Date in the Data pane, and use a continuous Day on the forecast chart.
- **A field name in a formula shows red:** the spelling in your Data pane differs (spaces, capitals). Drag the field into the formula.

## 7. Finish
- Save or publish the workbook and put screenshots of the 3 dashboards in `tableau/`.
- Production mapping (for the write-up): in production the same model would read Databricks or Synapse tables on Tableau Cloud or Server (live connection or scheduled extract), with row-level security by region.
