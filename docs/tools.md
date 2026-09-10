# Tool reference

Nova MCP exposes a pre-modelled Amazon data layer. Tools take structured filters and return tables, so a typical seller question resolves in one call.

## Reading data

| Tool | Returns |
| --- | --- |
| `query_metrics` | Any metric over any period, grouped by SKU, ASIN, marketplace, account or time grain |
| `find_movers` | Biggest gainers and losers for a metric versus a baseline period |
| `explain_movement` | Causal signals behind one SKU's movement: traffic, conversion, price, buy box, ads, rank |
| `query_advertising` | Sponsored Products at campaign, ad group, targeting, keyword, ASIN or SKU grain |
| `query_search_terms` | Customer search queries behind ad spend, for negatives and discovery |
| `query_inventory` | FBA and AWD stock health, velocity, days of inventory, out of stock risk |
| `query_listing_health` | Suppressed, removed, not buyable, buy box lost, hijackers, catalog content |
| `query_bsr` | Best Seller Rank for your own ASINs, latest or time series |
| `query_sqp` | Search Query Performance: impressions, clicks and purchase share by customer query |

## Catalogue and setup

| Tool | Returns |
| --- | --- |
| `list_metrics` | Every metric available, with its grain |
| `describe_metric` | Definition and calculation of one metric |
| `list_accounts` | Seller accounts connected to your Nova organisation |
| `list_tags` | Custom product groupings you have configured |
| `list_cost_inputs` | Configured per SKU COGS, FBM cost and VAT category, useful for setup audits |

## Maintaining your cost inputs

Profit is only as good as the costs behind it. These tools let you keep them current from inside the assistant rather than through a spreadsheet import.

| Tool | Does |
| --- | --- |
| `import_cogs` | Set or update cost of goods per SKU |
| `import_fbm_costs` / `import_fbm_zone_rates` | Maintain FBM shipping cost inputs |
| `import_vat_categories` / `set_vat_default_category` | Maintain VAT treatment per product |
| `import_product_tags` | Update custom product groupings |

These write into your Nova account only. They never touch Amazon.

## Notes

- Today, all Amazon access is read only. No tool can modify a listing, a campaign, inventory or a price.
- Cost configuration tools write only into your own Nova account, never to Amazon.
- Campaign and listing actions are in development. They will be opt in and scoped separately from read access.
- Data covers 23 Amazon marketplaces and refreshes hourly.
- Metric definitions are shared with the Nova web app, so an answer in Claude matches the dashboard.
