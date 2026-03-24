---
name: dev-debug
description: Investigates bugs by finding root causes before writing any fix. Reproduces the bug with a failing test first, then traces the root cause, then implements the minimal fix. Use when the user says "there's a bug", "something is broken", "this isn't working", "fix this error", "debug this", "why is X happening", or describes unexpected behavior. Do NOT use for planned features — use dev-tdd instead.
metadata:
  author: phbpx
  version: 1.0.0
  category: debugging
---

# Debug

Systematically find the root cause of a problem before writing any fix.

## Core rule
**No fix without a failing test that reproduces the bug.**
If the bug can't be reproduced in a test, understand why before proceeding.

## Process

### Phase 1 — Reproduce
Confirm the bug exists and understand its exact boundary:
- What is the observed behavior?
- What is the expected behavior?
- Is it deterministic or intermittent?
- What inputs or conditions trigger it?

Write a minimal failing test (or a reproduction script) that captures the bug.
This test must fail NOW. If it passes, you don't understand the bug yet.

### Phase 2 — Investigate
Read the code paths involved. Do NOT guess. Trace the execution:
- Where does the data enter the system?
- Where does it diverge from expected behavior?
- What assumptions does the code make that might be wrong?

Use parallel agents for large codebases. Look for:
- Off-by-one errors, nil/null dereferences
- State mutation side effects
- Incorrect error handling (swallowed errors, wrong error type)
- Race conditions (concurrent access to shared state)
- Incorrect data transformation or serialization

### Phase 3 — Hypothesize
Form a specific hypothesis:
> "The bug is caused by X because Y. Evidence: Z."

Do not proceed without a hypothesis. "Something is wrong in this area" is not a hypothesis.

Test the hypothesis by:
- Adding targeted logging or print statements
- Writing a unit test that isolates the suspected component
- Checking assumptions with assertions

If the hypothesis is wrong, form a new one. Maximum 3 hypotheses before stepping back and re-reading the problem statement.

### Phase 4 — Fix
Implement the minimal fix that makes the failing test pass without breaking existing tests.

Rules:
- Fix the root cause, not the symptom
- If fixing requires a larger refactor, do the minimal safe fix now and open a separate task for the refactor
- Remove any debugging code (print statements, temporary assertions) before committing

### Phase 5 — Verify
- The originally failing test now passes
- All existing tests still pass (run the project's full test suite)
- No new warnings introduced

Document the root cause in the commit message body (one sentence is enough).

## Language-specific debugging

Detect the project's language and apply its idiomatic debugging tools and techniques:
- Use the language's standard test runner with verbose/fail-fast flags
- Use the language's interactive debugger when tracing execution
- Check for the language's common bug patterns (null handling, error propagation, concurrency issues, type coercion, mutable state, etc.)

## Anti-patterns to refuse

- "I know what the bug is, let me just fix it" → write the failing test first
- "The test is too hard to write for this bug" → simplify the test, don't skip it
- Fixing symptoms (adding a null check on top of a logic error)
- Adding `try/catch` to swallow the error instead of fixing it
- "It works on my machine" without understanding why it doesn't work elsewhere
