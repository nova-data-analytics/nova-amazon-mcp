---
name: amazon-stockout-forecast
description: Use when the user asks what will run out of stock on Amazon, how many days of cover are left, or wants stockout alerts and reorder signals from Nova inventory and sales data.
---

# Can an AI agent forecast my Amazon inventory and alert me before I stock out?

This skill needs the Nova MCP server connected (`https://mcp.novadata.io/api/mcp`, OAuth sign-in with a Nova account). Every step calls a Nova tool that reads live Amazon data. Setup for each assistant: https://novadata.io/docs/mcp/connect-your-ai

## Instructions

Use Nova to forecast when each of my Amazon products runs out, and alert me only about the ones I must act on before then.

Scope: my Seller Central accounts and every stock region. If I name products or regions, keep to those.

1. Find the products at risk. Call Nova's query_inventory with groupBy "asin", includeReorder true, daysUntilReorderMax 14, sorted by daysUntilReorder ascending, limit 50. Then call it again with oosRiskFlag true. Keep the products from both calls, and give stock region, sellable units, inbound units, sales velocity, days of supply after inbound, external stock, units on order and the next expected date.

2. Check the lead times. Each row gives its lead times and targets with a source. If a source says fleet_default, say the forecast uses Nova's default lead times. If recoStatus is params_missing, say so and ask me for the lead times.

3. Read the season. Call Nova's query_metrics with groupBy "childAsin", timeGrain "week", limit 50, and units, for those products over the last 8 complete weeks, then over the same 8 weeks last year and the 8 weeks that followed them. A week at or near zero is a stockout, so leave it out of every average.

4. Adjust the velocity. For each product, divide last year's units in the coming 8 weeks by last year's units in the 8 weeks before them. Multiply today's velocity by that ratio. If last year has too few sales or holds a stockout, keep today's velocity and say so.

5. Add subscriber demand. From step 1, read snsForecastUnits30d. These are Amazon's projected Subscribe and Save deliveries, so say when they are a large share of the coming month.

6. Forecast the run-out date. For each product, divide sellable plus inbound units by the adjusted daily velocity and add the result to today. Compare it with today plus the total lead time from step 2.

7. Set the alert level. Stockout ahead: the run-out date falls before an order placed today could arrive. Order this week: daysUntilReorder is 7 or less. Watch: everything else from step 1. Do not alert on a product whose listing cannot sell: call Nova's query_listing_health for the Stockout ahead products and set those aside with the listing issue.

8. Alert. If nothing is at Stockout ahead or Order this week, reply in one line that there is no stockout alert today. Otherwise give one short table per level: product, region, run-out date, days left, lead time, units on order and recommendedOrderQty. Keep it short enough to read in a chat or an email.

Rules: the forecast is an estimate from velocity and last year's pattern, so say which products rest on little history. Never add the non-Pool quantities across SKUs. Relay every notice Nova returns. You cannot place orders or create shipments.

## How to read the result

| What you see | What it usually means | What to do |
| --- | --- | --- |
| Stockout ahead, nothing on order | An order placed today arrives after the shelf is empty | Order now, and plan a faster route for part of it |
| Stockout ahead, units on order | The order arrives after the run-out date | Ask your supplier to bring part of it forward |
| Run-out date moves earlier than last week | The season or a promotion lifted sales | Read the adjusted velocity before you size the order |
| Season ratio well above 1 | Last year sold more in the coming weeks | Size the order on the adjusted velocity |
| Uses Nova's default lead times | Your own lead times are not entered | Enter them, because the alert depends on them |
| Set aside for a listing issue | The listing blocks the sale | Fix the listing first |

## Limits

- Velocity is the last 30 days. The season adjustment is the assistant's estimate from last year's sales.
- Subscribe and Save forecasts come from Amazon and cover subscriber deliveries only.
- Stock is the state today. Inbound counts shipped and receiving units unless you include Working plans.
- A product with less than a year of sales has no season to read.
- Seller Central stock only. For Vendor Central, Amazon's stock and forecast come from the Vendor Central playbooks.
- The assistant alerts and recommends. It does not place supplier orders or create shipments.

Source and updates: https://novadata.io/amazon-ai-agents/amazon-stockout-forecast-alerts
