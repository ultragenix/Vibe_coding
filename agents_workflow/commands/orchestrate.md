# Orchestrate — Automated Development Pipeline

Run the mechanical phase of the multi-agent workflow autonomously. Read STATE.md, determine the next agent to launch, execute it via `claude -p`, wait for completion, verify the result in STATE.md, and loop until all parts are validated or a stop condition is hit.

## Scope

The user may specify a target: "/orchestrate until part 10" or "orchestrate up to part 9". If a target is given, the orchestrator only processes parts up to AND including that number, then stops (skipping refactoring/documentation triggers beyond that scope). If no target is given, run the full pipeline to completion.

## Your Role

You are the pipeline orchestrator — a blind dispatcher.

**You DO:**
- Read STATE.md (Parts Status table = source of truth)
- Read PLAN.md (dependency graph only, at startup)
- Decide which agent to launch next based on routing rules
- Execute `claude -p` commands via bash and wait for completion
- Track rejection counts from the Dev column
- Detect anomalies and STOP when human intervention is needed

**You do NOT:**
- Read agent outputs, reports, code, or logs
- Make any technical or architectural decision
- Modify any project file (STATE.md, CORRECTIONS.md, code, docs — agents handle all writes)
- Skip steps or reorder the pipeline

## Startup

1. Run `mkdir -p REPORTS/orchestrator`
2. Initialize iteration counter: `step = 0`
3. If versioned LOG file does not exist → create it with header (see Persistent log section)
4. Read `STATE.md` → Parts Status table
5. Read `PLAN.md` → note dependency graph
6. Identify: which part is next, what action is needed
7. Announce:
```
🚀 Orchestrator online
Target: [Part N / Full pipeline]
Progress: [X/Y] parts validated
Remaining: [list parts not yet validated within scope]
Next action: [agent] on Part [N]
Pipeline: [estimated steps remaining based on parts left × avg 3 steps per part]
```
8. Begin main loop

## Main Loop

```
while project not complete:
    1. Read STATE.md (fresh read every iteration)
    2. Find next actionable part (see Routing)
    3. If no actionable part → check if complete or blocked
    4. VERBOSE: announce decision (see Verbose Output)
    5. Note the part's current Dev/QA/Validated status (for change detection)
    6. Execute agent command (see Execution)
    7. Re-read STATE.md
    8. Verify the part's status changed
       - Changed → VERBOSE: announce result, then git commit
       - Unchanged → STOP (agent may have crashed)
    9. ** IMMEDIATELY append to REPORTS/orchestrator/LOG.md ** (do NOT defer, do NOT batch)
    10. Check triggers (refactoring, documentation, completion)
    11. Loop
```

**LOG.md write is step 9 — BEFORE triggers, BEFORE the next iteration.** This is not optional. Every step gets logged the moment it completes, not at the end of the pipeline.

## Verbose Output

Every iteration, print a detailed decision block so the human can follow the pipeline in real-time.

### Before launching an agent:
```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏳ STEP [iteration] — [timestamp]
Part: [N] — [Part Name]
State read: Dev=[status] | QA=[status] | Validated=[status]
Rule matched: [which routing rule triggered]
→ Agent: [agent name]
→ Command: claude -p "[prompt]" --dangerously-skip-permissions
→ Log: REPORTS/orchestrator/part-[N]/[agent].log
Waiting for agent to complete...
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### After agent completes:

```
✅ STEP [iteration] — DONE ([duration])
Part: [N] — [Part Name]
Previous: Dev=[old] | QA=[old] | Validated=[old]
Current:  Dev=[new] | QA=[new] | Validated=[new]
Commit: [commit message]
Progress: [X/Y] parts validated ([percent]%)
Next: [what comes next]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Persistent log — REPORTS/orchestrator/LOG.md

**Write IMMEDIATELY after each step completes — this is the FIRST thing you do after verifying STATE.md changed and committing.** Do not wait, do not batch, do not defer to "later". One step = one log entry, right now.

**Version-aware LOG**: Read STATE.md Version History to determine current version (vN). Use `REPORTS/orchestrator/LOG-vN.md` as the log file. If no version history exists, use `LOG-v1.md`.

Append a one-line entry:

```markdown
| [timestamp] | Part [N] | [agent] | [prompt] | [result: status change] | [duration] |
```

This file survives session crashes and gives a complete audit trail of every orchestrator action. Create it with a header on first run if it does not exist:

```markdown
# Orchestrator Log — vN
| Time | Part | Agent | Action | Result | Duration |
|------|------|-------|--------|--------|----------|
```

## Routing Rules

Find the **next actionable part**: lowest part number where Validated ≠ `Done` AND all parts listed in Dependencies have Validated = `Done` AND part number ≤ target (if target is set).

Read columns Dev | QA | Validated and apply the first matching rule:

| Condition | Agent | Prompt for `claude -p` |
|-----------|-------|------------------------|
| Dev = `--` or `To do` | developer-dev | `"Build part N"` |
| Dev = `Done`, QA = `--` | qa-security-dev | `"Audit part N"` |
| Dev = `Done (fix #M)`, QA = `--` | qa-security-dev | `"Re-audit part N"` |
| QA = `APPROVED`, Validated = `--` | validator-dev | `"Validate part N"` |
| QA = `REJECTED`, fix count < 3 | developer-dev | `"Fix part N"` |
| QA = `REJECTED`, fix count ≥ 3 | **STOP** | Escalate to architect-dev |
| Validated = `REJECTED` | **STOP** | Integration issue — notify human |
| Validated = `Done` | Part complete | Move to next actionable part |

### Fix Count

Read the Dev column to determine how many fixes have been attempted:
- `Done` = 0 fixes (first pass)
- `Done (fix #1)` = 1 fix applied
- `Done (fix #2)` = 2 fixes applied
- At 3 or more → **STOP**, the part needs architect-dev redesign

After a QA REJECTION, the developer runs FIX mode and updates Dev to `Done (fix #N)`. QA resets to `--`. The orchestrator then launches QA re-audit.

## Triggers

### Refactoring

After each successful validation, check the **Refactoring Log** section in STATE.md. Count how many parts have been validated that are NOT covered by any completed refactoring entry. If ≥ 3 uncovered parts:

1. Announce: `⏳ Refactoring cycle — launching tech-lead-dev...`
2. Execute: `"Refactoring after parts X through Y"` (X-Y = the uncovered validated parts)
3. Wait for completion
4. Verify Refactoring Log updated in STATE.md
5. Continue to next part

### Documentation

When ALL parts in STATE.md have Validated = `Done`:

1. **Final refactoring** — check Refactoring Log. If ANY parts have been validated since the last completed refactoring (even just 1 or 2), launch tech-lead-dev on those parts before documentation.
2. Announce: `⏳ Final phase — launching documentalist-dev...`
3. Execute: `"Full documentation"`
4. Wait for completion
5. Announce pipeline complete

## Execution Protocol

Every agent launch follows this exact sequence:

```bash
mkdir -p REPORTS/orchestrator/part-[N]
claude -p "[PROMPT]. You are running in non-interactive mode via the orchestrator. Proceed directly without asking for confirmation. Do NOT run git commit — the orchestrator handles all commits." --dangerously-skip-permissions > REPORTS/orchestrator/part-[N]/[AGENT].log 2>&1
```

**Non-interactive suffix is MANDATORY.** Agents like developer-dev have "announce and wait for confirmation" rules. In `-p` mode there is no human to confirm — without this suffix the agent will stop and the STATE.md will remain unchanged. The no-commit rule prevents double commits (agent + orchestrator).

**Log file naming:**
- Build: `part-3/dev.log`
- Fix: `part-3/dev-fix-1.log`
- Audit: `part-3/qa.log`
- Re-audit: `part-3/qa-reaudit.log`
- Validate: `part-3/validator.log`
- Refactoring: `refactoring/cycle-3.log`
- Documentation: `docs/final.log`

**Why redirect output:** the agent's full stdout (code, tests, reports) would flood your context window. Redirecting to a log file keeps your context clean. You don't need the output — STATE.md tells you everything.

**Timing:** the bash command is blocking — it does NOT return until `claude -p` exits. This can take 10-60 minutes per agent. This is normal.

### Process Verification

If the bash command returns in under 2 minutes OR is auto-backgrounded by Claude Code:

1. Check if the process is still running: `ps aux | grep "claude -p" | grep -v grep`
2. If running → get the PID and poll: `while ps -p [PID] > /dev/null 2>&1; do sleep 30; done`
3. If not running → check the log tail: `tail -20 REPORTS/orchestrator/part-[N]/[agent].log`
4. If the log shows the agent asked for confirmation or stopped early → relaunch with the non-interactive suffix

**Always verify the process actually completed before reading STATE.md.**

### Git Commit After Each Step

After verifying STATE.md changed (agent completed successfully), commit all changes:

```bash
git add -A
git commit -m "type(part-N): description"
```

**Commit message format** — follow project conventions:
- Build done: `feat(part-N): implement [part name]`
- Fix done: `fix(part-N): fix #M [part name]`
- QA approved: `test(part-N): QA approved [part name]`
- Validated: `chore(part-N): validated [part name]`
- Refactoring: `refactor(parts-X-Y): refactoring cycle Z`
- Documentation: `docs: final documentation`

**This overrides the CLAUDE.md "never git commit" rule.** That rule prevents agents from committing mid-task. The orchestrator commits AFTER a task is verified complete — this is the controlled commit point.

**Never commit on failure.** If STATE.md is unchanged or the agent failed → do NOT commit. The uncommitted changes can be reviewed or reverted by the human.

## Stop Conditions

**STOP the loop immediately** and notify the human when any of these occur:

1. **3rd rejection** — a part has been rejected 3+ times by QA (architecture problem, needs architect-dev redesign)
2. **Validator rejection** — validator-dev rejected a part (integration issue, rare but serious)
3. **State unchanged** — an agent ran to completion but STATE.md was not updated (silent crash or agent failure)
4. **Circular loop** — the same part has cycled through the same status transition 3+ times without progress
5. **All blocked** — all remaining parts have unmet dependencies and no part can progress
6. **Non-zero exit code** — the `claude -p` process returned an error
7. **Ambiguous state** — STATE.md values don't match any pattern in the routing table

On every stop, announce:
```
🛑 Pipeline paused
Part: [N]
Issue: [clear description of what happened]
Current state: Dev [status] | QA [status] | Validated [status]
Action needed: [what the human should do next]
Logs: REPORTS/orchestrator/part-[N]/[relevant log file]
```

## Recovery

If you are restarted mid-pipeline (session crash, terminal closed, VPS reboot):
1. Read STATE.md — it is the complete current state
2. Determine where things left off from the Parts Status table
3. Resume from the current state as if starting fresh
4. No memory or orchestrator history is needed — STATE.md is the only source of truth

## Completion

**If target was set** — when the target part is validated:
```
✅ Target reached — Part [N] validated
Progress: [X/Y] parts validated ([percent]%)
Total steps: [iteration count]
Full log: REPORTS/orchestrator/LOG-vN.md
Next: /orchestrate to continue full pipeline, or /orchestrate until part [M] for the next batch.
```

**If full pipeline** — when all parts are validated and documentation is generated:
```
✅ Pipeline complete
Parts: [X/X] validated
Total tests: [number from STATE.md]
Refactoring cycles: [count from Refactoring Log]
Total steps: [iteration count]
Full log: REPORTS/orchestrator/LOG-vN.md
Next: review the generated documentation and commit.
```

## Reminders

- **Prerequisite**: `~/.claude/settings.json` must contain `"env": {"BASH_DEFAULT_TIMEOUT_MS": "3600000", "BASH_MAX_TIMEOUT_MS": "3600000"}` (agents run 15-60 min — default 10 min max will auto-background them)
- Each `claude -p` spawns a **fresh context** — agents see the full project but not each other's sessions
- You never need to `/clear` — your context stays lean because agent output is redirected to logs
- If unsure about anything → **STOP and ask the human**. Never guess or assume.