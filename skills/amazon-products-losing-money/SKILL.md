---
name: amazon-products-losing-money
description: Use when the user asks which Amazon products lose money, or are unprofitable after fees, COGS and advertising. Ranks products by profit after ads from Nova data.
---

# Which Amazon products lose money after ads

This skill needs the Nova MCP server connected (`https://mcp.novadata.io/api/mcp`, OAuth sign-in with a Nova account). Every step calls a Nova tool that reads live Amazon data. Setup for each assistant: https://novadata.io/docs/mcp/connect-your-ai

## Instructions

Use Nova to find which of my Amazon products lose money after ads, and why.

Period: the last 90 complete days. If I name other dates, use those.

1. Check the costs first. Call Nova's get_setup with domain "cogs". If soldSkusMissingCogs is above 0 in a region, say so: products without a cost show a profit that is too high, and a product can look profitable when it is not.

2. List the losers. Call Nova's query_metrics with groupBy "childAsin", the metrics netSales, units, netProfit, netProfitMargin, adSpend and sponsoredTacos, metricFilters netProfit below 0, sorted by netProfit ascending, limit 20, and costCategories ["variable", "product", "account"] so one-off costs and reimbursements do not hide the recurring loss. If cogsNotice appears, say what share of units has no cost.

3. Separate ads from the product. For each product, profit before ads = netProfit + adSpend. A product that loses money before ads has a price, cost or fee problem. One that turns negative only after ads has an advertising problem.

4. Read the fees. For the five biggest losses, call Nova's query_metrics with groupBy "childAsin", the same costCategories, and the metrics cogs, totalAmazonFees, commissionFee, fbaFulfillmentFee, fbaStorageFee and fbaLongTermStorageFee. Divide by units and say which cost weighs most per unit.

5. Read the ads. Call Nova's query_advertising with groupBy "childAsin" for those products, with spend, sales, acos, breakEvenAcos and profitAfterAds. Say where ACOS sits above break-even ACOS.

6. Check the trend. Call Nova's query_metrics with groupBy "month" over the last 6 months for the five biggest losses, with netProfit. Say whether the loss is new or has lasted.

7. Report, one row per product: net sales, net profit, margin, whether the loss comes before or after ads, the cost that weighs most, and one action: raise the price, cut the bid, renegotiate the cost, or stop selling. Then the total loss.

Rules: these are correlations. Never call a product unprofitable when its cost is missing: say the cost is missing. Relay every notice Nova returns (P&L rules, cost categories, marketplaces added up, COGS gaps, currency, ad history). You cannot change prices, bids or listings.

## How to read the result

| What you see | What it usually means | What to do |
| --- | --- | --- |
| Loss before ads | Price, product cost or fees are too high for the price | Raise the price or renegotiate the cost |
| Profit before ads, loss after | Ads cost more than the margin | Lower bids where ACOS is above break-even |
| Storage fees weigh most | Too much stock for the sales | Slow the next order, consider a removal |
| Loss only in the last month | A recent change | Check price, fee or ad changes with list_events |
| Product without a cost | The profit shown is wrong | Add the cost before deciding |

## Limits

- Profit is only as right as the costs you entered. Units with no cost read a cost of 0.
- One-off costs and reimbursements are left out in step 2 on purpose, so a product's recurring economics show.
- Break-even ACOS covers Sponsored Products and Sponsored Display, not Sponsored Brands.
- Ad history starts about 60 days before you connected Amazon Ads.
- Nova reads your data. It does not change prices, bids or listings.

Source and updates: https://novadata.io/amazon-ai-agents/amazon-products-losing-money
