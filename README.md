<div align="center">

<img src="assets/mcll.png" width="440" alt="MCLL Real Estate"/>

<!-- omit in toc -->
# Agent Skills

**AI agent skills for [MCLL Real Estate](https://mcllrealestate.com): published Thai residences for sale and rent**

Frontmatter-validated and security-scanned on pull requests and pushes to main. Read-only, no API key.

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

Or read [`skills/mcll-real-estate/SKILL.md`](skills/mcll-real-estate/SKILL.md) directly. The skill is self-contained and works with agents that implement the [Agent Skills](https://agentskills.io) standard and support HTTP requests or an MCP client.

The Claude Code marketplace manifest lives at [`.claude-plugin/marketplace.json`](.claude-plugin/marketplace.json) and exposes the skill under `real-estate-skills`.

To make the MCP tools available in Codex, register the server:

```bash
codex mcp add mcll --url https://mcllrealestate.com/api/mcp
```

Reload the MCP connection if needed, then check that `mcll` is enabled in MCP settings.

## Requirements

- An agent that can make HTTP requests: an MCP client (Streamable HTTP), or plain `curl` / `fetch`
- No authentication, no API key, no write access.

## mcll-real-estate

Published [MCLL](https://mcllrealestate.com) listings, residences for sale and rent across Bangkok, Phuket, and the islands. Listings are reachable through an MCP server, a REST API, and Markdown. Areas, news, and developments are reachable as Markdown only.

### Listings

- **MCP server**: `https://mcllrealestate.com/api/mcp` (Streamable HTTP). Tools: `search_listings`, `get_listing`, and `execute`.
- **[Code Mode](https://developers.cloudflare.com/agents/model-context-protocol/codemode/)**: `execute` runs JavaScript in a Cloudflare sandbox with `mcll.search(args)` and `mcll.get(args)`. Use it to compare, rank, or compute across listings in one call.
- **REST API**: `GET /api/listings` and `GET /api/listings/{type}/{slug}`, described by [`/api/openapi.json`](https://mcllrealestate.com/api/openapi.json).
- **Markdown**: send `Accept: text/markdown` to a listing detail URL, or append `.md` to its path, for a clean text rendition.

Use a current URL returned by search or listed in the [sitemap](https://mcllrealestate.com/sitemap.xml)
when requesting listing Markdown; listings can be unpublished.

### Areas, news, developments

No MCP tool or REST endpoint covers these. Send `Accept: text/markdown` to an individual area, news or development page, or append `.md` to its URL path. Find current URLs through the [sitemap](https://mcllrealestate.com/sitemap.xml). With the header, index and static pages stay HTML; unsupported `.md` URLs return 404.

Discovery: [agent catalogue](https://mcllrealestate.com/.well-known/ard.json) · [sitemap](https://mcllrealestate.com/sitemap.xml) · [API catalog](https://mcllrealestate.com/.well-known/api-catalog) · [MCP server card](https://mcllrealestate.com/.well-known/mcp/server-card.json) · [site guide](https://mcllrealestate.com/llms-full.txt).

## Quality

- Follows the [agentskills.io](https://agentskills.io) open standard.
- CI validates SKILL.md frontmatter, marketplace parity, and eval metadata.
- [`cisco-ai-defense/skill-scanner`](https://github.com/cisco-ai-defense/skill-scanner) scans pull requests and pushes with policy `balanced`, fail-on `critical`.
- The site advertises the skill through [`/.well-known/agent-skills/index.json`](https://mcllrealestate.com/.well-known/agent-skills/index.json). Maintainers synchronise and verify the pinned artifact using the [release procedure](.agents/rules/release.md).

## License

[MIT](LICENSE.md)
