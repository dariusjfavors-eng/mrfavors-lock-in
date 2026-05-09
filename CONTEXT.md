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
| Current Status | v1.0 shipped; question bank incomplete (15 of 24) |

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
- Question bank has 15 of 24 target questions — `[TODO:Q]` marker in source flags the gap
- No `window.runPuzzleTests()` hook yet — add before moving to Classroom Ready status

---

## 6. BACKGROUND SYSTEM

Pure white `#FFFFFF` — styled as NYS Regents exam paper.
Fixed `.page-header` strip at top (36px, `border-bottom: 1px solid #000`).
No star field, no orbs, no gradients, no texture. This is an intentional DESIGN_BRIEF override.

---

## 7. LAST SESSION LOG

- 2026-05-09 — v1.0 shipped: white exam-paper theme, 10 lenses, 15 questions, Practice Mode, Challenge Mode with 90s per-question timer and per-standard results report. CONTEXT.md and TASKS.md written.
