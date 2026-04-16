# Multi-Agent Development Framework

> Drop `.claude/agents/` into any project and start working.

---

## Setup

### New project

Open Claude Code in your new project and paste:

```
Copy the agent framework from ~/WorkSpace/Agent_Workflow into this project: agents/, commands/, skills/ into .claude/ and templates/CLAUDE.md.template as ./CLAUDE.md
```

Then edit CLAUDE.md with your project's stack and conventions.

### Machine setup (once per machine)

Add these to `~/.claude/settings.json` (agents can run 15-60 min, default timeouts will kill or auto-background them):

```json
{
  "bashCommandTimeout": 14400000,
  "env": {
    "BASH_DEFAULT_TIMEOUT_MS": "3600000",
    "BASH_MAX_TIMEOUT_MS": "3600000"
  }
}
```

Claude auto-routes to the right agent from your request. Just speak naturally.

---

## The Workflow

```
briefer-dev → [data-analyst-dev → data-engineer-dev] → architect-dev → [dev → qa → validator] × N parts
```

Data cycle runs only if the brief mentions data sources. Otherwise: `briefer-dev → architect-dev → orchestrate`.

**Rule: 1 agent = 1 session.** Always commit + `/clear` between agents.

---

## Step by Step

### Step 0 — Define the project
```
"I want to build [X]"
```
Briefer-dev discusses, challenges, recommends tools (WebSearch). Generates `docs/BRIEF.md` only when you say **"lance le brief"**.

At the end, briefer-dev tells you the next step:
- **Brief mentions data** → go to Step 1
- **No data** → skip to Step 2

### Step 1 — Data Lifecycle (only if brief mentions data)

The brief's "Data Sources" section feeds the analyst. The analyst's spec feeds the engineer. The engineer's architecture feeds the architect.

```
briefer-dev
  → BRIEF.md with structured Data Sources section
  ↓
data-analyst-dev PRE-EXPLORE (or EXPLORE if DB accessible)
  → Produces docs/DATA_SPEC.md (business analysis & spec)
  ↓
data-engineer-dev STRUCTURE
  → Reads DATA_SPEC.md → produces docs/DATA_SOURCES.md (technical architecture)
  ↓
architect-dev
  → Reads BRIEF.md + DATA_SOURCES.md → creates PLAN.md with data parts
```

#### Role Boundaries

| Agent | Does | Does NOT |
|-------|------|----------|
| data-analyst-dev | Profile, analyze business relevance, discover enrichment sources, write DATA_SPEC.md | Design schemas, ERDs, pipelines, joins, partitions |
| data-engineer-dev | Design architecture (ERD, schemas, joins, lineage, partitions), write DATA_SOURCES.md | Explore new sources, discover enrichment |
| architect-dev | Define what parts are needed based on DATA_SOURCES.md | Design pipeline layers or data schemas |
| developer-dev | Implement data parts per PLAN.md + DATA_SOURCES.md | Explore data or design schemas |

#### Commands

| When | What to say | Agent | Mode |
|------|-------------|-------|------|
| Day 0 — no data access | "Pre-explore the data sources" | data-analyst-dev | PRE-EXPLORE |
| DB accessible | "Explore the data" | data-analyst-dev | EXPLORE |
| Find enrichment | "Search for enrichment sources" | data-analyst-dev | DISCOVER |
| Design architecture | "Structure the data model" | data-engineer-dev | STRUCTURE |
| Data changed | "Update DATA_SOURCES.md" | data-engineer-dev | UPDATE |
| Before milestone | "Validate data quality" | data-engineer-dev | VALIDATE |
| Monthly | "Check for new data sources" | data-analyst-dev | FEEDBACK |

#### When Things Change (Data)

**3 rules determine who starts:**
- **Data changes** (sources, schema, new mart, quality) → **data-analyst-dev** first → engineer → architect
- **Feature changes without data** (UI, logic, API) → **architect-dev** first → dev
- **Pipeline optimization** (performance, storage) → **data-engineer-dev** first → dev

The chain is always: analyst → engineer → architect (except the 2 cases below where a different agent starts).

| Situation | Flow | Rule |
|-----------|------|------|
| New external source (not yet accessible) | analyst DISCOVER → engineer STRUCTURE (design integration) → architect REVISION (add import parts) → dev builds import → analyst EXPLORE (profile imported data) → engineer UPDATE (adjust with real profiles) | What we have |
| New internal source (already in DB) | analyst EXPLORE → engineer STRUCTURE → architect REVISION | What we have |
| Source schema changed | analyst EXPLORE (re-profile) → engineer UPDATE (delta report + impacted parts list) → architect REVISION (de-validate impacted parts) | What we have |
| Sources outdated | analyst FEEDBACK → engineer UPDATE if sources changed → architect REVISION if parts impacted | What we have |
| Need a new mart/table | analyst EXPLORE (spec data needs) → engineer STRUCTURE (design mart) → architect REVISION (add parts) → dev implements | What we have |
| Data quality issue | **engineer** VALIDATE → if FAIL: analyst EXPLORE (investigate) → engineer UPDATE | How we build it |

**Delta protocol (engineer UPDATE):** When DATA_SOURCES.md changes, the engineer writes a delta in `REPORTS/data/update-[N].md` listing: what changed, which parts are potentially impacted. The architect reads this delta to de-validate the right parts. The developer reads it to know what to fix.

### Step 2 — Plan
```
"Read the brief and generate the plan"
```
Architect-dev creates PLAN.md, STATE.md, CORRECTIONS.md, CLAUDE.md.

**Data gate**: if the brief mentions data but DATA_SOURCES.md doesn't exist, the architect will stop and tell you to run the data cycle first.

### Step 3 — Build → Audit → Validate (repeat per part)
```
"Build part 1"       → developer-dev
"Audit part 1"       → qa-security-dev
"Validate part 1"    → validator-dev
"Build part 2"       → developer-dev
...
```

Or automate: `/orchestrate` runs the mechanical loop.

### Every 3 validated parts — Refactoring
```
"Refactoring after parts 1 through 3"    → tech-lead-dev
```

### Final — Documentation
```
"Full documentation"                     → documentalist-dev
```

---

## Version Management

Projects evolve. The architect manages versions.

### When to create a new version

- **SAFE/CAUTION/CRITICAL revisions** → add new parts to current version (no version bump)
- **STRUCTURAL revisions** → version bump (v1 → v2), old validated parts archived

### How it works

```
You: "revise the brief" → briefer-dev REVISION
  → BRIEF.md updated (current state), change details in REPORTS/brief/revision-[N].md
  ↓
If data impacted: data-analyst-dev → data-engineer-dev
  ↓
You: "revise the plan" → architect-dev REVISION
  → Classifies changes (SAFE/CAUTION/CRITICAL/STRUCTURAL)
  → If STRUCTURAL: archives old version, bumps to vN+1
  → Adds new parts, de-validates impacted parts
  ↓
/orchestrate → resumes mechanical loop on active parts
```

### What gets archived (STRUCTURAL only)

| File | Current version | Archive |
|------|----------------|---------|
| BRIEF.md | Always latest | `docs/BRIEF-vN.md` |
| PLAN.md | Active parts only | `REPORTS/plan/vN-archive.md` |
| STATE.md | Active parts + summary | Previous Versions Summary table |
| CORRECTIONS.md | Open items only | Resolved items removed |
| Orchestrator LOG | `LOG-vN+1.md` | Previous logs stay |

### Why versioning matters

At v3.28, without archiving:
- PLAN.md has ~280 parts → context explosion
- STATE.md has ~280 rows → agents read irrelevant data
- BRIEF.md has 28 changelogs → noise

With archiving, agents always see: one brief, ~10 active parts, ~10 STATE rows. Same context as v1.

---

## When Things Go Wrong

| Situation | What to say |
|-----------|-------------|
| QA rejects a part | "Fix part 3" → developer-dev, then "Re-audit part 3" → qa-security-dev |
| 3 rejections same part | "Part 3 blocked, revise the plan" → architect-dev |
| Validator finds regressions | "Fix regressions from part 4" → developer-dev, then qa → validator-dev |
| Requirements changed | "Revise the brief" → briefer-dev, then data cycle if needed, then architect-dev |

### Bug Tickets (production bugs, user reports)

Bugs from outside the dev cycle (user reports, monitoring, QA in production):

**Small bug on existing part** (no architecture change):
```
1. Add bug to CORRECTIONS.md (you or the dev)
2. "Fix part N" → developer-dev FIX
3. "Re-audit part N" → qa-security-dev
4. "Validate part N" → validator-dev
```

**Bug revealing a design issue** (needs rethinking):
```
1. "Revise the brief" → briefer-dev REVISION (if requirements were wrong)
2. If data impacted: analyst → engineer cycle
3. "Revise the plan" → architect-dev REVISION
4. Normal dev cycle on new/de-validated parts
```

**Bug on data quality** (wrong results, missing data):
```
1. "Validate data quality" → data-engineer-dev VALIDATE
2. If FAIL: "Explore the data" → data-analyst-dev EXPLORE
3. data-engineer-dev UPDATE → architect-dev REVISION if parts impacted
```

Rule: **start at the level where the problem is**. Code bug → dev. Design bug → architect. Data bug → data cycle. Requirements bug → briefer.

---

## Reports

Every agent MUST write a structured report. Reports are proof of work — no report = task incomplete.

### Report Structure

```
REPORTS/
├── brief/
│   ├── creation.md                ← briefer-dev
│   └── revision-[N].md           ← briefer-dev REVISION
├── data/
│   ├── pre-explore.md             ← data-analyst-dev PRE-EXPLORE
│   ├── explore.md                 ← data-analyst-dev EXPLORE
│   ├── discover.md                ← data-analyst-dev DISCOVER
│   ├── feedback-[N].md            ← data-analyst-dev FEEDBACK
│   ├── structure-[N].md           ← data-engineer-dev STRUCTURE
│   ├── update-[N].md              ← data-engineer-dev UPDATE (delta + impacted parts)
│   ├── validation-[N].md          ← data-engineer-dev VALIDATE
│   └── optimization-[N].md        ← data-engineer-dev OPTIMIZE
├── plan/
│   ├── creation.md                ← architect-dev CREATION
│   ├── revision-[N].md            ← architect-dev REVISION
│   └── vN-archive.md             ← architect-dev (version archives)
├── parts/
│   └── part-[N]/
│       ├── dev.md                 ← developer-dev BUILD
│       ├── dev-fix-[M].md         ← developer-dev FIX
│       ├── qa.md                  ← qa-security-dev AUDIT
│       ├── qa-reaudit-[M].md      ← qa-security-dev RE-AUDIT
│       └── validation.md          ← validator-dev
├── refactoring/
│   └── cycle-[N].md               ← tech-lead-dev
├── docs/
│   └── documentation-final.md     ← documentalist-dev
└── orchestrator/
    ├── LOG-v1.md                  ← orchestrator log (per version)
    └── part-[N]/
        └── *.log                  ← raw agent stdout (debug only)
```

**Rules:**
- `REPORTS/orchestrator/*.log` = raw stdout redirected by orchestrator. Debug only, not reports.
- `REPORTS/*/` (except orchestrator logs) = structured reports written by agents via Write tool.
- Fixed naming — agents do not invent report names.
- Reports are written BEFORE STATE.md updates.

---

## Quick Reference — All Agents

| Situation | Agent | What to say |
|-----------|-------|-------------|
| New project | briefer-dev | "I want to build [X]" |
| Generate/revise plan | architect-dev | "Read the brief and generate the plan" |
| Analyze data (no DB) | data-analyst-dev | "Pre-explore the data sources" |
| Analyze data (DB accessible) | data-analyst-dev | "Explore the data" |
| Find enrichment sources | data-analyst-dev | "Search for enrichment sources" |
| Design data architecture | data-engineer-dev | "Structure the data model" |
| Validate data quality | data-engineer-dev | "Validate data quality" |
| Build a part | developer-dev | "Build part N" |
| Fix a part | developer-dev | "Fix part N" |
| Audit a part | qa-security-dev | "Audit part N" |
| Validate a part | validator-dev | "Validate part N" |
| Refactoring | tech-lead-dev | "Refactoring after parts X to Y" |
| Update docs | documentalist-dev | "Update docs" |
| Final docs | documentalist-dev | "Full documentation for submission" |
| Personal report | documentalist-dev | "Rapport technique personnel" |
| Check progress | — | `cat STATE.md` |
| Check bugs | — | `cat CORRECTIONS.md` |

---

## Slash Commands

```
/data:pre-explore    → data-analyst-dev PRE-EXPLORE
/data:explore        → data-analyst-dev EXPLORE
/data:discover       → data-analyst-dev DISCOVER
/data:feedback       → data-analyst-dev FEEDBACK
/data:validate       → data-engineer-dev VALIDATE
/data:update         → data-engineer-dev UPDATE
/data:structure      → data-engineer-dev STRUCTURE
/data:optimize       → data-engineer-dev OPTIMIZE
/status              → show STATE.md progress
/plan                → show PLAN.md next steps
/corrections         → show open bugs
```

---

## Model Selection

**Simple rule: Opus + max effort for thinking. Sonnet + high for doing.**

| Opus (max) | Sonnet (high) |
|------------|---------------|
| briefer-dev | developer-dev |
| architect-dev | qa-security-dev |
| data-analyst-dev DISCOVER/PRE-EXPLORE | data-analyst-dev EXPLORE |
| data-engineer-dev STRUCTURE | data-engineer-dev VALIDATE/UPDATE |
| documentalist-dev PERSONAL REPORT | documentalist-dev UPDATE/FULL |
| | tech-lead-dev |
| | validator-dev |

---

## MCP Servers (optional)

```bash
# SQL access (data-analyst-dev EXPLORE, data-engineer-dev)
claude mcp add postgres -- npx -y @modelcontextprotocol/server-postgres

# Deep discovery (data-analyst-dev DISCOVER)
claude mcp add sequential-thinking -- npx -y mcp-sequentialthinking-tools
claude mcp add firecrawl -- npx -y firecrawl-mcp
claude mcp add memory -- npx -y @modelcontextprotocol/server-memory

# GitHub (developer-dev)
claude mcp add github -- npx -y @modelcontextprotocol/server-github
```

---

## Coordination Files

| File | Created by | Purpose |
|------|-----------|---------|
| `CLAUDE.md` | Architect-dev (from template, user validates) | Project conventions (all agents read this) |
| `docs/BRIEF.md` | Briefer-dev | Requirements (current state, no history — change tracking in REPORTS/brief/) |
| `docs/BRIEF-vN.md` | Architect-dev (archive) | Previous version briefs |
| `docs/DATA_SPEC.md` | Data-analyst-dev | Business data analysis & spec |
| `docs/DATA_SOURCES.md` | Data-engineer-dev | Technical data architecture (ERD, schemas, lineage) |
| `docs/ENRICHMENT_CATALOG.md` | Data-analyst-dev | Auxiliary sources catalog |
| `PLAN.md` | Architect-dev | Active parts only |
| `STATE.md` | Architect-dev | Live progress + version history |
| `CORRECTIONS.md` | Architect-dev | Open bugs only (validator removes fixed items after validation) |

---

## File Lifecycle — Keeping Context Lean

Over months, a project generates thousands of reports and hundreds of parts. Every file must stay lean.

**Working files = current state only, no history:**

| File | Stays lean because | History lives in |
|------|-------------------|-----------------|
| `docs/BRIEF.md` | Updated in-place, no changelog | `REPORTS/brief/revision-[N].md` |
| `PLAN.md` | Active parts only (archived on version bump) | `REPORTS/plan/vN-archive.md` |
| `STATE.md` | Active parts + summary table (archived parts = 1 row) | Previous Versions Summary |
| `CORRECTIONS.md` | Open items only (validator removes fixed items) | QA/fix reports in `REPORTS/parts/` |
| `docs/DATA_SPEC.md` | Cumulative but scoped (current data analysis) | `REPORTS/data/explore.md` |
| `docs/DATA_SOURCES.md` | Cumulative but scoped (current architecture) | `REPORTS/data/structure-[N].md` |

**Reports = write once, read selectively:**
- Agents do NOT read "all reports". Each agent reads only specific reports relevant to their task.
- Orchestrator logs (`REPORTS/orchestrator/*.log`) are debug artifacts — no agent reads them.
- Reports are proof of work and audit trail. They grow indefinitely but never pollute agent context.

**Version bumps clean everything:**
On STRUCTURAL revision, the architect archives validated parts and resets working files. This prevents unbounded growth.

---

## Golden Rules

1. **Check STATE.md before working** — know where you are
2. **One agent at a time** — never mix roles
3. **Respect the order** — dev → qa → validator, never skip
4. **Every agent writes a report** — mandatory, fixed path, before STATE.md update
5. **3 rejections max** — escalate to architect-dev
6. **Developer-dev only codes what's in PLAN.md** — no freelancing
7. **`/clear` between agents** — polluted context = confused agent
8. **Commit after each agent** — clean git history

---

## Skills

Standalone workflow disciplines loaded by agents automatically. You don't invoke skills — the right agent loads the right skills.

| Skill | File | Loaded by | When |
|-------|------|-----------|------|
| TDD | `skills/tdd.md` | developer-dev, tech-lead-dev | Always |
| Systematic Debugging | `skills/systematic-debugging.md` | developer-dev | FIX mode only |
| Verification | `skills/verification.md` | qa-security-dev, validator-dev | Always |
| Red Flags | `skills/red-flags.md` | developer-dev, qa-security-dev, validator-dev, tech-lead-dev | Always |

Skills are reference documents — agents read them at session start and follow their rules during execution. To add a new skill: create `skills/[name].md`, then add it to the relevant agent's "Files to Read FIRST" section.

---

## Adapting to a New Project

1. Copy `.claude/agents/` + `CLAUDE.md.template` to your project
2. Fill in CLAUDE.md (stack, naming, conventions)
3. (Optional) Configure MCP servers
4. "I want to build [X]" → briefer-dev starts
5. Follow the workflow

---

## Maintenance

Monthly: review agent definitions and update based on usage feedback.
Quarterly: full framework audit — check workflow coherence, report structure, and agent alignment.
