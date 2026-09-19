# Client brief: promotion effectiveness for a retail chain

## The question
"Are our promotions actually increasing sales, in which stores and situations, and which should we keep, change or stop?"

## Why it matters
Promotions cost money (discounts, marketing, staff effort). If they do not add sales, or add them only in some stores, that money is wasted. If they work well in some places, the chain should do more of it there. Today store managers decide from experience, with no chain-wide measure of what a promotion actually adds.

## Data
Rossmann Store Sales (Kaggle), a real drugstore chain of 1,115 stores in Germany:
- Daily sales per store, Jan 2013 - Jul 2015 (about 1M rows), with whether a promotion ran, holidays and customers.
- Store attributes: type (a-d), assortment, distance to the nearest competitor, and a second, long-running promotion scheme.

For a fast first version we use a **stratified 50-store sample** (about 38,000 store-days after cleaning).

## What is not in the data
Promotion cost, product margin, and which discount was offered. So results are **extra sales, not profit**, and any "stop" decision needs the cost figure from the client.

## Approach
1. **Clean** the data and keep a log of every change.
2. **Explore** the key patterns: promo vs non-promo, by store type, by competitor distance.
3. **Model** daily sales with a time-based test (never a random split) to estimate the promotion effect with store, weekday, month and holidays held fixed.
4. **Dashboard** in Tableau so managers can see uplift by segment and forecast vs actual.
5. **Decide:** a one-page keep / change / stop recommendation with money impact, assumptions and risks.

## Deliverables
Cleaned data and cleaning log; notebooks for cleaning, findings, model, dashboard data and decision numbers; Tableau dashboard; decision memo and slides; assumptions and risks; production mapping.

## Success looks like
- A clear answer to the question, with the size of the effect and where it is strongest and weakest.
- Numbers a manager can trust: checked three ways, with limits stated openly.
- A recommendation the client can act on, and the next data needed to firm it up.

## Out of scope
Profit and ROI (no cost data), discount-level optimisation, product-level analysis, and the long-running Promo2 scheme.
