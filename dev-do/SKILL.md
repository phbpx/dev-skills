---
name: dev-do
description: Executes an approved implementation plan from docs/plans/ task by task, running tests after each task and reviewing before moving on. Use when the user says "execute the plan", "implement the plan", "let's do it", "start coding", "run the plan", or references a plan file to implement. Requires an approved plan — use dev-plan first if none exists.
metadata:
  author: phbpx
  version: 1.0.0
  category: planning
---

# Do

Execute a plan created by `/plan`. Implements tasks in order, reviewing after each batch.

## Input
Specify the plan file: `/do docs/plans/YYYY-MM-DD-feature.md`

If no file is specified, look for the most recently modified plan in `docs/plans/` with status `approved`.

## Process

### Step 1 — Load and validate
Read the plan file. Confirm:
- Status is `approved` (not `draft`)
- All tasks have "done when" criteria
- No obvious blockers (missing dependencies, unclear requirements)

If the plan is incomplete or still a draft, stop and ask the user to review it first.

### Step 2 — Confirm execution mode
Ask the user:
```
Found plan: [title] — [N] tasks

Execution mode:
  A) Task by task — confirm before each task
  B) Batch — run all tasks, review at the end

Which? (A/B)
```

Wait for the answer.

### Step 3 — Execute

**Mode A (task by task):**
For each task:
1. State what you're about to do: "Starting Task N: [title]"
2. Implement using TDD where applicable (write test first)
3. Run tests: confirm they pass
4. Show a brief summary of what was done
5. Ask: "Continue to Task N+1?" — wait for confirmation

**Mode B (batch):**
Run all tasks in sequence. After all tasks:
1. Run the full test suite
2. Do a self-review (same 4 dimensions as `/dev-review`)
3. Present a summary with any issues found

### Step 4 — Per-task verification
After each task, verify the "done when" criterion from the plan:
- If it specifies a test: run it and confirm it passes
- If it specifies behavior: demonstrate it (output, test, or log)
- If verification fails: fix before moving to the next task

### Step 5 — Post-completion
After all tasks are done:
1. Run the full test suite one final time
2. Update the plan status to `completed`
3. Suggest next steps: `/dev-review`, `/dev-commit`, or follow-up work

## Rules

- Never skip a task without flagging it to the user
- If a task reveals a problem with the plan, pause and discuss — don't improvise a redesign
- If tests fail after a task, fix before continuing (no "I'll fix it at the end")
- Dispatch sub-agents for large tasks (>3 files affected)

## Anti-patterns to refuse

- Running without an approved plan
- Skipping test runs between tasks ("I'll test everything at the end")
- Proceeding past a broken state without user acknowledgment
- Silently deviating from the plan when it gets inconvenient
