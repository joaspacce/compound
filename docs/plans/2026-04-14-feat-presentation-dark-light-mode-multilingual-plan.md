---
title: "feat: Add Dark/Light Mode and Multilingual Support to Presentation"
type: feat
status: completed
date: 2026-04-14
---

# feat: Add Dark/Light Mode and Multilingual Support to Presentation

## Overview

Enhance the existing Reveal.js presentation (`presentation/index.html`) with two features:

1. **Dark/Light mode toggle** — default dark, switchable via a sun/moon icon
2. **Multilingual support** — English (default), Spanish, and Portuguese, switchable via a language selector

Both controls live in a fixed top-right corner overlay, always visible. Preferences persist in `localStorage`. Everything stays in a single HTML file with no build step.

## Problem Statement / Motivation

The presentation currently only supports English with a dark theme. To reach a broader audience (especially Portuguese and Spanish speakers) and accommodate different viewing environments (projectors, printed handouts, bright rooms), the presentation needs theme and language flexibility — all without breaking its single-file simplicity.

## Proposed Solution

### Architecture: In-Place Text Swap with CSS Theme Toggle

**Theme switching:** Use CSS custom properties on `:root` for colors. A `data-theme="light"` attribute on `<html>` flips the color scheme. No Reveal.js theme reload needed — just CSS variable overrides.

**Language switching:** Each translatable text element gets a `data-i18n` key. A JavaScript dictionary object holds all translations keyed by language code (`en`, `es`, `pt`). On language change, all `[data-i18n]` elements have their `textContent` (or `innerHTML` for rich text) replaced from the dictionary.

**Persistence:** On toggle, save `{ theme, lang }` to `localStorage`. On page load, read and apply before Reveal.js initializes (to avoid flash of wrong theme/language).

### Controls UI

```
┌─────────────────────────────────────────────────┐
│                                    [🌙] [EN ▾] │  ← fixed top-right
│                                                  │
│              Slide Content                       │
│                                                  │
└─────────────────────────────────────────────────┘
```

- **Theme toggle:** A single button with sun (☀️) / moon (🌙) icon. Clicking toggles `data-theme` on `<html>`.
- **Language selector:** A `<select>` dropdown with options: English, Español, Português.
- Both styled to be semi-transparent, unobtrusive, and visible in both themes.

## Technical Approach

### 1. CSS Theme System

Replace hardcoded colors with CSS custom properties. Define two sets:

```css
/* Dark theme (default) */
:root, :root[data-theme="dark"] {
  --bg-primary: #191919;
  --bg-secondary: #1e1e2e;
  --text-primary: #ffffff;
  --text-secondary: #8b949e;
  --accent: #58a6ff;
  --green: #3fb950;
  --red: #f85149;
  --code-bg: rgba(88,166,255,0.1);
  --border-color: rgba(255,255,255,0.1);
  --controls-bg: rgba(0,0,0,0.6);
}

/* Light theme */
:root[data-theme="light"] {
  --bg-primary: #ffffff;
  --bg-secondary: #f6f8fa;
  --text-primary: #1f2328;
  --text-secondary: #656d76;
  --accent: #0969da;
  --green: #1a7f37;
  --red: #cf222e;
  --code-bg: rgba(9,105,218,0.08);
  --border-color: rgba(0,0,0,0.1);
  --controls-bg: rgba(255,255,255,0.85);
}
```

Update all existing CSS rules to reference `var(--*)` instead of literal colors. Override the Reveal.js theme background via `.reveal` background-color.

### 2. Translation Dictionary

A single JavaScript object with all translatable strings:

```javascript
const translations = {
  en: {
    "title": "Compound Engineering Plugin",
    "subtitle": "Making each unit of work easier than the last",
    "by": "By",
    "slide2.title": "What Is It?",
    "slide2.item1": "A <span class=\"highlight\">plugin for AI coding editors</span> (Claude Code, Cursor, and more)",
    // ... all slide content
  },
  es: {
    "title": "Plugin de Ingeniería Compuesta",
    "subtitle": "Haciendo cada unidad de trabajo más fácil que la anterior",
    "by": "Por",
    "slide2.title": "¿Qué Es?",
    "slide2.item1": "Un <span class=\"highlight\">plugin para editores de código con IA</span> (Claude Code, Cursor y más)",
    // ... all slide content
  },
  pt: {
    "title": "Plugin de Engenharia Composta",
    "subtitle": "Tornando cada unidade de trabalho mais fácil que a anterior",
    "by": "Por",
    "slide2.title": "O Que É?",
    "slide2.item1": "Um <span class=\"highlight\">plugin para editores de código com IA</span> (Claude Code, Cursor e mais)",
    // ... all slide content
  }
};
```

### 3. HTML Markup Pattern

Each translatable element gets a `data-i18n` attribute matching the dictionary key:

```html
<h1 data-i18n="title">Compound Engineering Plugin</h1>
<p class="subtitle" data-i18n="subtitle">Making each unit of work easier than the last</p>
```

The English text stays as the default HTML content (works if JS fails).

### 4. JavaScript Logic

```javascript
// Apply saved preferences immediately (before Reveal init)
(function() {
  const saved = JSON.parse(localStorage.getItem('pres-prefs') || '{}');
  const theme = saved.theme || 'dark';
  const lang = saved.lang || 'en';
  document.documentElement.setAttribute('data-theme', theme);
  // Language applied after DOM ready
})();

function setTheme(theme) {
  document.documentElement.setAttribute('data-theme', theme);
  const prefs = JSON.parse(localStorage.getItem('pres-prefs') || '{}');
  prefs.theme = theme;
  localStorage.setItem('pres-prefs', JSON.stringify(prefs));
  // Update toggle icon
  document.getElementById('theme-icon').textContent = theme === 'dark' ? '🌙' : '☀️';
}

function setLang(lang) {
  document.querySelectorAll('[data-i18n]').forEach(el => {
    const key = el.getAttribute('data-i18n');
    if (translations[lang] && translations[lang][key]) {
      el.innerHTML = translations[lang][key];
    }
  });
  document.getElementById('lang-select').value = lang;
  document.documentElement.lang = lang;
  const prefs = JSON.parse(localStorage.getItem('pres-prefs') || '{}');
  prefs.lang = lang;
  localStorage.setItem('pres-prefs', JSON.stringify(prefs));
}

// Initialize after DOM ready
document.addEventListener('DOMContentLoaded', () => {
  const saved = JSON.parse(localStorage.getItem('pres-prefs') || '{}');
  setTheme(saved.theme || 'dark');
  setLang(saved.lang || 'en');
});
```

### 5. Controls HTML

```html
<div id="pres-controls" style="position:fixed; top:16px; right:16px; z-index:100;
     display:flex; gap:8px; align-items:center;">
  <button id="theme-toggle" onclick="setTheme(
    document.documentElement.getAttribute('data-theme') === 'dark' ? 'light' : 'dark'
  )" aria-label="Toggle theme">
    <span id="theme-icon">🌙</span>
  </button>
  <select id="lang-select" onchange="setLang(this.value)" aria-label="Select language">
    <option value="en">EN</option>
    <option value="es">ES</option>
    <option value="pt">PT</option>
  </select>
</div>
```

### 6. Reveal.js Integration

- Override Reveal.js's background color via CSS: `.reveal { background: var(--bg-primary); }`
- No need to switch Reveal.js themes (black.css vs white.css) — CSS variables handle everything
- Keep the `black.css` theme loaded as a base, override what's needed

## Edge Cases & Considerations

### Fullscreen Mode
- Reveal.js fullscreen (F key) applies CSS transforms to `.reveal` that can clip `position: fixed` children. **Controls must be placed OUTSIDE the `.reveal` div** in the DOM to survive fullscreen mode.
- Browser fullscreen (F11) should also work since controls are outside `.reveal`.

### Rich Text in Translations
- Some slides contain `<span>`, `<strong>`, `<code>` markup inside translatable text. Use `innerHTML` (not `textContent`) for the swap. The translation dictionary must include the markup.
- Code examples (command names like `/ce:brainstorm`) stay untranslated — they are literal commands.

### `<html lang>` Attribute
- When language changes, update `document.documentElement.lang` to the selected language code (`en`, `es`, `pt`). This is required for screen readers and browser translation tools to work correctly.

### Graceful Degradation
- If JavaScript fails, the page shows English dark mode (the current default). All text is in the HTML as-is.
- If `localStorage` is unavailable (private browsing in some browsers), wrap in try/catch and silently fall back to defaults. No visible error needed.
- If stored values are invalid (e.g., `theme=blue`), fall back to defaults.

### Mobile / Responsive
- Controls should scale down on narrow viewports. Use smaller font-size and tighter padding at `max-width: 600px`.
- Minimum 44x44px touch targets for theme toggle button.
- Ensure controls don't overlap Reveal.js's own navigation arrows.

### Accessibility
- Theme toggle button has `aria-label="Toggle theme"`
- Language select has `aria-label="Select language"`
- Respect `prefers-color-scheme` media query as initial default when no localStorage preference exists

### Theme Implementation: CSS Variables Only
- Do NOT swap Reveal.js theme CSS files (`black.css` vs `white.css`). Keep `black.css` loaded as the base and override everything via CSS custom properties. This avoids network requests and FOUC entirely.
- The light theme overrides Reveal.js's background, text colors, and component styles through higher-specificity CSS variable rules.

### Flash of Unstyled Content (FOUC)
- Theme is applied in a blocking `<script>` in `<head>` (before body renders) to prevent flash
- Language swap happens on `DOMContentLoaded` — there may be a brief flash of English text for non-English users. This is acceptable since English is the default and the swap is near-instant.

### Translatable Elements Scope
All elements that contain user-facing text get `data-i18n` attributes:
- All `<h1>`, `<h2>`, `<h3>` headings
- All `<li>` items
- All `<p>` content paragraphs
- `<blockquote>` text
- `<td>` command descriptions (but NOT command names in `<code>`)
- Workflow box labels (`<span class="workflow-box">`)
- Badge labels are NOT translated (they are proper nouns: "Claude Code", "Cursor", etc.)
- Stats/numbers stay as-is

## Acceptance Criteria

- [x] Dark mode is the default theme on first visit
- [x] Clicking the theme toggle switches between dark and light mode instantly
- [x] Light mode has good contrast and readability (not just inverted colors)
- [x] English is the default language on first visit
- [x] Language selector offers EN, ES, PT options
- [x] Switching language updates ALL slide text in-place without page reload
- [x] Theme and language preferences are saved in `localStorage`
- [x] Returning visitors see their saved preferences
- [x] Controls are visible and usable in both themes
- [x] Controls work in Reveal.js fullscreen mode (F key)
- [x] Presentation remains a single HTML file with no build step
- [x] Graceful degradation: page works (English, dark) if JS is disabled
- [x] `prefers-color-scheme` is respected as initial default when no saved preference
- [x] Controls are accessible (proper aria-labels, keyboard-navigable)
- [x] Mobile-friendly controls at narrow viewports

## Translation Scope

All user-facing text in the 15 slides must be translated. Technical terms and commands remain untranslated:
- Command names: `/ce:brainstorm`, `/workflows:plan`, etc.
- Code/tech terms: TypeScript, Bun, MCP, MIT License, Git worktrees
- URLs and repo paths
- Stats/numbers: 14,000+ stars, 94 releases, etc.

**Translatable content per slide (~60 strings total):**

| Slide | Translatable Elements |
|-------|----------------------|
| 1. Title | title, subtitle, "By" |
| 2. What Is It? | heading, 3 bullet items, stats line |
| 3. Core Philosophy | heading, quote, 3 bullet items |
| 4. Workflow Cycle | heading, box labels (6), loop text, note |
| 5. Key Commands | heading, 6 command descriptions |
| 6. Brainstorm & Plan | heading, 5 bullet items |
| 7. Work & Review | heading, 5 bullet items |
| 8. Compound | heading, 4 bullet items, note |
| 9. In Practice | heading, subtitle, 2 flow labels, 2 notes |
| 10. Multi-Platform | heading, support labels, legend |
| 11. Tech Stack | heading, 5 bullet items, stats |
| 12. Pros | heading, 6 bullet items |
| 13. Cons | heading, 6 bullet items |
| 14. When to Use | heading, 4 bullet items |
| 15. When NOT to Use | heading, 4 bullet items |
| 16. Getting Started | heading, 3 bullet items, "Also available as" |

## Implementation Phases

### Phase 1: CSS Theme System
- Extract all hardcoded colors into CSS custom properties
- Define dark and light variable sets
- Override Reveal.js backgrounds and text colors
- Add controls HTML and basic styling
- Add theme toggle logic with localStorage

### Phase 2: Translation System
- Build the full `translations` object for all 3 languages (~60 keys)
- Add `data-i18n` attributes to all translatable HTML elements
- Implement `setLang()` function
- Wire up language selector with localStorage persistence

### Phase 3: Polish
- Add `prefers-color-scheme` detection for initial theme
- Responsive controls for mobile
- Accessibility audit (aria-labels, focus management)
- Test all 6 combinations (2 themes x 3 languages)
- Verify Reveal.js fullscreen compatibility

## Success Metrics

- All 6 theme/language combinations render correctly
- No flash of wrong theme on page load
- Controls don't overlap with slide content
- Translations are natural and accurate (not machine-translated gibberish)
- File size increase is reasonable (the translation dictionary adds ~10-15KB)

## Dependencies & Risks

- **No new dependencies** — pure CSS variables + vanilla JS
- **Risk: Translation quality** — Spanish and Portuguese translations should be reviewed by a native speaker
- **Risk: Reveal.js CSS specificity** — the theme CSS may need `!important` overrides in some cases
- **Risk: innerHTML XSS** — translation strings are hardcoded in the source, not user-input, so this is safe

## References

- Existing presentation: `presentation/index.html`
- Previous plan: `docs/plans/2026-04-11-feat-compound-engineering-plugin-presentation-plan.md`
- Reveal.js theming docs: https://revealjs.com/themes/
- CSS custom properties: https://developer.mozilla.org/en-US/docs/Web/CSS/--*
- `prefers-color-scheme`: https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-color-scheme
