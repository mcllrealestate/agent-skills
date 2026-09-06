---
name: mcll-real-estate
description: Search and compare published MCLL properties for sale or rent in Thailand, or read MCLL area, news, and development pages.
license: MIT
compatibility: "Any agent that can make HTTP requests: an MCP client with Streamable HTTP, or plain curl/fetch. No authentication; read-only public data; prices in THB."
metadata:
  author: mcllrealestate
  homepage: https://mcllrealestate.com
  sources:
    - https://mcllrealestate.com/.well-known/api-catalog
    - https://mcllrealestate.com/api/openapi.json
    - https://mcllrealestate.com/.well-known/mcp/server-card.json
---

# MCLL Real Estate

MCLL (mcllrealestate.com) is a Thai real-estate firm. This skill lets an agent search
and read published sale and rent listings across Bangkok, Phuket, and the islands.
Everything here is read-only public data; no key or login is needed.

Pick the access method your runtime supports. MCP and REST return listings; Markdown
also renders the area, news, and development pages.

## 1. MCP server (prefer this)

Streamable-HTTP MCP endpoint: `https://mcllrealestate.com/api/mcp`
Server card: `https://mcllrealestate.com/.well-known/mcp/server-card.json`

Three tools:

- **`search_listings`**: find listings. Args: `type` (`"sale"` | `"rent"`, required),
  `locale` (`en`/`fr`/`th`/`zh`, default `en`), and optional `city`, `area`,
  `propertyType` (human names such as `"Phuket"` or `"Condo"`), `bedrooms`, `minPrice`,
  `maxPrice` (THB), `page`. Returns `{ total, page, results: [{ id, title, url,
  transactionType, city, area, bedrooms, bathrooms, areaSqm, priceThb, image }] }`.
  Each result's `url`
  ends in a `slug` you pass to `get_listing`.
- **`get_listing`**: full detail of one listing. Args: `type` (`"sale"`|`"rent"`),
  `slug` (from a search result), `locale`. Returns price, size, features, coordinates,
  and a Markdown `description`.
- **`execute`**: MCLL Code Mode. Args: `code` (JavaScript string). Runs server-side
  in a Cloudflare Worker sandbox with global `mcll.search(args)` and `mcll.get(args)`.
  No open network, filesystem, environment variables, secrets, or writes. Use `return`
  for the JSON-serializable result; top-level `await` works. Best for comparing,
  ranking, or computing across many listings in one call.

Example `execute` code:

```js
const { results } = await mcll.search({ type: "sale", city: "Bangkok", page: 1 });
const details = await Promise.all(
  results.slice(0, 2).map((r) =>
    mcll.get({ type: "sale", slug: r.url.split("/").pop(), locale: "en" }),
  ),
);

return details.filter((d) => d !== null).map((d) => ({
  title: d.title,
  url: d.url,
  pricePerSqm: d.priceThb && d.areaSqm ? Math.round(d.priceThb / d.areaSqm) : null,
}));
```

## 2. REST API (no MCP client needed)

OpenAPI: `https://mcllrealestate.com/api/openapi.json`

- **Search**: `GET /api/listings?type=sale|rent&locale=en` plus optional filters. Note
  these filters take **ids** (UUIDs), not names: `city`, `area`, `propertyType`,
  `bedrooms`, `bathrooms`, `priceMin`, `priceMax`, `sizeMin`, `sizeMax`, `furnished`,
  `features`, `sort`, `page`. Returns `{ total, page, results }`, where each result has
  the same safe public projection as MCP search: absolute `url`, public image URL, price
  fields, city/area names, and no admin-only fields. For name-based filtering such as
  "condos in Phuket", use the MCP `search_listings` tool, which resolves names to ids
  for you.
- **Detail**: `GET /api/listings/{type}/{slug}?locale=en` returns one listing as JSON, with a
  Markdown `description`. `type` is `sale` or `rent`; `slug` comes from a listing URL's
  last path segment.

```bash
# Detail of a known listing (slug from its public URL):
curl -s "https://mcllrealestate.com/api/listings/sale/kamala-cliff-villa?locale=en"

# Search (sale listings, page 1):
curl -s "https://mcllrealestate.com/api/listings?type=sale&locale=en"
```

## 3. Markdown content negotiation

Send `Accept: text/markdown` to any listing, area, news, or development URL and MCLL
returns a Markdown rendition instead of HTML, with fewer tokens than parsing the page.
The home page returns a site overview. Areas, news, and developments are reachable
only this way; no MCP tool or REST endpoint covers them. Find their URLs in the
sitemap.

```bash
curl -s -H "Accept: text/markdown" \
  "https://mcllrealestate.com/en/buy/villa/phuket/kamala/kamala-cliff-villa"
```

Index and static pages have no Markdown rendition and return HTML; use the search API for
those.

## Typical workflow

1. Use **`execute`** when the user asks for comparison, ranking, or derived metrics
   across multiple listings.
2. Otherwise search for what the user wants (`search_listings`, or `GET /api/listings`).
3. Read the result list and pick the relevant listing(s) by `title`, `area`, `priceThb`.
4. Fetch details for the chosen listing(s) (`get_listing`, the detail REST route, or
   Markdown on the listing `url`).
5. Answer the user with the listing's facts and link them to the public `url`.

An empty search or missing detail means no matching public data is available. Say so;
do not infer that a missing listing has a price upon enquiry. `mcll.get` returns `null`
for a missing slug, so skip it when comparing results.

## Discovery and conventions

- **Sitemaps** (every URL, per locale): `https://mcllrealestate.com/sitemap.xml`.
- **API catalog** (machine index): `https://mcllrealestate.com/.well-known/api-catalog`.
- **Site guide for agents**: `https://mcllrealestate.com/llms-full.txt`.
- **Locales**: `en` · `fr` · `th` · `zh`. Unpublished locales fall back to English.
- **Prices** are in **THB**; `priceOnRequest: true` (or `priceThb: null`) means the price
  is not public. Say "Price upon enquiry", never a number.
- Only **published** listings are returned; there is no auth and nothing is writable here.
