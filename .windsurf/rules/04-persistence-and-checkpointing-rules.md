---
trigger: always_on
---

# Persistence and Checkpointing Rules (Dev-Only)

Timestamp: 2025-08-14T16:35:00-04:00

## Version history

- v0.1.1 — 2025-08-14T16:35:00-04:00 — Add Scope/Validation/Rollback/Change Policy, neutralize refs, Lotus Wisdom, lint fixes
- v0.1.0 — 2025-08-14T11:22:57-04:00 — Initial dev-only rules

## Scope

- Dev-only guidance on optional persistence for continuity and replay.
- Keep persistence lightweight, opt-in, and safe to disable.

## Guidance

- Use lightweight local persistence for dev-only continuity (e.g., SQLite-based checkpointer if configured).
- Keep persistence optional; the graph must still run without it in dev.
- Store only what the graph needs to resume/replay safely; avoid PII.
- Provide a toggle/flag to disable quickly if behavior regresses.

## Mermaid (dev persistence)

```mermaid
flowchart LR
  Run[Graph run] --> CP[Checkpoint write]
  CP --> Store[(Local store)]
  Store --> Resume[Resume/Replay]
```

## References (components)

- `backend/src/agent/graph.py` — optional checkpointer wiring points in graph compilation.
- `backend/src/agent/configuration.py` — env-driven switches for local persistence in dev (if enabled).

## Validation Checklist (Dev)

- Graph runs normally with persistence disabled.
- When enabled, checkpoints capture only necessary state; no PII.
- Clear env/config switch controls persistence on/off.
- Resume/replay path functions end-to-end without manual DB inspection.

## Rollback / Disable (Dev)

- Disable persistence via env/config and clear local store if needed.
- Fall back to in-memory runs to isolate issues.
- Revert to last known-good checkpointer configuration.

## Change Policy (Future-Proofing)

- Keep schemas minimal; prefer additive fields.
- Treat persistence as optional; code must not require it in dev.
- Document any migration steps if structure changes.

## Changelog Summary

- v0.1.1 — Added Scope, Validation, Rollback/Disable, Change Policy, neutralized references, lint fixes.

## Open Questions

- Minimal necessary fields to support reliable resume/replay?

## Lotus Wisdom Alignment

- **Upaya (Skillful Means)**: Optional, lightweight persistence to aid iteration.
- **Direct**: Clear on/off toggle and validation for resume path.
- **Gradual**: Evolve schemas additively; migrations only when necessary.
- **Embody**: Easy rollback by disabling and clearing local store.
