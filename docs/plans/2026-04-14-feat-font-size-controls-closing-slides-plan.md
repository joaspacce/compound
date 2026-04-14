---
title: "feat: Add Font Size Controls and Questions/Thank You Slides"
type: feat
status: completed
date: 2026-04-14
---

# feat: Add Font Size Controls and Questions/Thank You Slides

## Overview

Two additions to the presentation:

1. **Font size controls** — A+/A-/Reset buttons so the presenter can increase/decrease font size on the fly, regardless of projector/screen size
2. **Two new closing slides** — "Questions?" and "Thank You"

## Font Size Controls

### Placement

Add to the existing `#pres-controls` div (top-right), to the left of the theme/language controls:

```
[A-] [A+] [↺]  [🌙] [EN]
```

### Behavior

- **A+** increases Reveal.js base font size by 10% per click
- **A-** decreases by 10% per click
- **Reset (↺)** returns to the default size (32px as defined in CSS)
- Minimum: 50% of default (16px). Maximum: 200% of default (64px)
- Works by scaling `.reveal { font-size }` — all relative `em` sizes cascade automatically
- Save current size to localStorage (`pres-prefs.fontSize`)

### Implementation

```javascript
var defaultFontSize = 32;

function setFontSize(size) {
  size = Math.max(16, Math.min(64, size));
  document.querySelector('.reveal').style.fontSize = size + 'px';
  var prefs = getPrefs();
  prefs.fontSize = size;
  savePrefs(prefs);
}

function changeFontSize(delta) {
  var current = parseFloat(getComputedStyle(document.querySelector('.reveal')).fontSize);
  setFontSize(current + delta);
}

function resetFontSize() {
  setFontSize(defaultFontSize);
}
```

### Button Styles

Match existing `#theme-toggle` and `#lang-select` styles:
- Same `var(--controls-bg)`, border, border-radius
- 44px min touch targets
- Semi-transparent, accent border on hover

### CSS for buttons

```css
#pres-controls .font-btn {
  background: var(--controls-bg);
  border: 1px solid var(--controls-border);
  border-radius: 8px;
  padding: 6px 10px;
  cursor: pointer;
  font-size: 14px;
  font-weight: 600;
  min-width: 44px;
  min-height: 44px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: var(--controls-text);
  transition: background 0.2s, border-color 0.2s;
}
#pres-controls .font-btn:hover {
  border-color: var(--accent);
}
```

### HTML

```html
<button class="font-btn" onclick="changeFontSize(-3.2)" aria-label="Decrease font size">A-</button>
<button class="font-btn" onclick="changeFontSize(3.2)" aria-label="Increase font size">A+</button>
<button class="font-btn" onclick="resetFontSize()" aria-label="Reset font size">↺</button>
```

Delta of 3.2px = 10% of 32px default.

## New Closing Slides

### Slide 17: Questions?

```html
<section>
  <h1 data-i18n="s17.title">Questions?</h1>
</section>
```

Translations:
- EN: "Questions?"
- ES: "¿Preguntas?"
- PT: "Perguntas?"

### Slide 18: Thank You

```html
<section>
  <h1 data-i18n="s18.title">Thank You!</h1>
  <p class="presenter" style="margin-top: 1em;">Joaquín Pacce</p>
  <p class="small" style="margin-top: 0.5em;">
    <a href="https://kelsus.com" target="_blank" rel="noopener" style="color: var(--accent);">kelsus.com</a>
  </p>
</section>
```

Translations:
- EN: "Thank You!"
- ES: "¡Gracias!"
- PT: "Obrigado!"

## Acceptance Criteria

- [x] A+/A-/Reset buttons visible in top-right controls bar
- [x] Font increases/decreases by 10% per click
- [x] Reset returns to 32px default
- [x] Font size persists in localStorage
- [x] Min 16px, max 64px (clamped)
- [x] Buttons match existing control styles (both themes)
- [x] "Questions?" slide added after "Getting Started"
- [x] "Thank You" slide added as final slide with name + kelsus.com link
- [x] Both new slides translated in EN/ES/PT

## References

- Existing controls: `presentation/index.html` — `#pres-controls` div
- Existing font-size: `.reveal { font-size: 32px; }`
