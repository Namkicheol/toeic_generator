# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Architecture

This is a **single-file static web app** — everything lives in `index.html`. There is no build system, no package.json, no test runner, and no separate source files. Changes are deployed automatically via Vercel when merged to `main`.

To preview locally, just open `index.html` in a browser.

## Data Structure

All question data is embedded as JavaScript objects inside `index.html`.

**Part 5** (`PART5_POOL`): Object with three keys — `초급`, `중급`, `고급` — each holding an array of question objects:
```js
{id, type, stem, options:{A,B,C,D}, answer, exp, tr}
```
- `stem`: The sentence with `-------` as the blank
- `answer`: `"A"`, `"B"`, `"C"`, or `"D"`
- `exp`: Korean grammar explanation
- `tr`: Korean translation

**Part 6** (`PART6_POOL`): Same three keys, each holding an array of passage-set objects:
```js
{ptype, title, passage, questions:[{id, blank, type, options, answer, exp, gp, vocab, tr}]}
```
- `passage`: Text with `[1]`, `[2]`… as numbered blanks
- `blank`: Integer matching the `[N]` placeholder in the passage

## ID Conventions

| Range | Pool |
|---|---|
| 1001–1999 | Part 5 초급 |
| 2001–2999 | Part 5 중급 |
| 3001–3999 | Part 5 고급 |
| 4001–4999 | Part 6 (questions inside passage sets) |

When adding new problems, continue from the last used ID in each range.

## Difficulty Guidelines

- **초급**: 8–12 words per stem, basic grammar (tense, preposition, part-of-speech)
- **중급**: 13–18 words per stem, moderate vocabulary and clause structures
- **고급**: 20+ words per stem, subordinate clauses, advanced vocabulary, inversion/ellipsis

## Key Functions

- `shuffle(arr)` — Fisher-Yates in-place shuffle (used before slicing the pool)
- `generate()` — Entry point; reads `part`, `diffIdx`, `selectedCnt` globals and renders questions
- `makeQ5(q, i)` / `makeQ6(q)` — Build DOM nodes for individual questions
- `pick(id, chosen, correct)` — Handles answer selection and tracks `userAns`/`wrongQs`
- `doWrongNote()` — Re-runs `generate()` using only `wrongQs`
- `savePDF()` — Opens a modal with print/PDF export

## Editing Tips

- Each Edit call must target a **unique string**. For bulk stem expansions, edit one problem at a time — batch scripts on this file have caused errors.
- The `stem` field uses `-------` (seven dashes) as the blank marker; keep this exact.
- Never duplicate an `id` within the same pool.
