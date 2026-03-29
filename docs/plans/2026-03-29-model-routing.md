# Task-aware Model Routing
Date: 2026-03-29
Status: completed

## Goal
Add model routing to the dev-plan → dev-do workflow so that each task in a plan is executed by the most cost-effective model for its complexity. `/dev-plan` annotates tasks with model recommendations based on complexity tags, and `/dev-do` reads those annotations when dispatching sub-agents.

## Approach
- Modify the two SKILL.md files (dev-plan, dev-do) — these are pure markdown instructions, no code
- Add a `Model:` metadata field to the plan template
- Extend the complexity tagging step in dev-plan to include model mapping
- Add model-aware agent dispatch instructions to dev-do
- Add escalation signals (risk flags, public API) as overrides
- Keep it backward-compatible: plans without `Model:` header work as before (use current model)

## Tasks

### Task 1 — Add model mapping table to dev-plan [small]
**Files:** `dev-plan/SKILL.md`
**Description:** In Step 3 (Complexity estimate), add the model mapping table after the existing complexity definitions. Map `[small]→haiku`, `[medium]→sonnet`, `[large]→opus`. Add escalation signals: `[risk]` flag → at least sonnet, public API/shared interface → at least sonnet, complex business logic → opus.
**Example:**
```markdown
### Step 3 — Complexity estimate and model assignment
For each task, add one of: `[small]` `[medium]` `[large]`
- Small: 1 file, <50 lines, well-understood → **haiku**
- Medium: 2–4 files, some design decisions → **sonnet**
- Large: many files, significant refactoring, or unknown territory → **opus**

Escalation signals (override the default mapping):
- Task has `[risk: ...]` flag → at least **sonnet**
- Task touches public API or shared interfaces → at least **sonnet**
- Task requires understanding complex business logic → **opus**
```
**Done when:** Step 3 in dev-plan/SKILL.md contains the model mapping table and escalation signals.

### Task 2 — Add Model header and per-task override to plan template [small]
**Files:** `dev-plan/SKILL.md`
**Description:** Update the plan template in Step 2 to include a `Model:` metadata field (the default model for the plan) and show `[model: <x>]` annotation on tasks that differ from the default. Add a new sub-step after Step 3: compute the plan-level default model (the model that covers the majority of tasks), set it as the `Model:` header, and only annotate tasks that diverge from the default.
**Example:**
```markdown
# [Feature Title]
Date: YYYY-MM-DD
Status: draft
Model: sonnet

...

### Task 1 — [Title] [small]
...

### Task 2 — [Title] [large] [model: opus]
...
```
**Done when:** The plan template in Step 2 includes the `Model:` field, and a new sub-step after Step 3 explains how to compute the default model and annotate divergent tasks.

### Task 3 — Add model-aware dispatch to dev-do [medium]
**Files:** `dev-do/SKILL.md`
**Description:** Update Step 1 (Load and validate) to read the `Model:` header from the plan as the default model. Update Step 3 (Execute) to: (1) for each task, check for `[model: <x>]` override, otherwise use the plan default, (2) pass the `model` parameter when dispatching sub-agents, (3) if no `Model:` header exists (backward compat), use the current model as-is. Update Step 5 (Post-completion) to include which model was used per task in the execution summary.
**Example:**
```markdown
### Step 1 — Load and validate
Read the plan file. Confirm:
- Status is `approved` (not `draft`)
- All tasks have "done when" criteria
- No obvious blockers (missing dependencies, unclear requirements)
- Read the `Model:` header if present — this is the default model for all tasks

...

### Step 3 — Execute

**Model routing:**
For each task, determine the model to use:
1. If the task has `[model: <x>]` annotation → use that model
2. Otherwise → use the plan's `Model:` header
3. If no `Model:` header exists → use the current model (backward compatible)

When dispatching sub-agents, pass the `model` parameter: `model: "<resolved model>"`
```
**Done when:** dev-do/SKILL.md Step 1 reads the Model header, Step 3 includes model routing logic, and Step 5 mentions model-per-task in the summary. Plans without a `Model:` header still work unchanged.

### Task 4 — Add execution summary format with model tracking [small]
**Files:** `dev-do/SKILL.md`
**Description:** Add an execution summary template to Step 5 that shows which model was used per task, enabling the user to evaluate if the routing was effective.
**Example:**
```markdown
### Execution summary
| Task | Status | Model | Notes |
|------|--------|-------|-------|
| Task 1 — Add config struct | done | haiku | — |
| Task 2 — Refactor auth | done | opus | — |
| Task 3 — Update tests | done | haiku | — |

Default model: sonnet | Overrides: 1 task(s)
```
**Done when:** Step 5 in dev-do/SKILL.md includes the execution summary table format with model column.

## Out of scope
- Automatic fallback on test failure (Phase 2 — future work)
- Model routing for other skills (dev-review, dev-debug, etc.)
- Token usage tracking or cost estimation
- Changes to dev-plan-issue (will inherit from dev-plan naturally)

## Test strategy
These are markdown instruction files, not code — no automated tests. Validation:
- After changes, read both SKILL.md files and verify the instructions are coherent and non-contradictory
- Verify backward compatibility: a plan without `Model:` header should still be valid per dev-do instructions
- Create a sample plan using the new template to confirm the format works
