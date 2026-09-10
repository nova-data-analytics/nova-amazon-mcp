# Nova Amazon Seller MCP

Connect your Amazon Seller Central and Amazon Ads data to Claude, ChatGPT, Gemini or Cursor through the Model Context Protocol, and ask questions in plain language.

```
Which SKUs lost the most net profit versus last month, and why?
Which keywords burned more than $100 with zero sales?
What runs out of stock in the next three weeks across all marketplaces?
```

This repository is the setup kit and reference for **Nova MCP**, a hosted MCP server. It contains ready to paste client configurations, a documented tool reference and an example prompt library. The server itself is a commercial service operated by [Nova](https://www.novadata.io); this repo does not contain server code.

---

## What it is

| | |
|---|---|
| **Endpoint** | `https://mcp.novadata.io/api/mcp` |
| **Transport** | Remote, Streamable HTTP |
| **Auth** | OAuth, per seller account, issued in Nova |
| **Access today** | Reads Amazon data. Writes only to your own Nova cost configuration (COGS, FBM, VAT) |
| **In development** | Campaign and listing actions, taken with full profit context |
| **Marketplaces** | 23 Amazon marketplaces |
| **Freshness** | Hourly sync |
| **Pricing** | Commercial. Nova plans from $29/mo, 14 day free trial, no card |

## Why a data layer rather than raw SP-API

Amazon's Selling Partner API and Advertising API return raw reports split across many endpoints, with no reconciliation between them. An assistant pointed at them spends most of its context paginating and joining.

Nova MCP exposes a pre-modelled layer instead: orders, settlements and 40+ Amazon fee types reconciled at SKU level, Sponsored Products down to the customer search term, FBA and AWD inventory with days of cover, listing health and buy box state, and Best Seller Rank for your own ASINs. Filtering and ranking run server side, so one seller question is usually one tool call rather than a pagination loop.

## Setup

You need a Nova account with Seller Central and Amazon Ads connected, and at least one completed hourly sync. Start a trial at [novadata.io](https://www.novadata.io/amazon-mcp-for-sellers) if you do not have one.

- **[Claude Desktop and Claude web](configs/claude-desktop.md)**
- **[Claude Code](configs/claude-code.md)**
- **[ChatGPT](configs/chatgpt.md)**
- **[Cursor](configs/cursor.md)**

The server URL is identical everywhere. Each client adds it in its own connector settings.

## Tool reference

Full detail in [docs/tools.md](docs/tools.md).

| Tool | What it returns |
| --- | --- |
| `query_metrics` | Any metric over any period, grouped by SKU, ASIN, marketplace, account or time grain |
| `find_movers` | Biggest gainers and losers for a metric versus a baseline period |
| `explain_movement` | Causal signals behind one SKU's movement: traffic, conversion, price, buy box, ads, rank |
| `query_advertising` | Sponsored Products at campaign, ad group, targeting, keyword, ASIN or SKU grain |
| `query_search_terms` | Customer search queries behind ad spend, for negatives and discovery |
| `query_inventory` | FBA and AWD stock health, velocity, days of inventory, out of stock risk |
| `query_listing_health` | Suppressed, removed, not buyable, buy box lost, hijackers, catalog content |
| `query_bsr` | Best Seller Rank for your own ASINs, latest or time series |
| `query_sqp` | Search Query Performance: impressions, clicks and purchase share by query |
| `list_cost_inputs` | Configured per SKU COGS, FBM cost and VAT category, for setup audits |
| `import_cogs` / `import_fbm_costs` / `import_vat_categories` | Maintain your cost inputs conversationally, without leaving the assistant |
| `list_metrics` / `describe_metric` | Metric catalogue and definitions |
| `list_accounts` / `list_tags` | Seller accounts and custom product filters |

## Example prompts

A longer library organised by job lives in [prompts/](prompts/README.md).

**Profit**
- Show me profit by SKU for the last 30 days after PPC.
- Which SKUs lost the most net profit versus last month, and why?
- What is my true TACoS by ASIN this quarter?

**Advertising**
- Which campaigns spent over $100 last week with zero orders?
- Find search terms with ACoS under 25% and at least 3 orders.
- Which keywords have good ACoS but negative contribution margin?

**Inventory**
- What runs out of stock in the next three weeks across all marketplaces?
- Which SKUs are sitting on more than 120 days of cover?

**Listings**
- Which listings are suppressed or losing the buy box right now?

## How this compares

Honest framing, because the choice depends on what you need.

| | Nova MCP | Amazon Ads MCP | Raw SP-API |
|---|---|---|---|
| Advertising data | Yes, to search term level | Yes, official | Via Ads API |
| Search Query Performance | Yes | No | Partial, separate report |
| Seller Central financials | Yes, reconciled to SKU | No | Raw reports, unreconciled |
| Your COGS and landed cost | Yes, maintained through the MCP itself | No | No |
| True profit after fees and ads | Yes | No | Build it yourself |
| Listing health and buy box | Yes | No | Separate APIs |
| Campaign and listing actions | In development | Yes | Yes |
| **Acts with profit context** | **In development** | **No** | **No** |

The last row is the point. Amazon's own Ads MCP Server can change a campaign, and it is free and official, but it does not know your landed cost, so it cannot tell a profitable ACoS from an unprofitable one. A profit tool that cannot act makes you do the work by hand. Nova is building the combination: change the bid knowing what the SKU actually earns.

Until those actions ship, Nova MCP reads Amazon and maintains your cost inputs. If you need campaign writes today, use Amazon's Ads MCP Server alongside it.

## Security and permissions

- OAuth against Nova. No Amazon password is ever shared with the MCP
- Credentials are per seller account and revocable at any time from Nova
- Today the server cannot modify anything on Amazon: not listings, campaigns, inventory or prices
- The write tools that exist today update only your own cost configuration inside Nova (COGS, FBM rates, VAT categories)
- Action tools currently in development will be opt in, scoped separately from read access, and revocable independently

If you are evaluating any Amazon MCP server, including this one, the questions worth asking are the same: does it use Amazon OAuth rather than credential sharing, are read and write scoped separately, can a human approve changes before they apply, and is there an audit trail of what the assistant changed.

## Links

- Product: [novadata.io/amazon-mcp-for-sellers](https://www.novadata.io/amazon-mcp-for-sellers)
- Setup guide: [novadata.io/amazon-ai-agents/connect-claude](https://www.novadata.io/amazon-ai-agents/connect-claude)
- Data coverage: [novadata.io/amazon-data-library](https://www.novadata.io/amazon-data-library)
- Prompt library: [novadata.io/amazon-mcp-prompts](https://www.novadata.io/amazon-mcp-prompts)

## Contributing

Issues and pull requests are welcome for the configs, docs and prompt library. For questions about the hosted service, contact Nova through the site.

## License

MIT for the contents of this repository. See [LICENSE](LICENSE). The Nova MCP service itself is commercial.
