# Taruvi Agent Skills

Four coordinated [Agent Skills](https://agentskills.io/) for building full-stack apps on the [Taruvi](https://taruvi.cloud) platform. One orchestrator, three specialists — covering MCP provisioning, serverless function bodies, and Refine.dev frontends.

> **Quick start:** `npx skills add Taruvi-ai/skills`

## Why these skills?

Agents working on Taruvi apps rediscover the same conventions every session — MCP tool signatures, Frictionless schema quirks, `@taruvi/refine-providers` meta vocabulary, the `def main(params, user_data, sdk_client)` runtime contract, Cerbos resource naming. Without codified procedural knowledge, each session burns context relearning the platform and produces inconsistent code.

These skills encode that knowledge once, validated against the actual source in the platform, SDK, and provider repos. They're portable across **30+ skills-compatible agents** (Claude Code, Cursor, GitHub Copilot, Gemini CLI, OpenAI Codex, OpenCode, Windsurf, Goose, and more) and use only the portable [Agent Skills spec](https://agentskills.io/specification) frontmatter — no platform lock-in.

## The four skills

| Skill | Triggers on | What it covers |
|---|---|---|
| [`taruvi-app-builder`](./taruvi-app-builder/) | "new Taruvi app", "end-to-end feature", "scaffold", "full-stack" | Orchestrates cross-layer feature work, routes to specialists, AGENTS.md template, deployment |
| [`taruvi-backend-provisioning`](./taruvi-backend-provisioning/) | "create datatable", "Cerbos policy", "provision", "Frictionless schema", "MCP" | All 24 MCP tools, Frictionless schemas, Cerbos policies, secrets, storage, raw SQL |
| [`taruvi-functions`](./taruvi-functions/) | "Taruvi function body", `def main`, `sdk_client`, "CEL filter", "event-driven" | Python SDK inside function runtimes, immutable auth, `log()`, 10 modules, scenarios |
| [`taruvi-refine-frontend`](./taruvi-refine-frontend/) | "Refine page", "useList Taruvi", "dataProvider", "@taruvi/refine-providers", "useCan" | All 6 providers, 24 filter operators, meta options, OAuth flow, Cerbos accessControl |

Specialists trigger independently on domain-specific prompts. The orchestrator kicks in when a request spans layers.

## Installation

```bash
# Install all four (project scope — committed with your repo)
npx skills add Taruvi-ai/skills

# Global (available across every project on your machine)
npx skills add -g Taruvi-ai/skills

# Just one
npx skills add Taruvi-ai/skills --skill taruvi-refine-frontend

# Target a specific agent only
npx skills add Taruvi-ai/skills -a claude-code
npx skills add Taruvi-ai/skills -a cursor -a codex
```

The [Vercel Labs `skills` CLI](https://github.com/vercel-labs/skills) detects which coding agents you have installed and places symlinks in each one's conventional skill directory (`.claude/skills/`, `.agents/skills/`, etc.). Run `npx skills update` later to pull the latest versions.

## What each Taruvi app needs

Every consuming Taruvi app should have an `AGENTS.md` at its repo root that announces Taruvi is in use and points agents at these skills. A template lives at [taruvi-app-builder/references/agents-md-template.md](./taruvi-app-builder/references/agents-md-template.md).

## Repository layout

```
./
├── README.md
├── LICENSE
├── taruvi-app-builder/
│   ├── SKILL.md
│   ├── evals/eval_queries.json
│   └── references/              # architecture, workflows, deployment, AGENTS template
├── taruvi-backend-provisioning/
│   ├── SKILL.md
│   ├── evals/eval_queries.json
│   ├── references/              # MCP quickref, schemas, Cerbos, secrets, raw SQL, analytics
│   └── scripts/check-versions.sh
├── taruvi-functions/
│   ├── SKILL.md
│   ├── evals/eval_queries.json
│   └── references/              # SDK reference, auth patterns, scenarios, templates, CEL filters
├── taruvi-refine-frontend/
│   ├── SKILL.md
│   ├── evals/eval_queries.json
│   ├── references/              # providers, operators, meta, auth, types & utilities
│   └── scripts/validate-resource-map.js
└── shared/                      # taruvi-overview, sdk-version-matrix
```

## Eval datasets

Each skill ships `evals/eval_queries.json` with 16 labeled prompts (should-trigger / should-not-trigger). These are the seed data for description-optimization runs per Anthropic's skill-creator methodology. Browse them to see what each skill is scoped to handle.

## Validation

To lint frontmatter against the Agent Skills spec:

```bash
pip install agentskills
for s in taruvi-app-builder taruvi-backend-provisioning taruvi-functions taruvi-refine-frontend; do
  skills-ref validate "$s"
done
```

Check SDK/package version drift:

```bash
bash taruvi-backend-provisioning/scripts/check-versions.sh
```

## Version compatibility

This release targets:

| Package | Version |
|---|---|
| `@taruvi/sdk` | 1.4.7 |
| `@taruvi/refine-providers` | 1.3.0 |
| `taruvi` (Python SDK) | 0.1.9 |
| `@refinedev/core` | ^5.0.0 |
| Taruvi MCP server | 24 tools |

See [shared/sdk-version-matrix.md](./shared/sdk-version-matrix.md) for details.

## Contributing

When the MCP tools, SDK, or provider surface changes in their respective repos, open a sibling PR here with the matching reference file update. Skill content must stay in lockstep with platform behavior.

When adding a new skill:

1. Create `<new-skill>/SKILL.md` with portable frontmatter only (`name`, `description`, `license`, `compatibility`, `metadata`).
2. Keep the body under ~500 lines; push detail into `references/`.
3. Include `evals/eval_queries.json` with at least 8 should-trigger + 8 should-not-trigger prompts.
4. Add a row to the table above.
5. Cross-link from related skills where relevant.

## Related repos

- [`@taruvi/refine-providers`](https://github.com/Taruvi-ai/refine-providers) — Refine.dev data providers
- [`@taruvi/sdk`](https://github.com/Taruvi-ai/js-sdk) — JavaScript/TypeScript SDK
- [`taruvi`](https://pypi.org/project/taruvi/) — Python SDK
- Platform / MCP server — internal

## License

Apache-2.0. See [LICENSE](./LICENSE).
