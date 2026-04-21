---
title: "docs: Pre-talk review docs — speaker notes + build-history case study"
type: docs
status: completed
date: 2026-04-21
brainstorm: docs/brainstorms/2026-04-21-pre-talk-review-docs-brainstorm.md
---

# docs: Pre-talk review docs — speaker notes + build-history case study

## Overview

Source brainstorm: `docs/brainstorms/2026-04-21-pre-talk-review-docs-brainstorm.md`. Two deliverables under a new `docs/presentation/` subdirectory for the Kelsus Camp 2026 talk.

1. **`docs/presentation/speaker-notes.md`** — slide-by-slide reference for rehearsal and on-stage backup.
2. **`docs/presentation/build-case-study.md`** — feedback-loop case study narrating how this deck was built, structured around the Brainstorm → Plan → Work → Review → Compound cycle the talk itself teaches.

Both docs describe existing artifacts. **No slide edits, no code changes.**

## Consolidated Inputs (already known)

### Deck structure (18 main + 4 deep-dives)

| # | Section title | Interactive affordance | Q-likely? |
|---|---|---|---|
| 1 | Title (Kelsus + Camp 2026) | Kelsus cluster → kelsus.com | No |
| 2 | What Is It? | — | No |
| 3 | The Core Philosophy | — | Yes |
| 4 | The Workflow Cycle | 5 clickable workflow boxes (Brainstorm dashed/optional) | Yes |
| 5 | Key Commands | "optional" chip on brainstorm row | Yes |
| 6 | Brainstorm & Plan | — | Yes |
| 7 | Work & Review | **2 term links** (Git worktrees, Rails/DHH); down-arrow to 2 deep-dives | Yes |
| 7.1 | *Deep-dive: Git worktrees* | navigator (Back to Work & Review / ← / →) | — |
| 7.2 | *Deep-dive: Rails & DHH* | navigator | — |
| 8 | Compound | — | Yes |
| 9 | In Practice (doc flow / my flow) | clickable boxes on both flows | No |
| 10 | Multi-Platform Support | — | Yes |
| 11 | Tech Stack | **1 term link** (MCP Server); down-arrow to 2 deep-dives | Yes |
| 11.1 | *Deep-dive: What is MCP?* | navigator (Back to Tech Stack / ← / →) | — |
| 11.2 | *Deep-dive: MCP transports & limitations* | navigator | — |
| 12 | Pros | — | Yes |
| 13 | Cons (red heading) | **1 cross-stack term link** (MCP limitations → 11.2) | Yes |
| 14 | When to Use It | — | No |
| 15 | When NOT to Use It (red heading) | — | Yes |
| 16 | Getting Started | — | No |
| 17 | Questions | — | No |
| 18 | Thank You | Kelsus watermark → kelsus.com | No |

Global affordances on every slide: **language toggle** (EN/ES/PT), **theme toggle** (dark/light), **font size A-/A+**, **reset button**.

### Case-study source material (git history as of today)

| PR | What it delivered | Feedback-loop phase it best illustrates |
|---|---|---|
| #1 `67c896c` | Align presentation with Every.to canonical Brainstorm→Plan→Work→Review→Compound flow | **Plan** (research-heavy; external source-of-truth alignment) |
| #2 `9cf2407` | Optional deep-dive slides + navigator + red When-NOT heading + clickable Kelsus title | **Work** (feature implementation from a detailed plan) |
| #3 `8b31101` | Fix term-link dotted underline — Reveal theme specificity bug | **Review** (caught after ship, fixed immediately) |
| #4 `3683532` | Compound learning: Reveal.js theme CSS silently outranks unscoped classes | **Compound** (solutions doc compounding the #3 lesson) |
| #5 *(unmerged)* | Brainstorm + plan on MCP stdio + compound plugin architecture | **Brainstorm** (scoping research before code) |
| #6 `15ad4d5` | Refocus MCP deep-dives on plugin's HTTP-based limitation | **Plan → Work** (driven by #5 research) |
| pre-PR `dbe476b` | Remove unshipped `/ce:ideate` command | **Review + Compound** (drift caught + documented) |

Compounding outputs in `docs/solutions/`:

- `documentation-drift/presentation-advertised-unshipped-slash-command.md`
- `ui-features/revealjs-presentation-with-theming-i18n-branding.md`
- `ui-features/revealjs-theme-css-specificity.md`

Plans in `docs/plans/` (8 files) — chronological record of what got scoped.

## Doc 1 — `docs/presentation/speaker-notes.md`

### Structure

```markdown
---
title: "Kelsus Camp 2026 — Speaker Notes"
date: 2026-04-21
event: Kelsus Camp 2026
presenter: Joaquín Pacce
target_duration: 25 minutes
---

# Kelsus Camp 2026 — Speaker Notes

## How to use this doc
- Read top-to-bottom the night before for rehearsal.
- On stage, skim the slide-section for the slide you just opened.
- Deep-dive / Q&A cues are called out inline.

## Global cues
- **Language**: EN default. Switch to ES/PT only if the audience skews that way and you're comfortable — the toggle doesn't pause.
- **Theme**: default dark. Flip to light only if projector washes out.
- **Font size**: A+ twice if the back row complains. Reset with the ↺ button.
- **Watermark**: Kelsus logo top-left on every slide but 1 — it's a kelsus.com link if you need to point there.

## Timing
Target total: 25 min. Rough blocks:
- Intro (slides 1–3): 3 min
- Core mechanics (slides 4–9): 10 min
- Plugin context (slides 10–13): 5 min
- Fit + close (slides 14–18): 5 min
- Buffer / Q&A: 2 min

## Slide 1 — Title
[3–5 lines]

## Slide 2 — What Is It?
[3–5 lines, no Q]

## Slide 3 — The Core Philosophy
- Talking point 1
- Talking point 2
- Talking point 3
- **If asked:** "Isn't 80/20 planning vs execution unrealistic for tight sprints?"
  - **Answer:** …

## Slide 4 — The Workflow Cycle
- Talking point 1
- Talking point 2
- **Demo cue:** every box is clickable — tap Plan to jump to slide 6. Brainstorm is dashed because it's optional pre-planning per Every.to's canonical flow.
- **If asked:** "Why is Brainstorm dashed and the rest solid?"
  - **Answer:** …

## Slide 5 — Key Commands
- Talking points
- **Demo cue:** note the "optional" chip on the brainstorm row.
- **If asked:** "Why no /ce:ideate?"
  - **Answer:** Early drafts of the deck had it. It's not shipped in the plugin, so we removed it — documented in docs/solutions/documentation-drift/…

## Slide 6 — Brainstorm & Plan
- …
- **If asked:** "What makes a good brainstorm vs diving straight to plan?"

## Slide 7 — Work & Review
- Talking points
- **Demo cue:** the "Git worktrees" and "Rails (DHH)" term links jump to deep-dives — tap if the audience looks like it wants depth. Otherwise skip down.
- **If asked:** likely Qs

### Deep-dive 7.1 — Git worktrees
- 3 bullets
- Navigator cue: ↑ Back returns to slide 7; ← / → skip to slides 6 / 8.

### Deep-dive 7.2 — Rails & DHH
- …

## Slide 8 — Compound
- …
- **If asked:** "Does this really compound in practice or is it aspirational?"

## Slide 9 — In Practice
[skip Q — transition slide, just move through]

## Slide 10 — Multi-Platform Support
- …
- **If asked:** "Does this work in Cursor / OpenCode?"

## Slide 11 — Tech Stack
- Talking points
- **Demo cue:** "MCP Server" is a term link — tap for the 2-slide MCP deep-dive.
- **If asked:** likely Qs

### Deep-dive 11.1 — What is an MCP?
- 3 bullets
- Navigator cue: ↑ Back → Tech Stack

### Deep-dive 11.2 — MCP transports & limitations
- 5 bullets centered on plugin's HTTP choice
- **Key reframe:** the stdio warning on slide 13 is *this plugin-specific*, not abstract.

## Slide 12 — Pros
- …

## Slide 13 — Cons
- **Demo cue:** "MCP limitations" is a cross-stack term link → lands on deep-dive 11.2.
- **If asked:** "Does that affect me on Claude Code?"
  - **Answer:** No — Claude Code supports HTTP. Bite is on OpenCode/Codex.

## Slide 14 — When to Use It
[no Q likely]

## Slide 15 — When NOT to Use It
- Red heading intentional — tonal contrast with slide 14.
- **If asked:** "So you'd skip compound engineering for prototypes?"

## Slide 16 — Getting Started
[no Q likely]

## Slide 17 — Questions
Open the floor. Expected topics (crib): MCP/Cursor compat, cost of AI pair-programming, testing discipline, review-agent accuracy.

## Slide 18 — Thank You
Thank audience. Drop links: kelsus.com, github.com/EveryInc/compound-engineering-plugin.

## References
- Deck: `presentation/index.html`
- Plans: `docs/plans/` (8 files, chronological)
- Solutions: `docs/solutions/` (3 docs — drift, i18n/theming, CSS specificity)
- Sibling brainstorm: `docs/brainstorms/2026-04-21-mcp-stdio-and-compound-plugin-brainstorm.md` (PR #5)
```

### Target length & tone

- **Length**: 800–1200 words total.
- **Tone**: terse, imperative, personal ("tap", "note", "skip"). Not marketing copy.
- **Q&A depth**: 1 paragraph per pre-baked answer. Link to deep-dive slides or solutions docs for more.
- **No new facts**: everything in speaker notes is derived from the existing deck + already-shipped solutions docs.

## Doc 2 — `docs/presentation/build-case-study.md`

### Structure

```markdown
---
title: "Building a talk about compound engineering, using compound engineering — Kelsus Camp 2026"
date: 2026-04-21
event: Kelsus Camp 2026
---

# Building this deck using the method it teaches

## One-paragraph pitch
This deck was built iteratively with an AI pair (Claude Code). The process followed the Brainstorm → Plan → Work → Review → Compound cycle that the deck itself describes. Each phase left a durable artifact: brainstorms in `docs/brainstorms/`, plans in `docs/plans/`, shipped code in `presentation/`, and lessons in `docs/solutions/`. This is a short walkthrough of the cycle with examples from the build, organized by phase. Reading time: ~5 minutes.

## Brainstorm
**Example:** MCP stdio investigation (PR #5, branch `docs/mcp-stdio-research-plan`).

Rather than grabbing the first interpretation of "tell me about MCP limitations", we explored what the end-state should be. Two shape questions — "what does 'done' look like?" and "which angle matters most?" — narrowed the deliverable from "generic MCP write-up" to a tight scope: **plugin architecture + why stdio is safer**, packaged as one searchable solutions doc. The brainstorm doc is the artifact; its Resolved Decisions section is what the plan inherits.

**Why this illustrates the cycle:** the brainstorm is *not* a plan — it answers **WHAT** to build. Without it, the plan would have been broader, longer, less useful.

## Plan
**Example:** Align presentation with Every.to canonical flow (PR #1, `67c896c`).

The plan ran research agents in parallel: a WebFetch of the canonical Every.to guide + two repo-research agents surveying the presentation's current workflow markup and related solutions docs. The finding that reshaped the work: Every.to's canonical cycle is **Plan → Work → Review → Compound** (Brainstorm is optional pre-planning, not co-equal). That one fact turned into a 4-surface edit: slide 4, slide 5 commands table, slide 9, and three i18n dictionaries.

**Why this illustrates the cycle:** the plan doc turned into the source-of-truth for implementation. `/workflows:work` read it and executed without additional research — that's the "80% planning / 20% execution" ratio, in practice.

## Work
**Example:** Optional deep-dive slides + navigator (PR #2, `9cf2407`).

Seven discrete tasks tracked with TaskCreate/TaskUpdate: CSS for navigator + term-links, vertical nesting for slide 7 and slide 11, cross-stack link on slide 13, red heading on slide 15, clickable Kelsus title on slide 1, i18n for all new keys in EN/ES/PT, visual verification via headless Chrome. Each task flipped in_progress → completed as it landed. The PR was one coherent feature, not a WIP.

**Why this illustrates the cycle:** "Work" isn't "just code it." It's execute-the-plan-with-task-tracking-so-nothing-drops. Screenshot verification before the commit was the in-session "test".

## Review
**Example:** Term-link dotted underline bug (PR #3, `8b31101`).

The term links shipped in PR #2 rendered as plain accent-colored text — no visible underline. Caught post-ship by the user ("I don't see the term links"). Root cause: Reveal.js's bundled `black.css` theme has `.reveal a { text-decoration: none; }` at specificity `(0,1,1)`, outranking the unscoped `.term-link` rule at `(0,1,0)`. Fixed by scoping under `.reveal a.term-link`.

**Why this illustrates the cycle:** Review isn't always a formal reviewer agent — sometimes it's the user noticing a visual regression. The value is the *speed* of the feedback loop. Reported and fixed in the same session.

## Compound
**Example:** `docs/solutions/ui-features/revealjs-theme-css-specificity.md` (PR #4, `3683532`).

Within minutes of fixing PR #3, the lesson was captured as a solutions doc with a carry-forward rule: *"Scope interactive-element CSS under `.reveal` whenever the vendored theme has a matching element selector."* The doc is now discoverable the next time someone adds a styled `<a>`, `<button>`, or `<input>` to the deck — the five-minute lookup that would otherwise be the same 30-minute specificity debug.

Another compound-step example from earlier in the build: the `/ce:ideate` drift (commit `dbe476b`) produced `docs/solutions/documentation-drift/presentation-advertised-unshipped-slash-command.md` with the rule *"Verify every slash command before shipping."* That rule explicitly shaped later PRs (#2, #6) — every command listed in new deep-dive content was grep-checked against the plugin's `commands/` directory before landing.

**Why this illustrates the cycle:** Compound is where the loop *becomes* a loop. Without the solutions doc, PR #3's lesson would be re-learned the next time. With it, future work starts 25 minutes ahead.

## The numbers
- **6 merged PRs** + 1 in-flight (#5, MCP research brainstorm + plan).
- **8 plans** in `docs/plans/`, chronological. Every feature / fix had a plan first.
- **3 solutions docs** compounding the lessons from drift, theming, and CSS specificity.
- **2 brainstorms** — one for MCP research, one for these pre-talk docs themselves.
- **4 review-caught-or-user-caught issues** that turned into solutions docs or fix PRs.

## What to notice
- The artifact trail is what makes the loop *real*. Conversations are ephemeral; `docs/` is not.
- Each compound step is small (~100–200 lines) but the expected payoff is *all future work in this area*.
- The MCP research branch (#5, still unmerged as of this writing) is the next Brainstorm → Plan boundary: the solutions doc it plans to produce will become another compound artifact.
```

### Target length & tone

- **Length**: 500–700 words.
- **Tone**: reflective, specific, example-led. Uses PR numbers and SHAs so the reader can verify.
- **Meta-recursion is the point**: the doc explicitly closes with "this loop built itself" — the Kelsus audience will see it.

## Acceptance Criteria

### Both docs

- [x] Both files exist at the specified paths under `docs/presentation/` (new subdirectory).
- [x] No edits to `presentation/index.html` or any other shipped artifact.
- [x] No new facts introduced beyond what's already in the deck, plans, brainstorms, and solutions docs. Every claim is grep-verifiable against an existing file.

### Speaker notes specifically

- [x] All 22 sections present (slides 1–18 + deep-dives 7.1, 7.2, 11.1, 11.2).
- [x] Every interactive affordance (Kelsus click, 5 workflow boxes on slide 4, "optional" chip on slide 5, 2 term links on slide 7, 1 term link on slide 11, 1 cross-stack term link on slide 13, 4 navigator rows on deep-dives, language/theme/font toggles) appears as a "Demo cue" line at least once.
- [x] Pre-baked audience Qs appear on slides 3, 4, 6, 7, 8, 10, 11, 13, 15 (the Q-likely set). No Q on slides 1, 2, 9, 14, 16, 17, 18.
- [x] Timing block at the top matches the brainstorm (25 min total; 3+10+5+5+2 blocks).
- [x] Length: 800–1200 words.

### Case study specifically

- [x] Organized as five sections (Brainstorm / Plan / Work / Review / Compound) + a "Numbers" section + a "What to notice" closing.
- [x] Each phase section cites exactly 1–2 real examples with PR numbers and short SHAs.
- [x] Every cited artifact (solutions docs, brainstorms, plans) resolves to a real file on disk.
- [x] Length: 500–700 words.
- [x] Notes PR #5 is unmerged (acknowledges in-flight state).

## Verification

```bash
mkdir -p docs/presentation/

# After writing both files:
wc -w docs/presentation/speaker-notes.md docs/presentation/build-case-study.md
# Expect speaker-notes.md ~800-1200, build-case-study.md ~500-700 (excluding frontmatter)

# Every cited artifact exists
grep -oE 'docs/[a-z/-]+\.md' docs/presentation/*.md | sort -u | while read f; do
  [ -f "$f" ] && echo "OK  $f" || echo "MISS $f"
done

# Every cited PR number is legitimate (1-6 merged, 5 unmerged)
grep -oE 'PR #[0-9]+' docs/presentation/*.md | sort -u

# Speaker notes covers all 22 slide sections
grep -cE '^## Slide [0-9]+|^### Deep-dive' docs/presentation/speaker-notes.md
# Expect: 22
```

## Out of Scope

- Rehearsal audio / video. Speaker notes only.
- A formal talk abstract for the Kelsus Camp program. If needed, that's a separate tiny deliverable.
- Editing the deck content based on what the speaker notes reveal. If a slide is unclear, fix it in a separate PR (the notes should not be a band-aid over weak slide content).
- A full chronology appendix. The case study is curated, not exhaustive — `git log` is the exhaustive view.
- Translating the speaker notes into ES/PT. The presenter speaks English notes + delivers in whichever language the deck is toggled to.
- Producing a blog post / tweet thread from the case study. That's downstream reuse, not this deliverable.

## Risks

- **PR #5 may still be unmerged at ship time.** The case study acknowledges this. If #5 merges before these docs ship, update the "in-flight" language to past tense.
- **Slide content may change between now and talk day.** Speaker notes are tightly coupled to slide structure. If a slide is added/removed, re-run this plan's verification.
- **Length creep on speaker notes.** Temptation to write prose per slide. Resist — notes beat paragraphs every time for on-stage use.

## MVP pseudocode — shape reminders

### `docs/presentation/speaker-notes.md`
Follow the structure block in section "Doc 1" above. Sections are Markdown `##` for slides and `###` for deep-dives.

### `docs/presentation/build-case-study.md`
Follow the structure block in section "Doc 2" above. Five phase sections + Numbers + What to notice.

## References

### Internal

- Brainstorm: `docs/brainstorms/2026-04-21-pre-talk-review-docs-brainstorm.md`
- Deck: `presentation/index.html`
- Sibling brainstorm (PR #5, unmerged): `docs/brainstorms/2026-04-21-mcp-stdio-and-compound-plugin-brainstorm.md`
- Plans (8): `docs/plans/` — chronological record, April 11 → April 21
- Solutions (3):
  - `docs/solutions/documentation-drift/presentation-advertised-unshipped-slash-command.md`
  - `docs/solutions/ui-features/revealjs-presentation-with-theming-i18n-branding.md`
  - `docs/solutions/ui-features/revealjs-theme-css-specificity.md`

### External

- Every.to Compound Engineering guide: https://every.to/guides/compound-engineering
- Plugin repo: https://github.com/EveryInc/compound-engineering-plugin
- Reveal.js: https://revealjs.com
- Kelsus: https://kelsus.com
