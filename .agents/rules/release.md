# Release Rules

- Version lives in `.claude-plugin/marketplace.json` `metadata.version`, the `X.Y.Z` git tag, and the GitHub release. Keep them identical.
- Every `SKILL.md` edit changes its digest. Before release, compute `shasum -a 256 skills/mcll-real-estate/SKILL.md`.
- Update `mcll/www` in the same rollout so `/.well-known/agent-skills/index.json` advertises the exact digest before marketplace publication or announcement.
- Verify the public discovery index after the `www` auto-deploy reaches production.
