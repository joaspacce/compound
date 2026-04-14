---
title: "feat: Add Kelsus Camp 2026 Branding to Presentation"
type: feat
status: completed
date: 2026-04-14
---

# feat: Add Kelsus Camp 2026 Branding to Presentation

## Overview

Rebrand the Compound Engineering Plugin presentation for the **Kelsus Camp 2026** company reunion. Add presenter name **Joaquin Pacce**, the official Kelsus SVG logo with blinking cursor animation, and persistent branding across all slides.

## What Was Implemented

### Title Slide (Slide 1)

```
┌──────────────────────────────────────────────────┐
│                                                    │
│          [Kelsus SVG] Camp 2026                    │  ← SVG wordmark + "Camp 2026" in accent blue
│                                                    │
│       Compound Engineering Plugin                  │  ← talk title (h1, translated)
│   By Every Inc · github.com/EveryInc/compound-..  │  ← attribution, small/muted, linked
│  Making each unit of work easier than the last     │  ← subtitle, muted (translated)
│                                                    │
│              Presented by                          │  ← translated (EN/ES/PT)
│            Joaquin Pacce                           │  ← presenter name
│                                                    │
└──────────────────────────────────────────────────┘
```

- No top-left watermark on slide 1 (hidden via `data-hide-watermark` attribute + JS)
- Kelsus SVG wordmark inline in the heading, scales with `height: 0.75em`
- "Camp 2026" in accent blue, `font-size: 1.2em` to visually match SVG cap-height
- "Every Inc" links to `https://github.com/EveryInc`
- All links open in new tab (`target="_blank"`)

### Persistent Watermark (Slides 2-16)

```
┌──────────────────────────────────────────────────┐
│ [Kelsus SVG] Camp 2026                   [🌙][EN]│
│                                                    │
│              Slide Content                         │
│                                                    │
└──────────────────────────────────────────────────┘
```

- Fixed top-left, outside `.reveal` div (survives fullscreen)
- Official Kelsus SVG wordmark (18px) + "Camp 2026" text (22px) — visually matched
- Clickable — links to kelsus.com in new tab
- 70% opacity, full on hover
- Hidden on slide 1 via `Reveal.initialize().then()` + `slidechanged` event
- Starts with `display: none` in CSS to prevent flash on first load
- Theme-aware via `currentColor`

### Logo: Official Kelsus SVG

- Used the official SVG wordmark from kelsus.com (not a CSS text approximation)
- All `fill="white"` replaced with `fill="currentColor"` for dark/light theme support
- Blinking cursor animation on the square element (`.kelsus-cursor` class)
  - `@keyframes kelsus-blink` with `step-end` timing, 1s interval
  - Matches the animation on kelsus.com

### Translation Updates

New keys added to EN/ES/PT dictionaries:
- `"presented_by"`: "Presented by" / "Presentado por" / "Apresentado por"

### All Links Open in New Tab

Every `<a href>` in the presentation has `target="_blank" rel="noopener"`:
- kelsus.com (watermark)
- github.com/EveryInc (Every Inc attribution)
- github.com/EveryInc/compound-engineering-plugin (slide 1 + slide 16)

## Acceptance Criteria

- [x] Title slide shows Kelsus SVG logo with blinking cursor animation
- [x] "Kelsus Camp 2026" prominently displayed with "Camp 2026" in accent blue
- [x] SVG and text visually aligned and same size (both title and watermark)
- [x] "Joaquin Pacce" name visible on title slide
- [x] "By Every Inc" links to https://github.com/EveryInc
- [x] All links open in new tab
- [x] All new text translated in EN/ES/PT
- [x] Works in both dark and light themes
- [x] Watermark hidden on slide 1, visible on all other slides
- [x] Watermark not visible on first page load (CSS `display: none` + async JS)
- [x] Persistent watermark on slides 2-16 (fixed top-left, outside .reveal)
- [x] Logo and branding look clean and professional

## References

- Existing presentation: `presentation/index.html`
- Kelsus website: https://kelsus.com
- Kelsus SVG source: kelsus.com navbar (extracted from page HTML)
- Kelsus social image (brand reference): https://images.ctfassets.net/v1ttc4uq4rym/5e0dyaHpdZqyJMM5zVnWQH/6d68ad595053945129126b24fe96052f/Linkedin_Facebook_1.91_1.jpg
