# TASKS — Mr. Favors' Regents Lock-In

Last updated: 2026-05-11 (session 9 — phase 2 session E complete)

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
### Done (Phase 2)
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
- MathJax or KaTeX — Unicode math characters sufficient (², −, ≤, ≥, π)

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
