---
name: dev-explore
description: Explores an unfamiliar codebase or feature area using parallel agents to build a comprehensive understanding before making changes. Produces a structured summary of architecture, data flow, key files, and pitfalls. Use when the user says "explore this", "understand the codebase", "how does X work", "I'm new to this code", "walk me through this", or before planning changes to unfamiliar code. Do NOT use when the goal is to make changes — follow up with dev-plan or dev-tdd after exploring.
metadata:
  author: phbpx
  version: 1.0.0
  category: exploration
---

# Explore

Systematically understand code before modifying or extending it. Uses parallel agents to avoid the tunnel vision of sequential reading.

## When to use
- Before making changes to code you haven't touched before
- When onboarding to a new project
- When trying to understand how a system actually works (vs. how you think it works)
- Before writing a plan for a complex change

## Core rule
Do not form conclusions before completing exploration. Reading 2 files and extrapolating is not exploration.

## Process

### Phase 1 — Discovery (parallel)
Dispatch 3–4 agents simultaneously to explore different entry points:

**Agent A — Structure**
- Directory layout, package/module organization
- Entry points (main, index, app bootstrap)
- Build system and dependencies (go.mod, pyproject.toml, package.json)
- Key configuration files

**Agent B — Data & Types**
- Core domain types/structs/models
- Database schemas or storage layer
- Data flow: where does data enter, transform, and exit?

**Agent C — Behavior**
- Key functions and their call chains
- Error handling patterns
- External integrations (HTTP clients, APIs, queues)
- Background jobs or scheduled processes

**Agent D — Tests** (if tests exist)
- Test structure and patterns
- What is tested vs. what isn't
- Fixtures, mocks, test helpers
- Integration vs. unit test split

### Phase 2 — Synthesis
Combine findings from all agents. Identify:
- The 3–5 most important files to understand the system
- Non-obvious design decisions (and why they exist, if discoverable)
- Potential gotchas or footguns for someone making changes
- Gaps in understanding that require targeted follow-up

### Phase 3 — Output
Produce a structured summary:

```
## Codebase Overview: [project or area name]

### Architecture
[How the system is organized — layers, packages, main patterns]

### Data flow
[How data moves through the system — entry → processing → output]

### Key files
- `path/to/file.go:42` — [why this file matters]
- ...

### Patterns in use
[Patterns to follow when making changes — dependency injection, error handling style, etc.]

### Watch out for
[Non-obvious things that will trip you up — init order, global state, implicit conventions]

### What's NOT here
[Things you might expect to find but don't — and why, if known]
```

If the exploration revealed something unexpected or contradicts the initial understanding, call it out explicitly.

### Phase 4 — Suggested next step
Based on the goal that triggered the exploration, suggest:
- The files to read in depth
- The right entry point for a planned change
- Whether a `/dev-plan` or `/dev-brainstorm` should come next

## Focus mode
If you're exploring a specific area (not the whole codebase), specify it:
`/explore how the price source fallback works`

In this case, skip the full Phase 1 and go directly to the relevant packages, tracing execution paths from the entry point.

## Anti-patterns to refuse

- "I know this codebase already" → confirm with evidence, don't assume
- Reading 1–2 files and declaring understanding
- Exploring without a goal (exploration serves a purpose)
- Skipping the test agent (tests reveal actual behavior, not intended behavior)
