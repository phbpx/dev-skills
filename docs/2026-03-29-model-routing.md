# Task-aware Model Routing for dev-skills
Date: 2026-03-29

## Problem
The `/dev-do` skill uses the same model for all tasks regardless of complexity. Simple tasks (rename, config change) consume the same tokens as complex refactors, wasting budget without quality benefit.

## Decision
Hybrid approach: `/dev-plan` assigns a default model for the plan plus per-task overrides where complexity diverges. `/dev-do` reads these annotations and routes each task to the appropriate model. Implemented in two phases — scoring first, automatic fallback later.

## Design

### Phase 1: Scoring + Routing

**dev-plan changes:**
- After tagging tasks with `[small]`/`[medium]`/`[large]`, map to models:
  - `[small]` → haiku
  - `[medium]` → sonnet
  - `[large]` → opus
- Compute plan-level default: the model that covers the majority of tasks
- Only annotate tasks that differ from the default with `[model: <x>]`
- Add a `Model:` header to the plan metadata with the default and estimated savings

**Plan output example:**
```markdown
Status: draft
Model: sonnet

### Task 1 — Add config struct [small]
Files: internal/config/config.go
...

### Task 2 — Refactor auth middleware [large] [model: opus]
Files: internal/auth/middleware.go, internal/auth/session.go, ...
...

### Task 3 — Update handler tests [small]
Files: internal/handler/handler_test.go
...
```

**dev-do changes:**
- Read `Model:` header from plan → set as default model for sub-agents
- For tasks with `[model: <x>]` override → use that model for the sub-agent
- Pass model via the `model` parameter when spawning agents
- Log which model was used per task in the execution summary

### Phase 2: Automatic Fallback (future)

- If a task fails tests after 2 attempts, escalate to next model tier (haiku→sonnet→opus)
- Log escalations so the user can recalibrate scoring criteria
- Add a `--strict` flag to disable fallback (fail instead of escalate)

### Scoring criteria

The complexity tag drives model selection. Criteria for tagging (already in dev-plan, refined here):

| Tag | Criteria | Model |
|-----|----------|-------|
| `[small]` | 1 file, <50 lines, mechanical change, no design decisions | haiku |
| `[medium]` | 2-4 files, some logic, well-understood patterns | sonnet |
| `[large]` | 5+ files, significant refactoring, new abstractions, risk flags | opus |

Additional signals that escalate a task:
- `[risk: ...]` flag present → at least sonnet
- Task touches public API or shared interfaces → at least sonnet
- Task requires understanding complex business logic → opus

## Out of scope
- Automatic fallback (Phase 2 — after validating scoring quality)
- Token usage tracking/reporting
- Model routing for skills other than dev-do (dev-review, dev-debug, etc.)
- Cost estimation or budget caps

## Open questions
- Should the user be able to override the model for the entire plan at approval time? (e.g., "use opus for everything" as a safety blanket)
- What's the right threshold for Phase 2 fallback — 2 failed attempts or 1?
