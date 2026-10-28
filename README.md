# Nova Amazon MCP Server

Connect Amazon Seller Central, Vendor Central, Amazon Ads and DSP data to Claude, ChatGPT, Microsoft Copilot, Grok, Gemini or Cursor through the Model Context Protocol, and ask questions in plain language.

```
Which SKUs lost the most net profit versus last month, and why?
Which search terms spent more than $100 last week with zero sales?
What runs out of stock in the next three weeks across all marketplaces?
```

This repository is the setup kit and reference for **Nova MCP**, a hosted MCP server. It contains ready to paste client configurations, the tool reference and an example prompt library. The server itself is a commercial service operated by [Nova](https://novadata.io/amazon-mcp-server); this repo does not contain server code.

---

## What it is

| | |
|---|---|
| **Endpoint** | `https://mcp.novadata.io/api/mcp` |
| **Transport** | Remote, Streamable HTTP |
| **Auth** | OAuth 2.0 with dynamic client registration, per Amazon account, revocable in Nova |
| **Amazon data** | Seller Central (3P), Vendor Central (1P), Amazon Ads, Amazon DSP, Search Query Performance, reviews, Subscribe & Save |
| **Marketplaces** | 23 Amazon marketplaces |
| **Freshness** | Orders and units within seconds of the sale. Ad spend and finance transactions hourly, search terms three times a day, other reports daily |
| **Assistants** | Claude, ChatGPT, Microsoft Copilot, Grok, Gemini, Cursor, and any MCP client |
| **Pricing** | Commercial. From $49/month per Amazon account, 14 day free trial, no card |

## Why a data layer rather than raw Amazon APIs

Amazon's Selling Partner, Vendor and Advertising APIs return raw reports split across many endpoints, with no reconciliation between them. An assistant pointed at them spends its context paginating and joining, and two questions can get two different numbers.

Nova MCP exposes a pre-modelled layer instead: orders, settlements and every Amazon fee reconciled at SKU level, your own costs applied (COGS, FBM shipping, VAT), Sponsored ads down to the customer search term, DSP by order and line item, FBA and AWD inventory with days of cover, listing health and Buy Box, Best Seller Rank. Filtering and ranking run server side, so one seller question is usually one tool call, and the answer matches the Nova dashboard.

## Setup

You need a Nova account with at least one Amazon account connected. Start a trial at [novadata.io](https://novadata.io/amazon-mcp-server) if you do not have one.

- **[Claude (web and desktop)](configs/claude-desktop.md)**
- **[Claude Code](configs/claude-code.md)**
- **[ChatGPT](configs/chatgpt.md)**
- **[Microsoft Copilot](configs/copilot.md)**
- **[Grok](configs/grok.md)**
- **[Gemini](configs/gemini.md)**
- **[Cursor](configs/cursor.md)**
- **Custom enterprise AI**: any MCP client that supports remote servers and OAuth. See [novadata.io/amazon-ai-agents/connect-enterprise-ai](https://novadata.io/amazon-ai-agents/connect-enterprise-ai)

The server URL is identical everywhere. Each client adds it in its own connector settings.

## Tools

Full reference in [docs/tools.md](docs/tools.md).

| Tool | What it returns |
| --- | --- |
| `query_metrics` | Any metric over any period (sales, fees, profit, ads, sessions...), by SKU, ASIN, marketplace, account or time grain |
| `find_movers` | Biggest gainers and losers for a metric versus a baseline period |
| `explain_movement` | Causal signals behind one product's movement: traffic, conversion, price, Buy Box, ads, rank |
| `query_advertising` | Sponsored Products, Brands and Display, from campaign down to keyword and ASIN |
| `query_search_terms` | Customer search queries behind ad spend, for negatives and discovery |
| `query_dsp` | Amazon DSP spend and results by advertiser, order, line item, product, device, creative or audience |
| `query_sqp` | Search Query Performance: impressions, clicks, cart adds and purchase share by query |
| `query_inventory` | FBA and AWD stock, velocity, days of cover, stockout risk, reorder quantities |
| `query_listing_health` | Suppressed, not buyable, Buy Box lost, catalog issues |
| `query_reviews` | Reviews, critical reviews and star rating per ASIN |
| `query_subscribe_and_save` | Subscribe & Save health, offers and eligibility |
| `import_cogs` / `import_fbm_costs` / `import_vat_categories` | Maintain your cost inputs from the conversation, previewed before anything is saved |

## Example prompts

A longer library organised by job lives in [prompts/](prompts/README.md) and at [novadata.io/amazon-mcp-prompts](https://novadata.io/amazon-mcp-prompts).

**Profit**
- Give me last week's P&L by marketplace and name the three biggest swings.
- Which SKUs lost the most net profit versus last month, and why?
- What is my true TACoS by ASIN this quarter?

**Advertising**
- Which search terms spent the most last week without a sale?
- Find SKUs where ad spend is up 30% but units are flat or down.
- What did Amazon DSP cost us last month, and what did it drive?

**Vendor Central**
- What did Amazon order from us this week versus last week?
- Compare Vendor Central shipped revenue with Seller Central sales for the same products this month.

**Inventory**
- Which SKUs will run out of stock in the next 30 days at current sales speed?
- Which products should I reorder this week, given my lead times?

## How it differs from Amazon's own MCP servers

Amazon is the source of truth for the data it serves, and its MCP servers are a good fit for what they cover: the Amazon Ads MCP server for campaign management, and Amazon's Selling Partner plugin for Seller Central (US stores at launch).

Nova adds the processing on top: reports pre-joined so one call returns a consistent answer, your own costs applied so the answer is profit rather than sales, ad and DSP spend inside the P&L, Vendor Central alongside Seller Central, and every account and marketplace in one question. Agencies and multi-brand teams query all their accounts at once.

More detail: [novadata.io/amazon-seller-central-mcp-server](https://novadata.io/amazon-seller-central-mcp-server)

## Security and permissions

- OAuth against Nova. No Amazon password is ever shared with the MCP
- Access is per Amazon account and revocable at any time from Nova
- Workspace members only see the accounts they are assigned to
- Cost tools write only to your own Nova configuration (COGS, FBM, VAT, tags, stock settings), never to Amazon, and always preview the change before saving it
- Listing content edits (title, bullets, description, backend keywords, product attributes, never price or quantity) are available on Seller Central when enabled for your workspace, for the account owner only, previewed with Amazon's validation before they are confirmed
- Campaign actions are in development and will follow the same preview and confirm pattern

## Links

- Amazon MCP server: [novadata.io/amazon-mcp-server](https://novadata.io/amazon-mcp-server)
- Seller Central MCP: [novadata.io/amazon-seller-central-mcp-server](https://novadata.io/amazon-seller-central-mcp-server)
- Connection guides: [novadata.io/amazon-ai-agents/connect](https://novadata.io/amazon-ai-agents/connect)
- Tool list: [novadata.io/amazon-mcp-tools](https://novadata.io/amazon-mcp-tools)
- Prompt library: [novadata.io/amazon-mcp-prompts](https://novadata.io/amazon-mcp-prompts)
- For agencies: [novadata.io/amazon-mcp-for-agencies](https://novadata.io/amazon-mcp-for-agencies)

## Contributing

Issues and pull requests are welcome for the configs, docs and prompt library. For questions about the hosted service, contact Nova through the site.

## License

MIT for the contents of this repository. See [LICENSE](LICENSE). The Nova MCP service itself is commercial.
