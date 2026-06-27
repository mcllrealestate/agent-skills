<div align="center">

<img src="assets/mcll.png" width="440" alt="MCLL Real Estate"/>

<!-- omit in toc -->
# Agent Skills

**AI agent skills for [MCLL Real Estate](https://mcllrealestate.com) — luxury residences across Thailand, for sale and rent**

Frontmatter-validated and security-scanned on every push. Read-only, no API key.

[![license](https://img.shields.io/badge/license-MIT-1C1A16?style=flat-square)](LICENSE.md)
[![standard](https://img.shields.io/badge/agentskills.io-1C1A16?style=flat-square)](https://agentskills.io)
[![ci](https://img.shields.io/github/actions/workflow/status/mcllrealestate/agent-skills/ci.yml?branch=main&style=flat-square&label=ci&color=1C1A16)](https://github.com/mcllrealestate/agent-skills/actions/workflows/ci.yml)
[![scan](https://img.shields.io/github/actions/workflow/status/mcllrealestate/agent-skills/scan-skills.yml?branch=main&style=flat-square&label=scan&color=1C1A16)](https://github.com/mcllrealestate/agent-skills/actions/workflows/scan-skills.yml)
[![mcllrealestate.com](https://img.shields.io/badge/mcllrealestate.com-C9A96E?style=flat-square&logoColor=1C1A16&logo=data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCA2NCA2NCI+PHBhdGggZmlsbD0iIzFDMUExNiIgZD0iTTMwLjA2OCA1MC4yNCAxNi43MjQgMTMuNzZoNC41ODRsMTEuMDg4IDI5LjkyOEw0Mi44NiAxMy43NmguNjQ4bC0xMi43MiAzNi40OHpNMTcuMDEyIDEzLjc2djM1LjRoMy42MjR2LjZoLTcuNjh2LS42aDMuNDU2di0zNC44SDEyLjc0di0uNnptMzQuMjQ4IDB2LjZINDcuOXYzNC44aDMuMzZ2LjZIMzkuNDc2di0uNmgzLjg2NHYtMzUuNHoiLz48L3N2Zz4=)](https://mcllrealestate.com)

</div>

- [Install](#install)
- [Requirements](#requirements)
- [Skills](#skills)
- [mcll-real-estate](#mcll-real-estate)
- [Security](#security)
- [Standards](#standards)
- [License](#license)

---

## Install

Install via [skills.sh](https://skills.sh):

```bash
# All skills
npx skills add mcllrealestate/agent-skills

# Individual skill
npx skills add mcllrealestate/agent-skills --skill mcll-real-estate
```

Or read [`skills/mcll-real-estate/SKILL.md`](skills/mcll-real-estate/SKILL.md) directly. The skill is self-contained and optimised for Claude Code; it degrades gracefully on any agent that implements the [Agent Skills](https://agentskills.io) standard.

---

## Requirements

- An agent that can make HTTP requests: an MCP client (Streamable HTTP), or plain `curl` / `fetch`
- No authentication and no API key. The data is public and read-only.

---

## Skills

| Domain | Skill | Description |
| --- | --- | --- |
| Real Estate | [mcll-real-estate](#mcll-real-estate) | Search and retrieve MCLL listings via the MCP server, REST API, or Markdown |

---

## mcll-real-estate

Published [MCLL](https://mcllrealestate.com) listings, residences for sale and rent across Bangkok, Phuket, and the islands. Listings are reachable through an MCP server, a REST API, and Markdown. Areas, news, and developments are reachable as Markdown only.

### Listings

- **MCP server** — `https://mcllrealestate.com/api/mcp` (Streamable HTTP). Tools: `search_listings` (city, area, type, bedrooms, price) and `get_listing` (full detail). The richest path; it resolves human names ("condos in Phuket") to filters.
- **REST API** — `GET /api/listings` and `GET /api/listings/{type}/{slug}`, described by [`/api/openapi.json`](https://mcllrealestate.com/api/openapi.json).
- **Markdown** — send `Accept: text/markdown` to a listing detail URL for a clean text rendition.

```bash
# One listing, as Markdown:
curl -s -H "Accept: text/markdown" \
  https://mcllrealestate.com/en/buy/villa/phuket/kamala/kamala-cliff-villa
```

### Areas, news, developments

No MCP tool or REST endpoint covers these. Send `Accept: text/markdown` to an individual area, news, or development page and the site returns Markdown in place of HTML. Find the URLs through the [sitemap](https://mcllrealestate.com/sitemap.xml); index and static pages stay HTML.

Discovery: [sitemap](https://mcllrealestate.com/sitemap.xml) · [API catalog](https://mcllrealestate.com/.well-known/api-catalog) · [MCP server card](https://mcllrealestate.com/.well-known/mcp/server-card.json) · [site guide](https://mcllrealestate.com/llms-full.txt). Prices are in THB. Only published listings are returned.

---

## Security

Every push and pull request scans the `skills/` tree with [`cisco-ai-defense/skill-scanner`](https://github.com/cisco-ai-defense/skill-scanner) — policy `balanced`, fail-on `critical`. The reusable workflow is SHA-pinned to a tagged release; Dependabot opens a pull request when a new version lands.

---

## Standards

Follows the [agentskills.io](https://agentskills.io) open standard: canonical frontmatter (`name`, `description`, `license`, `metadata`) plus `when_to_use` and a `compatibility` line. CI validates the frontmatter on every push.

The site advertises this skill to visiting agents through its discovery index at [`/.well-known/agent-skills/index.json`](https://mcllrealestate.com/.well-known/agent-skills/index.json), per the Agent Skills Discovery RFC v0.2.0.

---

## License

[MIT](LICENSE.md)
