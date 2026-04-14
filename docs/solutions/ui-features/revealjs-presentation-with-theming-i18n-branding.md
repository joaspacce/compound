---
title: "Kelsus Camp 2026 — Compound Engineering Plugin Presentation"
date: 2026-04-14
category: ui-features
tags:
  - revealjs
  - i18n
  - theming
  - svg
  - presentation
  - kelsus
  - dark-mode
  - font-controls
  - css-variables
  - localization
  - vendor-bundling
modules:
  - presentation/index.html
  - presentation/vendor/
severity: P2
status: resolved
---

# Kelsus Camp 2026 — Reveal.js Presentation with Theming, i18n, and Branding

## Problem / Challenge

Building a polished, single-file Reveal.js presentation with multiple cross-cutting concerns that must coexist without a build step, external frameworks, or runtime dependencies:

- **Theme flicker (FOUC)** — Dark/light mode requires the correct theme before any content paints
- **Multilingual content in static HTML** — 18 slides with ~80 translatable strings across 3 languages (EN/ES/PT), preserving rich HTML markup
- **Fullscreen survival** — Reveal.js fullscreen clips `position: fixed` children inside `.reveal`
- **SVG logo reuse** — Kelsus wordmark appears in multiple places, must adapt to both themes
- **Font size accessibility** — Runtime font scaling that cascades through all relative-sized elements
- **Offline reliability** — Conference WiFi is unreliable; CDN dependency is a risk

## Solution

### Architecture: Single HTML File, Zero Build Step

All CSS, HTML, JS, SVG, and translations live in one `index.html` (~53KB after optimization). Reveal.js is vendored locally in `presentation/vendor/` (~163KB).

### Key Techniques

**1. CSS Custom Properties as Theme Engine**

Two variable sets on `:root` keyed by `data-theme` attribute. A single attribute flip changes all colors instantly — no Reveal.js theme file swap needed:

```css
:root, :root[data-theme="dark"] {
  --bg-primary: #191919;
  --accent: #58a6ff;
}
:root[data-theme="light"] {
  --bg-primary: #ffffff;
  --accent: #0969da;
}
.reveal { background: var(--bg-primary) !important; }
```

**2. Blocking FOUC Prevention in `<head>`**

Synchronous localStorage read + `data-theme` set before body renders. Falls back to `prefers-color-scheme`, then dark:

```javascript
// In <head>, blocking
(function() {
  var saved = JSON.parse(localStorage.getItem('pres-prefs') || '{}');
  var theme = ['dark','light'].indexOf(saved.theme) !== -1 ? saved.theme : null;
  if (!theme) theme = matchMedia('(prefers-color-scheme: light)').matches ? 'light' : 'dark';
  document.documentElement.setAttribute('data-theme', theme);
})();
```

**3. `data-i18n` + innerHTML Translation Swap**

Elements carry translation keys. `setLang()` queries all `[data-i18n]` elements and replaces content from a JS dictionary. Uses `innerHTML` to preserve embedded `<strong>`, `<code>`, `<span>` markup. English hardcoded in HTML as fallback if JS fails.

**4. SVG `<symbol>` / `<use>` Deduplication**

Logo defined once as hidden `<symbol>`, referenced everywhere with `<use>`. `fill="currentColor"` makes SVG inherit theme text color automatically:

```html
<svg style="display:none">
  <symbol id="kelsus-logo" viewBox="0 0 64 16">
    <path d="..." fill="currentColor"/>
    <path d="..." fill="currentColor" class="kelsus-cursor"/>
  </symbol>
</svg>
<!-- Reference: -->
<svg viewBox="0 0 64 16"><use href="#kelsus-logo"/></svg>
```

**5. Controls Outside `.reveal` for Fullscreen**

Watermark and controls are siblings of `.reveal`, not children. This prevents Reveal.js CSS transforms from clipping them:

```html
<a id="kelsus-watermark">...</a>
<div id="pres-controls">...</div>
<div class="reveal">...</div>  <!-- slides inside -->
```

**6. Async Reveal.js Initialization**

`Reveal.initialize()` is async. API calls like `getCurrentSlide()` return null if called before `.then()`:

```javascript
Reveal.initialize({...}).then(function() {
  Reveal.on('slidechanged', updateWatermark);
  updateWatermark(); // safe — Reveal is fully initialized
});
```

**7. Font Size Controls with Clamping**

Delta of 3.6px (~10% of 36px default). All slide content uses `em` units so changes cascade. Clamped 16-64px. Persisted in shared `pres-prefs` localStorage key.

## Gotchas

### 1. Reveal.js Fullscreen Clips Fixed Elements
Never place persistent UI controls inside `.reveal`. Mount them as siblings of the `.reveal` div with `position: fixed; z-index: 100`.

### 2. `center: true` Conflicts with Forced Height
Setting `height: 700px !important` on sections breaks Reveal's centering algorithm. Use `max-height` instead to cap overflow while preserving centering.

### 3. `Reveal.initialize()` Is Async
Always chain Reveal API calls after `.then()`. Never call `getCurrentSlide()` at the top level of a DOMContentLoaded handler — it returns null.

### 4. SVG Text vs. Font Size Mismatch
SVG letterforms at the same pixel size as CSS text appear visually taller. Expect ~20% compensation: set SVG smaller or text larger. Always test visually.

### 5. CDN Dependency for Presentations
For any live delivery, vendor all dependencies locally. Total cost here is ~163KB — negligible compared to a blank screen on stage.

### 6. SVG Duplication Waste
For any SVG used more than once, define as `<symbol>` + `<use>`. Duplicating 18KB of path data wastes 25% of file size.

### 7. `currentColor` for Theme-Adaptive SVGs
Always use `fill="currentColor"` in monochrome SVGs. Define color in the parent via CSS variables. Never hardcode hex colors in SVG fills when theming is required.

### 8. FOUC Prevention Order
Apply visual theme in a blocking `<head>` script. Defer non-visual preferences (language, font size) to DOMContentLoaded. The head script must be defensive (try/catch, fallback defaults).

## Prevention Strategies

- **Defensive localStorage** — Every read wrapped in try/catch. Falls back to sensible defaults (dark, EN, 36px). Works in incognito mode.
- **Single preference key** — All user preferences in one JSON object under `pres-prefs`. Avoids key collision, easy to clear.
- **Data-attribute driven behavior** — `data-hide-watermark` on slides, `data-i18n` on elements. Decoupled from DOM structure and slide indices.
- **44px minimum touch targets** — All buttons/selects meet mobile accessibility guidelines.
- **`rel="noopener noreferrer"`** — All external `target="_blank"` links include security attributes.
- **Local vendor bundling** — Zero network requests needed to render. Fully offline-capable.

## Code Review Summary

Three review agents (Security Sentinel, Code Simplicity Reviewer, Performance Oracle) found:
- 0 P1 (Critical)
- 3 P2 (Important) — all fixed: SVG dedup, CDN pinning/SRI, local vendor bundling
- 5 P3 (Nice-to-have) — 4 fixed: CSS dedup, dead media queries, noreferrer, font-family consolidation. 1 accepted: no CSP (requires unsafe-inline for file:// context)

Full review: `docs/reviews/2026-04-14-code-review.md`

## References

- Presentation: `presentation/index.html`
- Vendor assets: `presentation/vendor/`
- Plans: `docs/plans/2026-04-14-feat-*.md` (5 plan documents)
- Review: `docs/reviews/2026-04-14-code-review.md`
- Reveal.js docs: https://revealjs.com/
- Kelsus: https://kelsus.com
- Plugin repo: https://github.com/EveryInc/compound-engineering-plugin
