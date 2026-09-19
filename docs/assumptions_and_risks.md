# Assumptions and risks

## Assumptions

| # | Assumption | Why it matters | If wrong | How to check |
|---|---|---|---|---|
| 1 | The 50-store stratified sample represents the 1,115-store chain | All numbers come from it | Segment results and the chain-wide figure shift | Re-run on all stores (a one-line change to the sample size in `01_cleaning`) |
| 2 | Comparing promo and non-promo **Mon-Fri days in the same store** is fair | Promotions never run at weekends, so this is the only like-for-like comparison | Uplift would be inflated or deflated | Already applied; weekends excluded everywhere |
| 3 | The model's "with vs without promo" gap reflects the promotion | Basis of the model uplift | Uplift includes days that were busy anyway | Compare with the simple methods (done: 35% / 38% / 39%); confirm with a controlled test |
| 4 | About 130 promo days per store per year, as in the data | Scales the chain-wide figure | Chain-wide figure moves in proportion | Check the client's promo calendar |
| 5 | Gross margins of 10%, 20% and 30% are used for break-even | Data has no margin | Break-even costs move in proportion | Replace with the client's real margin |
| 6 | Closed days and zero-sales open days are not promotion failures | Removed in cleaning (7,405 + 2 rows) | Promo vs non-promo means would shift | `cleaning_log.md` lists every removal |

## Risks

| Risk | Severity | What we did | What is still open |
|---|---|---|---|
| **Promotions not randomly assigned** (confounding): promos may fall on days managers expect to be busy | High | Held store, weekday, month and holidays fixed; three methods agree | Only a randomised test proves cause |
| **No promotion cost or margin data** | High | Report break-even cost, not profit | A "stop" decision cannot be made until cost is known |
| **Small sample:** 50 stores, type b has only 4 | Medium | Type b shown as low confidence everywhere | Verify on all 17 type-b stores |
| **One short test window (6 weeks, summer)** and one train/validation/test split | Medium | Time-based split, no random split | Rolling-origin evaluation and a full year |
| **Pull-forward / cannibalisation not measured:** shoppers may delay purchases until a promo, so non-promo days after a promo could dip | Medium | Not analysed | Compare sales in the days after promo spells with matched non-promo periods |
| **Model under-forecasts by about 6%** on the held-out weeks (sales were rising) | Low for the uplift, higher for forecasting | Reported openly | Add trend features or retrain more often |
| **Model does not beat a simple promo-aware average** at forecasting (10.6% vs 10.1% MAPE) | Low for this question | Reported openly; the model is used for the uplift, not for forecasting accuracy | Try a richer feature set or more history |
| **Data is from 2013-2015** | Medium | Stated in the client brief | Re-run on current data before any real decision |
| **Public data copy from a third-party mirror** (Kaggle is the original source; row count and columns match the official files) | Low | Verified row counts and columns | Re-download from Kaggle for a formal submission |
| **Store-level averages are unweighted**: a small store counts as much as a large one | Low | Stated on the charts and in the findings | Weight by store sales if the client wants a revenue-weighted view |
| **Promo2 (long-running promotion scheme) and competitor opening dates not analysed** | Low | Left out to keep scope small | Next iteration |

## Not claimed
- That promotions are profitable (no cost or margin data).
- That any effect is proven cause (observational data).
- That the chain-wide EUR figure is a forecast (it is an order-of-magnitude scaling).
