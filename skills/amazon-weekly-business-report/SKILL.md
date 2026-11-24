---
name: amazon-weekly-business-report
description: Use when the user asks for a weekly Amazon business review or report: sales, profit, ads, inventory and the movers that explain the week, built from live Nova data.
---

# Amazon weekly business report

This skill needs the Nova MCP server connected (`https://mcp.novadata.io/api/mcp`, OAuth sign-in with a Nova account). Every step calls a Nova tool that reads live Amazon data. Setup for each assistant: https://novadata.io/docs/mcp/connect-your-ai

## Instructions

Use Nova to write my Amazon weekly business report.

Week: the last complete Monday to Sunday. If I name another week, use that. Never compare a partial week with a complete one.
Scope: if I hold several accounts, call Nova's list_accounts and write one report per account, unless I ask for a total. Never add Seller Central and Vendor Central figures together.

1. Scoreboard. Call Nova's query_metrics with netSales, units, netProfit, netProfitMargin, adSpend, sponsoredTacos, sessions and conversionRate, once against the week before and once against the same week last year. Then repeat with groupBy "marketplace". If a comparison comes back empty, or adsHistoryNotice says ad data starts later, say so rather than reading it as zero.

2. Month so far. Call Nova's query_metrics with the same metrics, month to date against the same days last month.

3. What moved. Call Nova's find_movers on netSales, direction "down", dimension "sku", limit 5, against the week before. Then the same with direction "up". Ignore rows with an empty id or __UNALLOCATED__.

4. Why it moved. For the 3 biggest decliners, call Nova's explain_movement. It returns units, net sales, net sales per unit, Featured Offer share, customer promotions, ad spend, ACOS, impressions and clicks. It does not return sessions, conversion or rank, so read those per child ASIN with query_metrics if the cause is still open.

5. What could go wrong next week. Call Nova's query_inventory with oosRiskFlag true, sorted by daysOfInventory, sortOrder "asc", limit 10. Then call Nova's query_listing_health with bands [0, 1], sort "severity", limit 10. Both show the state today.

6. Ads. Call Nova's query_advertising with groupBy "campaign", the week against the week before, with spend, sales and acos, sorted by spend, limit 5. Add breakEvenAcos and profitAfterAds if they come back filled.

7. Demand. If Brand Analytics data exists, call Nova's query_sqp with grain "week", groupBy "keyword", queryTypes ["core", "presence"], and the metrics search_volume, search_volume_wow_pct and your_impression_share, sorted by search_volume, limit 10. It runs about 10 days behind, so give period.covers as the dates.

8. What changed. Call Nova's list_events for the week.

9. Write the report, one page, in this order:
   - One sentence: how the week went
   - Scoreboard table: this week, last week, same week last year
   - Top movers, each with its likeliest cause, stated as a correlation
   - Risks: stock and listings
   - Ads: what spent the most and whether it paid
   - Demand: what moved on core queries, with its dates
   - Decisions for this week: three at most, each with the figure behind it

Rules: be brief. Leave out anything that did not change a decision. Say which figures are missing and why. Relay every notice Nova returns (P&L rules, marketplaces added up, COGS gaps, currency, ad history). You cannot change anything on my account.

## How to read the result

| What you see | Where it points | Check next |
| --- | --- | --- |
| Sales down on last week, up on last year | Possibly normal seasonality | The same week last year against the week before it |
| Sales flat, net profit down | Costs moved, not demand | `adSpend`, `sponsoredTacos`, `netProfitMargin` per product |
| One marketplace explains the change | A local cause | `query_metrics` per child ASIN in that marketplace |
| The same product in the movers two weeks running | Not a blip | `explain_movement` over 14 days, `list_events` |
| Stock risk on a top seller | Lost sales ahead | Inbound units in `query_inventory` |
| Core query volume down, impression share flat | Demand, not your listing | `query_sqp` over several weeks |

## Limits

- Nova shows what moved at the same time. It does not prove why.
- Stock and listing health are the state today, not a history.
- Sessions and conversion refresh once a day and exist per child ASIN, not per SKU.
- Search Query Performance runs about 10 days behind and needs Brand Analytics. Its weeks run Sunday to Saturday, so it never lines up exactly with a Monday to Sunday report.
- A trial reads 3 months of orders, fees and returns, so the same week last year needs a paid plan's 2-year history.
- Ad data starts about 60 days before you connected Amazon Ads. Before that, ad spend reads 0 and net profit leaves ads out.
- Sponsored Products sales count at 7 days on Seller Central, so the last days of a week can still gain sales.

Source and updates: https://novadata.io/amazon-ai-agents/amazon-weekly-business-report
