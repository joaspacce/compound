---
title: "fix: Remove Unshipped /ce:ideate Command from Presentation"
type: fix
status: completed
date: 2026-04-17
---

# fix: Remove Unshipped `/ce:ideate` Command from Presentation

## Overview

The presentation advertised an `Ideate` workflow step and `/ce:ideate` slash command that doesn't ship with the `compound-engineering` plugin (v2.35.0). Audience members typing the advertised command would get an unknown-command error. Remove every reference to Ideate as a shipped capability from `presentation/index.html`.

## Root Cause

The April 11 plan (`2026-04-11-feat-compound-engineering-plugin-presentation-plan.md`) listed `/ce:ideate` alongside five real commands as aspirational. The slide implementation followed the plan faithfully without verifying each command exists in `~/.claude/plugins/cache/every-marketplace/compound-engineering/<version>/commands/workflows/`. Only five files live there: `brainstorm.md`, `compound.md`, `plan.md`, `review.md`, `work.md`.

A hedging note on slide 4 (*"Ideate is optional"*) acknowledged the uncertainty but left the broken command in the commands table and the clickable box in two workflow diagrams.

## Scope

Single file edits in `presentation/index.html` plus nothing else. The April-dated plans are left intact as historical record of what was originally planned — they represent aspirational state, not shipped state. A separate solutions doc captures the drift lesson.

## Removal Map

| Location (line ranges before edit) | What to remove |
|---|---|
| Slide 4 workflow diagram | `<span class="workflow-box optional clickable" data-i18n="wf.ideate">Ideate</span>` + trailing arrow |
| Slide 4 footnote | `<p data-i18n="s4.note">Ideate is optional · All other steps recommended by docs</p>` |
| Slide 5 commands table | First `<tr>` with `/ce:ideate` + aliases + `s5.cmd1` description cell |
| Slide 9 "Documentation flow" | `<span class="workflow-box optional clickable" data-i18n="wf.ideate">Ideate</span>` + trailing arrow |
| EN dictionary | `wf.ideate`, `s4.note`, `s5.cmd1` keys |
| ES dictionary | `wf.ideate`, `s4.note`, `s5.cmd1` keys |
| PT dictionary | `wf.ideate`, `s4.note`, `s5.cmd1` keys |

Remaining i18n keys (`s5.cmd2`–`s5.cmd6`) are **not renumbered** — they still align with the remaining table rows, so renumbering is gratuitous churn.

## Verification

```bash
# Should return zero matches after the fix:
grep -rni 'ideate\|idear' presentation/

# Source of truth for what commands actually ship:
ls ~/.claude/plugins/cache/every-marketplace/compound-engineering/*/commands/workflows/
# Expect: brainstorm.md  compound.md  plan.md  review.md  work.md
```

## Acceptance Criteria

- [x] No `ideate` / `idear` references remain in `presentation/index.html`
- [x] Slide 4 workflow diagram shows 5 boxes (Brainstorm → Plan → Work → Review → Compound)
- [x] Slide 5 commands table lists 5 commands matching the shipped plugin
- [x] Slide 9 "Documentation flow" diagram mirrors slide 4
- [x] All three language dictionaries (EN/ES/PT) are consistent — same keys, no orphans
- [x] Historical plans (April 11 & 14) remain unchanged as record of original intent
- [x] Drift lesson captured in `docs/solutions/documentation-drift/presentation-advertised-unshipped-slash-command.md`

## Prevention (Carry-Forward)

Before any future presentation or README lists a slash command, verify it with:

```bash
ls ~/.claude/plugins/cache/*/<plugin-name>/*/commands/**/
```

Plans that carry aspirational capabilities forward should annotate status (e.g., `~~/ce:ideate~~ (not shipped)`) so implementers don't treat every bullet as must-ship.

## References

- Solutions doc: `docs/solutions/documentation-drift/presentation-advertised-unshipped-slash-command.md`
- Original presentation plan (unchanged): `docs/plans/2026-04-11-feat-compound-engineering-plugin-presentation-plan.md`
- Clickable workflow boxes plan (unchanged): `docs/plans/2026-04-14-feat-clickable-workflow-cycle-boxes-plan.md`
- Related presentation solution: `docs/solutions/ui-features/revealjs-presentation-with-theming-i18n-branding.md`
