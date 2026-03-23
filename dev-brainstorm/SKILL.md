---
name: dev-brainstorm
description: Validates ideas and explores design solutions through structured phases before writing any code. Produces a design document. Use when the user says "brainstorm", "let's think about this", "how should we design", "I have an idea", "before we start coding", "explore options", or wants to compare approaches before implementing. Do NOT use when implementation has already started — use dev-explore instead.
metadata:
  author: phbpx
  version: 1.0.0
  category: design
---

# Brainstorm

Work through a design problem before committing to implementation. Produces a design document as output.

## When to use
- Before starting any non-trivial feature
- When requirements feel unclear or incomplete
- When there are multiple valid approaches worth comparing
- When the change touches core abstractions

## Process

### Phase 1 — Understanding (max 3 questions)
Ask up to 3 questions to clarify the problem. Focus on:
- What problem does this solve for the user?
- What does success look like? (behavior, not implementation)
- Are there constraints to respect? (performance, compatibility, scope)

**Wait for answers before proceeding.**

### Phase 2 — Exploration
Research the codebase and context silently. Use parallel agents if the codebase is large. Understand:
- How related code is currently structured
- What patterns are already established
- What dependencies or interfaces would be affected
- Prior art: has this or something similar been solved before in this codebase?

### Phase 3 — Design alternatives
Present 2–3 concrete approaches. For each:
- Brief description (1 paragraph)
- Key trade-offs (pros and cons)
- Rough complexity estimate (small / medium / large)
- Any risks or unknowns

Example format:
```
## Option A: [name]
[description]
**Pros:** ...
**Cons:** ...
**Complexity:** medium
**Risk:** ...

## Option B: [name]
...

## Recommendation
[which option and why — be direct, don't hedge]
```

**Ask the user to choose. Wait for explicit approval.**

### Phase 4 — Design document
Write the design document to `docs/YYYY-MM-DD-<topic>.md`:

```markdown
# [Feature/Change Title]
Date: YYYY-MM-DD

## Problem
[1–3 sentences: what and why]

## Decision
[chosen approach and rationale]

## Design
[key components, interfaces, data flow — only what's non-obvious]

## Out of scope
[explicitly list what this does NOT cover]

## Open questions
[anything unresolved — empty if none]
```

Keep it short. If the design fits in 30 lines, don't pad it.

### Phase 5 — Handoff
Summarize what was decided and suggest the next step:
- Run `/dev-plan` to write an implementation plan, or
- Run `/dev-tdd` to start coding with tests first, or
- Proceed directly if the change is small

## Anti-patterns to refuse

- "Skip the questions, I know what I want" → understanding the why prevents wrong implementations
- "We don't need a doc for this, it's small" → the doc is for future-you, not current-you
- Proposing only one option → always explore alternatives, even briefly
- Starting to write code during brainstorming → this phase is design only
