# Product

<!-- impeccable:product-schema 1 -->

## Platform

web

## Users

Thai secondary-school students (ม.1–ม.6, grades 7–12) practising math and physics on their own, outside class. They work alone, on whatever device they have, and want to know why an answer was wrong, not only that it was.

## Product Purpose

Generates random Thai-curriculum math and physics problems for self-study. Success is a student who finds out which specific mistake they made, fixes it, and gets the next problem right.

## Positioning

It diagnoses the mistake behind a wrong answer. Each problem knows the wrong values a student making a particular error would produce, so a wrong answer is matched to that error and the student is pointed at the solution step where it went wrong. A worksheet or generic quiz app only marks right or wrong.

## Operating Context

A student opens the page, answers a generated problem (physics also asks for the unit), reads the diagnosis, and works through the solution step by step. They can also attach a photo of handwritten working and self-grade against the solution.

## Capabilities and Constraints

- Topics for math and physics, grades ม.1–ม.6, three difficulty levels; topic and difficulty adapt to the learner's recent accuracy and recorded mistakes.
- Bilingual Thai/English UI; every user-facing string has both.
- Light and dark theme.
- Binding constraint: stays a single HTML file with no dependencies, build step, or server. The only external resource is Google Fonts.
- Current behaviour, not confirmed as binding: stats, settings, and attached photos stay in the browser (localStorage / memory) and are never uploaded.

## Evidence on Hand

The app itself is the evidence: `smart-problem-generator.html`, with a built-in `__selfTest()` for generators. No user research, testimonials, or usage data exist, and none should be invented.

## Product Principles

- Name the specific mistake, not just "wrong".
- Point to the step where it went wrong, so the student can fix it rather than only see the answer.
- Adapt to the learner without asking them to configure it.
- Stay simple to run: open the file and practise.

## Accessibility & Inclusion

No product-specific standard established.
