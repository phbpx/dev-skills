---
name: dev-plan-issue
description: Reads a GitHub issue and produces a detailed implementation plan saved to docs/plans/. Use when the user says "plan issue #123", "create a plan for issue", "plan this issue", "implement issue #N", or provides a GitHub issue number or URL to plan. Requires gh CLI authenticated. Do NOT use for issues that need design discussion first — use dev-brainstorm instead.
metadata:
  author: phbpx
  version: 1.0.0
  category: planning
---

# dev-plan-issue

Read a GitHub issue and produce a step-by-step implementation plan, exactly as `/dev-plan` would — but using the issue as the source of requirements.

## Input

Accept any of:
- Issue number: `42`
- Full URL: `https://github.com/owner/repo/issues/42`
- With explicit repo: `owner/repo#42`

If no repo is specified, infer it from the current git remote:
```bash
gh repo view --json nameWithOwner -q .nameWithOwner
```

## Process

### Step 1 — Fetch the issue
```bash
gh issue view <number> --repo <owner/repo> \
  --json number,title,body,labels,comments,assignees,milestone
```

Extract:
- **Title** — the feature or bug name
- **Body** — requirements, acceptance criteria, context
- **Labels** — type hint (`bug`, `enhancement`, `feature`, etc.)
- **Comments** — look for technical decisions, clarifications, or constraints added after the issue was opened

If the issue is not found or the repo is inaccessible, stop and report the error clearly.

### Step 2 — Parse requirements

From the issue content, identify:
- **Goal**: what needs to be built or fixed (1 sentence)
- **Acceptance criteria**: explicit or implicit conditions for "done"
- **Constraints**: performance, compatibility, scope limits mentioned
- **Ambiguities**: anything unclear that would block planning

If there are critical ambiguities (the issue doesn't have enough information to plan), ask the user to clarify before continuing. Maximum 3 clarifying questions.

### Step 3 — Explore the codebase

Read the relevant code before writing the plan. Understand:
- Existing patterns to follow
- Interfaces or types that will be affected
- Test infrastructure already in place
- Entry points where new code will hook in

Do NOT start writing the plan until exploration is complete.

### Step 4 — Write the plan

Save to `docs/plans/YYYY-MM-DD-issue-<number>-<slug>.md` where `<slug>` is a short kebab-case version of the issue title:

```markdown
# <issue title>
Date: YYYY-MM-DD
Status: draft
Issue: <owner/repo>#<number>

## Goal
[One paragraph: what this implements and why — drawn from the issue]

## Acceptance criteria
[List from the issue — rewrite as testable conditions if needed]

## Approach
[Key design decisions — what pattern to follow, what to avoid]

## Tasks

### Task 1 — [Title]
**Files:** `path/to/file`
**Description:** [What to implement]
**Example:**
```
// concrete code example using real types from the codebase
```
**Done when:** [specific, testable criterion that maps to an AC]

### Task 2 — [Title]
...

## Out of scope
[Explicitly list what this plan does NOT cover — including other issues]

## Test strategy
[What kinds of tests — unit, integration, e2e]
```

### Step 5 — Review gate

Present a summary to the user:
```
Issue: #<number> — <title>
Acceptance criteria found: <N>
Tasks planned: <N>
Plan saved to: docs/plans/YYYY-MM-DD-issue-<number>-<slug>.md

Does this match the issue intent? Any tasks missing or out of scope?
```

**Wait for explicit approval before the plan status changes to `approved`.**
Once approved, suggest running `/dev-do docs/plans/...` to execute.

## Important

- All method and function documentation (comments, docstrings) in code examples must be written in **English**
- Every acceptance criterion from the issue must map to at least one task
- Every task must have a "done when" criterion
- If the issue has no clear acceptance criteria, derive them from the description and flag them as inferred
- Link back to the issue number in the plan header — traceability matters
- Do not gold-plate: only plan what the issue asks for

## Stop and ask if

- The issue is too vague to plan (no description, no acceptance criteria, no context)
- The issue references another issue or PR as a dependency that isn't resolved
- The issue scope is too large for a single plan (suggest splitting)
