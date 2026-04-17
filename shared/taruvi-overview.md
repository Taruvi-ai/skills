# Taruvi platform overview

Shared reference for all four Taruvi skills. Loaded on demand when a skill needs to establish baseline context about the platform.

## What Taruvi is

Taruvi is a Django-based multi-tenant Backend-as-a-Service (BaaS) platform. Tenants are isolated via PostgreSQL schemas (`django-tenants`), so every backend resource (tables, storage, secrets, roles) exists inside a tenant boundary.

Core surfaces:

- **Datatables** — dynamic PostgreSQL tables defined via Frictionless Data Package schemas. Support CRUD, graph/hierarchy edges, aggregation, populate (FK joins), search.
- **Storage** — S3-compatible object buckets with quotas, visibility (public/private), and per-bucket policies.
- **Functions** — serverless handlers (Python or proxy/system modes) executed on Celery workers. Invoked sync or async.
- **Auth** — JWT + OAuth via `django-allauth`, with a `simplejwt` token flow.
- **Users & roles** — tenant-scoped users with app-level `UserRoleMembership`. Roles can nest via parent/child.
- **Policies** — authorization via Cerbos policies (resource/action/principal model) plus `django-guardian` for object-level perms.
- **Secrets** — typed, encrypted key-value store with 2-tier inheritance (app → tenant → global).
- **Analytics queries** — named SQL/Jinja2 queries registered as first-class resources, executed against internal or external connections.
- **Tags** — shared taxonomy used across resources.

## How agents interact with Taruvi

Three layers:

1. **MCP server** at `core/mcp_integration/` — 24 tools agents call to provision backend resources.
2. **Python SDK** (`taruvi` on PyPI, v0.1.9) — used inside function bodies and for server-side scripts. Auto-detects runtime via `TARUVI_FUNCTION_RUNTIME=true`.
3. **Frontend**: `@taruvi/sdk` (JS/TS, v1.4.7) and `@taruvi/refine-providers` (v1.3.0) — Every Taruvi-built app uses Refine.dev for the admin UI.

## Tenancy model

- Every request is tenant-resolved via subdomain or header.
- Middleware order matters: `HealthCheckBypassMiddleware` → `TenantMainMiddleware` → security/auth.
- Background tasks (Celery) **must** enter tenant context explicitly via `with schema_context(tenant_slug):`.
- `public` schema holds `SHARED_APPS` (core, django_tenants, admin). `TENANT_APPS` (`cloud_site`, business data) live per-tenant.

## Typical app layout (consumer side)

A Taruvi-built app is:

- A **Refine.dev** React frontend that consumes Taruvi via `@taruvi/refine-providers`.
- Optionally, **Taruvi functions** (Python) that run server-side for webhooks, scheduled tasks, or compute-heavy processing.
- All backend resources (tables, policies, buckets, secrets) defined via the Taruvi MCP server at build time.

## The four skills in this repo

- `taruvi-app-builder` — entry point. Orchestrates end-to-end features across the three layers.
- `taruvi-backend-provisioning` — agents use this when calling MCP tools to create/modify backend resources.
- `taruvi-functions` — agents use this when writing Python code that runs inside a Taruvi function.
- `taruvi-refine-frontend` — agents use this when building Refine pages that consume Taruvi.
