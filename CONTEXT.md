# CONTEXT.md — Mr. Favors' Regents Lock-In
> Read this file before changing the game. Keep DESIGN_BRIEF fidelity,
> the ephemeral session model, and the exam-paper aesthetic intact.

Version: 1.2 | Status: Classroom Ready
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
| Current Status | Classroom Ready — deployed to gh-pages 2026-05-15; 72/72 questions; Sessions A–K + J + L complete. All 161 walkthroughs are multi-step click-to-reveal arrays. Session M (parallel examples) PRD written — logic pass is next. |

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
LENSES            → 10 static strategy objects; keystrokes, example, standards, pitfall
STANDARD_CLUSTERS → 11 cluster objects mapping human-readable labels to standard code arrays
QUESTION_BANK     → question objects; choices use label:'1'–'4', correctAnswer:'1'–'4'
ANALYTICS         → anonymous FAVORit schema; reads Worker URL from localStorage('favoritWorkerUrl') — URL never in committed code
STATE             → UI state machine: view, selectedLensId, practice{subView,drillMode,filterStandard,filterLens,wrongPool,...}, challenge{attemptCount,questionStartTime,...}
UI_RENDER         → all view renderers including practice sub-views (see Screen Flow)
UI_HANDLERS       → navigate(), selectLens(), practice handlers, challenge + timer handlers, CSV download
INIT              → nav wiring, session-id footer, boot ANALYTICS.log
```

### Screen Flow

```
home → library → lensDetail → library → home
home → practice (subView='home') →
  Free Roam: startPractice('free') → [lens-pick → walkthrough → answer → review] loop → practiceComplete
  Standard Drill: renderStandardPicker() → startPractice('standard', clusterKey) → [lens-pick → walkthrough → answer → review] loop
  Lens Drill: renderLensPicker() → startPractice('lens', lensId) → [answer → review] loop (lens-pick collapsed; rail hint = lens tagline)
home → challenge:
  start screen → startChallenge() → sampleChallenge(attemptCount) draws 24 Qs
    → [question + 90s timer] × 24 → renderChallengeResults() → auto-downloads CSV → home
```

### Key Functions

| Function | What it does |
|----------|-------------|
| `choiceLayout(q)` | Returns `'grid'` or `'stack'` based on choice text length (HTML-stripped) |
| `sampleChallenge(attemptCount)` | Returns array of 24 question IDs: balanced by Jan 2026 Regents standard dist if `attemptCount===0`, random otherwise |
| `renderPractice()` | Branches on `STATE.practice.subView`: home/standard-picker/lens-picker/active |
| `renderPracticeHome()` | Three entry-point cards: Free Roam, Standard Drill, Lens Drill |
| `renderStandardPicker()` | Lists 11 STANDARD_CLUSTERS with question counts; calls `startPractice('standard', key)` |
| `renderLensPicker()` | Lists 10 lenses with applicable question counts; calls `startPractice('lens', id)` |
| `startPractice(drillMode, filter)` | Builds filtered pool, shuffles, sets `STATE.practice.subView = 'active'`, calls `render()` |
| `renderChallenge()` | Renders timer bar + choices; delegates to `renderChallengeResults()` when done |
| `startChallengeTimer()` | Sets `questionStartTime = Date.now()`; `setInterval` at 1s; updates `#timer-bar` and `#timer-text` |
| `challengePickAnswer(label)` | Sets `_challengeSelectedChoice`, calls `render()` — no DOM mutation |
| `generateCSV()` | Builds CSV string from `STATE.challenge.answers` (9 columns) |
| `downloadCSV()` | Creates Blob URL, triggers `<a>` click, cleans up — auto-called 400ms after `renderChallengeResults()` |
| `startWrongAnswerDrill()` | Reads `STATE.challenge.answers`, builds `STATE.practice.wrongPool` from missed questions, sets `STATE.view='practice'`, calls `startPractice('wrong')` |
| `ANALYTICS.log(type, payload)` | Pushes to `ANALYTICS.events[]`, console, and fires POST to hardcoded Apps Script endpoint (private repo — URL committed). `localStorage('favoritWorkerUrl')` overrides if set. Events: `boot`, `view_change`, `lens_view`, `lens_pick`, `mc_attempt`, `challenge_attempt`, `challenge_complete`. `mc_attempt` + `challenge_attempt` carry: `qid`, `standard`, `stem`, `picked_choice`, `picked_text`, `correct_answer`, `correct_text`, `correct`, `best_lens`, `best_lens_name`. `challenge_complete` carries: `total`, `correct`, `score_pct`. |
| `showRail(visible, showHint)` | Shows/hides `#persistent-rail`; optionally hides `#lens-hint-card`. `true/true` in practice active, `true/false` on active challenge question, `false/false` everywhere else |

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

- FAVORit POST is live and active — Apps Script endpoint hardcoded in `ANALYTICS.workerUrl` (repo is private; FERPA_RULES waived for private deployment). Sheet ID: `11tzYXMZim5gq_rJ9qBhL_PeuQgMIxbuSKbqUPYWC1jE`. Tracker script: `scripts/sheets-tracker.gs`. `localStorage('favoritWorkerUrl')` overrides the hardcoded URL if set on a device.
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

- 2026-05-19 (Session M — Logic Pass) — Three-way walkthrough branch wired in `renderPractice()` Phase 2: `examples[guess]` exists → click-to-reveal parallel example; `isValid` → neutral "No worked example — give it a try" + Answer button; `!isValid` → nudge to bestLens + re-pick only. Universal review rule: `!correct &&` replaces `isLensDrill &&` — wrong answers see `walkthroughs[lensId]` in ALL Practice modes; correct answers never see walkthrough (including Lens Drill, which previously always showed it). `Q.examples || {}` guard — no QUESTION_BANK changes needed. 72/72 runPuzzleTests() unaffected (data-only checks). Content pass (72 `examples` entries) is the next open task.
- 2026-05-19 (Session M — PRD) — Grill Me interview complete. Parallel example feature planned: `examples: { lensId: [...steps] }` field (pre-answer, setup-only, ends with "?"); `walkthroughs` moves to review phase, wrong answers only, universal across all modes; Challenge untouched; bestLens-only first pass. Mini-PRD in TASKS.md. WORKFLOW.md annotated.
- 2026-05-19 (Session L — Content Migration) — All 161 string walkthroughs converted to multi-step step arrays. Each lens follows strategy pattern. 13 apostrophes fixed. 72/72 pass. Deployed to mrfavors-lock-in.
- 2026-05-18 (Session L — Click-to-Reveal Walkthrough) — Walkthrough phase now reveals one step at a time. `STATE.practice.walkthroughStep` tracks current step. Step arrays show steps accumulated (Back/Next navigation); string walkthroughs normalized to single-element array ("Answer the question" shows immediately). Step counter ("Step N of M") shown only for multi-step walkthroughs. `nextWalkthroughStep()` / `prevWalkthroughStep()` handlers added. 72/72 pass.
- 2026-05-15 (Session J — Deployment) — main pushed (8 commits, Sessions I–K). `git subtree push --prefix games/regents-mc-trainer origin gh-pages` created gh-pages branch. GitHub Pages enabled → `https://dariusjfavors-eng.github.io/mrfavors-lock-in/`. COMMAND_CENTER.md updated: Build → Classroom Ready. Google Sites iframe QA pending (user action).
- 2026-05-15 (Session K — math() + Lens 3 step cards) — math() processing order fixed: ^{} and _{} now processed before renderFrac; fracs like \frac{2^{x+3}}{x^2-2} render correctly. Walkthrough renderer branches: array → .lens-steps bordered step cards; string → unchanged .ti-screen. 10 Lens 3 walkthroughs converted to step arrays (Q01 Q07 Q09 Q11 Q12 Q17 Q42 Q47 Q58 Q64). WORKFLOW.md documents processing order invariant + backward-compat migration pattern. 72/72 pass.
- 2026-05-13 (Session I + Analytics) — Session I Browser QA complete: all 17 checks green at 1366×768; `\sqrt{}` processing-order bug found and fixed in `math()` (Q62 `\frac{10}{\sqrt{2}}` now renders correctly). Google Apps Script tracker added (`scripts/sheets-tracker.gs`): `doPost()` writes to two tabs — Questions (one row per answered question) and Sessions (one row per Challenge completion). FAVORit endpoint hardcoded in `ANALYTICS.workerUrl` (private repo). `mc_attempt` and `challenge_attempt` enriched with `stem`, `picked_text`, `correct_text`, `best_lens_name`. New `challenge_complete` event fires in `renderChallengeResults()`. 72/72 pass.
- 2026-05-12 (Session H) — Wrong-answer drill added. `renderChallengeResults()` adds "Drill Mistakes (N)" button (hidden if score is perfect). `startWrongAnswerDrill()` reads `STATE.challenge.answers`, filters to wrong answers, builds `STATE.practice.wrongPool`, sets `STATE.view='practice'`, calls `startPractice('wrong')`. `startPractice()` extended: `drillMode='wrong'` uses pre-built `wrongPool` instead of filtering `QUESTION_BANK`. Lens-pick screen in wrong drill mode adds `.wrong-drill-strip` diagnostic (missed count + standard breakdown) and `.lens-highlight` bold styling on lenses that were `bestLens` for missed questions. `drillLabel` shows "Mistakes Drill" in question header. `renderPracticeComplete()` now uses `STATE.practice.queue.length` instead of hardcoded `QUESTION_BANK.length`. 72/72 pass.
- 2026-05-12 (Session G) — Practice Mode rework: `STATE.practice` gains `subView`, `drillMode`, `filterStandard`, `filterLens`; new renderers `renderPracticeHome()`, `renderStandardPicker()`, `renderLensPicker()`; `startPractice(drillMode, filter)` replaces zero-arg form. Lens Drill collapses lens-pick phase, pre-sets `lensGuess`, shows rail hint from start, shows walkthrough in review. `STATE.challenge` gains `attemptCount` and `questionStartTime`; `sampleChallenge(attemptCount)` replaces inline shuffle; `generateCSV()` + `downloadCSV()` added; `challengeLockAnswer()` and `challengeNext()` now push `timeSpent`, `stem`, `bestLens` into `C.answers`. CSV auto-downloads 400ms after `renderChallengeResults()`. 72/72 pass.
- 2026-05-11 (session 10) — Phase 2 Session F: math() renderer added. CSS classes .mfrac/.msqrt/.mcoef added; math() helper defined before QUESTION_BANK; choiceLayout() fixed to strip HTML before measuring length. Q05 choices (exponential ^t), Q18 stem (27^{2/3}), Q23 choices (1/3 slope fractions), Q34 stem (√3, √5), Q37 stem+choices (3/2, (1/2)^n sequences), Q38 stem (√25, √2), Q43 choices (surface area formula fractions), Q47 stem (√(x+1)+5), Q57 choices (radical expressions), Q58 stem (quadratic), Q62–Q67 (all fraction/radical/exponent questions) updated with math(). 72/72 pass.
- 2026-05-11 (session 9) — Phase 2 Session E: Q72 authored (exam Q23, F.IF.4 — axis of symmetry x=2). Choice (1) uses svgGraph() with f(x)=x²−2x−3 and f(x) y-axis label; choice (3) uses svgTable() for g(x) values. Renderer confirmed innerHTML-safe — no renderer change needed. Answer: (4) h(x)=x²−4x−5, verified against official scoring key. 72/72 pass. June 2025 Part I complete.
- 2026-05-11 (session 8) — Browser QA complete: all 13 SVG questions (Q59–Q71) verified in Practice Mode and Challenge Mode at 1366×768. Open circle/closed dot endpoints confirmed on Q70. Four-graph VLT panels confirmed on Q71. HTTP server killed. Session E open: Q23 (axis of symmetry, SVG in choices — renderer check needed).
- 2026-05-11 (session 7) — Phase 2 Session D: Grill Me interview completed (7 questions). 10 June 2025 Part I questions added: Q62–Q67 text/encoding-ambiguous (exam Qs 1,16,18,20,21,24; N.RN.2/3, F.IF.2, A.CED.4, A.REI.3, N.Q.1), Q68 (exam Q2, F.IF.4 — parabola svgGraph vertex x=3), Q69 (exam Q12, S.ID.2 — two box plots IIFE), Q70 (exam Q13, F.IF.1 — piecewise linear open/closed endpoints IIFE), Q71 (exam Q19, F.IF.1 — 4-graph VLT IIFE). Answers verified against official scoring key. 71/71 pass. Q23 deferred (SVG in choices — renderer check required). Browser QA at 1366×768 still open.
- 2026-05-11 (session 6) — Phase 2 Session C: Full audit of Q38–Q56 against actual Jan 2026 exam PDF; all corrected (Session B had fabricated content). Added Q57 (Jan Q5, N.RN.3), Q58 (Jan Q17, A.REI.4), Q59 (Jan Q1, F.IF.4 — parabola SVG via `svgGraph()`), Q60 (Jan Q7, F.IF.1 — 4-graph IIFE SVG), Q61 (Jan Q11, A.REI.3 — 4-number-line IIFE SVG). Q46 choices use `svgTable()` for 4 exponential decay tables. Q55 stem corrected to standard→vertex form direction. 61/61 pass.
- 2026-05-10 (session 5) — Phase 2 Session B: 19 January 2026 Part I questions added verbatim (Q38–Q56). Q50/Q53 use `svgTable()` in stem. 56/56 pass.
- 2026-05-10 (session 4) — Phase 2 Session A: 6 SVG helpers authored (`svgPlane`, `svgGraph`, `svgTable`, `svgScatter`, `svgBoxPlot`, `svgBarGraph`) with toX/toY accuracy protocol. 13 June 2025 questions added verbatim (Q25–Q37). 37/37 pass.
- 2026-05-09 (session 2) — Persistent calculator pass: TI-84 iframe moved to static `#persistent-rail` outside `<main>` (no reload on question advance); calculator added to Challenge Mode; loading screen hardcoded "Mister Favors" / "HSES-ALG1". Layout: `.practice-wrap` grid removed; `#content-row` flex + `#persistent-rail` flex child added. `showRail()` helper added to UI_RENDER. 24/24 self-tests pass.
- 2026-05-09 — v1.0 shipped: white exam-paper theme, 10 lenses, 15 questions, Practice Mode, Challenge Mode with 90s per-question timer and per-standard results report. CONTEXT.md and TASKS.md written.
