---
name: dev-tdd
description: Implements features or fixes using strict RED-GREEN-REFACTOR Test-Driven Development. Supports Go (testify), Python (pytest, hypothesis), and TypeScript/Next.js (vitest, testing-library). Use when the user says "use TDD", "write tests first", "implement with tests", "TDD this", or is starting a new feature or behavior. Do NOT use for bug investigation — use dev-debug instead.
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

## Language-specific patterns

### Go

```go
// File: internal/portfolio/engine_test.go
func TestBuildPortfolio_WithBuyTransaction_AccumulatesPosition(t *testing.T) {
    transactions := []Transaction{
        {Type: Buy, Ticker: "PETR4", Qty: 100, Price: 30.0},
    }
    portfolio, err := Build(transactions, mockPriceSource)
    require.NoError(t, err)
    assert.Equal(t, 100.0, portfolio.Positions["PETR4"].Quantity)
}
```

- Use `testing` stdlib + `testify/assert` and `testify/require`
- Use subtests with `t.Run` for table-driven tests
- Use `t.Helper()` in assertion helpers
- Run: `go test -run TestFunctionName ./internal/package/`
- Run with race detector: `go test -race ./...`

### Python

```python
# File: tests/test_portfolio.py
def test_build_portfolio_with_buy_transaction_accumulates_position():
    transactions = [Transaction(type="buy", ticker="PETR4", qty=100, price=30.0)]
    portfolio = build_portfolio(transactions, price_source=mock_price_source)
    assert portfolio.positions["PETR4"].quantity == 100
```

- Use `pytest`; run single test: `pytest tests/test_file.py::test_function_name -xvs`
- Use fixtures for shared setup (`@pytest.fixture`)
- Use `pytest.raises` for exception testing
- Use `hypothesis` for property-based tests when dealing with numeric calculations

### TypeScript / Next.js

```typescript
// File: __tests__/components/PortfolioSummary.test.tsx
import { render, screen } from '@testing-library/react'
import { PortfolioSummary } from '@/components/PortfolioSummary'

test('shows total value when positions are loaded', () => {
  render(<PortfolioSummary positions={mockPositions} />)
  expect(screen.getByText('R$ 10.000,00')).toBeInTheDocument()
})
```

- Use `vitest` (preferred) or `jest` + `@testing-library/react`
- Test behavior via rendered output and user interactions, not component internals
- `screen.getByRole` over `getByTestId` — test what the user sees
- For hooks: use `renderHook` from `@testing-library/react`
- Run single test: `vitest run path/to/test.ts`

---

## Test naming convention

`Test<Subject>_<Condition>_<ExpectedOutcome>`

Examples:
- `TestBuild_WithNoTransactions_ReturnsEmptyPortfolio`
- `test_calculate_return_when_no_transactions_returns_zero`
- `renders error message when fetch fails`

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
