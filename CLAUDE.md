# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

A single-file, dependency-free web app: `smart-problem-generator.html` (~1170 lines). It generates random Thai-curriculum math and physics problems (ม.1–ม.6 / grades 7–12), diagnoses *which* mistake a wrong answer matches, and adapts topic/difficulty selection to the learner's weak spots. UI is bilingual (Thai/English). There is no package.json, build step, bundler, linter, or test runner, and the directory is not a git repo.

## Running and testing

- Run: open `smart-problem-generator.html` directly in a browser (only external resource is Google Fonts).
- Test: no test framework. Generators are validated by a built-in self-test: in the browser console run `__selfTest()` (optionally `__selfTest(200)` for more samples per topic/level). It builds every generator at levels 1–3 and checks the answer is finite, steps exist, both `th`/`en` text exist, the correct answer passes `diagnose`, `unit` is in `unitOpts`, and every mistake tag exists in `MISTAKES` with a valid `step` index. It prints `all generators OK` or a list of failures. Run it after adding or editing any generator.

## Architecture (all in the one `<script>`)

The script is laid out in sections; the generator/checking core is fenced by `// @gen-start` … `// @gen-end` markers (helpers → `MISTAKES` → SVG diagram helpers → `GENERATORS` → `build`/`parseNum`/`diagnose`/`__selfTest`). Keep that block free of DOM access so it stays extractable/testable on its own; UI code (i18n `STR`, `store`, `state`, rendering, event wiring) sits after `@gen-end`.

**Generators.** Each topic is registered with `def({ id, subject: 'math'|'physics', grades: [..], name: L(th,en), gen(lv) })`. `gen(lv)` (lv 1–3) returns a problem object: `text`, `answer` (number), optional `answerText` (e.g. fraction display), `unit` + `unitOpts` (physics; the user must also pick the unit), `note`, `tol`, `steps[]`, `hint`, optional `diagram` SVG, and `mistakes[]`.

**Mistake diagnosis.** `mistakes` entries are `{ tag, value, step }`: `value` is the wrong answer a student making that error would produce, `tag` keys into the `MISTAKES` catalogue (bilingual label + tip), `step` is the solution step index to point the student at. `build()` drops mistakes that are non-finite, equal to the real answer, or duplicate another mistake value. `diagnose()` matches the submitted number against the answer (tolerance via `tolOf`: max(0.006, 0.5% of value)), then against mistake values, then falls back to generic sign-flip and power-of-ten (`magnitude`) detection. When adding a generator, compute the wrong values from the same random variables so the diagnosis is real, and add any new tag to `MISTAKES`.

**Bilingual text.** All user-facing strings are `L(th, en)` objects (`en` defaults to `th` if omitted, useful for pure-math steps); read them via `tr()`. UI chrome strings live in `STR.th` / `STR.en` and are read with `t(key)`; static HTML nodes use `data-i18n`. Keep both languages in sync. Rendered math uses the `ex()` and `sup()` helpers and `n2s`/`neg`/`fmt` for the Unicode minus (`−`) formatting; `parseNum` accepts both `-` and `−`, decimals and `a/b` fractions.

**State and adaptivity.** `state` holds language, `settings` (subjects, grades, difficulty, disabled topics, weak-mode) and `stats` (per-topic attempts, last-10 `recent` results, auto level `lvl`, streak counters, mistake tag counts). All persisted through the `store` wrapper (try/catch'd localStorage) under keys `smpg-lang`, `smpg-settings`, `smpg-stats`. `chooseTopic()` does weighted random selection (lower recent accuracy and more recorded mistakes ⇒ higher weight; avoids repeating the last topic; weak mode restricts to the 3 least-accurate tried topics) and returns a `reason` shown to the user. In "auto" difficulty, `record()` moves a topic's level up after 3 correct in a row and down after 2 wrong in a row.

**Rendering.** Plain string-template rendering into fixed DOM nodes via `renderSettings/Problem/Feedback/Solution/Insights`, composed by `renderAll()`. Use `esc()` for any user-originated text. Theme (light/dark, `data-theme` on `<html>`, defaults to `prefers-color-scheme`) is CSS-variable driven; SVG diagrams use shared classes (`d-line`, `d-shape`, `d-accent`, …) so they follow the theme.
