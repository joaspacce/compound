---
title: Code Review — Compound Presentation
date: 2026-04-14
reviewers:
  - security-sentinel
  - code-simplicity-reviewer
  - performance-oracle
target: presentation/index.html
status: resolved
---

# Code Review — 2026-04-14

**Target:** `presentation/index.html` (all commits on main)
**Reviewers:** Security Sentinel, Code Simplicity Reviewer, Performance Oracle

## Summary

No critical blockers. The presentation is well-structured with proper input validation, graceful degradation, and clean CSS variable theming. 3 important findings were fixed, 5 nice-to-have improvements identified.

## Findings

### P2 - Important (Fixed)

#### 1. Duplicated SVG (~18KB waste)

**Flagged by:** All 3 reviewers
**Severity:** P2
**Status:** Fixed

The Kelsus logo SVG was copy-pasted verbatim in two places (watermark + title slide). ~36KB of the 71KB file was duplicated path data.

**Fix:** Defined SVG once as `<symbol id="kelsus-logo">` in a hidden `<svg>` block. Referenced with `<use href="#kelsus-logo"/>` in both locations. ~25% file size reduction.

#### 2. CDN without SRI or version pinning

**Flagged by:** Security Sentinel, Performance Oracle
**Severity:** P2
**Status:** Fixed

Reveal.js loaded from `cdn.jsdelivr.net` with `@5` (semver range, not pinned) and no `integrity` attributes. A compromised CDN would get full JS execution in the page.

**Fix:** Pinned to exact version `reveal.js@5.1.0` and added `integrity` + `crossorigin="anonymous"` attributes on all 3 CDN resources.

#### 3. CDN dependency with no offline fallback

**Flagged by:** Performance Oracle
**Severity:** P2
**Status:** Fixed

Presentation fails completely if jsdelivr is down or blocked (corporate firewall, airplane mode). For a conference presentation this is a real risk.

**Fix:** Bundled Reveal.js locally in `presentation/vendor/` directory. CSS and JS files served from local filesystem with CDN as fallback not needed.

### P3 - Nice-to-Have (Fixed)

#### 4. Duplicated CSS for control buttons

**Flagged by:** Code Simplicity Reviewer
**Severity:** P3
**Status:** Fixed

`#theme-toggle`, `.font-btn`, `#lang-select` shared ~10 identical CSS properties. Extracted a shared `.pres-control` class. ~30 lines saved.

#### 5. Redundant mobile media query overrides

**Flagged by:** Code Simplicity Reviewer
**Severity:** P3
**Status:** Fixed

`min-width: 44px; min-height: 44px` redeclared in `@media` query identical to base styles. Dead code removed (4 lines).

#### 6. External links missing `noreferrer`

**Flagged by:** Security Sentinel
**Severity:** P3
**Status:** Fixed

Links used `rel="noopener"` but not `noreferrer`. Added `noreferrer` to all external links.

#### 7. `font-family` declared 3 times identically

**Flagged by:** Code Simplicity Reviewer
**Severity:** P3
**Status:** Fixed

Same system font stack repeated on `.kelsus-logo`, `#pres-controls`, and `.font-btn`. Declared once on `body`, removed from individual selectors.

### P3 - Nice-to-Have (Not Fixed — Acceptable)

#### 8. No Content Security Policy

**Flagged by:** Security Sentinel
**Severity:** P3
**Status:** Won't fix

Inline `onclick` handlers and inline `<script>` blocks require `'unsafe-inline'` in CSP, reducing its value. For a local `file://` presentation with no server, CSP provides minimal benefit. Would require refactoring all event handlers to `addEventListener`.

## Positive Findings

- No hardcoded secrets, eval(), or dynamic code execution
- localStorage values properly validated at every consumption point
- Theme applied in blocking `<head>` script to prevent FOUC
- Graceful degradation: works in English dark mode if JS fails
- All external links use `target="_blank" rel="noopener noreferrer"`
- CSS variables for theming is clean and maintainable
- Translation fallback pattern (HTML content + JS dictionary) is appropriate

## File Size Impact

| Metric | Before | After |
|--------|--------|-------|
| Total file size | ~71KB | ~53KB |
| SVG duplication | ~36KB (2x) | ~18KB (1x via symbol) |
| CSS lines | ~310 | ~280 |
| CDN dependency | 3 external files | 0 (local vendor) |
