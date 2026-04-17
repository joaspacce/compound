---
title: "Presentation Advertised a Slash Command That Didn't Ship (/ce:ideate)"
date: 2026-04-17
category: documentation-drift
tags:
  - revealjs
  - presentation
  - slash-commands
  - plugin-discovery
  - plan-to-implementation-drift
  - i18n
  - kelsus
modules:
  - presentation/index.html
  - docs/plans/2026-04-11-feat-compound-engineering-plugin-presentation-plan.md
  - docs/plans/2026-04-14-feat-clickable-workflow-cycle-boxes-plan.md
severity: P3
status: resolved
---

# Presentation Advertised a Slash Command That Didn't Ship (`/ce:ideate`)

## Problem / Challenge

The Kelsus Camp 2026 presentation (`presentation/index.html`) showed "Ideate" as the first step of the Compound Engineering workflow cycle and listed `/ce:ideate` (plus aliases `/workflows:ideate` and `/compound-engineering:workflows:ideate`) in the "Key Commands" table. If an audience member typed that command, Claude Code would report it as unknown.

The compound-engineering plugin (v2.35.0) ships these workflow commands only:

```
~/.claude/plugins/cache/every-marketplace/compound-engineering/2.35.0/commands/workflows/
  brainstorm.md
  compound.md
  plan.md
  review.md
  work.md
```

No `ideate.md`. Six commands promised, five actually exist.

## Root Cause

The April 11 presentation plan (`docs/plans/2026-04-11-feat-compound-engineering-plugin-presentation-plan.md`) listed six commands in the "Key Commands" slide, including `/ce:ideate` with the blurb *"Surface high-impact improvements"*. This was aspirational — a step the plan author expected to exist or believed should exist in the plugin. The slide implementation followed the plan faithfully without verifying each command was actually installed.

A self-defensive note was added to slide 4 (`"Ideate is optional · All other steps recommended by docs"`) which hinted at the uncertainty but didn't prevent the command row from going into the table or the clickable workflow box from appearing in two places.

## Solution

Remove every reference to Ideate as a shipped command. Three surfaces needed edits:

**1. HTML structure (`presentation/index.html`)**

```html
<!-- Slide 4: The Workflow Cycle -->
<!-- Removed: -->
<span class="workflow-box optional clickable" data-i18n="wf.ideate" onclick="Reveal.slide(4)">Ideate</span>
<span class="arrow">&rarr;</span>

<!-- Removed note: -->
<p class="small" data-i18n="s4.note">Ideate is optional · All other steps recommended by docs</p>

<!-- Slide 5: Key Commands table — removed first row: -->
<tr>
  <td>/ce:ideate<span class="aliases">/workflows:ideate · /compound-engineering:workflows:ideate</span></td>
  <td data-i18n="s5.cmd1">Surface high-impact improvements</td>
</tr>

<!-- Slide 9: In Practice — removed the Ideate box + arrow in the "Documentation flow" workflow -->
```

**2. i18n dictionaries (EN / ES / PT)**

Removed these keys from each of the three language dictionaries:

- `wf.ideate`
- `s4.note`
- `s5.cmd1`

Remaining keys (`s5.cmd2`–`s5.cmd6`) weren't renumbered — they still match the remaining table rows, so no gratuitous churn.

**3. Plan documents**

Updated both `2026-04-11-feat-compound-engineering-plugin-presentation-plan.md` and `2026-04-14-feat-clickable-workflow-cycle-boxes-plan.md` to remove Ideate from the workflow diagram, the command list, and the clickable-box mapping table. Plans that record history for a shipped feature should reflect what actually shipped, not the aspirational version.

## Verification

```bash
# Nothing left:
grep -ri 'ideate\|idear' presentation/ docs/plans/
# (no matches)

# Plugin commands we can actually invoke:
ls ~/.claude/plugins/cache/every-marketplace/compound-engineering/*/commands/workflows/
```

## Gotchas

### 1. Aspirational Specs Leaking Into User-Facing Content
Planning docs describe the intended future. User-facing output (slides, README, API docs) describes shipped reality. Copying command names from a plan into a slide without invoking each one is how gaps become public.

### 2. Hedging Words Don't Prevent the Bug
The slide deck included *"Ideate is optional"* — the author clearly sensed the uncertainty. An "optional" label on a nonexistent command is still a broken recommendation. If you find yourself hedging a capability, verify it before shipping the hedge.

### 3. Multiple Surfaces Per Feature
One logical concept ("Ideate") lived in six places inside a single HTML file: two workflow diagrams (slides 4 and 9), one commands-table row, and three i18n dictionaries. Removing a feature from a presentation means finding every projection of that feature. Grep first, edit second.

### 4. Historical Solutions Docs Are Frozen
`docs/solutions/ui-features/revealjs-presentation-with-theming-i18n-branding.md` was deliberately left untouched. A solutions doc is a time-stamped record of what was true at a point in time; editing it rewrites history. This new doc supersedes the stale piece of that record.

## Prevention Strategies

**Before documenting a command in any user-facing surface**, run a one-line verification:

```bash
# List the commands that actually ship with the plugin
ls ~/.claude/plugins/cache/*/compound-engineering/*/commands/workflows/
```

Or for any slash command: `ls ~/.claude/plugins/cache/*/<plugin-name>/*/commands/**/`.

**Plan → implementation reviews should cross-check user-facing claims** against reality. For a presentation or README that lists `/foo:bar` commands, the review step should be: *"invoke each one or prove it exists in the plugin directory."* This is a five-minute check that prevents a public embarrassment.

**When a plan lists a capability, annotate its status.** The April 11 plan could have written `~~/ce:ideate~~ (not shipped — drop from slide)` instead of carrying it forward silently. Status annotations in plans prevent implementers from treating every bullet as a must-ship.

**Single preference key / single source of truth for commands.** The plugin's `commands/workflows/` directory is authoritative. Presentations should derive their command list from that directory (even manually, via `ls`) rather than from a planning doc.

## References

- Presentation: `presentation/index.html`
- Related (stale, preserved as historical record): `docs/solutions/ui-features/revealjs-presentation-with-theming-i18n-branding.md`
- Original plan (Apr 11): `docs/plans/2026-04-11-feat-compound-engineering-plugin-presentation-plan.md`
- Clickable workflow boxes plan: `docs/plans/2026-04-14-feat-clickable-workflow-cycle-boxes-plan.md`
- Plugin source of truth: `~/.claude/plugins/cache/every-marketplace/compound-engineering/<version>/commands/workflows/`
