# SDK & platform version matrix

Pinned versions the skills were authored against. When you encounter API minutiae that don't match what's on disk, the package is likely newer than this cache — verify against the source or the package registry.

| Package | Version | Source |
|---|---|---|
| `taruvi` (Python SDK) | 0.1.9 | https://pypi.org/project/taruvi/ |
| `@taruvi/sdk` (JS SDK) | 1.4.7 | https://www.npmjs.com/package/@taruvi/sdk |
| `@taruvi/refine-providers` | 1.3.0 | https://www.npmjs.com/package/@taruvi/refine-providers |
| Taruvi MCP server | in platform repo | `core/mcp_integration/` |
| Django | 5.2.6 | platform `requirements.txt` |
| `refine` peer | @refinedev/core ≥ 4 | consumer's choice |

Cached: 2026-04-17.

## Drift detection

Each skill that hard-refers to SDK signatures ships a `scripts/check-versions.sh` that runs `npm view` / `pip show` and prints a diff against the pinned versions. Run it before trusting any API specifics in a skill body.

## When API changes

- **Minor version bump** (e.g., refine-providers 1.3 → 1.4): usually additive. Skill content stays correct; check the `gotchas.md` in each skill for new known issues.
- **Major version bump** (e.g., 1.x → 2.x): treat the skill as potentially stale. Verify against the source before acting.
- **Platform MCP tool changes**: because the MCP server is co-located in this repo, any MCP change should ship alongside the corresponding reference file update in `taruvi-backend-provisioning/references/`.
