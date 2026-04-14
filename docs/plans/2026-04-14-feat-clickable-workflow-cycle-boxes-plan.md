---
title: "feat: Make Workflow Cycle Boxes Clickable"
type: feat
status: completed
date: 2026-04-14
---

# feat: Make Workflow Cycle Boxes Clickable

## Overview

On slide 4 (The Workflow Cycle), make each workflow box (Ideate, Brainstorm, Plan, Work, Review, Compound) clickable to navigate to its corresponding detail slide.

## Mapping

| Box | Target Slide | Reveal Index |
|-----|-------------|-------------|
| Ideate | Slide 5: Key Commands | 4 |
| Brainstorm | Slide 6: Brainstorm & Plan | 5 |
| Plan | Slide 6: Brainstorm & Plan | 5 |
| Work | Slide 7: Work & Review | 6 |
| Review | Slide 7: Work & Review | 6 |
| Compound | Slide 8: Compound | 7 |

## Implementation

Add `onclick="Reveal.slide(N)"` and `style="cursor: pointer"` to each `.workflow-box` on slide 4. Add hover effect.

## Acceptance Criteria

- [x] Each workflow box on slide 4 is clickable
- [x] Clicking navigates to the correct detail slide
- [x] Cursor changes to pointer on hover
- [x] Visual hover feedback (accent border glow)
- [x] Also applies to the workflow boxes on slide 9 (In Practice)
