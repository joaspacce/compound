---
title: "feat: Align Presentation with Every.to Canonical Compound Engineering Flow"
type: feat
status: completed
date: 2026-04-17
---

# feat: Align Presentation with Every.to Canonical Compound Engineering Flow

## Overview

Re-skin the Reveal.js presentation at `presentation/index.html` so the workflow cycle shown on slides 4, 5, and 9 matches the canonical flow published at [every.to/guides/compound-engineering](https://every.to/guides/compound-engineering). Visually distinguish the **core four-step cycle** (solid borders, solid arrows) from **optional pre-planning** (dashed borders, dashed arrows).

## Problem Statement / Motivation

The current deck shows a 5-step workflow (Brainstorm → Plan → Work → Review → Compound) with all boxes styled identically. Every's canonical guide uses a **4-step core cycle** — **Plan → Work → Review → Compound → (loop)** — and treats Brainstorm as an optional pre-planning tool used "when you're not sure what to build." Rendering Brainstorm as co-equal with the core cycle mis-represents the source of truth and loses a key pedagogical distinction the audience should take away.

This also closes a loop with today's drift fix (commit `dbe476b`, solutions doc `documentation-drift/presentation-advertised-unshipped-slash-command.md`): the presentation should always anchor on shipped, canonical content, with a visual vocabulary for optional/aspirational steps so we never again need to silently advertise something that doesn't exist.

## Source-of-Truth Mapping

| Every.to concept | Presentation treatment |
|---|---|
| "Each unit of engineering work should make subsequent units easier—not harder" | Slide 3 quote — keep as-is |
| 4-step core cycle: Plan → Work → Review → Compound → (loop) | Slide 4 — **solid** boxes + **solid** arrows |
| Brainstorm as optional pre-planning: "When you're not sure what to build, start here" | Slide 4 — **dashed** box + **dashed** arrow, leading into Plan |
| "Plan and review should comprise 80 percent … work and compound the other 20" | Slide 3 item 2 — minor rewording (optional, see "Stretch scope") |
| Slash commands: `/workflows:plan`, `/workflows:work`, `/workflows:review`, `/workflows:compound`, `/workflows:brainstorm` | Slide 5 — mark brainstorm row as optional; keep 5 rows |
| "You don't need every step every time" (documentation vs personal flow) | Slide 9 — already exists, extend with new dashed vocabulary |
| "Ideate" | Not present in Every.to. Do NOT re-add. Referenced in solutions doc as prior drift. |

## Proposed Solution

### 1. Slide 4 — The Workflow Cycle

Change the 5-solid-box diagram to:

```
[Brainstorm] ⇢ [Plan] → [Work] → [Review] → [Compound]
  (dashed)    (dashed)    (solid)   (solid)   (solid)
```

- Brainstorm box: add `.optional` class (already styled with dashed border + muted color).
- Arrow **between Brainstorm and Plan**: apply a new `.arrow.optional` class (to be added — see CSS section).
- Arrows Plan→Work, Work→Review, Review→Compound: solid (unchanged).
- Add a small explanatory caption below the cycle:
  - EN: *"Brainstorm is optional pre-planning · Core cycle loops Plan → Work → Review → Compound"*
  - ES / PT equivalents.
- Keep the existing `s4.loop` "repeat — each cycle compounds knowledge" line.

### 2. Slide 5 — Key Commands

Keep the 5-row commands table and its current order. Add a small "optional" chip/badge in the first column of the Brainstorm row to reinforce its pre-planning status. No reordering, no renumbering of `s5.cmdN` keys (preserves the no-gratuitous-churn rule from the drift solutions doc).

Row after change:

```
/ce:brainstorm  [optional]   Explore requirements interactively
/ce:plan                     Convert ideas into technical plans
/ce:work                     Execute with worktrees & task tracking
/ce:review                   Multi-agent code review
/ce:compound                 Document learnings for reuse
```

The `[optional]` chip is a `<span class="optional-chip">` styled with the same muted/dashed vocabulary as `.workflow-box.optional`.

### 3. Slide 9 — In Practice

- **"Documentation flow"**: mirror slide 4 exactly — Brainstorm dashed, rest solid.
- **"My flow"**: leave untouched. It already uses `.workflow-box.optional` on the Compound box to express the author's personal "Compound when needed" habit. No change.

### 4. CSS additions (`presentation/index.html` style block)

```css
/* New: dashed arrow between an optional box and the next step */
.arrow.optional {
  color: var(--muted);
  opacity: 0.75;
}

/* New: compact optional chip for the commands table */
.cmd-table .optional-chip {
  display: inline-block;
  margin-left: 8px;
  padding: 2px 8px;
  border: 1px dashed var(--muted);
  border-radius: 999px;
  font-size: 0.55em;
  color: var(--muted);
  text-transform: uppercase;
  letter-spacing: 0.04em;
  vertical-align: middle;
}
```

No changes to existing `.workflow-box.optional` rules — they're already correct (dashed border + muted color + muted background).

### 5. Arrow character choice

Two options evaluated — picking **Option A** for simplicity and consistency with existing `&rarr;` pattern. Document the decision inline so future editors don't "fix" it.

- **Option A (chosen)**: Replace the single Brainstorm→Plan arrow character with the dashed Unicode arrow `⇢` (U+21E2), wrapped in `<span class="arrow optional">⇢</span>`. Other arrows stay `&rarr;` (→).
- Option B (rejected): Keep `&rarr;` everywhere and fake the dashed look with a `::before` CSS pseudo-element carrying a dashed `border-bottom`. Introduces alignment fragility across browsers; not worth the CSS.

### 6. i18n keys

Additions (all 3 dicts, EN inline fallback in HTML):

- `s4.note` (re-introduce, different semantics than the removed-dbe476b version):
  - EN: `"Brainstorm is optional pre-planning &middot; Core cycle: Plan &rarr; Work &rarr; Review &rarr; Compound"`
  - ES: `"El Brainstorm es planificaci\u00f3n previa opcional &middot; Ciclo principal: Plan \u2192 Work \u2192 Review \u2192 Compound"`
  - PT: `"Brainstorm \u00e9 planejamento pr\u00e9vio opcional &middot; Ciclo principal: Plan \u2192 Work \u2192 Review \u2192 Compound"`
- `s5.brainstorm_chip` (the word inside the optional chip):
  - EN: `"optional"`, ES: `"opcional"`, PT: `"opcional"`.

No removals. Existing keys (`wf.brainstorm`, `s5.cmd2`-`s5.cmd6`, `s9.*`) preserved.

## Acceptance Criteria

### Functional

- [x] Slide 4 shows Brainstorm with dashed border, followed by Plan/Work/Review/Compound with solid borders.
- [x] The single Brainstorm→Plan arrow is visibly dashed (`⇢`) and muted; all other arrows remain solid `→`.
- [x] Slide 4 caption reads the new `s4.note` text in the active language (EN/ES/PT).
- [x] Slide 5's Brainstorm row shows an "optional" chip; row order unchanged; other rows unchanged.
- [x] Slide 9 "Documentation flow" mirrors slide 4's dashed-Brainstorm treatment.
- [x] Slide 9 "My flow" is unchanged.
- [x] All three i18n dictionaries contain the new `s4.note` and `s5.brainstorm_chip` keys.
- [x] Every command listed in slide 5 is verified to exist in `~/.claude/plugins/cache/every-marketplace/compound-engineering/<version>/commands/workflows/` before merging.
- [x] No reference to "Ideate" / "Idear" reappears anywhere.

### Visual

- [x] Dashed vs solid borders are visually distinguishable under both dark and light themes.
- [x] Dashed arrow character `⇢` renders correctly in the presentation's font stack on macOS Chrome/Safari and Firefox (manual check).
- [x] The optional chip on slide 5 doesn't wrap the command cell onto a second line at the default font size.

### Quality gates

- [x] `grep -rni 'ideate\|idear' presentation/` returns zero matches.
- [x] `grep -n 'workflow-box' presentation/index.html` confirms the `.optional` class is applied only where intended (slide 4 Brainstorm, slide 9 doc-flow Brainstorm, slide 9 my-flow Compound).
- [x] Open the deck in browser, tab through all 18 slides — no layout regressions, no overflow on slides 4/5/9.
- [x] Toggle languages EN → ES → PT → EN. Every new string appears in each language; no raw keys leak through.

## Verification

```bash
# 1. Canonical source-of-truth check (no typos in the 5 shipped commands)
ls ~/.claude/plugins/cache/every-marketplace/compound-engineering/*/commands/workflows/

# 2. All surfaces that reference the workflow vocabulary
grep -n 'workflow-box\|wf\.\|s4\.\|s5\.\|s9\.' presentation/index.html

# 3. Regression check on the recently-fixed drift
grep -rni 'ideate\|idear' presentation/ docs/plans/

# 4. Open and eyeball under both themes + all three languages
open presentation/index.html
```

## Out of Scope

- Adding `/lfg`, `/deepen-plan`, `/triage`, `/resolve_pr_parallel`, `/resolve_todo_parallel` to slide 5 (they ship in the plugin and are cited by the Every.to guide, but they're meta/utility commands and would crowd the slide). Consider a follow-up "bonus commands" slide.
- Adding the 5-stage adoption model (Stage 0 Manual → Stage 5 Parallel Cloud). Worthwhile but belongs in a separate content-expansion plan.
- Restructuring slide 6 ("Brainstorm & Plan") into two slides. Current combined slide is fine given Brainstorm's now-secondary status.
- Re-adding Ideate in any form. The Every.to guide does not list it.

## Stretch Scope (confirm before doing)

- Slide 3 item 2 currently reads `"80% Planning & Review / 20% Execution"`. Every.to's exact phrasing is `"Plan and review should comprise 80 percent of an engineer's time, and work and compound the other 20 percent."` Consider updating to `"80% Plan & Review / 20% Work & Compound"` for verbatim alignment. Skip unless the user confirms — it's a copy change beyond the workflow-visual scope of this plan.

## Drift-Prevention Rules Carried Forward

From `docs/solutions/documentation-drift/presentation-advertised-unshipped-slash-command.md`:

1. **Verify every slash command exists** in the installed plugin's `commands/` directory before listing it on a slide.
2. **Grep all surfaces** (two workflow diagrams, one commands table, three i18n dicts — six places) whenever a workflow concept is added, renamed, or removed.
3. **Don't renumber `s5.cmdN` keys** just because rows are added or removed — alignment is by key, not index.
4. **Aspirational content needs explicit visual vocabulary** (dashed borders, dashed arrows, "optional" chip). Hedging text alone is not enough.

## MVP pseudocode

### `presentation/index.html` — Slide 4 workflow (HTML)

```html
<section>
  <h2 data-i18n="s4.title">The Workflow Cycle</h2>
  <div class="workflow">
    <span class="workflow-box optional clickable" data-i18n="wf.brainstorm" onclick="Reveal.slide(5)">Brainstorm</span>
    <span class="arrow optional">&#x21E2;</span>
    <span class="workflow-box clickable" data-i18n="wf.plan"       onclick="Reveal.slide(5)">Plan</span>
    <span class="arrow">&rarr;</span>
    <span class="workflow-box clickable" data-i18n="wf.work"       onclick="Reveal.slide(6)">Work</span>
    <span class="arrow">&rarr;</span>
    <span class="workflow-box clickable" data-i18n="wf.review"     onclick="Reveal.slide(6)">Review</span>
    <span class="arrow">&rarr;</span>
    <span class="workflow-box clickable" data-i18n="wf.compound"   onclick="Reveal.slide(7)">Compound</span>
  </div>
  <p class="loop-arrow" data-i18n="s4.loop">&larr;&larr;&larr; repeat &mdash; each cycle compounds knowledge &rarr;&rarr;&rarr;</p>
  <p class="small" data-i18n="s4.note">Brainstorm is optional pre-planning &middot; Core cycle: Plan &rarr; Work &rarr; Review &rarr; Compound</p>
</section>
```

### `presentation/index.html` — Slide 5 brainstorm row (HTML)

```html
<tr>
  <td>
    /ce:brainstorm
    <span class="aliases">/workflows:brainstorm &middot; /compound-engineering:workflows:brainstorm</span>
    <span class="optional-chip" data-i18n="s5.brainstorm_chip">optional</span>
  </td>
  <td data-i18n="s5.cmd2">Explore requirements interactively</td>
</tr>
```

### `presentation/index.html` — CSS additions (style block)

```css
.arrow.optional { color: var(--muted); opacity: 0.75; }

.cmd-table .optional-chip {
  display: inline-block;
  margin-left: 8px;
  padding: 2px 8px;
  border: 1px dashed var(--muted);
  border-radius: 999px;
  font-size: 0.55em;
  color: var(--muted);
  text-transform: uppercase;
  letter-spacing: 0.04em;
  vertical-align: middle;
}
```

## References

### Internal

- Presentation file: `presentation/index.html`
- Related solutions — drift: `docs/solutions/documentation-drift/presentation-advertised-unshipped-slash-command.md`
- Related solutions — patterns: `docs/solutions/ui-features/revealjs-presentation-with-theming-i18n-branding.md`
- Prior plan — Ideate removal: `docs/plans/2026-04-17-fix-remove-unshipped-ideate-command-plan.md`
- Prior plan — clickable boxes: `docs/plans/2026-04-14-feat-clickable-workflow-cycle-boxes-plan.md`
- Prior plan — original deck: `docs/plans/2026-04-11-feat-compound-engineering-plugin-presentation-plan.md`

### External

- Every.to canonical guide: https://every.to/guides/compound-engineering
- Plugin repository: https://github.com/EveryInc/compound-engineering-plugin
- Reveal.js: https://revealjs.com/
- Dashed arrow Unicode: U+21E2 (⇢)
