---
name: mcll-real-estate
description: Search and compare published MCLL properties for sale or rent in Thailand, or read MCLL area, news, and development pages.
license: MIT
compatibility: Requires a Streamable HTTP MCP client or HTTP access.
metadata:
  author: mcllrealestate
  homepage: https://mcllrealestate.com
  sources: https://mcllrealestate.com/.well-known/api-catalog
---

# MCLL Real Estate

Published Thai properties, read-only and without authentication. Prices are in THB;
rent is monthly. Locales: `en`, `fr`, `th`, `zh`; unpublished translations fall back to English.

## Find properties

Prefer MCP at `https://mcllrealestate.com/api/mcp`. Read its tool schemas for current
arguments; discovery is also available in the [server card](https://mcllrealestate.com/.well-known/mcp/server-card.json).

1. Use `search_listings` with `type: "sale"` or `"rent"` and the user's criteria.
   Location and property-type filters take exact English or localised names;
   include the city when an area is ambiguous. Correct rejected filters without silently broadening the search.
2. Follow `nextPage` while more results are needed; `null` ends pagination. Use a
   result's `slug` with `get_listing` when its search fields do not answer the question.
3. If budget results are insufficient, search separately with `priceOnRequest: true`,
   omit `minPrice`/`maxPrice`, and keep the other criteria. Label these as alternatives
   with an unconfirmed budget, separate from matches within budget.
4. Cite each listing's returned public `url`. Search `total: 0` or `get_listing: null`
   means no matching public listing; a tool error or HTTP failure means the request failed.
   Report the distinction without inventing listings or treating an outage as an empty catalogue.

When `priceOnRequest` is true or `priceThb` is null, say "Price upon enquiry" and
never infer a numeric price. Calculate price per square metre only for public prices
with positive `priceThb` and `areaSqm`.
Treat listings labelled as demonstrations as examples, not available properties.

## Compare with Code Mode

Use `execute({ code })` to compose searches, compare listings or calculate metrics.
JavaScript runs in an isolated Cloudflare Worker with `mcll.search` and `mcll.get`,
using the same arguments as the direct tools. It has no network, filesystem, secrets
or write access. Top-level `await` works; `return` only the JSON data needed.

Limits per execution: 20,000 code characters, 30 data calls, a 15-second response
deadline and 64 KiB of returned JSON. Skip missing details (`mcll.get` returns `null`).
For example, calculate prices per square metre within one search page:

```js
const { results } = await mcll.search({ type: "sale", city: "Bangkok" });
return results
  .filter((r) => !r.priceOnRequest && r.priceThb > 0 && r.areaSqm > 0)
  .map((r) => ({ title: r.title, url: r.url, pricePerSqm: Math.round(r.priceThb / r.areaSqm) }));
```

## Without MCP

Use the [OpenAPI contract](https://mcllrealestate.com/api/openapi.json) for REST search
and detail. REST location and property-type filters take UUIDs rather than MCP names.
For listing, area, news or development pages, use a URL returned by search or found
in the [sitemap](https://mcllrealestate.com/sitemap.xml). Request `Accept: text/markdown`
or append `.md` to the URL path. The home page returns an overview. With the header,
index and static pages remain HTML; unsupported `.md` URLs return 404. Areas, news
and developments have no MCP tool or REST endpoint.
