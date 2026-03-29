# Model Routing Follow-up
Date: 2026-03-29
Status: completed
Model: haiku

## Goal
Validate the newly merged task-aware model routing instructions in `dev-plan` and `dev-do`, then align outward-facing documentation so the repository describes the current behavior consistently.

## Approach
Start with a coherence pass across the affected skills and existing docs, focusing on whether the instructions are internally consistent and backward compatible for plans without a `Model:` header. If the behavior is sound, update the public documentation only where it materially helps users discover or apply the feature.

## Tasks

### Task 1 — Verify skill coherence [small]
**Files:** `dev-plan/SKILL.md`, `dev-do/SKILL.md`, `docs/2026-03-29-model-routing.md`
**Description:** Read the updated skill instructions against the design document and confirm that the model routing rules match: task-size mapping, plan-level default model, per-task override, and backward compatibility for older plans.
**Example:**
```markdown
Model: sonnet

### Task 1 — Update config parsing [small]
...

### Task 2 — Refactor shared auth flow [large] [model: opus]
...
```
**Done when:** The skill docs are confirmed coherent with the design doc, or any concrete mismatches are listed with exact file references.

### Task 2 — Check user-facing documentation gaps [small]
**Files:** `README.md`
**Description:** Review whether the README should mention task-aware model routing in the workflow or philosophy sections so users can discover the feature without reading the internal docs.
**Example:**
```markdown
Each skill works standalone, and planning can annotate tasks with model recommendations for later execution.
```
**Done when:** There is a clear decision to either keep the README unchanged or update it with a concise explanation of the feature.

### Task 3 — Apply documentation alignment [small]
**Files:** `README.md`
**Description:** If Task 2 finds a real gap, update the README minimally to describe the new planning/execution behavior without turning it into a changelog.
**Example:**
```markdown
`dev-plan` can assign a default model for a plan and per-task overrides. `dev-do` reads those annotations when executing the plan.
```
**Done when:** Public documentation reflects the current behavior, or no README change is made because the feature is judged internal-only.

## Out of scope
- Further changes to `dev-plan` or `dev-do` unless a real inconsistency is found
- Automatic fallback or retry escalation between models
- Model routing for skills other than `dev-do`
- Marketplace metadata or version bumping

## Test strategy
These are markdown instruction files, so validation is manual:
- Re-read `dev-plan/SKILL.md` and `dev-do/SKILL.md` after the coherence pass
- Validate at least one old-style plan mentally (without `Model:` header) and one new-style plan (with default plus override)
- If `README.md` changes, verify the wording matches the implemented behavior and does not overstate automation
