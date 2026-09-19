# Decision memo: keep, change or stop promotions?

**For:** retail chain leadership  **From:** Faizan  **Date:** 19 September 2026
**Evidence:** 50-store sample of Rossmann daily sales (2013-01 to 2015-07). Details in `key_findings.md`, `model_results.md`, `assumptions_and_risks.md`.

## Recommendation
**Keep promotions running. Redesign them for store type b. Stop using competitor distance to decide where to promote. Get promotion cost data before scaling anything.**
Nothing should be stopped on sales evidence alone: no store type or situation showed a promotion failing to lift sales.

## Why
- Promo days sell **38% more** than non-promo weekdays in the same stores (5,987 -> 8,242 average daily sales, Mon-Fri). **All 50 stores gain**, from +9% to +94%.
- Three different methods agree on the size: +35% (model, everything else held fixed), +38% (store vs itself), +39% (regression). About a quarter of promo-day sales are extra sales.
- The lift comes from **more customers (+19%)** and **a bigger spend per customer (+15%)**.

## Decision by segment

| Segment | Stores in chain | Decision | Model uplift | Extra sales per promo day | Break-even promo cost per promo day* |
|---|---:|---|---:|---:|---:|
| Types a, c, d | 1,098 | **Keep** | 35-38% | EUR 2,060-2,415 | EUR 410-480 |
| Type b | 17 | **Redesign and test** | 20% | EUR 1,430 | EUR 290 |
| Targeting by competitor distance | all | **Stop as a rule** | 33-36% at every distance | n/a | n/a |

*Extra sales x an **assumed 20% gross margin**. At 10% margin halve the figure, at 30% multiply by 1.5. The real margin and cost are not in the data: if a promo costs more per store-day than the break-even figure, it loses money even though it lifts sales.

## What it is worth (revenue, not profit)
- In the 6 held-out weeks the 50 sample stores made about **EUR 1.65M of extra sales because of promotions**, 12.5% of their sales in that period.
- Scaled to all 1,115 stores, an **indicative EUR 330M a year** of extra revenue, of which about EUR 327M comes from types a, c and d. Order of magnitude only: it scales one 6-week summer window to a year.
- Type b is 1.5% of stores and about 1% of that value. Redesigning it is **low stakes**: if it reached the lift of the other types it would add up to about **EUR 1.9M a year** of revenue, and cutting it would lose at most about EUR 3M. Do not spend big effort here; test cheaply.

## Next steps
1. **Get cost and margin data** for promotions, and replace the 20% margin assumption with the real number. This decides any "stop".
2. **Run a controlled test:** for 6-8 weeks, withhold or shorten promos in a random set of matched stores (start with type b) to confirm the lift is caused by the promotion.
3. **Repeat the analysis on all 1,115 stores and a full year** to check seasonality and the type b result (only 4 sample stores).
4. **Keep the dashboard** (`tableau/`) as the monthly view of uplift by segment.

## Biggest risks
Promotions were not randomly assigned, so part of the lift may be days managers already expected to be busy; the sample is small; there is no cost data. See `assumptions_and_risks.md`.
