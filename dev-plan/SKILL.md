---
name: dev-plan
description: Writes a detailed, zero-ambiguity implementation plan saved to docs/plans/ before any coding starts. Each task includes concrete code examples, file paths, and a testable "done when" criterion. Use when the user says "plan this", "write a plan", "how do we implement", "let's plan this feature", "break this down", or wants a step-by-step guide before starting. Do NOT use when a plan already exists — use dev-do to execute it.
metadata:
  author: phbpx
  version: 1.0.0
  category: planning
---

# Plan

Produce a step-by-step implementation plan saved to disk. To be executed later with `/do`.

## When to use
- Before implementing any feature that touches more than 2 files
- When the implementation path isn't obvious
- When you want to validate approach before writing code

## Process

### Step 1 — Explore
Read the relevant code. Use parallel agents if the codebase is large. Understand:
- Existing patterns to follow (don't invent new ones if a pattern exists)
- Interfaces or types that will be affected
- Test infrastructure already in place
- Entry points where new code will hook in

Do NOT start writing the plan until exploration is complete.

### Step 2 — Write the plan
Save to `docs/plans/YYYY-MM-DD-<feature>.md`:

```markdown
# [Feature Title]
Date: YYYY-MM-DD
Status: draft

## Goal
[One paragraph: what this implements and why]

## Approach
[Key design decisions — what pattern to follow, what to avoid]

## Tasks

### Task 1 — [Title]
**Files:** `path/to/file`
**Agent:** (optional — leave blank if simple)
**Description:** [What to implement]
**Example:**
\`\`\`
// concrete code example using real types from the codebase
\`\`\`
**Done when:** [specific, testable criterion]

### Task 2 — [Title]
...

## Out of scope
[Explicitly list what this plan does NOT cover]

## Test strategy
[What kinds of tests will be written — unit, integration, etc.]
```

### Step 3 — Complexity estimate
For each task, add one of: `[small]` `[medium]` `[large]`
- Small: 1 file, <50 lines, well-understood
- Medium: 2–4 files, some design decisions
- Large: many files, significant refactoring, or unknown territory

### Step 4 — Review gate
Present the plan to the user. Ask:
1. Does this match your intent?
2. Is anything missing or out of scope?
3. Should any tasks be reordered?

**Do not start implementing until the user approves.** Update the plan status to `approved` after approval.

## Plan quality rules

- All method and function documentation (comments, docstrings) in code examples must be written in **English**
- Every task must have a "done when" criterion — no vague tasks
- Code examples must be concrete (real types, real function names from the codebase)
- If a task depends on another, state it explicitly: "Requires Task 2 to be complete"
- If a task is risky or uncertain, flag it: `[risk: ...]`
- No task should be "refactor X" without specifying what changes and why

## Anti-patterns to refuse

- Writing the plan from memory without reading the code
- Tasks so vague they could mean anything ("update the service")
- Missing test tasks (implementation tasks must have corresponding test tasks)
- Starting to implement while writing the plan
