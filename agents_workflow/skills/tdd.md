# Skill: Test-Driven Development

Iron law: **NO production code without a failing test first.**

## The RED-GREEN-REFACTOR Cycle

### RED — Write a failing test
- One behavior per test
- Descriptive name: `test_[what]_[condition]_[expected]` (follow CLAUDE.md)
- Real code, no mocks unless unavoidable

### Verify RED (MANDATORY)
- Run the test
- Confirm it FAILS (not a syntax error — a correct failure)
- The test fails because the feature does not exist yet

### GREEN — Write minimal code to pass
- Just enough to make the test pass
- No extra features, no refactoring

### Verify GREEN (MANDATORY)
- Run the test — it passes
- Run ALL tests — no regressions

### REFACTOR — Clean up after green
- Remove duplication
- Improve names
- Extract helpers if needed
- Tests stay green

## When to use
ALWAYS — new features, bug fixes, refactoring, behavior changes.

## Red Flags — If you think this, STOP
- "Too simple to test" → Wrong. Write the test.
- "I'll test after" → Tests that pass immediately prove nothing.
- "I already tested manually" → Not reproducible. Write the test.
- "I'll keep the code as reference and write tests first" → Delete the code. Start over.
- "TDD will slow me down" → Debugging without tests slows you down more.

## Absolute rule
If code exists BEFORE the test: **delete it and start over.**
