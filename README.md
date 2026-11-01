# Promotion Effectiveness for a Retail Chain

**Are our promotions actually increasing sales, in which stores and situations, and which should we keep, change or stop?**

An end-to-end analytics project on real retail data (Rossmann drugstores): data cleaning, exploratory findings, a forecasting model, a dashboard, and a decision recommendation for a business audience.

## The answer in one paragraph
Promotions lift sales by about **38%** on weekdays, and **all 50 stores in the sample gain**. Three different methods agree on the size (+35% model, +38% store-vs-itself, +39% regression). The recommendation is to **keep promotions for store types a, c and d, redesign and test them for type b** (about +20%, the weakest, but only 4 sample stores), and **stop targeting promotions by competitor distance**, which makes no difference. There is no cost or margin data, so the results are extra *revenue*, not profit, and a "stop" decision needs the client's cost figures.

## Key results

| Finding | Number |
|---|---|
| Promo vs non-promo average daily sales (Mon-Fri, same stores) | 5,987 -> 8,242 euros (+38%) |
| Stores that sell more on promo days | 50 of 50 (weakest +9%, median +36%) |
| Where the lift comes from | +19% customers, +15% spend per customer |
| Uplift by store type (a / b / c / d) | about 41% / 21% / 46% / 39% |
| Uplift by competitor distance | 38-42% in every distance band |
| Extra sales from promotions, last 6 weeks, 50 stores | about 1.65M euros (12.5% of sales) |
| Indicative extra revenue, whole chain, per year | about 330M euros (order of magnitude only) |

## Read this first: limitations
- **Association, not proven cause.** Promotions were not randomly assigned. Part of the lift may be days already expected to be busy. A controlled test would prove it.
- **The model does not beat a simple average at forecasting** (10.6% vs 10.1% average error). Its value here is the promo uplift estimate with store, weekday, month and holidays held fixed. It also under-forecasts by about 6% on the held-out weeks.
- **No promotion cost or margin data,** so the decision memo gives break-even costs per promo day (using an *assumed* 20% margin) instead of profit.
- **50-store sample, one 6-week test window.** Type b rests on only 4 stores.

Full detail: [docs/assumptions_and_risks.md](docs/assumptions_and_risks.md).

## What is in the project

| Stage | Where |
|---|---|
| 1. Data cleaning | [notebooks/01_cleaning.ipynb](notebooks/01_cleaning.ipynb), [docs/cleaning_log.md](docs/cleaning_log.md) |
| 2. Key findings (3 charts, "insight -> so what") | [notebooks/02_key_findings.ipynb](notebooks/02_key_findings.ipynb), [docs/key_findings.md](docs/key_findings.md), [docs/figures/](docs/figures/) |
| 3. Model (LightGBM, time-based split, MLflow) | [notebooks/03_model.ipynb](notebooks/03_model.ipynb), [docs/model_results.md](docs/model_results.md) |
| 4. Dashboard data and Tableau build guides | [notebooks/04_dashboard_data.ipynb](notebooks/04_dashboard_data.ipynb), [docs/tableau_quickstart.md](docs/tableau_quickstart.md) (beginner), [docs/tableau_guide.md](docs/tableau_guide.md) (advanced) |
| 5. Decision | [docs/decision_memo.md](docs/decision_memo.md), [notebooks/05_decision_numbers.ipynb](notebooks/05_decision_numbers.ipynb), [docs/client_brief.md](docs/client_brief.md) |
| Slide deck (7 slides) | [Promotion Effectiveness Decision Deck.pdf](Promotion%20Effectiveness%20Decision%20Deck.pdf), outline in [docs/deck_outline.md](docs/deck_outline.md) |
| Production mapping (Azure) | [docs/production_mapping.md](docs/production_mapping.md) |

**Dashboard:** Tableau dashboard 1, "Do promotions increase sales?" (4 sheets: promo uplift, stores gaining, extra sales, promo vs no promo), is included as [tableau/promotion_effectiveness.twbx](tableau/promotion_effectiveness.twbx). Open it with the free [Tableau Public](https://public.tableau.com) app. Dashboards 2 and 3 are planned and fully specified in the quickstart. A picture of the intended design for all three is in [tableau/mockups/](tableau/mockups/).

## Method in brief
- **Cleaning:** a stratified 50-store sample (fixed seed); closed-store days and open days with zero sales removed; missing competitor distance filled with the median plus a flag; outliers flagged, not deleted. Every step and row count is in the cleaning log.
- **Fair comparison:** Mon-Fri only (promotions never run at weekends), and each store compared with itself.
- **Model:** predicts log daily sales; the last 6 weeks are held out as the final test and a 6-week slice before that is used only to decide when to stop training. `Customers` is deliberately excluded because it is a result of the promotion and would leak the answer. Two simple baselines are reported next to the model.
- **Uplift:** the model predicts each held-out store-day twice, with and without a promotion, changing nothing else; the gap is the uplift, cross-checked against the two simpler methods.

## Data
Rossmann Store Sales, from the Kaggle competition (`train.csv`, about 1M daily rows for 1,115 stores, and `store.csv`). The copy used here was downloaded from a public GitHub mirror (Khayati1/Rossman_Store_Sales_Kaggle_Dataset), whose row counts and columns match the official files; **Kaggle is the original source**. Raw files in `data/raw/` are never edited. Sales are in euros.

## How to reproduce
```bash
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
# macOS only, once, so LightGBM can load:  brew install libomp
# put train.csv and store.csv in data/raw/, then run the notebooks in order:
for n in 01_cleaning 02_key_findings 03_model 04_dashboard_data 05_decision_numbers; do
  jupyter nbconvert --to notebook --execute --inplace notebooks/$n.ipynb
done
mlflow ui --backend-store-uri sqlite:///mlflow.db   # optional: browse the tracked model runs
```
Python 3.12. Notebooks use relative paths and fixed seeds, and run top to bottom.

## Tools
Python (pandas, NumPy, scikit-learn, LightGBM, matplotlib), MLflow (open source, local), Tableau Public for the dashboard.

## Not included (planned for later)
- Tableau dashboards 2 (where promotions work best, click-to-filter) and 3 (forecast reliability): the data files and step-by-step build guide are ready in [docs/tableau_quickstart.md](docs/tableau_quickstart.md).
- A natural-language "ask the analyst" assistant (text-to-SQL on the data plus search over the project documents) was scoped but removed for time.

## Author
Sheikh Mohmmad Adil
