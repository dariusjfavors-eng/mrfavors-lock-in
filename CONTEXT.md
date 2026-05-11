# CONTEXT.md — Mr. Favors' Regents Lock-In
> Read this file before changing the game. Keep DESIGN_BRIEF fidelity,
> the ephemeral session model, and the exam-paper aesthetic intact.

Version: 1.0 | Status: Build
Standard: A.REI, F.IF, F.LE, A.APR, A.SSE, A.CED, S.ID, F.BF | Folder: `games/regents-mc-trainer/`

---

## 1. PROJECT IDENTITY

| Field | Value |
|---|---|
| Game Title | Mr. Favors' Regents Lock-In — Algebra I MC Trainer |
| Standards | A.REI.3/4/6, F.IF.2/4/6, F.LE.1/2, A.APR.1, A.SSE.2/3, A.CED.1, S.ID.7, F.BF.1 |
| Mechanic | Student reads a Regents-style MC question, picks the best TI-84 lens, sees a walkthrough, then locks an answer with (1)(2)(3)(4) labels |
| Delivery Target | Single-file HTML → iframe embed in Google Sites |
| Framework | Vanilla JS only, zero build tools |
| Storage | None — session is ephemeral, no localStorage |
| PII Policy | None stored; FAVORit analytics are anonymous (sid = `anon_` + timestamp) |
| Current Status | Phase 2 Session E complete; 72/72 questions; Browser QA ✅ complete |

---

## 2. HARD CONSTRAINTS

- Single-file HTML only: inline CSS and JS, **no Google Fonts imports**.
- Font: `Arial, Helvetica, sans-serif` only — verified against NYS Regents PDF metadata.
- Background: `#FFFFFF` page white — **no dark theme, ever**.
- Choice labels: `(1)(2)(3)(4)` — **never A/B/C/D**.
- Choice layout: if any choice text > 40 chars → stacked vertical; else → 2-column grid with odd choices `(1)(3)` in left column, even `(2)(4)` in right column.
- No card elevation: no box-shadow, no border-radius on question cards.
- Feedback colors: correct `#1A5C1A`, incorrect `#8B0000`, hint/lens `#00008B`.
- No localStorage, no cookies, no session persistence.
- FAVORit analytics: `{ sid, ts, type, ...payload }` — no student names, IDs, or PII.

---

## 3. ARCHITECTURE OVERVIEW [STABLE]

### Module Map

```
LENSES          → 10 static strategy objects; keystrokes, example, standards, pitfall
QUESTION_BANK   → question objects; choices use label:'1'–'4', correctAnswer:'1'–'4'
ANALYTICS       → anonymous FAVORit schema; reads Worker URL from localStorage('favoritWorkerUrl') — URL never in committed code
STATE           → UI state machine: view, selectedLensId, practice{}, challenge{}
UI_RENDER       → all view renderers (see Screen Flow)
UI_HANDLERS     → navigate(), selectLens(), practice handlers, challenge + timer handlers
INIT            → nav wiring, session-id footer, boot ANALYTICS.log
```

### Screen Flow

```
home → library → lensDetail → library → home
home → practice:
  [lens-pick → walkthrough → answer → review] → next question loop
  → practiceComplete → home
home → challenge:
  start screen → [question + 90s timer] × N → challengeResults → home
```

### Key Functions

| Function | What it does |
|----------|-------------|
| `choiceLayout(q)` | Returns `'grid'` or `'stack'` based on choice text length |
| `renderPractice()` | Drives all 4 practice phases from `STATE.practice.phase` |
| `renderChallenge()` | Renders timer bar + choices; delegates to `renderChallengeResults()` when done |
| `startChallengeTimer()` | `setInterval` at 1s; updates `#timer-bar` and `#timer-text` by ID after re-render |
| `challengePickAnswer(label)` | Sets `_challengeSelectedChoice`, calls `render()` — no DOM mutation |
| `ANALYTICS.log(type, payload)` | Pushes to `ANALYTICS.events[]`, console, and fires POST if `localStorage('favoritWorkerUrl')` is set |
| `showRail(visible, showHint)` | Shows/hides `#persistent-rail` (the static TI-84 aside outside `<main>`); optionally hides `#lens-hint-card`. Called by all renderers: `true/true` in practice, `true/false` on active challenge question, `false/false` everywhere else |

### Choice Schema

```js
// All questions use this schema — labels are '1'/'2'/'3'/'4', never letters
{
  id: 'Q01',
  exam: 'Algebra I — Regents style',
  standard: 'A.REI.3',
  stem: '...',
  choices: [
    { label: '1', text: '...' },
    { label: '2', text: '...' },
    { label: '3', text: '...' },
    { label: '4', text: '...' }
  ],
  correctAnswer: '1',         // matches a label value
  applicableLenses: [1, 3],   // lens IDs that work
  bestLens: 3,                // fastest/most direct lens
  walkthroughs: { 1: '...', 3: '...' },
  errorTags: ['sign_error']
}
```

---

## 4. DO NOT TOUCH

- `(1)(2)(3)(4)` choice label format — never change to A/B/C/D
- `choiceLayout()` logic — the >40 char threshold and grid-auto-flow: column order match the actual Regents booklet
- `DESIGN_BRIEF.md` color palette (`#FFFFFF`, `#000000`, `#1A5C1A`, `#8B0000`, `#00008B`)
- `LENSES` array — 10 entries, structure stable; do not add a Lens 11 without a session plan
- FAVORit analytics schema — field names `sid`, `ts`, `type` are fixed
- Fixed `.page-header` strip — always visible, matches booklet header format
- `_challengeSelectedChoice` module-level variable — challenge mode relies on it surviving across `render()` calls

---

## 5. KNOWN ISSUES

- FAVORit POST is live but silent until `localStorage.setItem('favoritWorkerUrl', 'https://...')` is run once in the browser console on the host device — URL intentionally kept out of committed code per FERPA_RULES.md
- `window.runPuzzleTests()` is live — run in browser console to validate all questions; returns `{ passed, failed, total }`
- Phase 2 SVG questions not yet authored — `[TODO:Q]` marker in source flags the insertion point

---

## 6. ASSETS

Per `docs/ASSET_GUIDE.md` — all visual assets must be logged here with tier and delivery strategy.

| Asset | Tier | Status | Notes |
|-------|------|--------|-------|
| Inline SVG coordinate planes | T2 | 🔜 Planned — Phase 2 | `svgPlane()` helper; exam-paper style (black stroke, white fill) |
| Inline SVG function graphs | T2 | 🔜 Planned — Phase 2 | `svgGraph()` helper; accuracy protocol required |
| Inline SVG scatter plots | T2 | 🔜 Planned — Phase 2 | `svgScatter()` helper |
| Inline SVG box plots | T2 | 🔜 Planned — Phase 2 | `svgBoxPlot()` helper |
| Inline SVG bar graphs | T2 | 🔜 Planned — Phase 2 | `svgBarGraph()` helper |
| HTML data tables | T2 | 🔜 Planned — Phase 2 | `svgTable()` returns `<table>` string; S.ID.5 questions |

**Delivery:** Strategy C (inline SVG/HTML) — single-file constraint requires all assets embedded in `Q.stem`.
**Authorship note:** Math diagrams are exam content, not game art. T2 is correct — accuracy matters more than authorship here.

---

## 7. BACKGROUND SYSTEM

Pure white `#FFFFFF` — styled as NYS Regents exam paper.
Fixed `.page-header` strip at top (36px, `border-bottom: 1px solid #000`).
No star field, no orbs, no gradients, no texture. This is an intentional DESIGN_BRIEF override.

---

## 7. LAST SESSION LOG

- 2026-05-11 (session 9) — Phase 2 Session E: Q72 authored (exam Q23, F.IF.4 — axis of symmetry x=2). Choice (1) uses svgGraph() with f(x)=x²−2x−3 and f(x) y-axis label; choice (3) uses svgTable() for g(x) values. Renderer confirmed innerHTML-safe — no renderer change needed. Answer: (4) h(x)=x²−4x−5, verified against official scoring key. 72/72 pass. June 2025 Part I complete.
- 2026-05-11 (session 8) — Browser QA complete: all 13 SVG questions (Q59–Q71) verified in Practice Mode and Challenge Mode at 1366×768. Open circle/closed dot endpoints confirmed on Q70. Four-graph VLT panels confirmed on Q71. HTTP server killed. Session E open: Q23 (axis of symmetry, SVG in choices — renderer check needed).
- 2026-05-11 (session 7) — Phase 2 Session D: Grill Me interview completed (7 questions). 10 June 2025 Part I questions added: Q62–Q67 text/encoding-ambiguous (exam Qs 1,16,18,20,21,24; N.RN.2/3, F.IF.2, A.CED.4, A.REI.3, N.Q.1), Q68 (exam Q2, F.IF.4 — parabola svgGraph vertex x=3), Q69 (exam Q12, S.ID.2 — two box plots IIFE), Q70 (exam Q13, F.IF.1 — piecewise linear open/closed endpoints IIFE), Q71 (exam Q19, F.IF.1 — 4-graph VLT IIFE). Answers verified against official scoring key. 71/71 pass. Q23 deferred (SVG in choices — renderer check required). Browser QA at 1366×768 still open.
- 2026-05-11 (session 6) — Phase 2 Session C: Full audit of Q38–Q56 against actual Jan 2026 exam PDF; all corrected (Session B had fabricated content). Added Q57 (Jan Q5, N.RN.3), Q58 (Jan Q17, A.REI.4), Q59 (Jan Q1, F.IF.4 — parabola SVG via `svgGraph()`), Q60 (Jan Q7, F.IF.1 — 4-graph IIFE SVG), Q61 (Jan Q11, A.REI.3 — 4-number-line IIFE SVG). Q46 choices use `svgTable()` for 4 exponential decay tables. Q55 stem corrected to standard→vertex form direction. 61/61 pass.
- 2026-05-10 (session 5) — Phase 2 Session B: 19 January 2026 Part I questions added verbatim (Q38–Q56). Q50/Q53 use `svgTable()` in stem. 56/56 pass.
- 2026-05-10 (session 4) — Phase 2 Session A: 6 SVG helpers authored (`svgPlane`, `svgGraph`, `svgTable`, `svgScatter`, `svgBoxPlot`, `svgBarGraph`) with toX/toY accuracy protocol. 13 June 2025 questions added verbatim (Q25–Q37). 37/37 pass.
- 2026-05-09 (session 2) — Persistent calculator pass: TI-84 iframe moved to static `#persistent-rail` outside `<main>` (no reload on question advance); calculator added to Challenge Mode; loading screen hardcoded "Mister Favors" / "HSES-ALG1". Layout: `.practice-wrap` grid removed; `#content-row` flex + `#persistent-rail` flex child added. `showRail()` helper added to UI_RENDER. 24/24 self-tests pass.
- 2026-05-09 — v1.0 shipped: white exam-paper theme, 10 lenses, 15 questions, Practice Mode, Challenge Mode with 90s per-question timer and per-standard results report. CONTEXT.md and TASKS.md written.
