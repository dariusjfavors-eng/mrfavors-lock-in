# DESIGN_BRIEF.md — Regents Algebra I MC Trainer

## Game Name
Regents Lock-In

---

## Core Concept

A high-focus, skill-building game that trains students to dominate
Algebra I Regents multiple-choice questions using TI-84 strategies.

This is NOT a trivia game.
This is a **pattern recognition + calculator mastery system**.

---

## Game Object (1 sentence)

A floating “question card” that presents Regents-style multiple-choice problems
with fast interaction and instant feedback.

---

## Player Experience

Players rapidly cycle through Regents-style questions while learning:

- what each question type is asking
- which TI-84 strategy to use immediately
- how to verify answers quickly

The goal is to build automaticity, not just correctness.

---

## Game Modes

### Practice Mode
- Unlimited time
- Hints available (Hint 1 → Hint 2 → TI-84 strategy)
- Feedback explains the mistake

### Challenge Mode
- Timed (same pacing as Regents Part I)
- No hints
- Minimal feedback
- Simulates real test pressure

---

## Core Mechanic

Each question is:

1. Recognize the question type
2. Apply the correct TI-84 move
3. Select the answer
4. Get targeted feedback

---

## TI-84 Focus (CORE DIFFERENTIATOR)

This game explicitly trains:

- Y1 vs Y2 equivalence checks
- TABLE comparisons
- INTERSECTION solving
- STAT → regression usage

This is what makes it **not generic**.

---

## Visual Identity

### Tone
- Focused
- Clean
- Slightly intense (like test mode)
- NOT playful / childish

---

## Color Palette

> Verified against January 2024 NYS Algebra I Regents exam (algone12024-exam.pdf).
> The exam is pure black-on-white. All color below is faithful to that.

Page background:  #FFFFFF (exam paper white)
Question card:    #FFFFFF (same — no card elevation, it IS the page)
Primary text:     #000000 (exam black — all body, stems, choices)
Border / rule:    #000000 (thin 1px rules matching printed booklet lines)
Header text:      #000000 (same weight as body, uppercase via CSS)

Correct feedback:  #1A5C1A (dark green — muted, not neon; shown only after answer)
Incorrect feedback: #8B0000 (dark red — muted, exam-error feel)
Hint accent:       #00008B (dark blue — matches printed annotation color)

Timer bar (challenge mode only): #000000 track, depleting fill #8B0000 when < 30s

No gradients. No shadows on cards. No transparency effects.
The suite default dark palette does NOT apply to this game.
Override justified: 1-to-1 Regents exam fidelity is the core design contract.

---

## Typography

> Verified: NYS Regents PDFs embed Arial (OpenType, v7.00) as the sole typeface.
> Source: PDF metadata from algone12024-exam.pdf (Adobe Illustrator 26.5, single black plate).

Font:         Arial, Helvetica, sans-serif  (system stack — no Google Fonts import needed)
Display use:  Arial Bold (700) — exam cover header, “ALGEBRA I”, part labels
Body use:     Arial Regular (400) — all question stems and answer choices
Size (stem):  16px / 1.5 line-height (matches ~11pt print at 96dpi)
Size (choices): 15px
Size (header): 13px uppercase letter-spacing 0.08em (matches booklet page header)
Size (title):  clamp(1.4rem, 3vw, 2rem) bold — exam cover “ALGEBRA I” scale

No Orbitron. No Inter. No Cinzel. Arial only.
Suite font defaults do NOT apply to this game.

---

## Title Screen Background

Pure white (`#FFFFFF`) — styled as the exam cover page.

Exact layout to replicate:
```
THE UNIVERSITY OF THE STATE OF NEW YORK
REGENTS HIGH SCHOOL EXAMINATION

ALGEBRA I

[Date] — [Time], only
```
Below that: “Student Name” and “School Name” blank lines (decorative, not functional).
Below that: mode-select buttons (Practice / Challenge) styled as the Part I instructions block.

No desk illustration. No calculator graphic. No particles. No animation.
The cover IS the title screen.

---

## Game Screen Background

Pure white (`#FFFFFF`) — styled as an exam booklet interior page.

Fixed header strip (top, full width, 1px bottom border `#000000`):
```
[left] Algebra I – [session]        [right] Use this space for computations.
```
This header is always visible. It is not a game HUD — it IS the page header.

The question area is the open white page. No card elevation, no surface color change,
no vignette, no texture. The printed booklet is the aesthetic.

---

## Anti-Generic Checklist

### What is the metaphor?
This is a **training simulator**, not a quiz.

### What makes this different from Kahoot/Quizizz?
- No gamified nonsense
- Focus on **how to solve**, not just answer
- Calculator strategies are explicitly taught

### What makes this feel authored?
- TI-84 workflows are embedded into gameplay
- Feedback is tied to real Regents mistakes
- Question types feel familiar and repeatable

---

## Answer Choice Layout (VERIFIED — DO NOT CHANGE)

> Source: Extracted text from algone12024-exam.pdf, pages 2–8.
> The exam uses two distinct layouts depending on choice length.

### Short choices (single line each): 2-column grid
```
(1) option text          (3) option text
(2) option text          (4) option text
```
(1) and (3) share a row. (2) and (4) share the next row.
Left column: odd choices. Right column: even choices.

### Long choices (multi-word / expression): stacked vertical
```
(1) He was walking at a constant rate.
(2) He was increasing his speed.
(3) He was decreasing his speed.
(4) He was standing still.
```

The game MUST use the same logic: measure choice length, pick layout.
Threshold: if any choice exceeds ~40 characters → stack vertically.

Labels are ALWAYS `(1)` `(2)` `(3)` `(4)` — parenthetical number. Never A/B/C/D.

Interactive state (game only — not on the printed exam):
- Default: `border: 1px solid #000; background: #fff; cursor: pointer`
- Hover:   `background: #f0f0f0`
- Selected: `background: #e8e8e8; border: 2px solid #000`
- Correct (after submit): `background: #d4edda; border: 2px solid #1A5C1A; color: #1A5C1A`
- Incorrect (after submit): `background: #f8d7da; border: 2px solid #8B0000; color: #8B0000`

---

## Asset Plan

- Question area (white page region, no card chrome)
- Answer choice grid (2-col or stacked, per length rule above)
- Page header strip (always visible — `Algebra I` left, `computations` right)
- Progress line (question X of 24 — plain text, no bar)
- Timer line (challenge mode — plain text countdown, turns red at 30s)
- Feedback block (below choices — error-specific plain text, muted color)

---

## Level Structure

- 24 questions per run (matches Regents Part I)
- Organized by standard weight (priority first)
- Final “boss” = mixed rapid-fire round

---

## Error Tags (Initial Set)

SIGN_ERROR
DISTRIBUTION
INVERSE_OPS
TABLE_MISREAD
GRAPH_MISREAD
FUNCTION_CONFUSION
RATE_MISINTERPRETATION
QUADRATIC_FACTORS
SYSTEMS_SETUP
INEQUALITY_DIRECTION

---

## What to Avoid (CRITICAL)

❌ Dark backgrounds — the exam is white paper  
❌ Orbitron, Inter, Cinzel, or any non-Arial font  
❌ A/B/C/D answer labels — the exam uses (1)(2)(3)(4)  
❌ Card chrome (shadows, rounded corners, elevation) — it's a flat printed page  
❌ Neon or saturated colors anywhere outside feedback states  
❌ Animated backgrounds, particles, orbs, or ambient effects  
❌ Emoji-heavy UI  
❌ Random gamification (streaks, coins, unlocks)  
❌ Generic quiz layouts  
❌ Missing TI-84 connection  

---

## Success Criteria

Students should:
- recognize question types instantly
- know which calculator move to use immediately
- reduce careless errors
- move faster under time pressure