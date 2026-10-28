# Tool reference

Nova MCP exposes a pre-modelled Amazon data layer. Tools take structured filters and return tables, so a typical seller question resolves in one call. Every call records a one-line reason, so you can see in Nova how your data was used.

## Sales, profit and diagnosis

| Tool | Returns |
| --- | --- |
| `query_metrics` | Any metric over any period, grouped by SKU, ASIN, marketplace, account or time grain |
| `find_movers` | Biggest gainers and losers for a metric versus a baseline period |
| `explain_movement` | Causal signals behind one product's movement: traffic, conversion, price, Buy Box, ads, rank |

## Advertising

| Tool | Returns |
| --- | --- |
| `query_advertising` | Sponsored Products, Brands and Display at campaign, ad group, targeting, keyword, ASIN or SKU grain, Seller and Vendor programmes |
| `query_search_terms` | Customer search queries behind ad spend, for negatives and discovery |
| `list_negative_targets` | Negative keywords and product targets already in place (Sponsored Products) |
| `query_dsp` | Amazon DSP by advertiser, order, line item, product, supply, device, creative, audience or country |
| `query_sqp` | Search Query Performance: impressions, clicks, cart adds and purchases, with your share, by query |

## Inventory and supply chain

| Tool | Returns |
| --- | --- |
| `query_inventory` | FBA and AWD stock, velocity, days of cover, stockout risk, recommended reorder quantity |
| `get_inventory_overview` | Stock by location with stock value at cost |
| `query_inbound_shipments` | FBA inbound shipments: in progress, received, and closed with missing units |
| `get_stock_setup` / `get_stock_worksheet` | Lead times and stock targets in place, and what is missing |
| `get_supply_chain_setup` / `get_supply_chain_products` | Warehouses, 3PL counts and open supplier orders per product |

## Catalogue, listings and customers

| Tool | Returns |
| --- | --- |
| `query_listing_health` | Suppressed, not buyable, Buy Box lost, catalog issues |
| `query_bsr` | Best Seller Rank for your own ASINs, latest or time series |
| `query_reviews` | Reviews, critical reviews, review rate and star rating per ASIN |
| `query_subscribe_and_save` | Subscribe & Save health, offers, eligibility changes |
| `list_events` / `create_events` | Dated notes on the charts (price change, A+ update, launch) to explain later movements |

## Catalogue of metrics and accounts

| Tool | Returns |
| --- | --- |
| `list_metrics` / `describe_metric` | Every metric available and its definition |
| `list_accounts` | Amazon accounts connected to your Nova workspaces |
| `list_tags` | Your custom product groupings |
| `list_cost_inputs` | Configured COGS, FBM cost and VAT category per SKU, for setup audits |

## Maintaining your inputs

Profit is only as good as the costs behind it. These tools keep them current from inside the assistant. Every write is two steps: a preview with the diff and warnings, then a commit you confirm.

| Area | Tools |
| --- | --- |
| Cost of goods | `get_cogs_setup`, `get_cogs_worksheet`, `import_cogs` |
| FBM shipping | `get_fbm_setup`, `get_fbm_worksheet`, `import_fbm_costs`, `import_fbm_zone_rates` |
| VAT | `get_vat_setup`, `get_vat_worksheet`, `import_vat_categories`, `set_vat_default_category` |
| Product tags | `get_tags_worksheet`, `import_product_tags` |
| Stock and supply | `set_stock_defaults`, `import_stock_params`, `import_supply_chain`, `import_external_stock`, `import_supplier_orders` |

These write into your Nova account only. They never touch Amazon.

## Notes

- Listing content edits on Seller Central are available when enabled for your workspace, for the account owner, previewed with Amazon's validation before they are confirmed. They never change price or quantity.
- Campaign actions are in development.
- Data covers 23 Amazon marketplaces. Orders arrive within seconds; ad spend and finance transactions refresh hourly.
- Metric definitions are shared with the Nova web app, so an answer in your assistant matches the dashboard.
