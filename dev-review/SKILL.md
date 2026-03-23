---
name: dev-review
description: Runs a structured 4-perspective code review (code quality, business logic, security, test coverage) using parallel agents. Adapted for Go, Python, and TypeScript/Next.js idioms. Use when the user says "review this", "code review", "check my code", "review the diff", "look at my changes", or wants feedback before merging or committing. Do NOT use for exploring unfamiliar code — use dev-explore instead.
metadata:
  author: phbpx
  version: 1.0.0
  category: quality
---

# Review

Run a structured code review across 4 dimensions in parallel. Works on staged changes, a branch diff, or a specific set of files.

## Input detection
If no input is specified:
- Use `git diff HEAD` for uncommitted changes
- Or `git diff main...HEAD` if on a feature branch

If files or a diff are specified, use those.

## The 4 reviewers (run in parallel)

Dispatch 4 sub-agents simultaneously, each focused on one dimension:

---

### Reviewer 1 — Code Quality
Evaluate correctness and language idioms.

**Go:**
- Exported types/functions have doc comments
- Errors are wrapped with context (`fmt.Errorf("...: %w", err)`) and not swallowed
- No `panic` in library code
- Interfaces defined at the point of use (consumer), not the provider
- No unnecessary pointer indirection
- `defer` used correctly (not in loops for resource-heavy operations)

**Python:**
- Type annotations on function signatures
- No mutable default arguments
- Context managers (`with`) for file/resource handling
- List comprehensions preferred over map/filter for readability
- `pathlib` over `os.path` for file operations

**TypeScript/Next.js:**
- No `any` — use `unknown` and narrow, or define proper types
- React components: props typed with interfaces, not inline objects
- `useEffect` dependencies array complete and correct
- Server vs. client component boundary is intentional (`"use client"` only when needed)
- No direct DOM manipulation when React state suffices

---

### Reviewer 2 — Business Logic
Evaluate whether the implementation matches the intent.

- Does the code do what the commit message / PR description claims?
- Are edge cases handled? (empty input, zero values, boundary conditions)
- Are error paths explicit and correct (not just happy path)?
- Is there any dead code or unreachable branches?
- Do variable and function names reflect their actual behavior?

---

### Reviewer 3 — Security
Evaluate for common vulnerabilities.

- **Injection:** SQL, shell commands, HTML — is user input ever passed unsanitized?
- **Secrets:** No API keys, tokens, or passwords in code or config files
- **Auth:** Are protected resources actually protected? Is auth checked before data access?
- **Dependencies:** Any newly added dependency with known CVEs or suspicious provenance?
- **File paths:** User-controlled paths could enable path traversal?
- **Sensitive data in logs:** No passwords, tokens, PII logged

---

### Reviewer 4 — Test Coverage
Evaluate whether the change is adequately tested.

- Does a test exist for the new behavior?
- Is the happy path tested?
- Are error paths and edge cases tested?
- Are tests testing behavior (inputs → outputs) or implementation details (internal state)?
- Do tests use real dependencies where feasible, or are mocks justified?
- Would a bug in this code cause at least one test to fail?

---

## Output format

Aggregate all findings into a single report:

```
## Code Review

### Summary
[1–2 sentences: overall assessment]

### Issues

#### Critical (must fix before merging)
- [Reviewer] file.go:42 — [description and suggestion]

#### High (should fix)
- ...

#### Medium (consider fixing)
- ...

#### Low / Suggestions
- ...

### Verdict
[ ] APPROVED — no issues
[ ] APPROVED WITH COMMENTS — low/medium issues only, can merge
[ ] CHANGES REQUESTED — critical or high issues present
```

If there are no issues in a category, omit it.

## Anti-patterns to refuse

- Rubber-stamping without reading the diff
- Flagging style preferences as "critical"
- Reviewing only the files explicitly mentioned (review all files in the diff)
- "Looks good to me" with no specific observations
