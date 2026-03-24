---
name: dev-pr
description: Creates a GitHub pull request from the current branch with an auto-generated title and description based on commits, plan files, and linked issues. Use when the user says "create a PR", "open a pull request", "send for review", "PR this", or has committed changes on a feature branch ready to merge. Requires gh CLI authenticated. Do NOT use for committing — use dev-commit first.
metadata:
  author: phbpx
  version: 1.0.0
  category: git
---

# PR

Create a GitHub pull request from the current branch.

## Prerequisites
- Changes are committed (no uncommitted work — suggest `/dev-commit` if needed)
- Current branch is NOT the base branch (`main`/`master`)
- `gh` CLI is authenticated

If any prerequisite fails, stop and tell the user what to fix.

## Process

### Step 1 — Gather context
Run in parallel:
- `git branch --show-current` — current branch name
- `git log main...HEAD --oneline` (or `master` if `main` doesn't exist) — commits to include
- `git diff main...HEAD --stat` — files changed summary
- `git status` — confirm working tree is clean
- Check `docs/plans/` for a plan file with status `completed` or `approved` that matches this work

### Step 2 — Detect linked issue
Look for an issue reference in:
1. Branch name patterns: `feature/42-*`, `fix/42-*`, `*/PROJ-123-*`, `*/#42-*`
2. Plan file header (if found): `Issue: owner/repo#N`
3. Commit messages: `#N`, `fixes #N`, `closes #N`

If found, fetch the issue title and labels:
```bash
gh issue view <number> --json title,labels -q '.title'
```

### Step 3 — Generate PR content
Draft a PR title and description:

**Title:** Short imperative summary (max 70 chars). Derive from:
- The plan's goal (if a plan exists)
- The issue title (if linked)
- A summary of the commits (fallback)

**Description:**
```markdown
## Summary
[2-4 bullet points describing what changed and why]

## Changes
[File change summary from git diff --stat]

## Test plan
[How to verify — derived from plan's test strategy or commit messages]

## Linked issue
Closes #<number> (if detected)
```

### Step 4 — Review gate
Present the draft to the user:
```
Branch: feature/my-branch → main
Commits: N
Files changed: N

Title: <proposed title>
Description:
<proposed description>

Create this PR? (Y/N)
```

**Wait for explicit approval.** Do not create the PR without confirmation.

### Step 5 — Create
Push the branch and create the PR:
```bash
git push -u origin <branch>
gh pr create --title "<title>" --body "<description>"
```

Display the PR URL when done.

## Rules

- Never force-push without asking
- If the branch already has an open PR, show its URL instead of creating a duplicate
- If there are uncommitted changes, stop and suggest `/dev-commit` first
- Keep the description concise — reviewers read diffs, not essays

## Anti-patterns to refuse

- Creating a PR with no commits ahead of base
- PRs with "WIP" in the title without the user explicitly requesting draft mode
- Creating a PR from `main` to `main`
- Pushing directly to `main` instead of creating a PR
