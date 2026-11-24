---
name: why-amazon-sales-dropped
description: Use when the user asks why Amazon sales, units or revenue dropped, or which products lost sales and why. Finds the products behind the drop and the broken link (traffic, conversion, price, Featured Offer, promotions, ads, stock, listing or demand) from live Nova data.
---

# Why did my Amazon sales drop?

This skill needs the Nova MCP server connected (`https://mcp.novadata.io/api/mcp`, OAuth sign-in with a Nova account). Every step calls a Nova tool that reads live Amazon data. Setup for each assistant: https://novadata.io/docs/mcp/connect-your-ai

## Instructions

Use Nova to find out why my Amazon sales dropped.

Period: the last 7 complete days. Baseline: the 7 days before. If I name other dates, use those. Never compare a partial day or week with a complete one.

1. Size the drop. Call Nova's query_metrics with netSales and units for both periods, once in total and once with groupBy "marketplace". Say whether the drop is real and where it sits.

2. Find the products. Call Nova's find_movers on netSales, direction "down", dimension "childAsin", limit 10. Ignore rows with an empty id or __UNALLOCATED__. Say what share of the drop the top products explain. Stop at the products that explain most of it.

3. Split traffic from conversion. For those products, call Nova's query_metrics with groupBy "childAsin" and the metrics sessions, conversionRate, units and netSalesPerUnit. Sessions and conversion do not exist per SKU, so read them per child ASIN. Say whether fewer people came or fewer people bought.

4. Read the levers. Add segmentBy "sku" to get the SKUs. For each SKU that matters, call Nova's explain_movement. It returns units, net sales, net sales per unit, Featured Offer share, customer promotions, ad spend, ACOS, impressions and clicks. It does not return sessions, conversion or rank.

5. Check availability. Call Nova's query_inventory and query_listing_health for those products. Both show the state today, not last week, so a product back in stock can still have sold out during the period.

6. Check rank and demand. Call Nova's query_bsr with the same baseline. Then call Nova's query_sqp with grain "week" for the latest complete week. It runs about 10 days behind, so use it for the market trend, not for this week's dip. Use period.covers as the dates for any comparison with it.

7. Check what changed. Call Nova's list_events for the period and the week before it. If ads moved, call Nova's query_advertising and query_search_terms for those products.

8. Report, one block per product:
   - Sales lost, and its share of the total drop
   - The broken link: traffic, conversion, price, Featured Offer, promotions, ads, stock, listing or demand
   - The figures behind it, this period and the baseline
   - The likeliest cause, your confidence, and what else could explain it
   - The one check in Seller Central that would confirm it
   - The one action to take, or "wait" if the data does not support one

Rules: these are correlations, so never state a cause as proven. Say which figures are missing and why. Relay every notice Nova returns (P&L rules, marketplaces added up, COGS gaps, currency). Do not add Seller Central and Vendor Central figures together.

## How to read the result

| What you see | Where it points | Check next |
| --- | --- | --- |
| Sessions down, conversion flat | Fewer people reached the page: ads, rank or demand | `impressions` and `clicks`, `query_bsr`, `query_sqp` |
| Sessions flat, conversion down | People came and did not buy: price, Featured Offer, listing, reviews | `netSalesPerUnit`, `buyBoxPercentage`, `query_listing_health`, `query_reviews` |
| Units flat, net sales down | You sold as many for less: price or promotions | `netSalesPerUnit`, `productPromotions` |
| Units at zero | Nothing to buy: stock or a listing problem | `query_inventory`, `query_listing_health` |
| One marketplace only | A local cause, not the product | `query_metrics` with `groupBy` set to `marketplace` |
| Most products down by a similar share | Demand or the whole account | `query_sqp`, `list_events` |

## Limits

- Nova shows what moved at the same time. It does not prove why.
- Stock and listing health are the state today, not a history.
- Sessions and conversion exist per child ASIN, not per SKU.
- Sessions and conversion refresh once a day, so the latest day can be missing while orders are already in.
- Search Query Performance runs about 10 days behind per week and about 7 weeks behind per month.
- Nova sees the current Featured Offer owner and price on your listings. It does not see a rival's ad spend or sales.
- On Vendor Central, Amazon reports no sessions, Featured Offer or promotions. Read `vendorShippedRevenue` and `vendorShippedUnits`, and pass `channels: ["VENDOR"]`.

Source and updates: https://novadata.io/amazon-ai-agents/why-did-my-amazon-sales-drop
