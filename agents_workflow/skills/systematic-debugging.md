# Skill: Systematic Debugging

Iron law: **NO fixes without root cause investigation first.**

## Phase 1 — Root Cause Investigation

1. Read error messages COMPLETELY (stack traces, logs)
2. Reproduce reliably (exact steps, every time)
3. Check recent changes (`git diff`, commits, env)
4. In multi-component systems:
   - Log data entering each component
   - Log data exiting each component
   - Verify config/env propagation
   - Check state at each layer
5. Trace data flow — where does the bad value FIRST appear?

## Phase 2 — Pattern Analysis

1. Find working examples (similar code without the bug)
2. Compare against reference (read the reference implementation COMPLETELY)
3. List EVERY difference, however small
4. Understand dependencies (which components, settings, assumptions)

## Phase 3 — Hypothesis and Test

1. Form ONE hypothesis: "I think X is the root cause because Y"
2. Test with the SMALLEST possible change
3. If it works → Phase 4. If not → new hypothesis.
4. If you don't understand something: say "I don't understand X". Don't pretend.

## Phase 4 — Implementation

1. Create a test case that reproduces the bug (follow skills/tdd.md)
2. Implement ONE fix (the root cause, not the symptom)
3. Verify: the test passes, no other tests break
4. Attempt counter:
   - < 3 attempts: return to Phase 1 with new information
   - >= 3 attempts: **STOP — this is an ARCHITECTURE problem, not a hypothesis problem**
   - Escalate to architect-dev for redesign

## Red Flags — If you think this, STOP and return to Phase 1
- "Quick fix for now, investigate later"
- "Let's just try changing X"
- "No need for a test, I'll verify manually"
- "It's probably X, let me fix that"
- "I don't fully understand but this should work"
- Each fix reveals a new problem elsewhere
- "One more attempt" (when you've already tried 2+)
