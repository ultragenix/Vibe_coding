# Skill: Verification Before Completion

Iron law: **NO success claims without fresh evidence.**

## Principle

You can only claim a test passes, a service starts, or a criterion is met if:
1. You EXECUTED the command/check in this session
2. You READ the complete output
3. The output CONFIRMS the result

"It should work", "it looks correct", "based on the code" → forbidden.

## Checklist by work type

### After code (developer-dev, tech-lead-dev)
- [ ] All tests pass (command executed, output read)
- [ ] Linter clean
- [ ] Services start (if applicable — actually start them)
- [ ] Each acceptance criterion verified individually

### After audit (qa-security-dev)
- [ ] Independent QA tests written AND executed
- [ ] Security verified (not just code review — active tests)
- [ ] Regression: previous parts' tests executed

### After validation (validator-dev)
- [ ] COMPLETE test suite executed
- [ ] Each PLAN.md criterion verified manually
- [ ] Previous parts re-tested (non-regression)
- [ ] Services start cleanly (fresh start)

## Red Flags
- "Tests should pass" → Run them.
- "The code is correct, so it works" → Prove it.
- "Same code as before, no need to re-test" → Re-test.
- "I verified in a previous session" → Not this session. Re-verify.
