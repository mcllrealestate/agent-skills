# Agent Skills

Public collection of installable AI agent skills for [MCLL Real Estate](https://mcllrealestate.com). Each skill is a self-contained folder in `skills/` with a `SKILL.md` at its root. The repo currently ships one skill, `mcll-real-estate`.

## At a glance

- **Standard** — [agentskills.io](https://agentskills.io) open standard (frontmatter, folder anatomy). A skill optimised for Claude Code declares `compatibility` and degrades gracefully on any open-standard agent.
- **Discovery digest** — the site's discovery index (`/.well-known/agent-skills/index.json`) publishes a sha256 of this SKILL.md. Editing `SKILL.md` changes that hash, so the published digest must be updated in step or agents reject the skill.
- **Authoring tool** — the official Anthropic `skill-creator` skill is mandatory for creating or editing a skill. We do not build our own.
- **Layout** — `skills/{name}/SKILL.md`, plus optional `references/`, `scripts/`, `assets/`. No per-skill `README.md`. The `name` frontmatter field must match the folder name.
- **Data** — every skill points at published, read-only data on `mcllrealestate.com`. No authentication, no API key, nothing writable. Prices are in THB; where no price is shown, write "Price upon enquiry".
- **Validation** — `.github/workflows/ci.yml` checks each SKILL.md frontmatter on every push and pull request. Run the same checks locally before reporting done.
- **Security** — `cisco-ai-defense/skill-scanner` scans the `skills/` tree on every push and pull request via `.github/workflows/scan-skills.yml` (policy `balanced`, fail-on `critical`). SHA-pinned to a tagged release; Dependabot opens a weekly PR for new versions.
- **Git** — branch `main`; no `CHANGELOG.md` (release notes live in the `gh release create` body only); the version lives only in git tags. Conventional Commits.

## Writing

Prose in this repo — README, SKILL.md bodies, release notes — uses the MCLL register: declarative, British English, no marketing adjectives, no rule-of-three lists, em-dashes rare, no emoji. State the fact plainly.
