# Task-Aware Model Routing

This repository supports task-aware model routing in the `dev-plan` -> `dev-do` workflow.

The goal is simple: use a cheaper model for straightforward tasks and reserve stronger models for tasks that are larger, riskier, or require deeper reasoning.

## How it works

`dev-plan` classifies each task by complexity:

| Tag | Typical shape | Default model |
|---|---|---|
| `[small]` | 1 file, small mechanical change, low ambiguity | `haiku` |
| `[medium]` | 2-4 files, some logic, known patterns | `sonnet` |
| `[large]` | broader refactor, unknown territory, or significant risk | `opus` |

After assigning a model to each task, `dev-plan`:
- counts the assigned model for each task
- chooses the model covering the majority of tasks as the plan default
- writes that value into the plan's `Model:` header
- adds `[model: ...]` only to tasks that differ from the default

Additional escalation signals used by `dev-plan`:
- a task with `[risk: ...]` is at least `sonnet`
- a task touching public API or shared interfaces is at least `sonnet`
- a task requiring deeper business-logic understanding is `opus`

`dev-do` then reads those annotations and resolves the model automatically for each task during execution.

## Example

```markdown
# Add portfolio exports
Date: 2026-03-29
Status: approved
Model: haiku

### Task 1 — Add export config parsing [small]
**Files:** `internal/config/export.go`
...

### Task 2 — Implement CSV export service [medium] [model: sonnet]
**Files:** `internal/export/service.go`, `internal/export/csv.go`
...

### Task 3 — Refactor shared reporting pipeline [large] [model: opus]
**Files:** `internal/reporting/pipeline.go`, `internal/reporting/jobs.go`
...
```

Resolved execution:
- Task 1 -> `haiku`
- Task 2 -> `sonnet`
- Task 3 -> `opus`

## Resolution rules

When `dev-do` executes a plan:
1. If a task has `[model: <x>]`, use that model.
2. Otherwise, use the plan's `Model:` header.
3. If the plan has no `Model:` header, use the current session model.

That last rule keeps older plans backward compatible.

## Why this exists

Without routing, every task uses the same model even when the work varies widely in complexity. That can waste budget on simple changes or underpower more complex ones.

Task-aware routing keeps the workflow lightweight:
- simple tasks can stay cheap
- harder tasks can still use a stronger model
- old plans continue to work

## Scope and limits

This routing currently applies to the `dev-plan` -> `dev-do` workflow.

It does not currently add:
- automatic retry escalation between models
- cost tracking or budget caps
- model routing for other skills such as `dev-review` or `dev-debug`

For the original design notes, see [2026-03-29-model-routing.md](./2026-03-29-model-routing.md).
