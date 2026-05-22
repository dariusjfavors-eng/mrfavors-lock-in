# TASKS — Mr. Favors' Regents Lock-In

Last updated: 2026-05-22 (Session P — August 2025 Regents Q73–Q96 added)

---

## Session P — August 2025 Question Bank Expansion ✅ COMPLETE 2026-05-22

**Design concept:** Add all 24 August 2025 Algebra I Regents Part I questions to QUESTION_BANK (Q73–Q96), sourcing walkthrough steps directly from the teacher's handwritten scratch work.

**Modules touched:** `QUESTION_BANK` only

**Interface changes:**
- Q73–Q96 appended after Q72
- `walkthroughs: { lensId: [...steps] }` — steps derived from teacher's Google Drive annotations
- `examples` field omitted on first pass (deferred to future content session)
- `source: 'August 2025 Algebra I Regents — Part I'` on all new questions

**Out of scope:** `examples` entries for new questions, UI changes, deployment

**Done when:** ✅ 96/96 runPuzzleTests() pass

**Open next:** Add `examples` entries for Q73–Q96 (second content pass); deploy to mrfavors-lock-in

---

## Session M — Parallel Example Walkthroughs — Logic Pass ✅ COMPLETE 2026-05-19 / Content Pass ⬜ OPEN

**Design concept:** Replace the pre-answer walkthrough (which currently solves the actual question) with a parallel example — a real past Regents question on the same standard — that ends on setup without revealing the answer. The actual solution walkthrough moves to the review phase, shown only on wrong answers, across all Practice modes. Challenge mode is untouched.

**Modules touched:** `QUESTION_BANK` (new `examples` field), `UI_RENDER` (walkthrough phase + review phase), `UI_HANDLERS` (no new handlers — reuses existing step navigation)

**Data model:**
- New field per question: `examples: { lensId: [...steps] }` — parallel example, pre-answer walkthrough phase
- Existing `walkthroughs: { lensId: [...steps] }` — actual solution, review phase wrong-answers only
- First pass scope: `bestLens` only per question (72 entries total — separate content session)
- Content rules: source from real past Regents exam (same standard, verbatim stem); 2–3 setup steps; NO computation; NO answer; final step always ends with `?`

**Walkthrough phase (pre-answer) — three-way split:**
| Lens pick | Student sees |
|---|---|
| `bestLens` + `examples[lensId]` exists | Click-to-reveal parallel example (ends on setup question) |
| Valid non-best lens | Neutral: "No worked example for this lens — give it a try." + Answer button |
| Invalid lens (bad fit) | Nudge: "Switch to [bestLens name] to see a worked example" + re-pick button |

**Review phase (post-answer) — universal rule:**
| Result | Student sees |
|---|---|
| Wrong answer | `walkthroughs[lensId]` actual solution — ALL Practice modes |
| Correct answer | No walkthrough — ALL Practice modes (suppresses current Lens Drill behavior) |

**Anti-hallucination rules for `examples` content:**
- Source every parallel example from a real past Regents question (NYSED-verified numbers)
- Final step must end with `?` (never a computed answer)
- No step may contain a declarative answer choice like "Answer: (3)"
- Authoring sequence: pick exam question → write setup steps → verify with TI-84

**Out of scope:**
- Writing the 72 `examples` entries (content pass — Session M-content, separate session)
- Any changes to Challenge mode
- CSS animations or transition effects
- Lens-pick screen changes
- `runPuzzleTests()` extension for parallel example validation

**Done when:**
- Walkthrough phase shows parallel example (click-to-reveal) for `bestLens` picks when `examples` entry exists
- Walkthrough phase shows neutral message + Answer button for valid non-best lens picks
- Walkthrough phase shows nudge + re-pick button for invalid lens picks
- Review phase shows actual solution (`walkthroughs`) for wrong answers in ALL Practice modes
- Review phase shows NO walkthrough for correct answers in ALL Practice modes (including Lens Drill)
- Challenge mode behavior unchanged
- 72/72 `runPuzzleTests()` pass

---

## Session L — Click-to-Reveal Walkthrough Steps — ✅ COMPLETE 2026-05-19

### Content migration: all 161 walkthroughs → step arrays ✅ COMPLETE 2026-05-19
All string walkthroughs across all 72 questions converted to multi-step arrays.
Each lens follows its strategy pattern (L1: enter/intersect, L2: graph/zeros, L3: plug/check,
L6: AC method, L7: vertex, L8: domain/range, L9: structural, L10: eliminate).
13 unescaped apostrophes in step strings fixed. 72/72 runPuzzleTests() pass.
Deployed to mrfavors-lock-in public repo (commit f129371).

---

## Session L — Click-to-Reveal Walkthrough Steps — Session PRD

**Design concept:** Walkthrough phase reveals one step at a time; student clicks "Next" / "Back" to navigate; on the last step "Next" becomes "Answer the Question." Step arrays get true multi-step reveal; string walkthroughs are normalized to a single-element array — consistent UX across all 72 questions with zero content migration required.
**Modules touched:** `STATE` (new `walkthroughStep` field), `UI_RENDER` (walkthrough phase renderer), `UI_HANDLERS` (two new step-navigation handlers + resets in `pickLens`, `resetLensPick`, `nextPracticeQ`, `startPractice`)
**Interface changes:**
- `STATE.practice` gains `walkthroughStep: 0` — reset to 0 whenever walkthrough phase is (re)entered or a new question loads
- Walkthrough renderer: normalizes `walkRaw` to `steps[]` (array → as-is; string → single-element); renders `steps[0..walkthroughStep]` accumulated; shows "Back" when `step > 0`; shows "Next" when not on last step; shows "Answer the Question" on last step
- `nextWalkthroughStep()` / `prevWalkthroughStep()` — new handlers; increment/decrement `walkthroughStep`, call `render()`
- "Now pick your answer →" button removed — replaced by "Answer the Question" on last step
- Step counter ("Step N of M") shown only when `steps.length > 1`
**Out of scope:** Converting more walkthroughs to step arrays, CSS transition animations, Lens Drill review walkthrough, Challenge mode (no walkthroughs there)
**Done when:** 72/72 `window.runPuzzleTests()` pass; string walkthroughs show full text + "Answer the Question" immediately; step-array walkthroughs reveal one step at a time with Back/Next; "Answer the Question" advances to answer phase; Re-pick lens still works; step counter shows "Step N of M" only for multi-step walkthroughs

---

## Session K — math() Bug Fix + Lens Step Cards — ✅ COMPLETE 2026-05-15

**Design concept:** (1) Fix `math()` renderer so fractions with superscripts/subscripts inside numerator/denominator display correctly instead of showing raw LaTeX. (2) Add bordered numbered step cards for Lens walkthroughs — backward-compatible: arrays render as cards, strings render as before. Convert all Lens 3 (bestLens) walkthroughs to step arrays as proof of concept.
**Modules touched:** `math()` function (processing order), `<style>` block (step card CSS), `UI_RENDER` (walkthrough renderer branch), `QUESTION_BANK` (Lens 3 walkthroughs for Q01, Q07, Q09, Q11, Q12, Q17, Q42, Q47, Q58, Q64)
**Interface changes:**
- `math()`: superscripts `^{}` and subscripts `_{}` now processed before `renderFrac` — fracs like `\frac{2^{x+3}}{x^2-2}` now render correctly
- `walkthroughs[lensId]` renderer: if array → `.lens-steps` bordered step cards; if string → unchanged `.ti-screen` paragraph
- 10 Lens 3 walkthroughs converted to step arrays (Q01, Q07, Q09, Q11, Q12, Q17, Q42, Q47, Q58, Q64)
- CSS added: `.lens-steps`, `.lens-step`, `.lens-step-num`, `.lens-step-body`
**Out of scope:** Lens 1, 2, 4–10 conversions (future sessions); click-to-reveal steps; screenshot QA workflow; variant drill mode
**Done when:** 72/72 `window.runPuzzleTests()` pass; Q58/Q64 fractions render correctly; Lens 3 step cards visible with bordered cards; string walkthroughs unchanged

---

## Google Sheets Tracker — ✅ COMPLETE 2026-05-13

**Design concept:** Real-time student response tracking via Google Apps Script Web App — every answered question writes a row to a "Questions" tab; every completed Challenge writes a row to a "Sessions" tab. No student setup required; endpoint hardcoded in game (private repo).
**Modules touched:** ANALYTICS (payload enrichment + hardcoded endpoint), new `scripts/sheets-tracker.gs`
**Interface changes:**
- `ANALYTICS.workerUrl` now hardcodes the Apps Script `/exec` URL as fallback (localStorage override still works)
- `mc_attempt` event enriched: adds `stem`, `picked_text`, `correct_text`, `best_lens`, `best_lens_name`
- `challenge_attempt` event enriched: adds `stem`, `picked_text`, `correct_text`, `best_lens_name`
- New `challenge_complete` event fires in `renderChallengeResults()`: `{ total, correct, score_pct }`
- `scripts/sheets-tracker.gs`: `doPost()` routes by `type`; creates Questions (15 cols) and Sessions (6 cols) tabs automatically
**Sheet:** `11tzYXMZim5gq_rJ9qBhL_PeuQgMIxbuSKbqUPYWC1jE` (blank sheet, dedicated tracker)
**Out of scope:** Practice-mode session summary rows, per-lens analytics, student identity
**Done when:** Questions tab receives rows with full text on every answered question; Sessions tab receives one row per Challenge completion; 72/72 pass

---

## Session H — Wrong-Answer Drill — Session PRD ✅ COMPLETE 2026-05-12

**Design concept:** After a Challenge attempt, students with wrong answers get a "Drill Mistakes" button that feeds only missed questions into the existing Practice Mode Free Roam loop; the lens-pick screen surfaces a diagnostic strip and bold hints on the most relevant lenses, but the student picks freely.
**Modules touched:** UI_RENDER (`renderChallengeResults()` + lens-pick screen), UI_HANDLERS (`startPractice()` extension + new `startWrongAnswerDrill()` handler)
**Interface changes:**
- `renderChallengeResults()` adds "Drill Mistakes" button — rendered only when wrong-answer count > 0
- `startPractice()` extended: accepts `drillMode: 'wrong'` with a pre-built pool array; bypasses `QUESTION_BANK` filter logic
- `STATE.practice` gains `wrongPool: []` — pre-built at drill start from `STATE.challenge.answers`; read-once, never mutated by the drill
- Lens-pick renderer in `drillMode === 'wrong'`: adds a diagnostic strip (missed count + standard breakdown) and bold class on lens rows whose id appears as `bestLens` on any missed question
**Out of scope:** Saving wrong-answer pool across sessions, new questions, filtering drill pool by standard/lens before entering, "you improved" comparison, deployment
**Done when:** 72/72 `window.runPuzzleTests()` pass; "Drill Mistakes" button absent on perfect Challenge score; button present and functional with wrong answers; bold lens hints render only in wrong-answer drill (`drillMode === 'wrong'`); Free Roam / Standard Drill / Lens Drill flows unchanged
**Also fixed:** `math()` helper bug — `\text{}` stripping moved before `renderFrac` so fractions containing `\text{ mi}` / `\text{ hr}` etc. render correctly (Q66 unit conversion choices). Root cause: `[^{}]*` in `renderFrac` regex rejected numerators/denominators containing braces from un-stripped `\text{}`.

---

## Session G — Challenge + Practice Mode Rework — Session PRD ✅ COMPLETE 2026-05-12

**Design concept:** Practice Mode gains two new entry points (standard/cluster drill and lens drill) alongside free roam; Challenge Mode draws 24 questions balanced by January 2026 Regents standards on first play, random on subsequent plays this session, and auto-downloads a CSV study guide on completion.
**Modules touched:** STATE (new fields on `challenge{}` and `practice{}`), UI_RENDER (new Practice home, lens-drill view with collapsible reference card, Challenge results with CSV trigger), UI_HANDLERS (new `sampleChallenge()`, `generateCSV()`, standard/lens picker handlers)
**Interface changes:**
- `STATE.challenge{}` gains `attemptCount: 0` (incremented each play; gates balanced vs. random draw; ephemeral, no localStorage)
- `STATE.practice{}` gains `drillMode: 'free' | 'standard' | 'lens'`, `filterStandard: string | null`, `filterLens: string | null`
- New `sampleChallenge(attemptCount)` → returns array of 24 question objects (balanced if `attemptCount === 0`, random otherwise)
- New `generateCSV(results)` → builds and auto-downloads CSV; columns: `sid, question_id, standard, student_answer, correct_answer, result, time_spent_seconds, question_stem, best_lens`
- Practice home renders three entry-point cards: Free Roam, Standard Drill (human-readable cluster picker), Lens Drill (lens picker)
- Lens drill skips lens-pick phase; collapsible `.lens-reference-card` available throughout answer + review phases
- Standard clusters: human-readable labels mapped to domain code arrays (e.g., "Quadratics" → [A.REI.4, A.APR.3, A.SSE.2])
**Out of scope:** Lens color/classification system, Lens 11+, Session H wrong-answer mode, new questions, deployment, DESIGN_BRIEF changes
**Done when:** 72/72 `window.runPuzzleTests()` pass; first Challenge play draws balanced 24; subsequent plays draw random 24; CSV auto-downloads on completion with all 9 columns; Practice home shows three entry points; standard picker uses human-readable labels; lens drill collapses lens-pick phase with collapsible reference card; no regression on existing flows

---

## Phase 2 Session F — Session PRD ✅ COMPLETE 2026-05-11
**Design concept:** Custom CSS renderer (not KaTeX) — `.mfrac`/`.msqrt`/`.mcoef` CSS classes + `math(latex)` helper; fix `choiceLayout()` to strip HTML before measuring length; update QUESTION_BANK in batches to replace plain-text math with properly rendered notation matching the NYS Regents exam look.
**Modules touched:** `<style>` block (`.mfrac`, `.msqrt`, `.mcoef` classes), `<script>` block (`math()` helper + `choiceLayout()` fix), QUESTION_BANK (Q05, Q18, Q23, Q34, Q37–Q38, Q43, Q47, Q57–Q58, Q62–Q67)
**Interface changes:** `math(latex)` helper returns inline HTML string; `choiceLayout(q)` now strips HTML tags before measuring; 14 questions updated with stacked fractions, radical overlines, superscript exponents
**Out of scope:** Challenge mode changes, new questions, new lenses, new modes, deployment
**Done when:** ✅ 72/72 pass; ✅ stacked fractions (Q67), radical overlines (Q62/Q63), superscript exponents (Q58); ✅ `choiceLayout()` still returns 'grid' for short-choice questions

---

## Phase 2 Session E — Session PRD
**Design concept:** Author June 2025 Q23 (axis of symmetry — SVG in choices) and any remaining June 2025 Part I questions; audit choice renderer first and patch UI_RENDER only if needed to support innerHTML in choices.
**Modules touched:** QUESTION_BANK primary; UI_RENDER (choice renderer) only if audit shows textContent is used
**Interface changes:** Q72 appended to QUESTION_BANK; renderer change (if needed) is minimal — one line, choice interpolation only
**Out of scope:** Part II–style MC, new lenses, new modes, CSS changes, deployment
**Done when:** `window.runPuzzleTests()` passes all questions; Q23 choice SVG renders correctly in Practice and Challenge modes; no regression on existing questions

---

## Phase 2 Session D — Session PRD
**Design concept:** Append 10 June 2025 Part I questions (6 text-only + 4 SVG image questions) to QUESTION_BANK, verified against PDF renders at 1366×768 in both Practice and Challenge modes.
**Modules touched:** QUESTION_BANK only
**Interface changes:** New question objects Q62–Q71 appended after Q61; text questions use Unicode math; SVG questions use existing helpers (`svgGraph` for Q2/Q13, IIFE multi-panel for Q12/Q19)
**Out of scope:** Q23 (SVG in choices — deferred), Part II–style MC, renderer/CSS/handler changes, deployment
**Done when:** `window.runPuzzleTests()` passes all questions; each SVG question visually verified at 1366×768 in Practice and Challenge modes against PDF renders

---

## Context

v1.0 shipped. Phase 2 Sessions A+B+C complete. The game now has 61 questions, 10 TI-84 lenses,
6 SVG helper functions, Practice Mode and Challenge Mode. `index.html` is ~2600+ lines,
single-file, vanilla JS, no Google Fonts. 61/61 `window.runPuzzleTests()` pass.

The FAVORit fetch() is wired — the Worker URL is set via `localStorage.setItem('favoritWorkerUrl','...')`
on the host device (never in code). No localStorage for session data; ephemeral by design.

Read CONTEXT.md before touching code. Pay close attention to the choice schema (`label: '1'–'4'`,
never letters) and `choiceLayout()` — both are DESIGN_BRIEF-verified and must not regress.

---

## Starting Checklist

1. Read `CONTEXT.md` fully before touching `index.html`
2. Read `docs/UBIQUITOUS_LANGUAGE.md` — shared domain model; use its terms in all prompts
3. Read `DESIGN_BRIEF.md` if you plan to change any visual element
4. Read `docs/DESIGN_SYSTEM.md` TIER 1 rules — required for any SVG/visual work (accessibility, iframe)
5. Read `docs/ASSET_GUIDE.md` — inline SVG is Strategy C; all SVG assets must be logged in CONTEXT.md ## Assets
6. Open `index.html` in browser and verify: white background, Arial font, `(1)(2)(3)(4)` labels visible
7. Search `[TODO:Q]` in source to find where new questions should be appended

---

## Session O — Question Picker (minimal pass) — ✅ COMPLETE 2026-05-22

**Design concept:** Students who finish a Challenge and want to drill a specific missed question tap "Practice a question" on the Challenge results screen, see a flat list of all 72 questions (Q-number · standard · stem preview), pick one, work through the full Practice loop (lens-pick → walkthrough → answer → review), and return to Practice Home when done.

*Why minimal: the primary use case is post-Challenge targeted remediation, which the results-screen entry point covers completely. Full scope deferred — see below.*

**Work type:** Feature — UI + State
**Modules touched:**
- `STATE` — `practice` gains `filterQuestion: null | qid`
- `UI_RENDER` — new `renderQuestionPicker()`; `renderChallengeResults()` (one new button); `renderPracticeComplete()` (single-mode route to Practice Home)
- `UI_HANDLERS` — `startPractice('single', qid)` path (pool of one)
- No `QUESTION_BANK` changes, no analytics changes, no new CSS classes required

**Interface changes:**
- Challenge results: "Practice a question" button → `renderQuestionPicker()`
- `renderQuestionPicker()`: flat list of all 72 questions sorted by Q-number; each row: `Q01 · A.REI.3 · stem preview ~80 chars` (stem stripped of HTML); click → `startPractice('single', qid)`
- `startPractice('single', qid)`: pool of one question; sets `STATE.practice.filterQuestion = qid`, `drillMode = 'single'`, `subView = 'active'`
- `renderPracticeComplete()` in `drillMode === 'single'`: "← Practice Home" replaces "Start Over"; routes to `subView = 'home'`

**Out of scope (this pass):**
- Practice Home 4th entry-point card ("Pick a Question")
- Back-to-picker routing after review (returns to Practice Home)
- Per-standard grouping or search in the picker
- Any changes to existing Practice modes or Challenge logic

**Full scope (future session — Grill Me required):**
- Practice Home 4th card
- Back-to-picker routing: review complete → picker (requires `subView: 'question-picker'`)
- Per-standard grouping or search filter in the flat list

**Done when:**
- "Practice a question" button visible on Challenge results screen
- Flat list renders all 72 questions with Q-number, standard, and stem preview
- Student picks one question → full lens-pick → walkthrough → answer → review loop runs
- Review complete → "← Practice Home" returns to Practice Mode home screen
- 72/72 `runPuzzleTests()` pass
- No regression on existing Practice flows or Challenge mode

---

## Open (priority order)

---

## Phase 2 — Question Bank Expansion (June 2025 + 2026 Regents, image-bearing questions)

> Grill Me pass was run 2026-05-10. SVG accuracy is non-negotiable — wrong graphs teach wrong math.

### Session PRD
**Design concept:** Expand QUESTION_BANK from 24 to ~84 questions using verbatim June 2026 + June 2025 NYS Algebra 1 Part I exams (post-shift format) and authored Part II–style MC; image-bearing questions use inline SVG derived from actual math — no eyeballing.
**Work type:** Content (question authoring) + Visual/Asset (inline SVG)
**Modules touched:** QUESTION_BANK only — zero renderer, CSS, or handler changes
**Interface changes:** `Q.stem` may now contain inline `<svg>` — existing interpolation handles it; no code changes needed
**Out of scope:** New lenses, new modes, new renderers, external image files, MathJax/KaTeX
**Done when:** `window.runPuzzleTests()` passes all questions; each image question renders SVG correctly in Practice and Challenge modes at 1366×768

### Starting Checklist
1. Pull June 2026 and June 2025 Algebra I Regents Part I PDFs from nysedregents.org
2. Author SVG helper functions before writing any image questions
3. Apply SVG accuracy protocol: derive pixel coords from math, verify every point
4. Run `window.runPuzzleTests()` after every batch of 6 new questions

### Open
- [ ] Part II–style MC: S.ID.1/2/5/6 (box plots, scatter plots, two-way frequency tables)
- [ ] Part II–style MC: F.IF.1/9, F.BF.3, A.REI.11 (graph-reading questions)

### Future Sessions (Grill Me required before starting)
- [x] **Session G** — Challenge + Practice Mode rework: Practice home with 3 entry points (free roam, standard drill, lens drill); Challenge draws 24 balanced by Jan 2026 standards on first play, random thereafter; CSV auto-download on Challenge completion — 2026-05-12
- [x] **Session H** — Wrong-answer drill: "Drill Mistakes (N)" button on Challenge results (hidden on perfect score); feeds missed questions into Practice Mode Free Roam loop; lens-pick screen shows diagnostic strip + bold/highlight for best-lens matches; `Mistakes Drill` label in question header; 72/72 pass — 2026-05-12
- [x] **Session I** — Browser QA at 1366×768 — all 17 checks pass; `\sqrt{}` processing-order bug found and fixed (Q62 stem `\frac{10}{\sqrt{2}}` now renders correctly); 72/72 pass — 2026-05-12
- [ ] **Session J** — Deployment pass (no Grill Me required — no new mechanics). GitHub Pages → Google Sites iframe → Cloudflare Worker → Classroom Ready.

---

## Session I — Browser QA Checklist ✅ COMPLETE 2026-05-12

> Verify all UI added in Sessions G and H at 1366×768 (Chromebook target). One bug found and fixed (`\sqrt{}` order in `math()`).

**Work type:** System — QA
**No Grill Me required.**
**Done when:** All checks below pass at 1366×768 with no visual regressions.

### Checks

**Practice Mode — Home**
- [x] Three entry-point cards render without overflow (Free Roam, Standard Drill, Lens Drill)
- [x] Standard Drill picker lists all 11 clusters with question counts
- [x] Lens Drill picker lists all 10 lenses with applicable question counts

**Practice Mode — Standard Drill**
- [x] Question loop runs correctly for at least one cluster (e.g. Quadratics)
- [x] `(1)(2)(3)(4)` labels visible; 2-col grid for short choices, stack for long

**Practice Mode — Lens Drill**
- [x] Lens-pick phase collapses correctly; rail hint shows lens tagline
- [x] Walkthrough appears in review phase

**Challenge Mode**
- [x] Start screen renders; 24-question draw runs
- [x] Timer bar visible; turns red at ≤30s
- [x] Results screen renders: score, per-standard table, all three buttons
- [x] CSV auto-downloads on completion (check Downloads folder)
- [x] "Drill Mistakes (N)" button appears after a non-perfect run
- [x] Perfect score (24/24) → no "Drill Mistakes" button; only Re-download CSV, Try Again, ← Home

**Mistakes Drill**
- [x] Clicking "Drill Mistakes" enters Practice Mode
- [x] `.wrong-drill-strip` diagnostic strip visible above lens grid
- [x] Highlighted lenses show blue border + tint
- [x] "Mistakes Drill" label in question header
- [x] Full lens-pick → walkthrough → answer → review loop completes

**Math rendering**
- [x] Stacked fractions render (e.g. Q67, Q43)
- [x] Unit-conversion fraction chains render (Q67 — `49 mi / 1 hr` choices)
- [x] Radical overlines render (Q62, Q63) — **bug fixed in this session**: `\sqrt{}` must be processed before `renderFrac`; Q62 `\frac{10}{\sqrt{2}}` now renders correctly
- [x] Superscript exponents render (Q58)

**Persistent rail**
- [x] TI-84 iframe loads and does not reload between questions in Practice
- [x] Calculator visible during active Challenge question; hidden on results screen

---

## Session J — Deployment Checklist

> Ship to production. No new features. Complete in order — each step gates the next.

**Work type:** System — Deployment
**No Grill Me required.**
**Done when:** Google Sites iframe QA passes at 1366×768 and COMMAND_CENTER.md shows "Classroom Ready."

### Steps

- [x] **1. GitHub Pages** — private repo; created public deploy repo `mrfavors-lock-in` at `https://github.com/dariusjfavors-eng/mrfavors-lock-in`; pushed `index.html` to main; Pages auto-enabled via API → `https://dariusjfavors-eng.github.io/mrfavors-lock-in/` — 2026-05-15
- [x] **2. Google Sites iframe QA** — functional QA passed at 1366×768 on live URL: fixed header ✅, white bg ✅, (1)(2)(3)(4) choices ✅, TI-84 persistent ✅, timer red at ≤30s ✅, CSV auto-downloads ✅, 4 analytics POSTs confirmed ✅. Embed in actual Google Sites page still recommended as a 30s sanity check before first classroom use — 2026-05-15
- [x] **3. Analytics endpoint** — Google Apps Script Web App deployed (`scripts/sheets-tracker.gs`); endpoint hardcoded in `ANALYTICS.workerUrl`; Questions + Sessions tabs live in tracking sheet — 2026-05-13
- [x] **4. Promote to Classroom Ready** — `docs/COMMAND_CENTER.md` updated: `Build` → `Classroom Ready` — 2026-05-15
### Done (Phase 2)
- [x] **Session F** — math() renderer: custom CSS (.mfrac/.msqrt/.mcoef) + helper function; choiceLayout() HTML-strip fix; 14 questions updated (Q05, Q18, Q23, Q34, Q37–Q38, Q43, Q47, Q57–Q58, Q62–Q67) with stacked fractions, radical overlines, superscript exponents; 72/72 pass — 2026-05-11
- [x] **Session E** — June 2025 Q23 (axis of symmetry — SVG graph in choice 1, svgTable in choice 3); renderer confirmed innerHTML-safe; no renderer change needed; 72/72 pass — 2026-05-11
- [x] Browser QA — SVG rendering at 1366×768 for Q59–Q71 in Practice and Challenge modes — 2026-05-11
- [x] Author `svgPlane()`, `svgGraph()`, `svgTable()`, `svgScatter()`, `svgBoxPlot()`, `svgBarGraph()` helpers — 2026-05-10
- [x] June 2025 Part I — text-only questions Q25–Q37 (exam Qs 3–11, 14, 15, 17, 22) — 2026-05-10
- [x] January 2026 Part I — text-only questions Q38–Q56 (exam Qs 2–4, 6, 8–10, 12–16, 18–24) — corrected from PDF 2026-05-11
- [x] January 2026 Part I — encoding-ambiguous (Q57 Jan Q5, Q58 Jan Q17) — 2026-05-11
- [x] January 2026 Part I — image questions Q59 (Jan Q1 parabola), Q60 (Jan Q7 4-graph), Q61 (Jan Q11 4-number-lines) — 2026-05-11
- [x] June 2025 Part I — text/encoding-ambiguous Q62–Q67 (exam Qs 1,16,18,20,21,24; N.RN.2/3, F.IF.2, A.CED.4, A.REI.3, N.Q.1) — 2026-05-11
- [x] June 2025 Part I — image questions Q68–Q71 (exam Qs 2,12,13,19; F.IF.4, S.ID.2, F.IF.1×2) — 2026-05-11

### Out of Scope (this phase)
- New lenses — 10 lenses are sufficient for all new questions
- New modes or renderers — zero code changes needed; SVG works in stem today
- External image files — inline SVG only, single-file constraint holds
- MathJax or KaTeX — custom CSS renderer chosen over KaTeX (264kb overhead rejected); math() helper is sufficient for Regents notation

---

## Phase 1 — Deployment

- [ ] **Deploy Cloudflare Worker** — activate FAVORit endpoint; paste URL via `localStorage.setItem('favoritWorkerUrl', 'https://...')` on host device
- [ ] **GitHub Pages deployment** — publish `games/regents-mc-trainer/index.html` so it can be embedded in Google Sites
- [ ] **Google Sites iframe QA** — test at 1366×768 inside an actual Google Sites iframe (not just standalone browser)
- [ ] **Promote to Classroom Ready** — update COMMAND_CENTER.md lifecycle stage once Worker + GitHub Pages are live
- [x] **Expand question bank to 24 questions** — Q16–Q24 added covering A.REI.4, A.REI.12, N.RN.2, F.IF.7, A.CED.2, A.APR.3, F.IF.5, G.GPE.5 — 2026-05-09
- [x] **Wire FAVORit Sheets endpoint** — fetch() is live; URL stored in localStorage (never in code); run `localStorage.setItem('favoritWorkerUrl', 'https://...')` once in the browser console on the host device — 2026-05-09
- [x] **Add `window.runPuzzleTests()`** — self-test hook verifies: (a) all 24 questions have exactly 4 choices with labels '1'–'4', (b) correctAnswer is one of the 4 labels, (c) bestLens is in applicableLenses; logs PASS/FAIL per question + summary to console — 2026-05-09
- [x] **Browser QA — Chromebook (1366×768)** — all five checks passed: fixed header visible, white #FFFFFF confirmed via computed style, 2-col choice grid (1)(3)/(2)(4) correct, TI-84 iframe loading, Challenge timer depletes and turns #8B0000 at ≤30s — 2026-05-09
- [x] **Register in COMMAND_CENTER.md** — moved `regents-mc-trainer` entry into COMMAND_CENTER.md — 2026-05-09
- [x] **Write UBIQUITOUS_LANGUAGE entries** — added Regents Lock-In Module Map (8 modules + interfaces) and Game Mechanics (19 terms: lens, choiceLayout, Practice/Challenge phases, FAVORit, _challengeSelectedChoice, Part I scope) — 2026-05-09

---

## Out of Scope (this milestone)

- Dark theme or any deviation from `#FFFFFF` background
- A/B/C/D answer labels — these are permanently banned per DESIGN_BRIEF
- localStorage or any session persistence
- Animated backgrounds, particles, orbs, or visual effects
- Gamification (streaks, coins, points, leaderboard)
- Hint system changes to Lens Library — stable, do not restructure
- New lens (Lens 11+) — 10 lenses are sufficient for Regents Part I coverage

---

## Blocked

- **Cloudflare Worker deployment** — the fetch() is wired and ready; the Worker itself still needs to be deployed and its URL set via `localStorage.setItem('favoritWorkerUrl', '...')` on the host device

---

## Done

- [x] DESIGN_BRIEF.md authored with verified exam color palette, typography, and choice layout rules — 2026-05-09
- [x] v1.0 built: white exam-paper theme, fixed header strip, Arial-only, no Google Fonts — 2026-05-09
- [x] 10 lenses implemented: keystrokes, worked examples, pitfall warnings, standards tags — 2026-05-09
- [x] 15 Regents-style questions in `QUESTION_BANK` with `(1)(2)(3)(4)` label schema — 2026-05-09
- [x] Practice Mode: lens-pick → walkthrough (strong/workable/off-target feedback) → answer → review — 2026-05-09
- [x] Challenge Mode: 90s per-question timer with red urgent state at 30s, no lens picker, per-standard results table — 2026-05-09
- [x] TI-84 iframe in Practice side rail with fallback link — 2026-05-09
- [x] **Persistent TI-84 rail** — iframe moved to static `#persistent-rail` outside `<main>`; no longer re-created on question advance; fixes calculator reload in Practice Mode — 2026-05-09
- [x] **Calculator in Challenge Mode** — `showRail(true, false)` on active question; hidden on splash + results screens; no lens hint shown (no-hints policy) — 2026-05-09
- [x] **Loading screen identity** — Student Name: "Mister Favors", School Name: "HSES-ALG1" hardcoded on cover page — 2026-05-09
- [x] `choiceLayout()` auto-detects short vs long choices; 2-column grid uses correct odd/even column order per DESIGN_BRIEF — 2026-05-09
- [x] CONTEXT.md and TASKS.md written — 2026-05-09
- [x] Question bank expanded to 24 questions (Q16–Q24: A.REI.4, A.REI.12, N.RN.2, F.IF.7, A.CED.2, A.APR.3, F.IF.5, G.GPE.5) — 2026-05-09
