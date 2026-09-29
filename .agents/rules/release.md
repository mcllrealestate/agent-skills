# Release Rules

- Version lives in `.claude-plugin/marketplace.json` `metadata.version`, the `X.Y.Z` git tag, and the GitHub release. Keep them identical.
- Push the version tag before updating the public discovery index. Its skill URL must reference the tagged commit; its SHA-256 digest must match the exact `SKILL.md` bytes.
- Coordinate the index update with the site maintainer. Verify <https://mcllrealestate.com/.well-known/agent-skills/index.json> against the pinned artifact before publishing or announcing the release.
