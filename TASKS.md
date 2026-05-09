# TASKS — Mr. Favors' Regents Lock-In

Last updated: 2026-05-09

---

## Context

v1.0 shipped today. The game has a complete white exam-paper aesthetic (verified against DESIGN_BRIEF),
10 TI-84 lenses, 15 Regents-style questions, a full Practice Mode (lens-pick → walkthrough → answer →
review), and a Challenge Mode with a 90-second per-question timer and per-standard results report.
The `index.html` is 1730 lines, single-file, vanilla JS, no Google Fonts.

The two most urgent gaps: the question bank is 15 of 24 (one full Part I set), and the FAVORit
Sheets POST endpoint is stubbed — the Worker URL needs to be pasted in. No localStorage is used;
session data is ephemeral by design.

Read CONTEXT.md before touching code. Pay close attention to the choice schema (`label: '1'–'4'`,
never letters) and `choiceLayout()` — both are DESIGN_BRIEF-verified and must not regress.

---

## Starting Checklist

1. Read `CONTEXT.md` fully before touching `index.html`
2. Read `DESIGN_BRIEF.md` if you plan to change any visual element
3. Open `index.html` in browser and verify: white background, Arial font, `(1)(2)(3)(4)` labels visible
4. Search `[TODO:Q]` in source to find where new questions should be appended
5. Search `[TODO:AN]` in source to find the Sheets Worker URL placeholder

---

## Open (priority order)

- [x] **Expand question bank to 24 questions** — Q16–Q24 added covering A.REI.4, A.REI.12, N.RN.2, F.IF.7, A.CED.2, A.APR.3, F.IF.5, G.GPE.5 — 2026-05-09
- [x] **Wire FAVORit Sheets endpoint** — fetch() is live; URL stored in localStorage (never in code); run `localStorage.setItem('favoritWorkerUrl', 'https://...')` once in the browser console on the host device — 2026-05-09
- [ ] **Add `window.runPuzzleTests()`** — write a self-test hook that verifies: (a) all 24 questions have exactly 4 choices with labels '1'–'4', (b) correctAnswer is one of the 4 labels, (c) bestLens is in applicableLenses; must log pass/fail to console
- [ ] **Browser QA — Chromebook (1366×768)** — verify: fixed page header visible, practice 2-col choice grid readable, TI-84 iframe loads or fallback link shows, challenge timer bar renders correctly
- [ ] **Register in COMMAND_CENTER.md** — move `regents-mc-trainer` from untracked to the Structured or Build row in `docs/COMMAND_CENTER.md`
- [ ] **Write UBIQUITOUS_LANGUAGE entries** — add regents-mc-trainer game mechanics (lens, walkthrough, choiceLayout, FAVORit, Part I) to `docs/UBIQUITOUS_LANGUAGE.md`

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
- [x] `choiceLayout()` auto-detects short vs long choices; 2-column grid uses correct odd/even column order per DESIGN_BRIEF — 2026-05-09
- [x] CONTEXT.md and TASKS.md written — 2026-05-09
- [x] Question bank expanded to 24 questions (Q16–Q24: A.REI.4, A.REI.12, N.RN.2, F.IF.7, A.CED.2, A.APR.3, F.IF.5, G.GPE.5) — 2026-05-09
