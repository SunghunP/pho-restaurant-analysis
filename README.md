# Pho Restaurant Sales Analysis

**Checking whether "business feels slower" actually shows up in the sales data (Python + Pandas)**

*Prepared by Joe Park*

Data source: ~3 months of point-of-sale export data from my parents' restaurant (June 3 – September 2, 2026), 38,293 line items.

![Average sales by day of week and hour, showing peaks at lunch, early dinner, and late night](assets/sales_heatmap.png)
*Average sales by day and hour across the quarter — darker green marks the busiest slots.*

## The Question

My parents felt like business had been slowing down and wanted a specific number they could see, rather than just a feeling from being there every day. This analysis was meant to check that feeling against the actual data, along with:

- When is the restaurant busiest, by day and hour?
- Which menu items are the most popular and most profitable, and are they the same?
- Is revenue trending up, down, or holding steady?

## The Conclusion

**Revenue stayed flat across the quarter at roughly $26,000 per week — this contradicts what my parents assumed going in.** The data doesn't support "business is slowing down." That changes the conversation from "how do we stop the decline" to "how do we grow from a stable baseline."

![Weekly revenue trend showing a flat line around $26,000 per week across the quarter](assets/weekly_revenue_trend.png)
*Weekly revenue, holding steady across the quarter rather than trending up or down.*

**Key findings:**
- Demand peaks around 12PM, 5PM, and 9PM, and is driven mostly by to-go orders rather than dine-in.

  ![Sales heatmap split into dine-in and to-go, showing to-go outpacing dine-in at peak hours](assets/sales_heatmap_by_order_type.png)
  *Same day/hour breakdown, split by dine-in vs. to-go — to-go consistently outpaces dine-in at the peak hours.*

- Friday, Saturday, and Sunday are the busiest days. Tuesday is the slowest.
- Steak (Pho) is both the top seller and the top revenue item. Some items are popular but don't make much money — appetizers like Spring Roll sell a lot but rank lower in revenue.

  ![Top 15 items by revenue, led by Steak (Pho)](assets/top15_items_revenue.png)
  *Top 15 items by revenue — Steak (Pho) leads by a wide margin.*

**Business recommendations:**
- Run a targeted special/happy hour during 1PM–4PM, the slowest window of the day.
- Run a Tuesday-specific promotion, since it's consistently the slowest day.
- Since the 5PM and 9PM peaks are mostly to-go orders, focus extra staffing there on packing to-go orders rather than table service.

**Follow-up check:** The week after a new Combo item went live (testing one of the recommendations above), actual revenue came in slightly *below* simple baseline forecasts rather than above them — so there's no measurable lift yet. See [Checking the Recommendations Against Real Data](#checking-the-recommendations-against-real-data) below.

## Methodology

Starting from the raw POS export, columns that were redundant, unused, or order-level values duplicated across every line item (e.g. `Order Subtotal`) were dropped, and remaining columns were renamed and retyped. `Category` values were standardized.

The core analysis builds a day-by-hour sales heatmap — averaged per weekday occurrence rather than by item count, so it reflects how busy the restaurant actually is rather than the average price of items sold — then splits it by dine-in vs. to-go, ranks menu items by both units sold and revenue, and tracks total revenue week over week.

**Tools:** pandas, seaborn, matplotlib.

## Checking the Recommendations Against Real Data

To see whether the recommendations actually moved revenue once real data came in, rather than just assuming they worked, I built two simple baselines on top of the weekly revenue series: a **Naive forecast** (next week = this week) and a **3-Week Moving Average**. Neither is meant to be a sophisticated forecast — they're a floor to check any real business change against. If a recommendation doesn't move revenue past what these simple baselines already expect, it isn't showing a measurable effect yet.

I projected both baselines one week forward (week ending Sep 6), then checked that projection against a new week of real POS data (Sep 3–9) that picks up right where the original data cuts off. That new data also includes a new Combo item the restaurant started testing based on one of the recommendations above.

![Baseline forecast vs. actual revenue for the week ending Sep 6](assets/weekly_revenue_baseline_actual.png)
*Actual revenue for the week ending Sep 6 against both baselines.*

**Result:** Actual revenue for the week ending Sep 6 came in at $26,356. Naive was about 1.7% off and the 3-Week MA about 3.0% off — both landed a bit high, meaning actual revenue came in *below* what either baseline expected.

That's only one week of data with the new Combo item in it, so it's too early to say the changes made a real difference either way. If anything, this week doesn't show a lift yet. I want to keep comparing actual revenue against these baselines over the next several weeks to see if a clearer pattern shows up.

## Limitations

- **No baseline for comparison.** This is a single quarter with no prior period (last year, last quarter) to benchmark against, so "flat" describes this 13-week window only.
- **Small sample per heatmap cell.** Each day-and-hour average is built from only 13–14 data points (one per matching weekday in the quarter), so a single unusual day could shift a cell more than a real pattern would.
- **Forecast check is one data point.** The Naive and 3-Week MA baselines were checked against a single new week, so it's too early to treat this as validation of either baseline or of the recommendations' impact.

## Full Analysis

The complete notebook — cleaning steps, all charts, and full write-up — is in [`notebooks/eda.ipynb`](notebooks/eda.ipynb).
