---
title: "Brainstorm: pre-talk review docs — speaker notes + build-history case study"
topic: pre-talk-review-docs
date: 2026-04-21
status: active
next: /workflows:plan
---

# Brainstorm: pre-talk review docs

## What We're Building

Two documents in `docs/presentation/` to support the Kelsus Camp 2026 compound-engineering talk:

**Doc 1 — Slide-by-slide speaker notes** (`docs/presentation/speaker-notes.md`).
Master reference keyed to every one of the 18 slides + 4 deep-dives. Per slide: 2–4 key talking points, transition cue, any interactive-demo cue (clickable workflow box / term link / navigator), the likeliest audience question with a pre-baked answer, key numbers/URLs. Rehearsal companion the night before and on-stage backup during the talk.

**Doc 2 — Build-history case study** (`docs/presentation/build-case-study.md`).
Meta-narrative of how this deck + supporting docs came together through AI collaboration. Structured around the compound-engineering cycle the talk itself teaches — **Brainstorm → Plan → Work → Review → Compound** — with real examples from our session mapped to each phase. Meta-recursive by design: the build of a talk about compound engineering, *using* compound engineering.

## Why This Approach

- **Speaker notes over one-pager cheat-sheet**: the talk has many interactive affordances (workflow-box clickables, term links, 4 deep-dives, language/theme toggles). A flat cheat-sheet would lose the "when to click what" cue that matters most during live delivery. Slide-keyed coverage keeps the presenter oriented.
- **Case study over chronological log**: a chronological "what happened on day N" log would read like a changelog — accurate but pedagogically flat. Organizing by the **Brainstorm → Plan → Work → Review → Compound** cycle turns the history into a live demonstration of the talk's thesis. "Exhibit A: I built this deck using the method I'm showing you."
- **Both live under `docs/presentation/`**: new subdirectory, talk-specific. Keeps them away from `docs/solutions/` (durable patterns) and `docs/plans/` (work planning). Lets future artifacts land there too (next presentation, different talk, etc.).

## Key Decisions

- **One directory, two files.** `docs/presentation/speaker-notes.md` and `docs/presentation/build-case-study.md`. Create the directory if it doesn't exist.
- **Speaker notes scope: all 22 slides** — 18 main + 4 vertical deep-dives. Per-slide section 3–6 lines; headline + bullets + "if asked X, say Y" cue when relevant.
- **Case study scope: 5 phases, 1–2 examples each.** Don't catalog every PR; curate the examples that best illustrate each phase. Brainstorm = the MCP research brainstorm (PR #5). Plan = the workflow-cycle alignment plan. Work = the deep-dive navigator feature. Review = the term-link specificity bug caught post-ship. Compound = the three solutions docs we produced along the way.
- **No PR appendix in the case study.** Kept at the end of the file as a thin "artifacts" list only if needed; don't let it bloat into a catalog (that's what the repo's `git log` is for).
- **No slide edits.** These docs describe the deck; they don't modify it.
- **Target length**: speaker notes ~800–1200 words (condense aggressively per slide); case study ~500–700 words.

## Open Questions

*(None — all resolved below.)*

## Resolved Decisions

- **Doc shape 1**: slide-by-slide speaker notes.
- **Doc shape 2**: feedback-loop case study (Approach B), not narrative + appendix (Approach D).
- **Location**: `docs/presentation/`.
- **Interactive demo cues**: speaker notes cover every interactive affordance (4 workflow boxes, 4 term links, 4 navigator rows, language toggle, theme toggle, font sizing). Presenter picks on-stage which to actually demo.
- **Audience Q&A depth**: include a pre-baked Q only on slides where a realistic audience question is likely (typically slides 3, 4, 6–8, 10–13, 15). Skip on filler/transition slides (1, 2, 17, 18).
- **Timing**: total-deck target of 25 min + rough block estimates (intro 3 min, core 15 min, demo 5 min, close 2 min). No per-slide budgets.

## Links & Follow-Ups

- Planning: next step is `/workflows:plan` (will auto-detect this brainstorm).
- Source material for speaker notes: `presentation/index.html` + all `docs/plans/` files + all `docs/solutions/` files.
- Source material for case study: the session's PR history (#1–#6), brainstorms, plans.
- Open PRs to merge before / after this work: PR #5 (`docs/mcp-stdio-research-plan`) still unmerged; its MCP solutions doc is still unwritten. Speaker notes should link to it (even if it lands later).
