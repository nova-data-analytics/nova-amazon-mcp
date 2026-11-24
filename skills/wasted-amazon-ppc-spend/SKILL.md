---
name: wasted-amazon-ppc-spend
description: Use when the user wants to find wasted Amazon PPC spend: search terms, keywords and campaigns that spend without sales or without profit, and candidates for negatives. Reads Sponsored Products, Brands and Display data through Nova.
---

# How to find wasted Amazon PPC spend

This skill needs the Nova MCP server connected (`https://mcp.novadata.io/api/mcp`, OAuth sign-in with a Nova account). Every step calls a Nova tool that reads live Amazon data. Setup for each assistant: https://novadata.io/docs/mcp/connect-your-ai

## Instructions

Use Nova to find wasted spend in my Amazon ads and list the negative keywords to add.

Period: the last 60 complete days. If I name other dates, use those.

1. Set the bar. Call Nova's query_advertising with groupBy "childAsin" and the metrics spend, sales, orders, acos, breakEvenAcos and profitAfterAds. For each advertised product, work out what one order earns before ads: sales / orders x breakEvenAcos. Use the amount of the product with the most ad spend as the spend threshold below, unless I name one. Say which amount you used.

2. Size the waste on targets. Call Nova's query_advertising with groupBy "keyword" and the metrics spend, clicks, sales, acos, breakEvenAcos, profitAfterAds and spendWithoutSales, sorted by spendWithoutSales. Set aside targets whose state or servingStatus shows they are paused, because that money has already stopped. Call it again sorted by profitAfterAds, sortOrder "asc", to find live targets selling above their break-even ACOS.

3. Find the queries that never sold. Call Nova's query_search_terms with groupBy "searchTerm", the metrics spend, clicks, sales and orders, sorted by spend, and metricFilters spend above the threshold and sales below 1. Run it once with adProducts ["sponsored_products"] and once with ["sponsored_brands", "sponsored_brands_video"]. Ignore rows with the search term "*": those are product page placements, not queries. A row that looks like an ASIN comes from a product target, so treat it as a negative product target. Never add sales across ad types. Then call it once more for Sponsored Products with only sales below 1 as the filter, and limit 200. Its summary.total is all the spend on no-sale queries, however small each one is. Look for words that repeat across them.

4. Remove what is too early. Repeat step 3 with the period ending 14 days earlier. A query on both lists has had time for its sales to land. Mark the others "too early", and any query under 10 clicks too.

5. Find the queries that sell at a loss. Call Nova's query_search_terms for Sponsored Products with metricFilters spend above the threshold, sales above 0, and acos above the break-even ACOS from step 1. A query carries no product margin, so say this comparison uses the product's break-even, not the query's.

6. Check what is already blocked. For the 5 biggest candidates, call Nova's list_negative_targets with q set to the query. q matches part of the text, so a rule counts only if its keywordText matches the query exactly. It covers Sponsored Products only, so say that Sponsored Brands negatives need checking in Amazon Ads.

7. Find where to act. For the 5 biggest candidates, call Nova's query_search_terms with searchTerms set to that one query and groupBy "adGroup". It returns the campaigns and ad groups the query spent in. If the query sold in one of them, say so before suggesting a negative.

8. Report, in three lists. Compare each query with the product names from step 1 to judge whether it is relevant.
   - Negate: query, spend, clicks, where it spent, negative exact or negative phrase, and why it does not fit the product
   - Lower the bid: relevant queries with no sales or an ACOS above break-even, with the figures
   - Leave alone: too early, already blocked, or the target is paused
   Then give the total spend in the Negate list as a share of ad spend for the same ad types, any word that repeats across no-sale queries, and the Negate list ready to paste.

Rules: you cannot change anything on my account, so I apply every change in Amazon Ads. Do not suggest a negative only because a relevant query has no orders. Say which figures are missing and why. Relay every notice Nova returns (P&L rules, marketplaces added up, COGS gaps, currency, ad history).

## How to read the result

| What you see | What it usually means | What to do in Amazon Ads |
| --- | --- | --- |
| Spend above the threshold, no sales, query unrelated to the product | The ad showed for the wrong search | Add a negative exact in that campaign or ad group |
| The same word across several no-sale queries | A pattern, not one bad query | Add a negative phrase, after checking no selling query contains the word |
| Relevant query, no sales | The query fits, so price, listing or bid may be the problem | Lower the bid and check the listing. Do not negate yet |
| Sales, ACOS above break-even | Each sale loses money after ads | Lower the bid, or keep it as a chosen cost during a launch |
| On the 60-day list only | Its sales may not have landed | Wait and run it again |
| An ASIN row with spend and no sales | A product target that does not convert | Add a negative product target |
| Large `spendWithoutSales` on a paused target | The spend already stopped | Nothing to do |

## Limits

- This playbook only reads, so you apply its changes in Amazon Ads. PPC management is in beta and opening to the public soon. You can already [request access](https://app.novadata.io/signup?source=ppc_management_beta).
- `query_search_terms` returns queries with at least one click. A query blocked before the period does not appear at all.
- Amazon's search term report has no ASIN, so Nova gives a query no margin of its own. Break-even ACOS is read per product or per target.
- There are no impressions or click-through rate per query.
- Sponsored Display has no search term report.
- `list_negative_targets` covers Sponsored Products only.
- Break-even ACOS and profit after ads cover Sponsored Products at every level and Sponsored Display per product. They are empty for Sponsored Brands and on Vendor Central.
- Sales count at each ad type's own window: Sponsored Products 7 days on Seller Central and 14 on Vendor Central, Sponsored Brands 14. Compare spend across ad types, never sales.
- Break-even ACOS uses the costs you entered. Units with no COGS overstate margin, and Nova returns a notice when that happens.
- Ad history starts about 60 days before you connected Amazon Ads. `historyStartsAtByProduct` gives the exact date per ad type.

Source and updates: https://novadata.io/amazon-ai-agents/find-wasted-amazon-ppc-spend
