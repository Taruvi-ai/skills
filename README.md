# Taruvi Agent Skills

A set of four coordinated [Agent Skills](https://agentskills.io/) that package procedural knowledge for building on the [Taruvi](https://taruvi.cloud) platform. Portable across Claude Code, Cursor, GitHub Copilot, Gemini CLI, OpenAI Codex, OpenCode, and 25+ other skills-compatible agents.

## The four skills

| Skill | Use for |
|---|---|
| [`taruvi-app-builder`](./taruvi-app-builder/) | End-to-end feature work, greenfield app scaffolds, routing to specialists |
| [`taruvi-backend-provisioning`](./taruvi-backend-provisioning/) | Calling Taruvi MCP tools to provision datatables, policies, roles, functions, secrets, buckets |
| [`taruvi-functions`](./taruvi-functions/) | Writing Python function bodies that run inside Taruvi's serverless runtime |
| [`taruvi-refine-frontend`](./taruvi-refine-frontend/) | Building Refine.dev admin UIs with `@taruvi/refine-providers` |

The **app-builder** is the orchestrator. The other three are specialists. Each triggers independently on domain-specific prompts; the orchestrator routes cross-layer work.

## Installation

### Via `npx skills` (recommended)

Install all four skills across whichever agent you use:

```bash
# All four
npx skills add Taruvi-ai/skills

# One at a time
npx skills add Taruvi-ai/skills --skill taruvi-refine-frontend
```

The Vercel Labs [`skills` CLI](https://github.com/vercel-labs/skills) handles symlink installation into each agent's convention (`.claude/skills/`, `.agents/skills/`, etc.) and detects which agents you have installed automatically.

Project-scope installs (committed with your repo so the whole team gets them):

```bash
cd path/to/your-taruvi-app
npx skills add Taruvi-ai/skills
```

Global installs (available across all your projects):

```bash
npx skills add -g Taruvi-ai/skills
```

### Manual install (no CLI)

Clone this repo and symlink into your agent's skill directory:

```bash
git clone https://github.com/Taruvi-ai/skills.git ~/taruvi-skills

# Claude Code (user-level, all skills)
for skill in taruvi-app-builder taruvi-backend-provisioning taruvi-functions taruvi-refine-frontend; do
  ln -s "$HOME/taruvi-skills/$skill" "$HOME/.claude/skills/$skill"
done

# Portable .agents/skills/ (works across Cursor, Copilot, Codex, and others)
mkdir -p .agents/skills
for skill in taruvi-app-builder taruvi-backend-provisioning taruvi-functions taruvi-refine-frontend; do
  ln -s "$HOME/taruvi-skills/$skill" ".agents/skills/$skill"
done
```

## Consuming Taruvi apps

Each Taruvi app should have an `AGENTS.md` at its repo root that announces Taruvi is in use and points agents at these skills. See [taruvi-app-builder/references/agents-md-template.md](./taruvi-app-builder/references/agents-md-template.md) for the template.

## What's here

```
./
├── README.md                              # this file
├── LICENSE
├── taruvi-app-builder/
│   ├── SKILL.md
│   ├── evals/eval_queries.json            # trigger-optimization dataset
│   └── references/
│       ├── architecture-overview.md
│       ├── feature-workflow-examples.md
│       ├── agents-md-template.md
│       ├── env-setup.md
│       ├── integration-pitfalls.md
│       └── deployment.md                  # Frontend Worker deploy
├── taruvi-backend-provisioning/
│   ├── SKILL.md
│   ├── evals/eval_queries.json
│   ├── references/
│   │   ├── mcp-tool-quickref.md
│   │   ├── datatable-schema-patterns.md
│   │   ├── cerbos-policy-cookbook.md
│   │   ├── secrets-and-types.md
│   │   ├── raw-sql-safety.md
│   │   └── analytics-queries.md
│   └── scripts/
│       └── check-versions.sh
├── taruvi-functions/
│   ├── SKILL.md
│   ├── evals/eval_queries.json
│   └── references/
│       ├── sdk-module-reference.md
│       ├── auth-patterns.md
│       ├── function-templates.md
│       ├── scenarios.md                   # right-vs-wrong patterns
│       └── event-filters.md               # CEL filter expressions
├── taruvi-refine-frontend/
│   ├── SKILL.md
│   ├── evals/eval_queries.json
│   ├── references/
│   │   ├── data-provider.md
│   │   ├── filter-operators.md
│   │   ├── meta-options-cookbook.md
│   │   ├── auth-access-control.md
│   │   ├── provider-quickref.md
│   │   ├── sdk-primer.md
│   │   └── types-and-utilities.md
│   └── scripts/
│       └── validate-resource-map.js
└── shared/
    ├── taruvi-overview.md
    └── sdk-version-matrix.md
```

## Eval datasets

Each skill ships `evals/eval_queries.json` with 16 labeled queries (should-trigger / should-not-trigger). These are the seed data for description-optimization runs — the trigger-tuning loop from Anthropic's skill-creator methodology. For now they also serve as examples of what each skill is scoped to handle.

## Portability

All four skills use only the portable Agent Skills frontmatter (`name`, `description`, `license`, `compatibility`, `metadata`). No Claude Code-only fields. They work in any skills-compatible agent.

Vendor-specific extensions go under `metadata.taruvi.*`.

## Validation

To lint frontmatter against the Agent Skills spec:

```bash
pip install agentskills
skills-ref validate taruvi-app-builder
skills-ref validate taruvi-backend-provisioning
skills-ref validate taruvi-functions
skills-ref validate taruvi-refine-frontend
```

To check SDK/package version drift:

```bash
bash taruvi-backend-provisioning/scripts/check-versions.sh
```

## Contributing

When modifying a Taruvi MCP tool, provider method, or SDK surface in the platform / SDK repos:

1. Update the code in the appropriate repo.
2. Open a PR here with the matching reference file update.
3. If you hit a new gotcha that bit you, add it to the "Gotchas" section of the relevant skill.
4. Run `check-versions.sh` if dependencies changed.

When adding a new skill:

1. Create `<new-skill>/SKILL.md` with portable frontmatter only.
2. Keep the body under ~500 lines; push detail to `references/`.
3. Include an `evals/eval_queries.json` with at least 8 should-trigger + 8 should-not-trigger prompts.
4. Add a row to the table in this README.
5. Cross-link from related skills where it makes sense.

## Version compatibility

This skill set targets (as of this release):

- `@taruvi/sdk` — 1.4.7
- `@taruvi/refine-providers` — 1.3.0
- `taruvi` (Python SDK) — 0.1.9
- `@refinedev/core` — ^5.0.0
- Taruvi platform MCP server — 24 tools

See [shared/sdk-version-matrix.md](./shared/sdk-version-matrix.md) for details and the drift-detection script.

## Related repos

- Taruvi platform (MCP server lives here): private — `core/mcp_integration/` in the main Taruvi repo
- [`@taruvi/refine-providers`](https://github.com/Taruvi-ai/refine-providers) — the 6 Refine data providers
- [`@taruvi/sdk`](https://github.com/Taruvi-ai/js-sdk) — JavaScript/TypeScript SDK
- [`taruvi`](https://pypi.org/project/taruvi/) — Python SDK

## License

Apache-2.0. See [LICENSE](./LICENSE).
