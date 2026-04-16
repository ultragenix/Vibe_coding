# Agent Workflow

Multi-agent framework for Claude Code. 9 specialized agents, zero role confusion, full traceability.

## Vision

A fully autonomous dev team that follows a software project through its **entire lifecycle**: from initial spec to production deployment, through bug fixes, feature additions, and version evolution over months and years.

**Current state**: spec, planning, development, QA, validation, refactoring, documentation — all automated with human oversight on key decisions.

**Target**: containerized testing, automated deployment to preprod/prod, ticket ingestion, autonomous bug resolution, live monitoring — a 100% autonomous development team that ships, monitors, and maintains software end-to-end.

---

## The Cycle

```
briefer-dev → [data-analyst-dev → data-engineer-dev] → architect-dev → [dev → qa → validator] × N parts
```

Data cycle runs only when the brief mentions data sources. Otherwise: `briefer-dev → architect-dev → orchestrate`.

Maintenance: `tech-lead-dev` (refactoring every 3 parts) → `documentalist-dev` (docs).

One agent per session. `/clear` between agents. They communicate through files, not context.

---

## Agents

| Agent | Role | Model | Output |
|-------|------|-------|--------|
| **briefer-dev** | Consults, evaluates tools, recommends architecture | Opus | `docs/BRIEF.md` |
| **architect-dev** | Decomposes into testable parts, manages versions | Opus | `PLAN.md`, `STATE.md` |
| **data-analyst-dev** | Profiles data, discovers enrichment, writes business spec | Sonnet/Opus | `docs/DATA_SPEC.md`, `docs/ENRICHMENT_CATALOG.md` |
| **data-engineer-dev** | Designs data architecture, validates quality | Sonnet/Opus | `docs/DATA_SOURCES.md` |
| **developer-dev** | Implements one part at a time with TDD | Sonnet | Code + `REPORTS/parts/part-[N]/dev.md` |
| **qa-security-dev** | Independent tests, security audit, data quality | Sonnet | `REPORTS/parts/part-[N]/qa.md` |
| **validator-dev** | Integration tests, regression checks, final gate | Sonnet | `REPORTS/parts/part-[N]/validation.md` |
| **tech-lead-dev** | Refactoring, performance, tech debt | Sonnet | `REPORTS/refactoring/cycle-[N].md` |
| **documentalist-dev** | README, guides, technical reports | Sonnet | Project documentation |

**Rule: Opus + max effort for thinking. Sonnet + high for doing.**

---

## Quick Start

```bash
# Copy agents + template to your project
cp -r agents/ your-project/.claude/agents/
cp templates/CLAUDE.md.template your-project/CLAUDE.md

# Edit CLAUDE.md with your stack and conventions
cd your-project && claude

# Go
> "I want to build [X]"                    → briefer-dev
> "Read the brief and generate the plan"   → architect-dev
> "Build part 1"                           → developer-dev
```

See `AGENTS_USAGE.md` for the complete workflow and all commands.

---

## Coordination Files

```
project/
├── .claude/agents/           # Agent definitions
├── CLAUDE.md                 # Project conventions (you write)
├── PLAN.md                   # Active parts only (architect-dev)
├── STATE.md                  # Current progress + version history (all agents)
├── CORRECTIONS.md            # Open bugs only (QA adds, validator cleans)
├── docs/
│   ├── BRIEF.md              # Requirements — current state, no history (briefer-dev)
│   ├── BRIEF-vN.md           # Archived briefs per version (architect-dev)
│   ├── DATA_SPEC.md          # Business data analysis (data-analyst-dev)
│   ├── DATA_SOURCES.md       # Technical data architecture (data-engineer-dev)
│   └── ENRICHMENT_CATALOG.md # Auxiliary sources (data-analyst-dev)
└── REPORTS/                  # Structured reports per agent, per step
```

All working files stay lean over the project lifecycle. History lives in REPORTS/, not in working files.

---

## Golden Rules

1. Check STATE.md before working — know where you are
2. One agent at a time — never mix roles
3. Respect the order — dev → qa → validator
4. Every agent writes a report — mandatory, fixed path
5. 3 rejections max → escalate to architect-dev
6. Developer-dev only codes what's in PLAN.md
7. `/clear` between agents — polluted context = confused agent
8. Commit after each agent — clean git history

---

## Repo Structure

```
Agent_Workflow/
├── agents/           # 9 agent definitions
├── commands/         # Orchestrator (automated dev loop)
├── skills/           # Standalone skills (TDD, debugging, verification)
├── templates/        # CLAUDE.md.template, .gitignore.example
├── AGENTS_USAGE.md   # Complete user guide
└── README.md         # This file
```

## Maintenance

Monthly: review agent definitions and update based on usage feedback.
Quarterly: full framework audit — check workflow coherence, report structure, and agent alignment.

---

## License

Private repository. Agent definitions are proprietary.
