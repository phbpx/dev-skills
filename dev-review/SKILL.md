---
name: dev-review
description: Runs a structured 4-perspective code review (code quality, business logic, security, test coverage) using parallel agents. Applies idiomatic checks for the project's language. Use when the user says "review this", "code review", "check my code", "review the diff", "look at my changes", or wants feedback before merging or committing. Do NOT use for exploring unfamiliar code — use dev-explore instead.
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
Evaluate correctness and language idioms. Detect the project's language and apply its idiomatic standards:

- Public APIs have proper documentation (doc comments, docstrings, JSDoc, etc.)
- Errors are handled explicitly — not swallowed, not ignored, wrapped with context where idiomatic
- Type safety is used to its full extent (type annotations, generics, narrowing — whatever the language offers)
- Resource management follows language conventions (defer, context managers, try-with-resources, RAII, etc.)
- Code follows the project's established patterns and the language's community conventions
- No unnecessary complexity or abstraction beyond what the language idioms require

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
