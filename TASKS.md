# TASKS — Mr. Favors' Regents Lock-In

Last updated: 2026-05-10 (session 3 — phase 2 handoff)

---

## Context

v1.0 shipped. The game has a complete white exam-paper aesthetic (verified against DESIGN_BRIEF),
10 TI-84 lenses, 24 Regents-style questions, a full Practice Mode (lens-pick → walkthrough → answer →
review), and a Challenge Mode with a 90-second per-question timer and per-standard results report.
The `index.html` is ~2004 lines, single-file, vanilla JS, no Google Fonts.

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
- [ ] Author `svgPlane()`, `svgGraph()`, `svgTable()`, `svgScatter()`, `svgBoxPlot()`, `svgBarGraph()` helpers at top of QUESTION_BANK block
- [ ] June 2026 Part I — text-only questions (~16 of 24, verbatim from exam)
- [ ] June 2026 Part I — image questions (~8 of 24, inline SVG, accuracy protocol required)
- [ ] June 2025 Part I — text-only questions (post-shift format, verbatim)
- [ ] June 2025 Part I — image questions (inline SVG, accuracy protocol required)
- [ ] Part II–style MC: S.ID.1/2/5/6 (box plots, scatter plots, two-way frequency tables)
- [ ] Part II–style MC: F.IF.1/9, F.BF.3, A.REI.11 (graph-reading questions)
- [ ] Browser QA — SVG rendering at 1366×768 in both Practice and Challenge modes

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
