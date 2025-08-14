---
trigger: always_on
---

# Configuration and Environment Rules (Dev-Only)

Timestamp: 2025-08-14T15:52:17-04:00

## Version history

- v0.1.1 — 2025-08-14T15:52:17-04:00 — Add standardized sections and lint fixes
- v0.1.0 — 2025-08-14T11:22:57-04:00 — Initial dev-only rules

## References (components)

- `backend/src/agent/configuration.py` — runtime configuration schema; `Configuration.from_runnable_config()` merges overrides.
- `backend/src/agent/app.py` — dev HTTP server that serves the static UI under `/app`.
- `frontend/vite.config.ts` — build base; align with server route (typically `/app/`).
- `frontend/src/App.tsx` — reads API base (default dev base or `VITE_API_URL`).
- `.env.example` — documents dev env keys (no secrets committed).

## Env keys (dev)

- Required: `GEMINI_API_KEY` (backend).
- Optional: `LANGSMITH_API_KEY` (telemetry) — off by default in dev.
- Frontend: `VITE_API_URL` (optional override for API base in dev).

## Configuration flow (high-level)

```mermaid
flowchart LR
  Env[.env / process env] --> RC[Configuration.from_runnable_config]
  RC --> Nodes[Graph nodes]
  UI[Frontend (Vite env)] --> Stream[useStream options]
```

## Rules

- Prefer environment-driven overrides via `from_runnable_config()`.
- Keep base path consistent: backend serves `/app`; align Vite base accordingly.
- Don’t hardcode secrets in code or docs.
- Document any added env keys in `.env.example` (dev only).

## Validation Checklist (Dev)

- Backend loads `GEMINI_API_KEY`; fail fast if missing in dev.
- `LANGSMITH_API_KEY` is optional and defaults to off in dev.
- `VITE_API_URL` override works; when unset, UI defaults to the dev API base.
- Base path alignment: UI served under `/app` in dev; build base matches.
- Nodes obtain settings via runtime configuration; no direct env reads inside nodes.
- `.env.example` lists all dev keys and defaults (no secrets).

## Rollback / Disable (Dev)

- Remove env overrides to return to configuration defaults.
- Unset `VITE_API_URL` to use the default dev API base.
- Remove `LANGSMITH_API_KEY` to ensure telemetry stays off.

## Change Policy (Future-Proofing)

- Additive env keys only; prefer UPPERCASE names aligned to configuration fields.
- Every new key must be documented in `.env.example` and this doc.
- Do not include secret values in docs or repository; dev-only guidance.
- Document and enforce precedence clearly (env/configurable/defaults); avoid ambiguous overrides.

## Changelog Summary

- v0.1.1 — Added Validation, Rollback/Disable, Change Policy; normalized spacing.

## Open Questions

- Standardize checkpointer path env name for future SQLite integration?
- Per-node model toggles: global feature flag vs per-node flags?
- Confirm documented precedence ordering (env > configurable > defaults) matches code.

## Lotus Wisdom Alignment (concise)

- Upaya: keep configuration centralized; use env for reversible overrides.
- Direct: validate presence of required keys; fail fast in dev.
- Gradual: additive, documented keys; evolve without breaking existing flows.
- Embody: rollback is configuration-only and non-destructive in dev.
