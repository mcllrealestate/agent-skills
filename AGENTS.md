# MCLL Agent Skills

Installable skills for [MCLL Real Estate](https://mcllrealestate.com). The repo ships `skills/mcll-real-estate/SKILL.md`.

## Project constraints

- Use the official `skill-creator` skill for every skill edit.
- Keep the repo small: `README.md` for users, `AGENTS.md` for maintainer rules, `.agents/rules/` for durable release rules, `CLAUDE.md` as the pointer.
- Follow the [agentskills.io](https://agentskills.io) layout. `skills/{name}/SKILL.md` must have `name: {name}`.
- Ground claims in runtime truth: server card, OpenAPI, sitemap, and `llms-full.txt`.
- MCLL data access is public, read-only, unauthenticated, and priced in THB.
- `execute` is MCLL Code Mode: JavaScript over `mcll.search` and `mcll.get` only. No network, secrets, filesystem, or writes.
- Every skill belongs in `.claude-plugin/marketplace.json`.
- Use Conventional Commits. Write README, SKILL.md, PRs, and releases in concise British English, with no marketing filler.

## Rule index

- For a skill release or discovery change, read `.agents/rules/release.md` for version and published digest requirements.

## Validation

- For skill, manifest, or eval changes, run the affected frontmatter and metadata checks from `.github/workflows/ci.yml`. GitHub also runs `cisco-ai-defense/skill-scanner` with policy `balanced`, fail-on `critical`.
- For documentation changes, check changed Markdown, links, and source claims. Runtime checks are needed when a public service contract changes.
