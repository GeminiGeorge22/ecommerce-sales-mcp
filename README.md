# E-commerce Sales Signals MCP: Amazon bestsellers, eBay sold prices, TikTok Shop, Etsy shop sales

**What is actually selling, as MCP tools**: Amazon Best Sellers ranks, Amazon reviews, confirmed eBay sold prices, TikTok Shop sales estimates and Etsy shop sales counters, in one hosted bundle. One Actor per sales signal.

Hosted remote MCP server (Streamable HTTP) on [Apify's MCP gateway](https://mcp.apify.com). **Client config plus a small open-source stdio proxy server. No Actor source.**

**Disclosure:** entries marked "(maintained by us)" are Actors I maintain on Apify Store (publisher `publicrecords`). Every other Actor is independent, made by its own publisher and picked on merit (Store usage, ratings and recent maintenance). This repo is not affiliated with those publishers or with the sites the Actors read.

## MCP endpoint

```
https://mcp.apify.com/?tools=junglee/amazon-bestsellers,junglee/amazon-reviews-scraper,caffein.dev/ebay-sold-listings,unseenuser/TikTok-Shop-Scraper,publicrecords/etsy-shop-velocity,publicrecords/etsy-search-scraper
```

Auth: sign in with Apify (OAuth) when your client prompts, or send an Apify API token as a Bearer token. Runs are billed on your own Apify account under each Actor's own Store pricing; see each Store page.

## Tools

| Actor | Store page | MCP tool | What it returns |
|---|---|---|---|
| **Amazon Bestsellers Scraper** | [junglee/amazon-bestsellers](https://apify.com/junglee/amazon-bestsellers) | `junglee--amazon-bestsellers` | Top 100 items in any Amazon Best Sellers category: rank, name, price, URL. |
| **Amazon Reviews Scraper** | [junglee/amazon-reviews-scraper](https://apify.com/junglee/amazon-reviews-scraper) | `junglee--amazon-reviews-scraper` | Amazon product reviews: rating, text, date, reactions and images, for review-velocity and sentiment. |
| **eBay Sold Listings Search** | [caffein.dev/ebay-sold-listings](https://apify.com/caffein.dev/ebay-sold-listings) | `caffein-dot-dev--ebay-sold-listings` | Confirmed eBay sold prices by keyword across 8 marketplaces, with date-range filters. |
| **TikTok Shop Scraper** | [unseenuser/TikTok-Shop-Scraper](https://apify.com/unseenuser/TikTok-Shop-Scraper) | `unseenuser--TikTok-Shop-Scraper` | TikTok Shop products, reviews, sellers, creator affiliates and GMV/sales estimates (US, UK). |
| **Etsy Shop Sales Tracker** (maintained by us) | [publicrecords/etsy-shop-velocity](https://apify.com/publicrecords/etsy-shop-velocity) | `publicrecords--etsy-shop-velocity` | Etsy shop sales counters with the date each was read, so repeat reads give sales over time. One row per shop. |
| **Etsy Search Scraper** (maintained by us) | [publicrecords/etsy-search-scraper](https://apify.com/publicrecords/etsy-search-scraper) | `publicrecords--etsy-search-scraper` | Etsy search results with Bestseller and Star Seller badges, review counts and search rank. |

The Apify gateway also exposes helper tools to read run results (`get-actor-run`, `get-dataset-items`). Want fewer tools? Drop any Actor from the `tools=` list.

## Who it's for

- Product research: which items are selling now, by marketplace?
- Resellers: what did this item actually sell for on eBay recently?
- Brands and agencies: how fast are a competitor's reviews or shop sales growing?
- Analysts: track bestseller ranks and sales counters over time.

## Example prompts

- List today's top 20 Amazon Best Sellers in `<category>` with prices.
- What did `<item>` sell for on eBay in the last 30 days? Give min, median and max.
- How many sales did these Etsy shops show today: `<shop1>`, `<shop2>`? Read again next week and give the difference.
- Which TikTok Shop products in `<category>` have the highest sales estimates?

## Cursor / Claude Code / any remote-MCP client

```json
{
  "mcpServers": {
    "ecommerce-sales": {
      "url": "https://mcp.apify.com/?tools=junglee/amazon-bestsellers,junglee/amazon-reviews-scraper,caffein.dev/ebay-sold-listings,unseenuser/TikTok-Shop-Scraper,publicrecords/etsy-shop-velocity,publicrecords/etsy-search-scraper"
    }
  }
}
```

Claude Code:

```sh
claude mcp add --transport http ecommerce-sales "https://mcp.apify.com/?tools=junglee/amazon-bestsellers,junglee/amazon-reviews-scraper,caffein.dev/ebay-sold-listings,unseenuser/TikTok-Shop-Scraper,publicrecords/etsy-shop-velocity,publicrecords/etsy-search-scraper"
```

## Claude Desktop (local bridge)

```json
{
  "mcpServers": {
    "ecommerce-sales": {
      "command": "npx",
      "args": ["-y", "@apify/actors-mcp-server", "--actors", "junglee/amazon-bestsellers,junglee/amazon-reviews-scraper,caffein.dev/ebay-sold-listings,unseenuser/TikTok-Shop-Scraper,publicrecords/etsy-shop-velocity,publicrecords/etsy-search-scraper"],
      "env": { "APIFY_TOKEN": "YOUR_APIFY_TOKEN" }
    }
  }
}
```

## Local stdio server (open source)

This repo also ships a small MCP server in [`src/`](src/) for clients that only run local stdio servers. It answers `initialize` and `tools/list` locally from [`tools.json`](tools.json), which is generated from each Actor's public input schema and checked for drift in CI and weekly, so any client or directory can inspect the tools with no token. `tools/call` is forwarded to the hosted Apify MCP endpoint above with **your own** `APIFY_TOKEN`; without a token a call returns a clear error instead of running anything. The token is only sent in the `Authorization` header and is never logged.

```json
{
  "mcpServers": {
    "ecommerce-sales": {
      "command": "npx",
      "args": ["-y", "github:GeminiGeorge22/ecommerce-sales-mcp"],
      "env": { "APIFY_TOKEN": "YOUR_APIFY_TOKEN" }
    }
  }
}
```

Or from a clone:

```bash
git clone https://github.com/GeminiGeorge22/ecommerce-sales-mcp.git
cd ecommerce-sales-mcp && npm install
APIFY_TOKEN=YOUR_APIFY_TOKEN node src/server.js
```

Optional env: `APIFY_ACTORS` (comma-separated subset of the Actors above) and `APIFY_MCP_URL` (full endpoint override).

Tests run against an in-memory mock server, with no network and no token: `npm test`. Requires Node.js 18+.

## See also

- [ecommerce-search-rankings-mcp](https://github.com/GeminiGeorge22/ecommerce-search-rankings-mcp): E-commerce search results and rankings
- [forecast-accuracy-mcp](https://github.com/GeminiGeorge22/forecast-accuracy-mcp): Forecast accuracy and track records
- [sports-betting-stats-mcp](https://github.com/GeminiGeorge22/sports-betting-stats-mcp): Sports betting odds and stats
- [sports-odds-mcp](https://github.com/GeminiGeorge22/sports-odds-mcp): Kambi sportsbook odds
- [etsy-sales-mcp](https://github.com/GeminiGeorge22/etsy-sales-mcp): Etsy search and shop sales
- [ats-jobs-mcp](https://github.com/GeminiGeorge22/ats-jobs-mcp): ATS job postings (Greenhouse, Lever, Ashby, SmartRecruiters)
- [gov-tenders-mcp](https://github.com/GeminiGeorge22/gov-tenders-mcp): Government tenders and grants

## Registry

Official MCP Registry name: `io.github.GeminiGeorge22/ecommerce-sales-mcp`

## License

MIT, for this documentation, config and the proxy server code in `src/`. Each Actor belongs to its publisher.
