# TASKS — Mr. Favors' Regents Lock-In

Last updated: 2026-05-27 (Session Z PRD — Lens Toggle Header Relocation)

---

## Session AA — Lens Color Coding (Lens-Pick UX) ✅ COMPLETE 2026-05-27

**Design concept:** Color-code the lens-pick grid by fit quality — green left-border for `bestLens`, yellow left-border for workable (`applicableLenses` but not `bestLens`), red left-border for non-applicable (disabled, non-interactive) — so students instantly see strategy fit without being able to pick a wrong-family lens.

**Modules touched:** `UI_RENDER` only (`renderPractice()` — `lensOptions` map in lens-pick phase)

**Interface changes:**
- `lensOptions` map: each lens button receives one of three classes — `.lens-best` (green left-border, clickable), `.lens-workable` (yellow left-border, clickable), `.lens-notrelated` (red left-border, `disabled` attribute, cursor not-allowed, no onclick)
- CSS: three new classes added to `<style>` block with left-border treatment consistent with existing `.lens-highlight` pattern

**Out of scope:** Tooltip/label text explaining color meaning, Lens Drill / Mistakes Drill / Challenge mode, `pickLens()` / `toggleLens()` handler changes, `QUESTION_BANK` or `LENSES` data changes, `runPuzzleTests()` extension

**Done when:**
- Green left-border on `bestLens` button, clickable
- Yellow left-border on workable lens buttons, clickable
- Red left-border + `disabled` on non-applicable lens buttons (cursor not-allowed, no click)
- Mistakes Drill `.lens-highlight` blue border still applies on applicable lenses (no collision — bestLens is always in applicableLenses)
- Lens Drill unaffected
- 100/100 `runPuzzleTests()` pass

---

## Session Z — Lens Toggle Header Relocation ✅ COMPLETE 2026-05-27

**Design concept:** Move the lens ON/OFF toggle from its current two-location placement (lens-pick screen + answer/review screen) to a single persistent badge in the question header, right side inline with the standard tag and Reference Sheet button, visible across all Practice phases in all modes except Lens Drill and wrong-answer drill.

**Modules touched:** `UI_RENDER` only (`renderPractice()`)

**Interface changes:**
- `renderPractice()` lens-pick phase: REMOVE `"LENS: ON — Skip going forward"` button
- `renderPractice()` answer/review phase: REMOVE `"Lens: OFF — Re-enable"` button
- `renderPractice()` question header (all active phases): ADD toggle badge right side — `S.ID.2 · [LENS: ON] [Reference Sheet]` / `S.ID.2 · [LENS: OFF] [Reference Sheet]`; reads `STATE.practice.lensEnabled` on every render; hidden when `drillMode === 'lens'` or `drillMode === 'wrong'`

**Out of scope:** Challenge mode, `toggleLens()` logic, `lensEnabled` localStorage persistence, label text changes beyond ON/OFF, picker sub-view changes, other header element changes

**Done when:**
- Header badge shows `LENS: ON` / `LENS: OFF` correctly on lens-pick, walkthrough, answer, and review phases
- Badge reflects persisted state across questions (toggle OFF on Q2 → Q3 header still shows `LENS: OFF`)
- No badge in Lens Drill or wrong-answer drill
- No badge on picker sub-views (standard-picker, lens-picker, exam-picker, question-picker)
- Old toggle buttons gone from lens-pick phase and answer/review phase
- Header layout intact at 1366×768 — no overflow between standard tag, badge, and Reference Sheet button
- 100/100 `runPuzzleTests()` pass

---

## Session Y-1 — S.ID Lens Definitions (Phase 1 of 3) ✅ COMPLETE 2026-05-26

**Design concept:** Add three new lens objects (L11 Box Plot Read, L12 LinReg / Scatter, L13 Two-Way Table) to the LENSES array so students see a named TI-84 strategy for every S.ID question type. The Library and Lens Drill already iterate LENSES dynamically — no renderer changes needed.

**Modules touched:** `LENSES` array only.

**Interface changes:**
- L11 Box Plot Read (id:11, S.ID.1/2/3): five-number summary, IQR, outlier rule, TI-84 Boxplot option
- L12 LinReg / Scatter (id:12, S.ID.6/7/8): STAT→CALC→LinReg keystroke chain, r (DiagnosticOn), predict from model
- L13 Two-Way Table (id:13, S.ID.5): joint/marginal/conditional frequency reading strategy

**Out of scope (this pass):**
- Updating applicableLenses/bestLens on S.ID questions — Session Y-2
- Writing walkthroughs/examples for new lenses — Session Y-3
- Other standard groups (F.LE, A.CED, N.Q, A-APR) — future phases

**Done when:** ✅ Library shows 13 lenses; ✅ Lens Drill shows 13 rows (L11/L12/L13 show "0 q" — expected); ✅ lensDetail for L11 renders keystrokes, standards, pitfall; ✅ 100/100 runPuzzleTests() pass; ✅ getLens(11/12/13) returns correct objects.

**NOTE:** L11/L12/L13 show "0 q" in Lens Drill — not a bug. Will be corrected in Session Y-2 when S.ID questions' applicableLenses/bestLens are updated.

**S.ID questions to update in Session Y-2:**
| qid | standard | Target bestLens |
|-----|----------|-----------------|
| Q08 | S.ID.7 | L12 LinReg/Scatter |
| Q26 | S.ID.2 | L11 Box Plot Read |
| Q50 | S.ID.5 | L13 Two-Way Table |
| Q69 | S.ID.2 | L11 Box Plot Read |
| Q84 | S.ID.6 | L12 LinReg/Scatter |
| Q86 | S.ID.2 | L11 Box Plot Read |

---

## Session Y-2 — S.ID Question Schema Update (Phase 2 of 3) ✅ COMPLETE 2026-05-27

For each of 6 S.ID questions: updated `applicableLenses` to include new lens ID(s) and set `bestLens` to target lens.
- Q08 S.ID.7: applicableLenses [9,10] → [9,10,12], bestLens 9 → 12
- Q26 S.ID.2: applicableLenses [9,10] → [9,10,11], bestLens 9 → 11
- Q50 S.ID.5: applicableLenses [9,10] → [9,10,13], bestLens 9 → 13
- Q69 S.ID.2: applicableLenses [8,10] → [8,10,11], bestLens 8 → 11
- Q84 S.ID.6: applicableLenses [4] → [4,12], bestLens 4 → 12
- Q86 S.ID.2: applicableLenses [4] → [4,11], bestLens 4 → 11

---

## Session Y-3 — S.ID Walkthroughs + Examples (Phase 3 of 3) ✅ COMPLETE 2026-05-27

For each of 6 S.ID questions: added `walkthroughs[newLensId]` and `examples[newLensId]` in teacher annotation voice.
- Q08: walkthroughs[12] + examples[12] (LinReg/Scatter — interpret slope from given equation)
- Q26: walkthroughs[11] + examples[11] (Box Plot Read — compare mean and spread from table)
- Q50: walkthroughs[13] + examples[13] (Two-Way Table — conditional frequency)
- Q69: walkthroughs[11] + examples[11] (Box Plot Read — compare IQR from two box plots)
- Q84: walkthroughs[12] + examples[12] (LinReg/Scatter — compute r with DiagnosticOn)
- Q86: walkthroughs[11] + examples[11] (Box Plot Read — mode/median/mean from dot plot)
100/100 runPuzzleTests() pass.

---

## Session X — Skip Button + Lens Toggle — Session PRD ✅ COMPLETE 2026-05-25

**Design concept:** Add two friction-reducing controls to the Practice active loop — a "Skip →" button on the example step screen that jumps directly to answer choices, and a "LENS ON/OFF" toggle on the lens-pick screen that bypasses both lens-pick and example phases entirely for students building toward mastery.

**Modules touched:** STATE (new `lensEnabled` field), UI_RENDER (`renderPractice()` — lens-pick phase + walkthrough phase + answer phase), UI_HANDLERS (new `toggleLens()`, updates to `startPractice()` and `nextPracticeQ()`)

**Interface changes:**
- STATE: `STATE.practice.lensEnabled` boolean, default `true`, session-only; resets to `true` in `backToPracticeHome()`
- UI_RENDER lens-pick phase: toggle button "LENS: ON — Skip going forward" (hidden in wrong-answer drill)
- UI_RENDER walkthrough phase Case 1: "Skip →" button (calls `advanceToAnswer()`); only on non-last steps
- UI_RENDER answer/review phase: "Lens: OFF — Re-enable" button when `lensEnabled=false` and not lens/wrong drill
- UI_HANDLERS: new `toggleLens()` — flips flag; if now false → `lensGuess=Q.bestLens`, `phase='answer'`, render; if now true → stay at current phase (takes effect next question)
- `startPractice()`: if `lensEnabled===false` AND drillMode not in [lens, wrong] → `phase='answer'`, `lensGuess=firstQ.bestLens`
- `nextPracticeQ()`: same lensEnabled check alongside existing `isLensDrill` check

**Out of scope:** localStorage persistence, Challenge mode, Lens Drill changes, FAVORit analytics for toggle state, Session W per-standard grouping

**Done when:**
- "Skip →" button appears on example step screen (non-last steps only) and jumps to answer choices
- Toggle button appears on lens-pick screen in Free Roam, Standard Drill, Past Exam, Pick a Question (not wrong-answer drill)
- Toggle OFF: phase jumps to answer immediately, lensGuess auto-set to bestLens
- Toggle ON mid-question: no disruption, current question stays at answer; re-enables from next question
- Next question after toggle OFF: lands directly on answer choices (skips lens-pick + example)
- Wrong-answer drill always shows lens-pick regardless of toggle
- Lens Drill and Challenge mode unaffected
- 99/99 runPuzzleTests() pass; browser QA green

---

## Session S — Code Review Fixes ✅ COMPLETE 2026-05-24

> Source: `/code-review max @games/regents-mc-trainer` run 2026-05-24, immediately after Session R.
> All 9 findings are verified or plausible. Implement in priority order below.
> No Grill Me required — root causes are confirmed. No new features, no new modules.

### Fix 1 — Modal fraction bars missing (HIGH — visually broken for every user) ✅

**What's wrong:** The Reference Sheet modal uses bare `<span>` children inside `.mfrac` spans,
but the CSS rule that draws the fraction bar targets `.mfrac .mnum` with `border-bottom`.
Bare spans never match, so slope/quadratic/axis formulas render as two stacked text blocks
with no horizontal bar — unreadable as fractions.

**File:** `index.html` — the static modal HTML block starting at ~line 789.
The CSS `.mfrac .mnum { border-bottom: ... }` is at ~line 649.

**Fix:** Add `class="mnum"` to the numerator span and `class="mden"` to the denominator span
in every `.mfrac` in the modal body. There are 3 fractions: slope, quadratic formula, axis.

Current (broken):
```html
<span class="mfrac"><span>y₂ − y₁</span><span>x₂ − x₁</span></span>
```
Fixed:
```html
<span class="mfrac"><span class="mnum">y₂ − y₁</span><span class="mden">x₂ − x₁</span></span>
```
Apply to all three: slope (`ref-row` line 1 under Linear), quadratic formula x=, and axis x=.

---

### Fix 2 — Modal stays open on navigate() (HIGH — overlays Home/Library) ✅

**What's wrong:** `navigate()` replaces `main.innerHTML` but the modal is `position:fixed`
outside `<main>`, so it persists over every view (Home, Library, Practice Home, Challenge
splash). Those views have no `refSheetBtn()` trigger, so students who don't find the ×
button inside the modal are stuck.

**File:** `index.html` — `navigate()` function. Find it by searching `function navigate(`.

**Fix:** Add one line at the top of `navigate()`:
```js
document.getElementById('reference-sheet-modal').classList.add('hidden');
```

---

### Fix 3 — getLens(Q.bestLens).name unguarded in isValid walkthrough branch (HIGH) ✅

**What's wrong:** The null guard `(getLens(Q.bestLens)||{}).name||''` was added to the
review-phase feedback in Session R, but the identical pattern exists ONE MORE TIME in the
walkthrough-phase `isValid` branch and was missed. It crashes when a student picks a
valid-but-non-best lens on any question where bestLens lookup fails.

**File:** `index.html` — search for the string `is faster` inside `renderPractice()`.
The line reads (approximately):
```js
else if (isValid) fb = `...<p>${guessLens.name} can crack it, but ${getLens(Q.bestLens).name} is faster.</p>`;
```

**Fix:** Change `${getLens(Q.bestLens).name}` to `${(getLens(Q.bestLens)||{}).name||''}` on
that one line. (Note: `guessLens` on the same line is already safe because `getLens(guess)`
is guarded by the `isValid` check a few lines above — only `Q.bestLens` needs the guard.)

---

### Fix 4 — touchcancel not handled: _dragActive locks on forever (MEDIUM) ✅

**What's wrong:** The drag IIFE wires `touchstart/touchmove/touchend` but not `touchcancel`.
If the OS interrupts a drag (incoming call, notification, focus steal), `touchend` never fires
and `_dragActive` stays `true` permanently. Every subsequent touchmove suppresses scroll
via `e.preventDefault()` for the rest of the session.

**File:** `index.html` — the drag IIFE, after the `touchend` listener line.

**Fix:** Add one line immediately after `document.addEventListener('touchend', endDrag);`:
```js
document.addEventListener('touchcancel', endDrag);
```

---

### Fix 5 — Non-passive touchmove degrades all scroll on touch devices (MEDIUM) ✅

**What's wrong:** `document.addEventListener('touchmove', fn, { passive: false })` is always
active, even when the modal is closed and nothing is being dragged. The browser cannot use
compositor-thread scroll for any touchmove on the page because this listener exists.

**File:** `index.html` — the drag IIFE touchmove handler.

Current:
```js
document.addEventListener('touchmove', function(e) {
  if (!_dragActive) return;
  var t = e.touches[0];
  moveDrag(t.clientX, t.clientY);
  e.preventDefault();
}, { passive: false });
```

**Fix:** Move `preventDefault` inside the active-drag guard so the browser sees it's
unconditionally-returning when inactive, then keep `{ passive: false }` only for the active path.
Actually the minimal correct fix is to keep `{ passive: false }` (required to call preventDefault
at all) but ensure the early return makes it a no-op when not dragging — the current code already
does this. The real fix is structural: use a `passive: true` listener that only upgrades to
non-passive during an active drag via a flag + a separate listener. For this codebase the
pragmatic fix is acceptable as-is on Chromebook (auto-hidden scrollbars, few touch-scroll
conflicts), but document the tradeoff in a comment:
```js
// passive:false required to preventDefault during drag; early-return minimizes cost when idle
document.addEventListener('touchmove', function(e) {
  if (!_dragActive) return;
  var t = e.touches[0];
  moveDrag(t.clientX, t.clientY);
  e.preventDefault();
}, { passive: false });
```
**Priority:** Fix 4 (touchcancel) first — that's the crash. Fix 5 is an accepted perf tradeoff
on Chromebook; annotate the comment and move on.

---

### Fix 6 — Q.errorTags[0] unguarded: renders "undefined" on empty errorTags (MEDIUM) ✅

**What's wrong:** `Q.errorTags[0]` is interpolated directly into `innerHTML` at the review
feedback line. If a future question has `errorTags: []` or omits the field, students see
**"Common error: undefined."** in bold. The same value also logs as `undefined` to the
FAVORit Sheets endpoint.

**File:** `index.html` — review phase feedback builder. Search for `errorTags[0]`.
There are two occurrences: one in the `innerHTML` string and one in the `ANALYTICS.log` call.

**Fix both occurrences:**
- In the `innerHTML` string: `${Q.errorTags[0]}` → `${(Q.errorTags && Q.errorTags[0]) || ''}`
- In the analytics call: `error_tag: Q.errorTags[0]` → `error_tag: (Q.errorTags && Q.errorTags[0]) || null`

---

### Fix 7 — runPuzzleTests() check (d) misses empty array walkthroughs (MEDIUM) ✅

**What's wrong:** Check (d) uses `!q.walkthroughs[q.bestLens]` which is `false` for `[]`
(empty array is truthy), so a question with `walkthroughs: { 9: [] }` passes the test
but renders a blank walkthrough panel in the UI. Check (e) already has the correct
`.length === 0` guard; check (d) needs the same.

**File:** `index.html` — `runPuzzleTests()`, the `// (d)` block.

Current:
```js
if (!q.walkthroughs || !q.walkthroughs[q.bestLens]) {
  errors.push('walkthroughs[' + q.bestLens + '] missing');
}
```

**Fix:**
```js
if (!q.walkthroughs || !q.walkthroughs[q.bestLens] ||
    (Array.isArray(q.walkthroughs[q.bestLens]) && q.walkthroughs[q.bestLens].length === 0)) {
  errors.push('walkthroughs[' + q.bestLens + '] missing or empty');
}
```

---

### Fix 8 — Modal position not reset on re-open after window resize (LOW) ✅

**What's wrong:** After a drag, `modal.style.left/top` are set inline and `style.right` is
forced to `'auto'`. These persist across close/reopen. After a viewport resize (e.g., split-screen
on Chromebook), the old absolute position may be off-screen with no recovery except another drag.

**File:** `index.html` — `toggleReferenceSheet()` function.

**Fix:** When showing (not hiding), clamp the current position to the viewport:
```js
function toggleReferenceSheet() {
  const modal = document.getElementById('reference-sheet-modal');
  modal.classList.toggle('hidden');
  if (!modal.classList.contains('hidden')) {
    // Re-clamp after potential viewport resize
    const left = parseFloat(modal.style.left) || 0;
    const top = parseFloat(modal.style.top) || 0;
    if (modal.style.left) {
      modal.style.left = Math.max(0, Math.min(left, window.innerWidth - modal.offsetWidth)) + 'px';
      modal.style.top = Math.max(0, Math.min(top, window.innerHeight - modal.offsetHeight)) + 'px';
    }
  }
}
```

---

### Fix 9 — Text selection during fast drag (LOW — cosmetic) ✅

**What's wrong:** `mousemove` on `document` doesn't call `e.preventDefault()`, so dragging
the modal quickly across formula text in the modal body produces a visible text selection.

**File:** `index.html` — drag IIFE `mousemove` listener.

Current:
```js
document.addEventListener('mousemove', function(e) { moveDrag(e.clientX, e.clientY); });
```

**Fix:**
```js
document.addEventListener('mousemove', function(e) {
  if (_dragActive) e.preventDefault();
  moveDrag(e.clientX, e.clientY);
});
```

---

### Done when:
- Fixes 1–3 implemented (HIGH priority — required before gh-pages deploy)
- Fixes 4, 6, 7 implemented (MEDIUM — data-safety and touch-correctness)
- Fix 5 comment annotated (accepted tradeoff, no code change needed beyond the comment)
- Fixes 8–9 implemented if time allows (LOW)
- 96/96 `runPuzzleTests()` pass after every change
- Browser QA at 1366×768: modal fractions visible with horizontal bars; modal closes on navigate; no crash on non-best lens pick

---

## Session R — Bug Fixes + Reference Sheet Modal ✅ COMPLETE 2026-05-24

### Phase 1 — Bug Fixes

**Bug 1 — Q96 Fraction Display (CRITICAL)**
All 4 choices in Q96 render as `rac{...}` text — backslash was dropped. Students cannot read the choices.
- **File:** `index.html` lines 5374–5377
- **Fix:** Wrap each choice in `math()` and restore `\\frac{...}` prefix
- **Root cause confirmed:** `\f` stripped in source; `math()` defined at line 942, available at parse time

**Bug 2 — Null Guard on bestLens Lookup (MEDIUM)**
`getLens(Q.bestLens).name` at line 5778 crashes review screen if `getLens()` returns undefined.
- **Fix:** `${(getLens(Q.bestLens)||{}).name||''}`

**Bug 3 — runPuzzleTests() Extension**
Extend existing validation loop to catch content gaps silently passing today:
- Check `Q.walkthroughs[Q.bestLens]` exists for every question
- Check `Q.examples[Q.bestLens]` exists for every question
- Check each examples entry is a non-empty array
- No new test API — extend `errors.push(...)` block at lines 6305–6344

### Phase 2 — Reference Sheet Modal

**Design concept:** A small trigger button in the question header, consistent across Practice active and Challenge active views, opens a floating draggable formula sheet — always available, never filtered by standard — matching the NYS Regents exam where students always have the reference sheet in front of them.

**Grill Me:** Complete 2026-05-24. Interview produced the floating modal approach (not a rail card). Key decisions: static HTML outside `<main>` (survives `main.innerHTML` reassignment); `position: fixed`; drag-by-handle with viewport constraint; mouse + touch events; `refSheetBtn()` helper called by both renderers.

**Modules touched:**
- `HTML body` — new `<div id="reference-sheet-modal" class="ref-modal hidden">` as sibling of `<main>` and `#persistent-rail`; never re-created by renderers
- `UI_RENDER` — `renderPractice()` (active phase) and `renderChallenge()` (active question) each call `refSheetBtn()` in the question header; `refSheetBtn()` is the single source of truth for button HTML
- `CSS` — `.ref-modal` (fixed position, z-index, border), `.ref-modal-handle` (drag strip), `.ref-cat-label`, `.ref-row`, `.ref-toggle-btn`
- `JS` — `toggleReferenceSheet()` (toggles `.hidden` on `#reference-sheet-modal`); drag listeners wired once in INIT (mousedown/mousemove/mouseup + touchstart/touchmove/touchend; viewport-constrained)

**Interface changes:**
- `refSheetBtn()` → `<button onclick="toggleReferenceSheet()">Reference Sheet</button>` — small, inline in question header
- `toggleReferenceSheet()` → toggles `.hidden`; no STATE change needed
- Drag: `mousedown` on `.ref-modal-handle` sets `_dragActive = true` + records offset; `mousemove` on document updates `style.left`/`style.top`; `mouseup` clears flag; constrained to viewport bounds; touch mirrors mouse
- Content: static HTML in modal body using existing `.mfrac` for fractions, Unicode for subscripts/superscripts; no JS rendering of content
- Default position: top-right of viewport, clear of answer choices — verified in QA at 1366×768

**Formula categories (source: teacher's Google Drive reference PDF, file ID 1jQ9NBon4xjurHWv3IK_isgRKc5KtSua8):**
- Linear: slope m = (y₂−y₁)/(x₂−x₁), y = mx+b, y−y₁ = m(x−x₁)
- Quadratic: y = ax²+bx+c, x = (−b±√(b²−4ac))/2a, axis x = −b/2a
- Exponential/Growth: y = ab^x, A = P(1+r)^n
- Sequences: arithmetic aₙ = a₁+d(n−1), geometric aₙ = a₁·r^(n−1)
- Statistics: IQR = Q3−Q1, lower Q1−1.5·IQR, upper Q3+1.5·IQR
- Unit conversions (compact): 1 mi = 5,280 ft, 1 mi ≈ 1.609 km, 1 kg ≈ 2.2 lb

**Out of scope:**
- Keyboard shortcut to toggle the sheet
- Per-section collapsing inside the modal
- Responsive/mobile layout changes for the modal
- Persistence of modal position across page reload (no localStorage)
- Any QUESTION_BANK, walkthrough, or examples changes
- Full-scope Question Picker (deferred from Session O)
- Q73–Q96 annotation-style example rewrite (deferred)
- Animation or transition effects

**Done when:**
- Button appears in question header in Practice active and Challenge active; absent elsewhere
- Modal opens/closes on button click
- Modal is draggable by handle; constrained to viewport; cannot go off-screen
- Modal default position does not overlap answer choices at 1366×768
- Open modal → advance question → modal stays open
- Touch and mouse drag both work
- 96/96 `runPuzzleTests()` pass
- No regression on existing Practice flows, Challenge mode, or persistent rail

---

## Session Q — bestLens Examples, Annotation Style ✅ COMPLETE 2026-05-22

**Design concept:** Fill in the 5 missing `examples[bestLens]` entries on Q73–Q96 using the teacher's annotation voice — direct, imperative, TI-84-focused — applied to a parallel Regents question on the same standard.

**Modules touched:** `QUESTION_BANK` only

**Interface changes:** Q82 (L1), Q90 (L4), Q91 (L3), Q92 (L3), Q93 (L3) each gained `examples[bestLens]: [step, step, 'setup question?']`

**Discovery:** Most Q73–Q96 questions already had bestLens examples from earlier passes; only 5 were genuinely missing.

**Out of scope:** Rewriting existing Q73–Q96 bestLens examples in annotation style (19 entries use formal framing — deferred), Q1–Q72 changes, UI changes

**Done when:** ✅ 96/96 runPuzzleTests() pass; all 5 entries verified in browser; deployed

---

## Session P — Non-bestLens Examples (all 96 questions) ✅ COMPLETE 2026-05-22

**Design concept:** Add at least one alternate-lens `examples` entry to every question so students who pick a non-bestLens can still see a parallel example for that strategy.

**Modules touched:** `QUESTION_BANK` only

**Interface changes:** Each question's `examples` block gains ≥1 additional `lensId: [...steps]` entry beyond the bestLens entry that already existed.

**Out of scope:** bestLens examples for Q73–Q96 (completed in Session Q), UI changes, new lenses

**Done when:** ✅ 96/96 runPuzzleTests() pass; deployed to mrfavors-lock-in

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

**Open next (deferred):** bestLens `examples` entries for Q73–Q96 (parallel walkthrough pass — same-standard setup question, ends with "?")

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
- Practice Home 5th entry-point card ("Pick a Question") — Past Exam became 4th in Session U
- Back-to-picker routing after review (returns to Practice Home)
- Per-standard grouping or search in the picker
- Any changes to existing Practice modes or Challenge logic

**Full scope (future session — Grill Me required):**
- Practice Home 5th card
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

## Session T — Q73–Q96 Annotation-Style Example Rewrite ✅ COMPLETE 2026-05-24

> No Grill Me required — content-only pass, no UI or logic changes.
> Do this first: no dependencies, no risk, pure teaching quality improvement.

**Design concept:** The 19 Q73–Q96 `examples[bestLens]` entries added in Session P use formal
framing ("Consider the expression…", "Notice that…") — the style rejected in Session Q for Q1–Q72.
Rewrite all 19 to match the teacher annotation voice used in Q1–Q72: direct imperative, TI-84
keystrokes named by exact label (Y=, 2nd+GRAPH, VARS), arrow notation for navigation,
no hedging verbs, final step always ends with "?".

**Annotation voice rules (from Session Q):**
- Direct imperative: "Enter the equation into Y=." not "Consider entering…"
- TI-84 keystrokes named: "Press 2nd+GRAPH to open the table." not "use the table feature"
- Arrow notation for navigation: "Arrow down to find x = 3."
- No "Consider / Notice / Remember" openers
- Final step ends with "?" — never a computed answer

**Modules touched:** `QUESTION_BANK` only — `examples` field on Q73–Q96

**Out of scope:** walkthroughs, UI, new questions, Q1–Q72 examples

**Done when:**
- ✅ All 19 Q73–Q96 bestLens examples use annotation voice (14 rewritten; 5 already compliant from Session Q)
- ✅ 96/96 `runPuzzleTests()` pass
- ✅ Spot-check 3 confirmed annotation voice: Q73 (L3 table compare), Q74 (L9 coin labeling), Q96 (L9 unit chain)

---

## Session U — Practice by Specific Past Exam ✅ COMPLETE 2026-05-25

**Design concept:** A 4th "Past Exam" card on Practice Home lets students pick one of three real
Regents Part I exams (June 2025, January 2026, August 2025) and work all 24 questions in exact
exam order — full lens-pick → walkthrough → answer → review — ending on a score screen with
X/24, percentage, and the 65% Regents passing threshold.

**Discovery:** TASKS.md referred to a `source` field; actual codebase uses `exam:` field already
present on all 96 questions. No backfill needed. Each real exam has exactly 24 questions.

**Modules touched:** `STATE`, `UI_RENDER`, `UI_HANDLERS`, `Tests` — no QUESTION_BANK changes.

**Interface changes:**
- `STATE.practice` gains `filterExam: null`, `examCorrect: 0`; `drillMode` gains `'exam'`; `subView` gains `'exam-picker'`
- Practice Home: 4th "Past Exam" card → `showExamPicker()`
- `renderExamPicker()`: 3 rows (June 2025 / Jan 2026 / Aug 2025), "24 questions · fixed exam order" each
- `startPractice('exam', examKey)`: filters by `q.exam === examKey`, no shuffle (insertion order = exam order)
- `lockAnswer()`: increments `STATE.practice.examCorrect` when correct + drillMode='exam'
- `renderPracticeComplete()` exam branch: exam name, X/24, %, pass/fail vs 65%, Try Again + ← Home
- `runPuzzleTests()` check (f): each real exam key yields exactly 24 questions, no Q1–Q24 leakage

**Out of scope:** Timed exam mode, per-standard breakdown on results, Drill Mistakes on exam results,
per-standard filter in picker, Challenge mode changes, Q1–Q24 in picker.

**Done when:** ✅ 4-card Practice Home; ✅ picker shows 3 exams; ✅ August 2025 starts at Q73;
✅ header shows "QUESTION N OF 24 · AUGUST 2025"; ✅ results screen: X/24, %, 65% threshold;
✅ Try Again restarts from Q1 in fixed order; ✅ 99/99 runPuzzleTests() pass (96 Qs + 3 exam pools);
✅ no regression on Free Roam, Standard Drill, Lens Drill, Mistakes Drill, Challenge, Question Picker.

---

## Post-U Code-Review Fixes ✅ COMPLETE 2026-05-25

> Source: `/code-review` run on Session U diff. 5 findings — 3 CONFIRMED (math() rendering),
> 1 PLAUSIBLE (state hygiene), 1 dead code. No Grill Me required — root causes confirmed,
> no new interfaces.

### Fix 1-3 — math() not applied to step strings (HIGH — raw LaTeX visible in steps)

**Root cause:** Stems and choices are pre-rendered through `math()` at data-definition time
(stored as HTML). Walkthrough and example step arrays were authored in Sessions L, M, Q, T as
raw strings — the step renderer never called `math()` on them. Students saw `\frac{1}{2}` as
literal text in walkthrough and example steps, while stems and choices rendered correctly.

**Three sites fixed in `renderPractice()`:**
- Example step body (Case 1 — bestLens + parallel example):
  `${s}` → `${math(s)}`
- Walkthrough array step body (review phase):
  `${step}` → `${math(step)}`
- String-fallback walkthrough body (`.ti-screen` branch):
  `${drillWalkRaw}` → `${math(drillWalkRaw)}`

### Fix 4 — backToPracticeHome() stale exam state (PLAUSIBLE — bleeds into Free Roam)

`backToPracticeHome()` set `subView='home'` but left `drillMode='exam'`, `filterExam`, and
`examCorrect` set. No current crash — but if any renderer branched on `drillMode` before
`startPractice()` reset it, exam state would contaminate a Free Roam run.

**Fix:** Added `STATE.practice.drillMode = 'free'; STATE.practice.filterExam = null;
STATE.practice.examCorrect = 0;` before the `render()` call.

### Fix 5 — Dead `sub:` property in EXAMS array (trivial — dead code)

`renderExamPicker()` defined `sub: '...'` on all 3 EXAMS entries; only `e.key` and `e.label`
were ever read. Removed `sub:` from all three entries.

**Done when:** ✅ 99/99 pass; ✅ walkthrough steps with LaTeX now render styled fractions/exponents;
✅ Free Roam entered via ← Back from exam results shows drillMode='free' in STATE.

---

## Session V — Full-scope Question Picker ✅ COMPLETE 2026-05-25

**Design concept:** Surface the existing Question Picker from Practice Home as a 5th entry-point
card ("Pick a Question"), and change single-question review-complete routing from Practice Home
back to the picker so students can run through targeted questions without losing their place in
the list.

**Grill Me:** Complete 2026-05-25. 7-question interview resolved: 5th card opens picker directly
(no submenu); all 96 questions shown (no standard filter this session); review complete routes to
picker via `showQuestionPicker()`. Mini-PRD written before any code touched.

**Modules touched:** `STATE` (subView comment updated), `UI_RENDER` (`renderPracticeHome` 5th card,
`renderPracticeComplete` single-mode button), `UI_HANDLERS` (`showQuestionPicker` — verified,
no changes needed)

**Interface changes:**
- `STATE.practice.subView` comment: added `'question-picker'` to valid values list
- `renderPracticeHome()`: 5th card "Pick a Question" (🎯) → `showQuestionPicker()`
- `renderPracticeComplete()` single-mode branch: button label "← Back to Questions",
  action `showQuestionPicker()` (was `backToPracticeHome()` / "← Practice Home")
- `renderPractice()` already had `'question-picker'` branch from Session O — no change needed

**Out of scope:** per-standard grouping, search/filter, sort options, Challenge changes,
analytics event, scroll position persistence, QUESTION_BANK changes

**Done when:** ✅ 5-card Practice Home; ✅ card → picker; ✅ full single-question loop;
✅ review complete → "← Back to Questions" → picker; ✅ Challenge results → picker (no regression);
✅ 99/99 runPuzzleTests() pass; ✅ browser QA at localhost:8765 — all checks green

---

## Session W — Per-Standard Grouping in Question Picker ✅ COMPLETE 2026-05-25

> Grill Me complete 2026-05-25. Mini-PRD below.

**Design concept:** Replace the flat Q-number list in `renderQuestionPicker()` with a clustered view — always-expanded sections using `STANDARD_CLUSTERS` headers — so students can browse by topic. A "Show All" toggle restores the flat list. All 96 questions grouped by standard; Q1–Q24 appear under their matching cluster(s) alongside Q25–Q96 with no separate "Regents style" bucket.

**Modules touched:** `UI_RENDER` (`renderQuestionPicker()` only) — no STATE, no QUESTION_BANK, no handler changes.

**Interface changes:**
- `renderQuestionPicker()`: default view = clustered; each cluster header shows label + question count; questions appear under every matching cluster (a question with multiple matching standards appears in each); always-expanded, no collapse toggle
- "Show All" button at top of picker toggles between clustered and flat views; flat view = current Q-number order (no change to existing flat render)
- Toggle state is local to `renderQuestionPicker()` — a simple `_pickerShowAll` module-level boolean (or inline state via a data attribute); resets to clustered on next `showQuestionPicker()` call
- `runPuzzleTests()`: new check — every question in QUESTION_BANK appears in at least one cluster; failure logs the qid and standard

**Invariants (must not change):**
- `startPractice('single', qid)` — picking a question works identically regardless of which view was active
- "← Back to Questions" routing from review complete → `showQuestionPicker()` — no change
- Challenge results → picker entry point — no change
- Flat list Q-number order and row format (`Q01 · A.REI.3 · stem preview`) — unchanged in Show All view

**Out of scope:**
- Text search / free-form filter
- Sorting within a cluster beyond Q-number order
- Scroll position memory after returning from a single-question loop
- Analytics event for cluster browsed
- Collapsible cluster sections
- Any QUESTION_BANK changes

**Done when:**
- Clustered view is the default; all 11 cluster headers visible with label + question count
- Q1–Q24 appear under matching standard clusters alongside Q25–Q96
- A question with multiple matching standards appears under each matching cluster
- "Show All" button toggles to flat list (existing Q-number format); toggle back works
- `runPuzzleTests()` new check passes: every question in at least one cluster
- Picking any question in either view → full single-question loop runs correctly
- "← Back to Questions" from review complete → picker (clustered default)
- 99/99 + new cluster check runPuzzleTests() pass; browser QA green at 1366×768

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
