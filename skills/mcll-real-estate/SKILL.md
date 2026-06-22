---
name: mcll-real-estate
description: Search and retrieve MCLL real-estate listings — residences for sale and rent across Thailand (Bangkok, Phuket, and the islands) from mcllrealestate.com. Use this skill whenever a user asks about Thai property, a condo/villa/house/apartment to buy or rent in Thailand, prices or areas for Thai real estate, branded residences or new developments in Thailand, or wants to browse, filter, or compare MCLL listings — even if they don't name "MCLL". It queries published listings three ways — the MCLL MCP server (richest), a public REST API, or Markdown over plain HTTP — so it works for MCP-capable agents and for plain curl/CLI. Read-only, no auth, no API key. Skip for real estate outside Thailand or for non-MCLL portals.
when_to_use: A user wants Thai property data — search by city, area, property type, price (THB), or bedrooms, or fetch one listing's full details (price, size, features, location, description). Prefer the MCP server (tools search_listings then get_listing) when your runtime speaks MCP; otherwise use the REST API or Markdown content negotiation. All three hit the same published-gated data on mcllrealestate.com.
license: MIT
compatibility: "Any agent that can make HTTP requests — an MCP client (Streamable HTTP), or plain curl/fetch. No authentication; read-only public data; prices in THB."
metadata:
  author: mcllrealestate
  homepage: https://mcllrealestate.com
  sources:
    - https://mcllrealestate.com/.well-known/api-catalog
    - https://mcllrealestate.com/api/openapi.json
    - https://mcllrealestate.com/.well-known/mcp/server-card.json
---

# MCLL Real Estate

MCLL (mcllrealestate.com) is a premium Thai real-estate firm. This skill lets an agent
search and read its published listings — for sale and for rent, across Bangkok, Phuket,
and the islands. Everything here is read-only public data; no key or login is needed.

Pick the access method your runtime supports. MCP and REST return listings; Markdown
also renders the area, news, and development pages.

## 1. MCP server (richest — prefer this)

Streamable-HTTP MCP endpoint: `https://mcllrealestate.com/api/mcp`
Server card: `https://mcllrealestate.com/.well-known/mcp/server-card.json`

Two tools:

- **`search_listings`** — find listings. Args: `type` (`"sale"` | `"rent"`, required),
  `locale` (`en`/`fr`/`th`/`zh`, default `en`), and optional `city`, `area`,
  `propertyType` (human names — `"Phuket"`, `"Condo"`), `bedrooms`, `minPrice`, `maxPrice`
  (THB), `page`. Returns `{ total, page, results: [{ id, title, url, transactionType,
  city, area, bedrooms, bathrooms, areaSqm, priceThb, image }] }` — each result's `url`
  ends in a `slug` you pass to `get_listing`.
- **`get_listing`** — full detail of one listing. Args: `type` (`"sale"`|`"rent"`),
  `slug` (from a search result), `locale`. Returns price, size, features, coordinates,
  and a Markdown `description`.

If your platform supports Cloudflare-style "code mode", point it at the endpoint and it
will convert these tools into a typed API automatically (the JSON Schemas above carry the
doc comments) — no special setup on MCLL's side.

## 2. REST API (no MCP client needed)

OpenAPI: `https://mcllrealestate.com/api/openapi.json`

- **Search** — `GET /api/listings?type=sale|rent&locale=en` plus optional filters. Note
  these filters take **ids** (UUIDs), not names: `city`, `area`, `propertyType`,
  `bedrooms`, `priceMin`, `priceMax`, `sort`, `page`. Returns `{ items, total }`. (For
  name-based filtering — "condos in Phuket" — use the MCP `search_listings` tool, which
  resolves names to ids for you.)
- **Detail** — `GET /api/listings/{type}/{slug}?locale=en` → one listing as JSON, with a
  Markdown `description`. `type` is `sale` or `rent`; `slug` comes from a listing URL's
  last path segment.

```bash
# Detail of a known listing (slug from its public URL):
curl -s "https://mcllrealestate.com/api/listings/sale/kamala-condo-5?locale=en"

# Search (sale listings, page 1):
curl -s "https://mcllrealestate.com/api/listings?type=sale&locale=en"
```

## 3. Markdown content negotiation (any page → clean text)

Send `Accept: text/markdown` to any listing, area, news, or development URL and MCLL
returns a Markdown rendition instead of HTML — far fewer tokens than parsing the page.
The home page returns a site overview. Areas, news, and developments are reachable
only this way — no MCP tool or REST endpoint covers them; find their URLs in the
sitemap.

```bash
curl -s -H "Accept: text/markdown" \
  "https://mcllrealestate.com/en/buy/condo/phuket/kamala/kamala-condo-5"
```

Index and static pages have no Markdown rendition and return HTML — use the search API for
those.

## Typical workflow

1. **Search** for what the user wants (`search_listings`, or `GET /api/listings`).
2. Read the result list; pick the relevant listing(s) by `title`, `area`, `priceThb`.
3. **Fetch details** for the chosen listing(s) (`get_listing`, the detail REST route, or
   Markdown on the listing `url`).
4. Answer the user with the listing's facts and link them to the public `url`.

## Discovery and conventions

- **Sitemaps** (every URL, per locale): `https://mcllrealestate.com/sitemap.xml`.
- **API catalog** (machine index): `https://mcllrealestate.com/.well-known/api-catalog`.
- **Site guide for agents**: `https://mcllrealestate.com/llms-full.txt`.
- **Locales**: `en` · `fr` · `th` · `zh`. Unpublished locales fall back to English.
- **Prices** are in **THB**; `priceOnRequest: true` (or `priceThb: null`) means the price
  is not public — say "Price upon enquiry", never a number.
- Only **published** listings are returned; there is no auth and nothing is writable here.
