---
name: dev-commit
description: Organizes all uncommitted changes into clean, atomic, bisectable commits using Conventional Commits. Use when the user says "commit", "create a commit", "save my changes", "write a commit message", or has staged or unstaged changes to record. Do NOT use for pushing, branching, pull requests, or rebasing.
metadata:
  author: phbpx
  version: 1.0.0
  category: git
---

# Commit

Transform all uncommitted changes into a clean, organized commit history.

## Process

### Step 1 — Gather context
Run in parallel:
- `git status` — list all modified, staged, and untracked files
- `git diff` — full diff of unstaged changes
- `git diff --cached` — full diff of staged changes
- `git log --oneline -5` — recent commit style reference

### Step 2 — Analyze and group
Group changes into logical atomic units. Each group becomes one commit. Rules:
- One concern per commit (feature, fix, refactor, docs, test, chore)
- Tests travel with the implementation they test
- Config and dependency changes get their own commit
- Each commit must leave the codebase in a working state (bisectable)
- Avoid grouping unrelated changes just because they're small

### Step 3 — Propose commit plan
Present the proposed commits to the user as a numbered list:
```
1. feat(portfolio): add income tracking for dividends and JCP
2. test(portfolio): add unit tests for income calculation
3. chore(deps): bump modernc/sqlite to v1.47.0
```

**Wait for explicit approval before proceeding.** Silence is not approval.

### Step 4 — Draft messages
For each commit, write a message following Conventional Commits:

```
<type>(<scope>): <short imperative summary>

[optional body: why this change, not what — the diff shows what]
```

Types: `feat`, `fix`, `refactor`, `test`, `docs`, `chore`, `perf`, `ci`

Rules:
- Summary line: max 72 chars, lowercase, no period at the end
- Use imperative mood ("add", "fix", "remove" — not "added", "fixes")
- Body only when the why isn't obvious from the diff
- No "WIP", no "misc changes", no "updates"

### Step 5 — Execute
Stage and commit each group in the agreed order. Use:
```bash
git add <specific files>
git commit -m "type(scope): summary"
```

Never use `git add -A` or `git add .` — always stage specific files.

### Step 6 — Verify
Run `git log --oneline -<n>` where n = number of commits made. Confirm all commits appear as planned.

## Conventional Commits quick reference

| Type | When to use |
|---|---|
| `feat` | New feature or behavior |
| `fix` | Bug fix |
| `refactor` | Code change without behavior change |
| `test` | Adding or fixing tests |
| `docs` | Documentation only |
| `chore` | Tooling, deps, config, CI |
| `perf` | Performance improvement |

## Anti-patterns to refuse

- "Just one big commit, it's a small project" → atomic commits pay off during debugging
- "Add all files then we split later" → stage specific files from the start
- Committing `.env`, secrets, or binary artifacts → stop and warn the user
