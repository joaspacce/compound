---
title: "Building a talk about compound engineering, using compound engineering"
subtitle: "Kelsus Camp 2026 — build case study"
date: 2026-04-21
event: Kelsus Camp 2026
---

# Building this deck using the method it teaches

## One-paragraph pitch

This deck was built iteratively with an AI pair (Claude Code). The process followed the same **Brainstorm → Plan → Work → Review → Compound** cycle that the deck itself describes. Each phase left a durable artifact: brainstorms in `docs/brainstorms/`, plans in `docs/plans/`, shipped code in `presentation/`, and lessons in `docs/solutions/`. This is a short walkthrough of the cycle with real examples from the build. Reading time: ~5 minutes.

## Brainstorm

**Example: the MCP stdio investigation** (PR #5, branch `docs/mcp-stdio-research-plan`).

A mid-build question — "some platforms only support stdio, why?" — could have been answered in two sentences. Instead, we paused and explored what *"done"* looked like. Two shape questions ("what does 'done' look like?" and "which angle matters most?") narrowed the deliverable from a generic MCP write-up to a tight scope: **plugin architecture + why stdio is safer**, packaged as one searchable solutions doc.

The brainstorm doc is the artifact. Its *Resolved Decisions* section is what the plan inherits and doesn't re-litigate.

**Why it illustrates the cycle:** the brainstorm answers *WHAT*, not *HOW*. Without it, the plan would have been broader, longer, and less useful. The cost of 10 minutes of clarification paid for itself in every subsequent phase.

## Plan

**Example: align the deck with Every.to's canonical flow** (PR #1, `67c896c`).

The plan ran three research agents in parallel: a WebFetch of the canonical Every.to guide + two repo-research agents surveying the presentation's current workflow markup and related solutions docs. The finding that reshaped the work: Every.to's canonical cycle is **Plan → Work → Review → Compound**. Brainstorm is optional pre-planning, not co-equal.

That one fact turned into a four-surface edit in the deck: slide 4, slide 5 commands table, slide 9, and all three i18n dictionaries. The plan specified each edit before any code was written.

**Why it illustrates the cycle:** the plan became the source-of-truth for implementation. `/workflows:work` read the plan and executed without additional research — that's the "80% Plan & Review / 20% Work & Compound" ratio from slide 3, in practice.

## Work

**Example: optional deep-dive slides + navigator** (PR #2, `9cf2407`).

Seven discrete tasks tracked with `TaskCreate` / `TaskUpdate`: CSS for navigator + term links, vertical nesting for slide 7 and slide 11, cross-stack link on slide 13, red heading on slide 15, clickable Kelsus title on slide 1, i18n for all new keys in EN/ES/PT, visual verification via headless Chrome. Each task flipped `in_progress → completed` as it landed.

Screenshot verification happened *before* the commit, not after. The PR that shipped was one coherent feature, not a mid-build work-in-progress.

**Why it illustrates the cycle:** "Work" isn't "just code it." It's execute-the-plan, with task tracking so nothing drops, and verification so nothing regresses. The visual checks were the in-session "tests."

## Review

**Example: the term-link dotted underline bug** (PR #3, `8b31101`).

The term links shipped in PR #2 rendered as plain accent-colored text — no visible underline. Caught post-ship by the user's report: *"I don't see the term links."*

Root cause took ~5 minutes to find. Reveal.js's bundled `black.css` theme defines `.reveal a { text-decoration: none; }` at specificity `(0,1,1)`. The unscoped `.term-link` rule sat at `(0,1,0)` — silently outranked, `text-decoration` stripped on every term link. Fixed by scoping the selector under `.reveal a.term-link`.

**Why it illustrates the cycle:** Review isn't always a formal reviewer agent. Sometimes it's the user noticing a visual regression. The value is the *speed* of the feedback loop — reported, diagnosed, and fixed inside the same session.

## Compound

**Example 1: Reveal.js theme CSS specificity solutions doc** (PR #4, `3683532`).

Within minutes of fixing PR #3, the lesson was captured as `docs/solutions/ui-features/revealjs-theme-css-specificity.md` with a carry-forward rule: *"Scope interactive-element CSS under `.reveal` whenever the vendored theme has a matching element selector."* The doc is now discoverable the next time someone adds a styled `<a>`, `<button>`, or `<input>` to this deck — a 30-second lookup instead of a 20-minute specificity debug.

**Example 2: the `/ce:ideate` drift lesson** (earlier commit `dbe476b`).

Earlier in the build, the deck advertised a `/ce:ideate` slash command that the plugin doesn't ship. Caught during a review-style check against the plugin's `commands/` directory. Removed from the deck; the *pattern* of the mistake was captured in `docs/solutions/documentation-drift/presentation-advertised-unshipped-slash-command.md` with the rule *"Verify every slash command before shipping."*

That rule explicitly shaped later PRs. Every command referenced in new deep-dive content (PRs #2 and #6) was grep-checked against the plugin's actual source before landing.

**Why it illustrates the cycle:** Compound is where the loop *becomes* a loop. Without the solutions docs, each lesson would be re-learned. With them, future work starts 20 minutes ahead of where it would have otherwise.

## The numbers

- **6 merged PRs** (#1, #2, #3, #4, #6, and an earlier direct commit `dbe476b`).
- **1 in-flight PR** (#5, MCP research brainstorm + plan — unmerged at the time of this writing).
- **8 plans** in `docs/plans/`, chronological from April 11 to April 21. Every feature and fix had a plan first.
- **3 solutions docs** compounding lessons from drift, theming, and CSS specificity.
- **2 brainstorms** — one for MCP research, one for these pre-talk docs themselves.
- **4 bugs or drift issues** caught (by review agents or by the user) that turned into solutions docs or fix PRs.

## What to notice

- The artifact trail is what makes the loop *real*. Conversations are ephemeral; `docs/` is not.
- Each compound step is small — typically 100–200 lines of markdown — but the expected payoff is *all future work in this area*. That's the ratio the talk describes.
- The MCP research branch (#5, still unmerged as of this writing) is the next Brainstorm → Plan boundary. The solutions doc it plans to produce will become another compound artifact. The cycle keeps turning.
- These two pre-talk docs are themselves part of the cycle. The brainstorm + plan that produced them lives in `docs/brainstorms/` and `docs/plans/`; you're reading the *Work* phase right now.

## References

- Sibling doc (speaker notes): `docs/presentation/speaker-notes.md`
- Source brainstorm: `docs/brainstorms/2026-04-21-pre-talk-review-docs-brainstorm.md`
- Source plan: `docs/plans/2026-04-21-docs-pre-talk-review-docs-plan.md`
- All plans: `docs/plans/` (8 files)
- All solutions: `docs/solutions/` (3 docs)
- Every.to canonical guide: https://every.to/guides/compound-engineering
- Plugin repo: https://github.com/EveryInc/compound-engineering-plugin
