# Tableau quickstart (for a first-time user)

> **Current scope:** sheets 1-4 and Dashboard 1 are built. Sheets 5-10 and Dashboards 2 and 3 are planned for later; the steps below are ready when you are.

You will build **3 dashboards (the pages people see) from 10 small sheets (the building blocks)**. No formulas and no table relationships: every chart uses a tiny ready-made file. Time: about **60-90 minutes** the first time.

Pictures of the finished result are in `tableau/mockups/`: one image per dashboard, plus `how_it_is_built.png`, which shows which sheet goes where. Your Tableau version will look similar, not identical.

The harder version with formulas is in [tableau_guide.md](tableau_guide.md); you do not need it.

Menu names can differ slightly between Tableau versions. If a step does not match your screen, keep the goal in mind and tell me what you see.

## 0. Words you need
- **Sheet** = one chart or one number. **Dashboard** = several sheets arranged on one page. The tabs are at the bottom-left.
- **Data pane** (left side) lists the fields of the file. **Dimensions** (blue) are labels like a store type. **Measures** (green) are numbers.
- **Columns** and **Rows** are the two shelves at the top of the sheet. Dragging a field there draws the chart.
- **Marks card** (middle-left) controls colour, labels, text and tooltip of the chart.
- Tableau adds spaces to field names: `PromoUplift` appears as **Promo Uplift**, `ForecastMAPE` as **Forecast MAPE**. They are the same fields; use the spelling you see.
- Files with one row per bar show `SUM(...)`, which is fine (the sum of one row is the number). Where a file has many rows per bar, the steps tell you to switch to **Average**.

## 1. Install and open
Download **Tableau Public** (free) from public.tableau.com and install it. You need a free account only when you publish at the end.

## 2. Connect the four files
They are in `data/analytics/` inside your project folder.

| # | File | Feeds |
|---|---|---|
| 1 | `tb_kpis.csv` | sheets 1, 2, 3, 9, 10 (the big-number cards) |
| 2 | `tb_promo_compare.csv` | sheet 4 (promo vs no promo) |
| 3 | `tb_store_uplift.csv` | sheets 5, 6, 7 (by store type, by distance, by store) |
| 4 | `tb_forecast_by_store.csv` | sheet 8 (forecast vs actual) |

- First file: start screen -> **Text file** -> pick `tb_kpis.csv` -> click the **Sheet 1** tab at the bottom.
- Each further file: menu **Data -> New Data Source** -> **Text file** -> pick the file -> click the sheet tab. To switch a sheet's data, click the data source name at the top of the Data pane.

Right after connecting a file, set number formats once: in the Data pane, right-click a field -> **Default Properties -> Number Format**:
- **Percentage, 1 decimal:** `Promo Uplift`, `Uplift`, `Forecast MAPE`, `Forecast Bias`.
- **Number (Custom), 0 decimals, thousands separator:** `Avg Sales`, `Extra Sales6wk`, `Sales`.

## 3. The 10 sheets
Rename each sheet by double-clicking its tab. **Colour rule: blue = promo / uplift, grey = no promo, orange = forecast.** After each sheet, compare with the "you should see" value.

**Sheet 1 - "Promo uplift"** (`tb_kpis`): drag `Promo Uplift` onto **Text** in the Marks card. Click **Text** -> the **...** button -> font size 36 or more. You should see **37.7%**.

**Sheet 2 - "Stores gaining"** (`tb_kpis`): drag `Stores Gaining` to **Text**. You should see **50**.

**Sheet 3 - "Extra sales"** (`tb_kpis`): drag `Extra Sales6wk` to **Text**. You should see **1,653,977**.

**Sheet 4 - "Weekday sales, with and without a promotion"** (`tb_promo_compare`): `PromoLabel` to **Columns**, `Avg Sales` to **Rows**. Drag `PromoLabel` onto **Color**, then **Color -> Edit Colors**: pick a blue for "Promo" and a grey for "No promo". Turn on labels (Marks card -> **Label** -> Show mark labels). You should see **5,987** and **8,242**.

**Sheet 5 - "By store type"** (`tb_store_uplift`): `TypeGroup` to **Columns**, `Uplift` to **Rows**. The pill says SUM: right-click it -> **Measure -> Average**. Labels on, colour blue. For hover help, drag **tb_store_uplift.csv (Count)** (bottom of the Data pane) onto **Tooltip** in the Marks card: hovering then shows how many stores are in a bar. You should see **40.8%, 20.5%, 46.1%, 38.9%** for types a, b, c, d.

**Sheet 6 - "By distance to nearest competitor"** (`tb_store_uplift`): `DistanceGroup` to **Columns**, `Uplift` to **Rows**, switch to **Average**, labels on, blue, and the same Count on **Tooltip**. The leading numbers in the names keep the order Closest -> Farthest. You should see **39.3%, 41.6%, 38.4%, 38.8%** (the first may show 39.4%; both are fine).

**Sheet 7 - "Each store's uplift"** (`tb_store_uplift`): `StoreLabel` to **Columns**, `Uplift` to **Rows** (SUM is fine here). Sort descending with the toolbar's sort icon. Blue bars. Right-click the store names along the bottom -> untick **Show Header** to remove clutter. Then open the **Analytics** tab (top of the left pane, next to Data) and drag **Average Line** onto the chart, dropping it on **Table**. You should see **50 bars**, the highest about 93%, the lowest about 9%, all above zero, and a dashed line at **39.5%**.

**Sheet 8 - "Actual vs forecast, last 6 weeks"** (`tb_forecast_by_store`): `Date` to **Columns**, then right-click that pill and choose **Exact Date** (or a **Day** option from the lower green group). `Sales` to **Rows** (SUM is fine). `Series` onto **Color** (default blue and orange are fine). Then right-click `TypeGroup` in the Data pane -> **Show Filter**: a filter box appears on the sheet. You should see **two lines** with the same weekly pattern, the forecast slightly below actual on most days.

**Sheet 9 - "Forecast error (all 50 stores)"** (`tb_kpis`): `Forecast MAPE` to **Text**. You should see **10.6%**.

**Sheet 10 - "Forecast bias (all 50 stores)"** (`tb_kpis`): `Forecast Bias` to **Text**. You should see **-6.3%**.

If a number differs, you are probably on the wrong data source (check the name at the top of the Data pane) or the wrong field.

## 4. The 3 dashboards
Click the **New Dashboard** icon (bottom-left, next to the new-sheet icon). Drag sheets from the left **Sheets** list onto the page. To put two side by side, drop the second on the edge of the first (a grey zone shows where it lands). Tick **Show dashboard title** (bottom-left) and double-click the title to edit it. Drag the **Text** object from the left **Objects** list for notes and subtitles.

**Dashboard 1 - title "Do promotions increase sales?"**
- Subtitle (Text object): "Yes. Promo days sell about 38% more, and every one of the 50 stores gains."
- Top row: sheets 1, 2, 3. Below: sheet 4 on the left and a Text object on the right: "So what: promotions work on average. The useful question is where they pay back, not whether to stop them."

**Dashboard 2 - title "Where do promotions work best?"**
- Subtitle (Text object): "Click a bar to filter the store chart below. Click it again to clear."
- Top row: sheets 5 and 6. Below: sheet 7. Bottom note (Text): "Type b = only 4 of the 50 sample stores (17 in the chain). Treat it as a signal to verify, not a proven fact."
- **Make the bars clickable:** in the dashboard, click sheet 5 once (a frame appears) and click the small **funnel icon** ("Use as Filter") at its top-right. Do the same for sheet 6. Now click the "Type b" bar: sheet 7 shows only those 4 stores. Click it again to clear.

**Dashboard 3 - title "Is the forecast reliable?"**
- Subtitle (Text object): "It follows the weekly pattern but runs about 6% low. Use the filter to look at one store type."
- Sheet 8 large on the left, with its **Type Group** filter box; sheets 9 and 10 stacked on the right; a note (Text): "Accuracy is comparable to a simple average (10.6% vs 10.1%). The model's value is the promo uplift estimate."
- On the filter box, click its small arrow and choose **Multiple Values (dropdown)** so it takes little space. The two number cards do not change when you filter; their titles say "all 50 stores".

Add a small footer Text on each dashboard: "Sample: 50 Rossmann stores. Revenue, not profit. Association, not proven cause."

## 5. Check it works like a user would use it
- Dashboard 2: click Type b, then click it again. Hover over a bar: the tooltip shows the number of stores.
- Dashboard 3: pick only "Type b" in the filter: the lines change to that type's total.
- Each dashboard reads top to bottom: title, one-sentence answer, the picture, the "so what".

## 6. Save, publish and screenshot
- **File -> Save to Tableau Public As...** (sign in with your free account). This publishes it to the public web; the Rossmann data is already public (cite Kaggle as the source). If your version has **Save** to your computer, also save a `.twbx` file into `tableau/`.
- Copy the published link and put it in `README.md` where it says "add the Tableau Public link".
- Take a screenshot of each dashboard (**Cmd + Shift + 4**) and put them in `tableau/`.

## 7. If you get stuck
- **A number shows as `SUM` where you need an average:** right-click the pill -> **Measure -> Average**.
- **A field is in the wrong place (a number under Dimensions):** right-click it -> **Convert to Measure**.
- **The date shows as text:** click the small `Abc` icon next to `Date` in the Data pane and choose **Date**.
- **Clicking a bar does nothing:** the funnel ("Use as Filter") must be switched on for that sheet, and both sheets must come from `tb_store_uplift`. Sheets from different files cannot filter each other.
- **The bars are in the wrong order:** use the sort icon in the toolbar, or right-click the header -> **Sort**.
- **Everything is the same colour:** drag the field onto **Color** in the Marks card.
- Still stuck: take a screenshot or copy the exact message and tell me which sheet you are on.
