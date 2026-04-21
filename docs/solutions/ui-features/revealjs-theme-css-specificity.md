---
title: "Reveal.js Theme CSS Silently Outranks Unscoped Classes (the `.term-link` Incident)"
date: 2026-04-21
category: ui-features
tags:
  - revealjs
  - css
  - specificity
  - vendored-theme
  - presentation
  - ui-bug
  - hover-state
  - text-decoration
modules:
  - presentation/index.html
  - presentation/vendor/black.css
severity: P3
status: resolved
---

# Reveal.js Theme CSS Silently Outranks Unscoped Classes (the `.term-link` Incident)

## Problem / Challenge

After adding four inline "term links" to the Kelsus Camp 2026 presentation (dotted-underline anchors that jump to vertical deep-dive slides), the audience-facing render showed them as **plain accent-colored text with no underline**. The anchors still worked — click navigation, cursor, color all applied — but the "this is clickable" affordance (`text-decoration: underline dotted`) was invisible.

Symptoms:

- Hover did nothing visible.
- The `.term-link { text-decoration: underline dotted; }` rule was present in the stylesheet and the class was on every anchor.
- DevTools inspection showed the rule struck through on every `<a class="term-link">`.

## Root Cause

The vendored Reveal.js theme at `presentation/vendor/black.css` defines, around line 303:

```css
.reveal a {
  text-decoration: none;
}
```

Specificity breakdown:

| Selector | Specificity |
|---|---|
| `.reveal a` (vendored theme) | `(0, 1, 1)` — one class + one element |
| `.term-link` (our rule) | `(0, 1, 0)` — one class |

`(0,1,1)` beats `(0,1,0)`. The theme's `text-decoration: none` wins, and strips the dotted underline from every term link. Color (`color: var(--accent)`) still landed because the theme didn't also set `color` on `.reveal a`. That's why the links looked *partially* styled — enough to look like intentional prose styling, not enough to signal "link."

The sneaky part: DevTools shows "strikethrough" on the losing property, but unless you're suspicious, you read the winning value as the final state and don't notice the class rule was silently demoted. The bug is invisible in the source file.

## Solution

Scope the rule under `.reveal` so specificity matches or exceeds the theme's rule:

```css
/* Scope under .reveal to beat Reveal theme's `.reveal a { text-decoration: none; }` */
.reveal a.term-link {
  color: var(--accent);
  text-decoration: underline dotted;
  text-decoration-thickness: 2px;
  text-underline-offset: 4px;
  cursor: pointer;
  font-weight: inherit;
}
.reveal a.term-link:hover,
.reveal a.term-link:focus {
  text-decoration-style: solid;
  outline: none;
}
```

Specificity of `.reveal a.term-link` is `(0, 2, 1)` — two classes + one element — which is strictly higher than the theme's `(0, 1, 1)`. The `:hover` / `:focus` variants tie at `(0, 2, 2)` with any theme `:hover` selector but win by being both more specific and later in the source.

Also bumped `text-decoration-thickness` from the browser default (~1px) to 2px and `text-underline-offset` to 4px so the underline is clearly distinguishable at the body font size (~0.7em). This is polish, not a correctness fix.

### Verification

Rendered screenshots of slide 7, slide 11, and slide 13 all show the four term links (`Git worktrees`, `Rails (DHH)`, `MCP Server`, `MCP limitations`) with a visible underline, hover turns solid. Confirmed in both dark and light themes.

## Gotchas

### 1. `color` Landing Without `text-decoration` Is a False-Positive
If a class partially applies — your color works but your underline / border / hover doesn't — suspect a specificity conflict with a vendored theme, not a typo. The conflict is usually on the property the theme has an opinion about.

### 2. Vendored Theme Rules Are Invisible in Your Source Tree
`presentation/vendor/black.css` is about 700 lines. Nobody reviews it when editing `presentation/index.html`. But every `.reveal <tag>` selector in that file is a silent ceiling on what unscoped class rules can do. If you write `.my-button { background: red; }` and the theme has `.reveal button { background: transparent; }`, your rule loses.

### 3. Specificity Isn't Intuitive for Inline Markup
Most developers mentally rank class selectors as "strong." But `.reveal a` (class + tag) outranks bare `.anything` (class alone), and `.reveal a:hover` (class + tag + pseudo-class) outranks bare `.anything:hover` (class + pseudo-class). The element part of the descendant selector is the whole story.

### 4. `!important` Is the Wrong Fix Here
Tempting to reach for `!important` to force the rule. Don't — it starts a specificity arms race and makes future CSS unreadable. Scope the selector instead. The presentation already uses `!important` sparingly (only on `.reveal .slides > section { overflow-y: auto !important; }` and a few controls overrides); keep that list short.

### 5. Dots Can Merge at 1px Thickness
`text-decoration: underline dotted` with the browser default thickness (~1px) on small body text renders as dots that nearly touch, which can look like a solid underline or like a rendering glitch. Bump `text-decoration-thickness` to 2px and `text-underline-offset` to ≥3px so the dotted pattern is unambiguous.

## Prevention Strategies

**Rule of thumb: scope interactive-element CSS under `.reveal` whenever the vendored theme has a matching element selector.** In practice this means:

- Any `<a>` → use `.reveal a.my-class`, not `.my-class`.
- Any `<button>` → use `.reveal button.my-class`, not `.my-class`.
- Any `<li>` → use `.reveal li.my-class`, not `.my-class`.
- Form controls (`<input>`, `<select>`) — same pattern.

**Pre-ship checklist for new styled elements in the deck:**

1. Grep the vendored theme(s) for your element tag: `grep -n '\.reveal[^{]*<tag>' presentation/vendor/*.css`. If there's any match, scope your rule under `.reveal`.
2. After rendering, inspect the element in DevTools and **scroll the Styles pane for strikethrough rules**. A strikethrough on the property you care about is the tell.
3. Headless screenshot before and after. Diff visually — "I think it looks right in dev" is not verification; the presentation is viewed at fullscreen where subtle stripping is most visible.

**Test at real font size.** The bug was hardest to notice because the anchor color alone looked like a reasonable "highlighted word" at small body text. At 0.7em, a missing underline reads as "emphasis," not as "missing state." Always screenshot at the target render size, not DevTools zoom.

## References

- Fix commit: `8b31101` (PR #3, branch `fix/term-link-specificity`)
- Feature commit that shipped the bug: `9cf2407` (PR #2, `feat/optional-deep-dive-slides`)
- Plan with post-ship follow-up: `docs/plans/2026-04-18-feat-optional-deep-dive-slides-with-navigator-plan.md`
- Related: `docs/solutions/ui-features/revealjs-presentation-with-theming-i18n-branding.md` — covers the deck's theming, i18n, and SVG patterns; this doc is its specificity-focused cousin.
- Related: `docs/solutions/documentation-drift/presentation-advertised-unshipped-slash-command.md` — the sibling "verify before shipping" discipline. Drift-prevention rule there is about correctness of *content*; this doc's rule is about correctness of *presentation*.
- Reveal.js theme source: `presentation/vendor/black.css` (L303 for the offending rule)
- MDN on CSS specificity: https://developer.mozilla.org/en-US/docs/Web/CSS/Specificity
