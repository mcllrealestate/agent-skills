# Agent Skills

Installable skills for [MCLL Real Estate](https://mcllrealestate.com). The repo ships `skills/mcll-real-estate/SKILL.md`.

## Rule Index

- `.agents/rules/release.md` - version, release, and `www` discovery digest rules.

## Rules

- Use the official `skill-creator` skill for every skill edit.
- Keep the repo small: `README.md` for users, `AGENTS.md` for maintainer rules, `.agents/rules/` for durable release rules, `CLAUDE.md` as the pointer.
- Follow the [agentskills.io](https://agentskills.io) layout. `skills/{name}/SKILL.md` must have `name: {name}`.
- Ground claims in runtime truth: server card, OpenAPI, sitemap, and `llms-full.txt`.
- MCLL data access is public, read-only, unauthenticated, and priced in THB.
- `execute` is MCLL Code Mode: JavaScript over `mcll.search` and `mcll.get` only. No network, secrets, filesystem, or writes.
- Every skill belongs in `.claude-plugin/marketplace.json`.
- Run the local CI frontmatter check before reporting done. GitHub also runs `cisco-ai-defense/skill-scanner` with policy `balanced`, fail-on `critical`.
- Use Conventional Commits. Write README, SKILL.md, PRs, and releases in concise British English, with no marketing filler.
