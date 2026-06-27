<div align="center">

<img src="assets/mcll.png" width="440" alt="MCLL Real Estate"/>

<!-- omit in toc -->
# Agent Skills

**AI agent skills for [MCLL Real Estate](https://mcllrealestate.com): published Thai residences for sale and rent**

Frontmatter-validated and security-scanned on every push. Read-only, no API key.

[![license](https://img.shields.io/badge/license-MIT-1C1A16?style=flat-square)](LICENSE.md)
[![latest](https://img.shields.io/github/v/release/mcllrealestate/agent-skills?style=flat-square&label=latest&color=1C1A16)](https://github.com/mcllrealestate/agent-skills/releases)
[![standard](https://img.shields.io/badge/agentskills.io-1C1A16?style=flat-square)](https://agentskills.io)
[![ci](https://img.shields.io/github/actions/workflow/status/mcllrealestate/agent-skills/ci.yml?branch=main&style=flat-square&label=ci&color=1C1A16)](https://github.com/mcllrealestate/agent-skills/actions/workflows/ci.yml)
[![scan](https://img.shields.io/github/actions/workflow/status/mcllrealestate/agent-skills/scan-skills.yml?branch=main&style=flat-square&label=scan&color=1C1A16)](https://github.com/mcllrealestate/agent-skills/actions/workflows/scan-skills.yml)

</div>

## Install

Install via [skills.sh](https://skills.sh):

```bash
# All skills
npx skills add mcllrealestate/agent-skills

# Individual skill
npx skills add mcllrealestate/agent-skills --skill mcll-real-estate
```

Or read [`skills/mcll-real-estate/SKILL.md`](skills/mcll-real-estate/SKILL.md) directly. The skill is self-contained and optimised for Claude Code; it degrades gracefully on any agent that implements the [Agent Skills](https://agentskills.io) standard.

The Claude Code marketplace manifest lives at [`.claude-plugin/marketplace.json`](.claude-plugin/marketplace.json) and exposes the skill under `real-estate-skills`.

## Requirements

- An agent that can make HTTP requests: an MCP client (Streamable HTTP), or plain `curl` / `fetch`
- No authentication, no API key, no write access.

## mcll-real-estate

Published [MCLL](https://mcllrealestate.com) listings, residences for sale and rent across Bangkok, Phuket, and the islands. Listings are reachable through an MCP server, a REST API, and Markdown. Areas, news, and developments are reachable as Markdown only.

### Listings

- **MCP server**: `https://mcllrealestate.com/api/mcp` (Streamable HTTP). Tools: `search_listings`, `get_listing`, and `execute`.
- **[Code Mode](https://developers.cloudflare.com/agents/model-context-protocol/codemode/)**: `execute` runs JavaScript in a Cloudflare sandbox with `mcll.search(args)` and `mcll.get(args)`. Use it to compare, rank, or compute across listings in one call.
- **REST API**: `GET /api/listings` and `GET /api/listings/{type}/{slug}`, described by [`/api/openapi.json`](https://mcllrealestate.com/api/openapi.json).
- **Markdown**: send `Accept: text/markdown` to a listing detail URL for a clean text rendition.

```bash
# One listing, as Markdown:
curl -s -H "Accept: text/markdown" \
  https://mcllrealestate.com/en/buy/villa/phuket/kamala/kamala-cliff-villa
```

### Areas, news, developments

No MCP tool or REST endpoint covers these. Send `Accept: text/markdown` to an individual area, news, or development page and the site returns Markdown in place of HTML. Find the URLs through the [sitemap](https://mcllrealestate.com/sitemap.xml); index and static pages stay HTML.

Discovery: [sitemap](https://mcllrealestate.com/sitemap.xml) · [API catalog](https://mcllrealestate.com/.well-known/api-catalog) · [MCP server card](https://mcllrealestate.com/.well-known/mcp/server-card.json) · [site guide](https://mcllrealestate.com/llms-full.txt).

## Quality

- Follows the [agentskills.io](https://agentskills.io) open standard.
- CI validates SKILL.md frontmatter, marketplace parity, and eval metadata.
- [`cisco-ai-defense/skill-scanner`](https://github.com/cisco-ai-defense/skill-scanner) scans pull requests and pushes with policy `balanced`, fail-on `critical`.
- The site advertises the skill through [`/.well-known/agent-skills/index.json`](https://mcllrealestate.com/.well-known/agent-skills/index.json). Updating `SKILL.md` requires updating that published digest before release.

## License

[MIT](LICENSE.md)
