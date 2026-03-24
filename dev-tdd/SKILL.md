---
name: dev-tdd
description: Implements features or fixes using strict RED-GREEN-REFACTOR Test-Driven Development. Detects the project's language and applies idiomatic testing practices. Use when the user says "use TDD", "write tests first", "implement with tests", "TDD this", or is starting a new feature or behavior. Do NOT use for bug investigation — use dev-debug instead.
metadata:
  author: phbpx
  version: 1.0.0
  category: testing
---

# TDD

Implement using Test-Driven Development. The test must exist and fail before any production code is written.

## The cycle

```
RED   → Write a failing test that describes the behavior
GREEN → Write the minimal code to make it pass
REFACTOR → Clean up without changing behavior
```

**The rule:** If production code exists before a failing test, stop. The test must come first — no exceptions, no "I'll add the test after."

## Process

### RED phase — Write the failing test
1. Understand what behavior to implement (from the plan, issue, or user description)
2. Write a test that:
   - Describes the behavior in its name (`TestCalculateReturn_WithNoTransactions_ReturnsZero`)
   - Tests inputs and outputs, not internal state
   - Will clearly fail because the code doesn't exist yet
3. Run the test. Confirm it fails with the expected error (compilation error or assertion failure — not a panic or infrastructure error)

**Do not write any production code until the test fails for the right reason.**

### GREEN phase — Make it pass
Write the minimal code to make the test pass. "Minimal" means:
- No extra features
- No premature abstractions
- No handling of cases not yet tested
- Hardcoding is acceptable in early iterations

Run the test. It must pass. All existing tests must still pass.

### REFACTOR phase — Clean up
Improve the code without changing behavior:
- Remove duplication
- Improve naming
- Extract functions if the logic is complex
- Apply language idioms

Run all tests after every change. If tests break, you introduced a behavior change — undo it.

### Repeat
Add the next test for the next behavior. Work incrementally.

---

## Language and tooling detection

Before writing any test, identify the project's language and test stack by inspecting configuration files (`go.mod`, `pyproject.toml`, `package.json`, `Cargo.toml`, `build.gradle`, `pom.xml`, `*.csproj`, `mix.exs`, `Gemfile`, etc.) and existing test files.

Then apply the **idiomatic testing practices** for that stack:
- Use the project's established test framework and assertion library
- Follow the project's existing test file organization and naming patterns
- Use the language's standard test runner and flags
- Prefer the project's existing test helpers and fixtures over creating new ones

If no tests exist yet, choose the most common test framework for the detected language and follow community conventions.

---

## Test naming convention

Use the project's existing naming convention. If none exists, follow the language's community standard. The name must describe **subject**, **condition**, and **expected outcome**.

---

## Code documentation

All method and function documentation (comments, docstrings) must be written in **English** — regardless of the project's spoken language.

## Anti-patterns to refuse

- Writing production code before the test
- Writing tests after the fact to reach coverage numbers
- Testing implementation details (internal state, private methods)
- Tests that always pass regardless of implementation
- Mocking everything — only mock what crosses a system boundary (HTTP, DB, file system)
- "The logic is simple, I don't need a test" → simple logic has simple tests, write it
