# UPDATE-FRONT — Frontend Workflow Evolution (V2)

This file documents the planned evolution to support frontend development. Do NOT implement — reference only for a future session.

---

## PROBLEM

The current Agent_Workflow framework is optimized for backend and data work. The full cycle (developer → qa-security → validator) assumes every change can be tested with automated tests. Frontend work breaks this assumption:

1. **Visual iteration is fast and subjective** — "move this left", "make it bigger", "wrong color" are not bugs, they're design feedback. Running the full QA cycle for each visual tweak is wasteful.
2. **QA cannot automate visual validation** — QA tests logic, security, edge cases. "Does this look right?" is a human judgment call.
3. **The developer agent is backend-specialized** — no built-in mechanism to show renders, capture visual feedback, or iterate on UI.

---

## THREE PROBLEMS TO SOLVE

### 1. Visual Companion — Show renders during development

**What:** A local browser companion that displays the frontend output while the developer works in the terminal. Based on Superpowers' brainstorming visual companion.

**How it works:**
- Zero-dependency Node.js server (~350 lines) serves HTML from a session directory
- Developer writes/updates frontend code → server auto-reloads browser
- User sees the render, provides feedback in the terminal
- User can click on elements with `data-choice` attributes → clicks captured in `.events` file (JSONL)
- Developer reads `.events` + terminal feedback → iterates

**Technical reference:** `superpowers/skills/brainstorming/scripts/`
- `server.cjs` — HTTP + WebSocket server, file watching, zero dependencies
- `helper.js` — browser client, click capture, reload handling
- `frame-template.html` — UI frame with dark/light theming, responsive CSS
- `start-server.sh` — session management, port selection, directory setup
- `stop-server.sh` — graceful shutdown

**Key architecture decisions:**
- Non-blocking: terminal always available, browser is display-only
- Session isolation: unique directory per session, no conflicts
- File-based communication: developer writes HTML → server detects → browser reloads → user clicks → .events file → developer reads
- Platform: requires Node.js, works on WSL2/Linux/Mac

**Integration as skill:** `skills/visual-companion.md` loaded conditionally by developer and briefer when the project has a frontend component.

### 2. Lightweight frontend cycle — Skip full QA for visual iterations

**Problem:** The current cycle is:
```
developer (BUILD) → qa-security (AUDIT) → validator (VALIDATE)
```

For frontend visual work, most iterations are cosmetic — layout, spacing, colors, typography. Running QA + validator for each is overkill.

**Proposed solution — Two-tier cycle for frontend parts:**

```
Tier 1 — Visual iteration (lightweight):
  developer builds → user validates visually (via visual companion) → developer adjusts
  Repeat until user says "looks good"
  No QA, no validator — this is design iteration, not code validation

Tier 2 — Logic + integration (full cycle):
  developer finalizes → qa-security audits (logic, security, accessibility) → validator validates
  Same as backend — runs once after visual iteration is complete
```

**How the architect would mark this:**
```markdown
### Part 7: Dashboard Layout
- Type: frontend-visual
- Cycle: Tier 1 (visual iteration) → Tier 2 (full audit after approval)
- Acceptance Criteria:
  - Visual: [user approves layout via visual companion]
  - Logic: [data loads correctly, filters work, responsive on mobile]
  - Security: [no XSS in dynamic content, API calls authenticated]
```

The architect splits visual criteria (Tier 1) from logic criteria (Tier 2). Developer iterates on visual with user, then QA audits the logic.

**Open questions:**
- Does the developer need a new mode (e.g., BUILD-VISUAL) or just a modified BUILD?
- How does STATE.md track Tier 1 progress? (visual approved by user vs QA approved)
- Should there be a visual sign-off step in STATE.md?

### 3. QA for frontend — What can be tested

QA-security currently tests: logic, security, edge cases, data quality. For frontend, additional checks are possible:

**Automatable (QA can do):**
- Accessibility (a11y): ARIA labels, contrast ratios, keyboard navigation
- Responsive: renders correctly at standard breakpoints
- XSS: no unsanitized user input in DOM
- Performance: bundle size, lighthouse score, no N+1 API calls
- Cross-browser: basic rendering checks

**Not automatable (user must do):**
- Visual correctness ("does it look right?")
- UX flow ("is this intuitive?")
- Design consistency ("matches the mockup?")

**Proposed QA Step 1 extension for frontend parts:**
```markdown
### For frontend-visual parts, add to Step 2 — Security Checklist:
- **XSS**: no innerHTML with user data, sanitized outputs, CSP headers
- **Accessibility**: ARIA labels on interactive elements, contrast ratio ≥ 4.5:1, keyboard navigable
- **Performance**: bundle size < [threshold], no blocking scripts, images optimized
- **Responsive**: tested at 320px, 768px, 1024px, 1440px breakpoints
```

---

## IMPLEMENTATION PLAN (for future session)

### Phase 1 — Visual Companion Skill
1. Copy and adapt server scripts from `superpowers/skills/brainstorming/scripts/` into `scripts/visual-companion/`
2. Create `skills/visual-companion.md` with usage instructions, startup commands, content patterns
3. Add conditional loading to `agents/developer.md`: load when project has frontend (detected from CLAUDE.md stack)
4. Add conditional loading to `agents/briefer.md`: offer when discussing UI/UX

### Phase 2 — Lightweight Frontend Cycle
1. Define `frontend-visual` part type in architect's decomposition rules
2. Add BUILD-VISUAL mode to developer (or modify BUILD to support visual iteration)
3. Add visual sign-off tracking in STATE.md
4. Define Tier 1 → Tier 2 transition rules

### Phase 3 — QA Frontend Extension
1. Add frontend-specific checks to qa-security (a11y, XSS, responsive, performance)
2. Define what QA audits vs what user validates
3. Update severity classification for frontend issues

### Phase 4 — Documentation
1. Update AGENTS_USAGE.md with frontend workflow
2. Update CLAUDE.md.template with frontend conventions section
3. Add frontend examples to "When Things Go Wrong" table

---

## DEPENDENCIES

- Node.js must be available (for visual companion server)
- Framework V1 update must be complete first (skills system, agent modifications)
- superpowers/ directory must still be available as reference for server scripts

---

## REFERENCE FILES IN SUPERPOWERS

| File | Purpose |
|------|---------|
| `superpowers/skills/brainstorming/scripts/server.cjs` | Zero-dep HTTP/WebSocket server |
| `superpowers/skills/brainstorming/scripts/helper.js` | Browser client for click capture |
| `superpowers/skills/brainstorming/scripts/frame-template.html` | UI frame with CSS patterns |
| `superpowers/skills/brainstorming/scripts/start-server.sh` | Server launch with session management |
| `superpowers/skills/brainstorming/scripts/stop-server.sh` | Graceful shutdown |
| `superpowers/skills/brainstorming/visual-companion.md` | Usage guide for agents |
| `superpowers/tests/brainstorm-server/server.test.js` | Integration tests |
| `superpowers/tests/brainstorm-server/ws-protocol.test.js` | WebSocket protocol tests |
