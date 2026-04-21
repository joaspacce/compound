---
title: "feat: Optional Deep-Dive Slides (MCP, Worktree, Rails/DHH) with Navigator + Red Warning Heading"
type: feat
status: completed
date: 2026-04-18
---

# feat: Optional Deep-Dive Slides with Navigator + Red Warning Heading

## Overview

Add **optional deep-dive slides** that branch off the main flow as Reveal.js vertical stacks, reachable either by (a) clicking an inline "term link" in a parent slide or (b) pressing the down arrow. Each deep-dive carries a small **in-slide navigator** so the audience can return to the main flow, advance to the next main slide, or browse sibling deep-dives without losing their place.

Four deep-dives in this batch:

1. **What is an MCP?** — nested under slide 11 (Tech Stack)
2. **MCP limitations (stdio vs remote)** — nested under slide 11 (Tech Stack)
3. **Git worktree** — nested under slide 7 (Work & Review)
4. **Rails / DHH** — nested under slide 7 (Work & Review)

Also: color the **"When NOT to Use It"** heading (slide 15) **red**, consistent with the existing `.cons` styling on slide 13.

Also: make the **Kelsus logo + "Camp 2026" cluster on the title slide clickable**, linking to `https://kelsus.com` in a new tab — matching the persistent watermark's existing behavior on every other slide (the watermark is hidden on slide 1 via `data-hide-watermark`, so the title slide currently has no way to reach kelsus.com).

## Problem Statement / Motivation

The deck keeps the main flow tight (one big idea per slide) but references proper nouns — **MCP**, **Git worktree**, **Rails/DHH**, **stdio transport** — that not every audience member has context for. Today those terms render as prose and the presenter has to choose between (a) skipping the context and losing the non-expert, or (b) spending time mid-main-flow on background that the expert already knows.

Vertical-nested deep-dives give the presenter a third option: *tap once* on an unfamiliar term, cover the concept, tap **Back**, and continue. Audience never diverted if the term is familiar; deep explanation always one keystroke away if it isn't.

The red heading on slide 15 is a small but long-standing tonal fix: "When to Use It" and "When NOT to Use It" currently render identically, which mutes the contrast the slides are meant to draw.

## Proposed Solution

### 1. Information Architecture — parent / child mapping

```
Slide 7 (Work & Review)
  ├─ v1: Git worktree
  └─ v2: Rails / DHH

Slide 11 (Tech Stack)
  ├─ v1: What is an MCP?
  └─ v2: MCP limitations (stdio vs remote)

Slide 13 (Cons)        → item5 "MCP limitations" links to slide 11 / v2 (cross-stack link, not a nested child)
Slide 15 (When NOT)    → h2 gets `class="cons"` for red color; no children
```

Main flow stays 18 slides wide. Four new vertical slides live beneath two existing parents. Linear left/right navigation **skips the deep-dives by default** (native Reveal.js behavior) — they're only visited by a click on a term link or an explicit down-arrow.

### 2. Navigation design

**Native Reveal.js behavior (unchanged):**
- `←` / `→` — next/prev **horizontal** (main flow). From within a deep-dive, `→` jumps out to the next main slide — it does NOT walk through sibling deep-dives.
- `↓` / `↑` — next/prev **vertical** (within the current deep-dive stack).
- URL hash format: `#/h/v` (e.g. `#/11/1` = slide 11, vertical 1). Reveal.js `hash: true` is already enabled (L1089), so anchor links work natively.

**Term link (inline, in parent slides):**

```html
<a class="term-link" href="#/11/1">MCP Server</a>
```

Styled as dotted underline in accent color. Click jumps to the first deep-dive in the stack. Reveal.js parses the hash and does the transition.

**In-slide navigator (on every deep-dive):**

A small row at the bottom of each deep-dive with three controls:

```
[ ↑ Back to <parent title> ]   [ ← Previous ]   [ Next → ]
```

- **Back to <parent>** → `onclick="Reveal.slide(H, 0)"` where H is the parent's horizontal index. Always returns to the top of the stack, regardless of which vertical the user is on.
- **Previous** → `onclick="Reveal.left()"`. Goes to the previous main-flow slide (skipping sibling deep-dives).
- **Next** → `onclick="Reveal.right()"`. Goes to the next main-flow slide.

If there are multiple deep-dives in a stack (there are, for both Work & Review and Tech Stack), a subtle hint is shown on the first deep-dive:

```
↓ More in this deep-dive (<N> of <total>)
```

Styled muted; hides with CSS on the last vertical of the stack. No JS required; pure CSS using `:not(:last-child)`.

### 3. Visual vocabulary

**Term links** — dotted underline in accent color. Hover turns solid.

```css
.term-link {
  color: var(--accent);
  text-decoration: underline dotted;
  text-underline-offset: 3px;
  cursor: pointer;
  font-weight: inherit;
}
.term-link:hover, .term-link:focus {
  text-decoration-style: solid;
  outline: none;
}
```

**Deep-dive navigator** — muted sans-serif chips at the bottom of each deep-dive. Uses existing `--muted` and `--accent` variables to stay theme-aware.

```css
.deepdive-nav {
  display: flex; justify-content: center; gap: 12px; flex-wrap: wrap;
  margin-top: 30px; padding-top: 16px;
  border-top: 1px dashed var(--muted);
  font-size: 0.5em;
}
.deepdive-nav button, .deepdive-nav a {
  background: transparent;
  border: 1px solid var(--muted);
  color: var(--text-primary);
  padding: 6px 14px;
  border-radius: 999px;
  cursor: pointer;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Helvetica, Arial, sans-serif;
  text-decoration: none;
  transition: border-color 0.2s, color 0.2s;
}
.deepdive-nav button:hover, .deepdive-nav a:hover {
  border-color: var(--accent);
  color: var(--accent);
}
.deepdive-nav .nav-back { border-color: var(--accent); color: var(--accent); }

.deepdive-more-hint {
  display: block;
  text-align: center;
  font-size: 0.5em;
  color: var(--muted);
  margin-top: 10px;
}
/* hide the hint on the last vertical of its stack */
.reveal .slides > section > section:last-child .deepdive-more-hint { display: none; }
```

**Nested-section overflow fix** (carry-over from the existing direct-child constraint at L73):

```css
.reveal .slides > section > section {
  overflow-y: auto !important;
  max-height: 700px !important;
  scrollbar-width: thin;
  scrollbar-color: var(--muted) transparent;
}
.reveal .slides > section > section::-webkit-scrollbar { width: 6px; }
.reveal .slides > section > section::-webkit-scrollbar-thumb { background: var(--muted); border-radius: 3px; }
```

**Red "When NOT to Use It" heading** — reuse existing `.cons` class; no new CSS needed.

```html
<!-- Before -->
<h2 data-i18n="s15.title">When NOT to Use It</h2>
<!-- After -->
<h2 class="cons" data-i18n="s15.title">When NOT to Use It</h2>
```

### 4. Slide-level HTML sketches

**Slide 7 — Work & Review (add two vertical children):**

```html
<section>
  <section>
    <h2 data-i18n="s7.title">Work &amp; Review</h2>
    <ul>
      <li data-i18n="s7.item1">
        <strong>Work</strong> uses
        <a class="term-link" href="#/6/1">Git worktrees</a>
        for isolated, parallel execution
      </li>
      <!-- items 2–4 unchanged -->
      <li data-i18n="s7.item5">
        Security &middot; Performance &middot; Architecture &middot;
        <a class="term-link" href="#/6/2">Rails (DHH)</a>
        &middot; Simplicity
      </li>
    </ul>
  </section>

  <!-- v1: Git worktree -->
  <section data-deepdive-parent="7">
    <h2 data-i18n="dd.worktree.title">Deep-dive: Git worktrees</h2>
    <ul>
      <li data-i18n="dd.worktree.item1"><code>git worktree add &lt;path&gt; &lt;branch&gt;</code> creates a parallel working directory</li>
      <li data-i18n="dd.worktree.item2">Same <code>.git</code>, multiple checkouts &mdash; no duplicate storage</li>
      <li data-i18n="dd.worktree.item3"><strong>/workflows:work</strong> runs the agent in its own worktree so your main checkout stays untouched</li>
      <li data-i18n="dd.worktree.item4">Enables parallel agent swarms: each teammate gets its own branch + working dir</li>
    </ul>
    <!-- navigator component, see CSS above -->
  </section>

  <!-- v2: Rails / DHH -->
  <section data-deepdive-parent="7">
    <h2 data-i18n="dd.dhh.title">Deep-dive: Rails &amp; DHH</h2>
    <ul>
      <li data-i18n="dd.dhh.item1"><strong>Rails</strong> &mdash; Ruby on Rails, a server-rendered web framework (2004&rarr;)</li>
      <li data-i18n="dd.dhh.item2"><strong>DHH</strong> &mdash; David Heinemeier Hansson, Rails creator &amp; 37signals cofounder</li>
      <li data-i18n="dd.dhh.item3">Opinionated: <em>convention over configuration</em>, REST purity, Hotwire, "majestic monolith"</li>
      <li data-i18n="dd.dhh.item4"><strong>dhh-rails-reviewer</strong> agent reviews Ruby/Rails code in his style &mdash; flags JS-framework contamination, anti-patterns, convention drift</li>
    </ul>
    <!-- navigator -->
  </section>
</section>
```

**Note on horizontal indices**: Reveal.js horizontal indices are **0-based**. Slide 7 is H-index 6. Slide 11 is H-index 10. Anchor hrefs must use the 0-based value.

**Slide 11 — Tech Stack (add two vertical children):**

```html
<section>
  <section>
    <h2 data-i18n="s11.title">Tech Stack</h2>
    <ul>
      <!-- items 1–3 unchanged -->
      <li data-i18n="s11.item4">
        <strong><a class="term-link" href="#/10/1">MCP Server</a></strong>
        integration (Model Context Protocol)
      </li>
      <!-- item 5 unchanged -->
    </ul>
  </section>

  <!-- v1: What is MCP? -->
  <section data-deepdive-parent="11">
    <h2 data-i18n="dd.mcp.title">Deep-dive: What is an MCP?</h2>
    <ul>
      <li data-i18n="dd.mcp.item1"><strong>Model Context Protocol</strong> &mdash; open protocol by Anthropic (Nov 2024)</li>
      <li data-i18n="dd.mcp.item2">Lets AI assistants talk to external tools, data, APIs in a standard way</li>
      <li data-i18n="dd.mcp.item3">Client/server: the AI is the client; an MCP server exposes <em>tools</em>, <em>resources</em>, and <em>prompts</em></li>
      <li data-i18n="dd.mcp.item4">Often described as <em>"USB-C for AI integrations"</em></li>
      <li data-i18n="dd.mcp.item5">compound-engineering ships MCP tooling that Claude Code and other clients can consume</li>
    </ul>
    <!-- navigator -->
  </section>

  <!-- v2: MCP limitations (stdio vs remote) -->
  <section data-deepdive-parent="11">
    <h2 data-i18n="dd.mcp_limits.title">Deep-dive: MCP transports &amp; limitations</h2>
    <ul>
      <li data-i18n="dd.mcp_limits.item1">Two supported transports: <strong>stdio</strong> (local subprocess) and <strong>Streamable HTTP</strong> (remote)</li>
      <li data-i18n="dd.mcp_limits.item2">Some hosts <strong>only support stdio</strong> &mdash; HTTP servers are unreachable from those clients</li>
      <li data-i18n="dd.mcp_limits.item3">stdio is simple and secure (process-level isolation) but tied to the developer's machine</li>
      <li data-i18n="dd.mcp_limits.item4">Remote MCP enables shared servers, auth, hosted agents &mdash; but requires host support</li>
      <li data-i18n="dd.mcp_limits.item5">Plugin strategy: ship <strong>stdio-first</strong> for max compatibility; offer HTTP variants where hosts support them</li>
    </ul>
    <!-- navigator -->
  </section>
</section>
```

**Slide 13 — Cons (item 5 becomes a cross-stack link):**

```html
<li data-i18n="s13.item5">
  <strong class="cons"><a class="term-link" href="#/10/2">MCP limitations</a></strong>
  &mdash; some platforms only support stdio servers
</li>
```

**Slide 15 — When NOT to Use It (one-class addition):**

```html
<h2 class="cons" data-i18n="s15.title">When NOT to Use It</h2>
```

**Slide 1 — Title (wrap Kelsus logo + "Camp 2026" in an anchor):**

```html
<!-- Before -->
<h1 style="font-size: 2em; display: flex; align-items: center; justify-content: center; gap: 14px;">
  <svg viewBox="0 0 64 16" style="height: 0.75em;"><use href="#kelsus-logo"/></svg>
  <span style="color: var(--accent); font-size: 1.2em;">Camp 2026</span>
</h1>

<!-- After -->
<h1 style="font-size: 2em; display: flex; align-items: center; justify-content: center; gap: 14px;">
  <a href="https://kelsus.com" target="_blank" rel="noopener noreferrer"
     style="display: inline-flex; align-items: center; gap: 14px; color: inherit; text-decoration: none;"
     aria-label="Kelsus Camp 2026 &mdash; visit kelsus.com">
    <svg viewBox="0 0 64 16" style="height: 0.75em;"><use href="#kelsus-logo"/></svg>
    <span style="color: var(--accent); font-size: 1.2em;">Camp 2026</span>
  </a>
</h1>
```

`color: inherit` preserves the Kelsus logo's `fill="currentColor"` theming. `text-decoration: none` suppresses the default link underline on a title. `rel="noopener noreferrer"` matches every other external link in the deck (security guideline already documented in `docs/solutions/ui-features/revealjs-presentation-with-theming-i18n-branding.md`).

### 5. Navigator component (pseudo-template)

Each deep-dive `<section>` ends with:

```html
<div class="deepdive-nav">
  <a class="nav-back" href="#/6/0">
    <span data-i18n="dd.nav.back">&uarr; Back to Work &amp; Review</span>
  </a>
  <button type="button" onclick="Reveal.left()" data-i18n="dd.nav.prev">&larr; Previous</button>
  <button type="button" onclick="Reveal.right()" data-i18n="dd.nav.next">Next &rarr;</button>
</div>
```

**Back button is a parent-specific anchor, not a generic script** — two reasons:

1. Using `href="#/<parent-h>/0"` means the URL reflects the destination; deep-linking and the browser back button behave correctly.
2. No JS needed to figure out "which parent am I under?" — the author encodes it explicitly, matching the existing `onclick="Reveal.slide(N)"` pattern on workflow boxes.

The **Previous** / **Next** buttons use `Reveal.left()` / `Reveal.right()` so they always traverse the main flow regardless of whether you're on v1, v2, or v3 of a stack.

### 6. i18n keys to add (EN/ES/PT)

```
dd.worktree.title, dd.worktree.item1..item4
dd.dhh.title,       dd.dhh.item1..item4
dd.mcp.title,       dd.mcp.item1..item5
dd.mcp_limits.title, dd.mcp_limits.item1..item5

dd.nav.prev   ("← Previous" / "← Anterior" / "← Anterior")
dd.nav.next   ("Next →" / "Siguiente →" / "Pr\u00f3ximo →")
dd.nav.back_s7   ("↑ Back to Work & Review" / "↑ Volver a Work & Review" / "↑ Voltar para Work & Review")
dd.nav.back_s11  ("↑ Back to Tech Stack" / "↑ Volver a Tech Stack" / "↑ Voltar para Tech Stack")
```

All keys must land in **all three dictionaries at commit time**. EN fallback is hardcoded in the HTML.

## Acceptance Criteria

### Functional

- [x] Slide 1 title cluster (Kelsus SVG + "Camp 2026") is a single anchor to `https://kelsus.com`, opens in a new tab, matches the watermark's behavior on every other slide.
- [x] Slide 1 anchor carries `rel="noopener noreferrer"` and an `aria-label` describing the destination.
- [x] Slide 1 title cluster keeps its current visual (no link underline, logo + text identical color/size to current render).
- [x] Slide 15 `<h2>` has `class="cons"` and renders in red under both dark and light themes.
- [x] Slide 7 has 3 sections (main + 2 vertical children). Slide 11 has 3 sections (main + 2 vertical children).
- [x] Clicking the **MCP Server** term link on slide 11 lands on the "What is an MCP?" deep-dive.
- [x] Clicking the **Git worktrees** term link on slide 7 lands on the "Git worktrees" deep-dive.
- [x] Clicking the **Rails (DHH)** term link on slide 7 lands on the "Rails & DHH" deep-dive.
- [x] Clicking the **MCP limitations** term link on slide 13 lands on the "MCP limitations" deep-dive (cross-stack link).
- [x] On every deep-dive, the navigator Back button returns to the parent slide (top of stack).
- [x] On every deep-dive, navigator Prev/Next take the user to the previous/next main slide, **not** to a sibling deep-dive.
- [x] Native keyboard navigation: `→` on a deep-dive jumps to the next main slide; `↓` goes to the next deep-dive in the stack; `↑` returns to the parent from v1.
- [x] Default linear `→→→` traversal from slide 1 never enters a deep-dive unless the presenter explicitly goes down.
- [x] The "↓ More in this deep-dive" hint appears only on the **first** vertical of a multi-deep-dive stack (hidden on the last via CSS).

### i18n / Quality

- [x] Every new `dd.*` key exists in all three dictionaries (EN/ES/PT). No raw key leaks when switching languages.
- [x] Language toggle EN → ES → PT → EN on each new deep-dive slide shows correct translations.
- [x] Existing slides (1–18) remain visually unchanged except slides 7, 11, 13 (added term links), 15 (red heading).
- [x] No `data-i18n` key collisions with the existing `s*`, `wf.*`, `s5.cmd*`, `s9.*` namespace — new keys use `dd.*` prefix.

### Drift-Prevention

- [x] Each reference to a plugin feature in a deep-dive is verified:
  - `dhh-rails-reviewer` agent exists → check `~/.claude/plugins/cache/every-marketplace/compound-engineering/*/agents/` or review directory.
  - `/workflows:work` using worktrees → confirmed in the shipped `work.md` command.
  - MCP server shipping with the plugin → confirm via plugin README or source.
- [x] No term link points to a non-existent slide index. Grep check: every `href="#/h/v"` maps to an actual section.
- [x] No reintroduction of "Ideate" / "Idear" anywhere.

### Visual / Layout

- [x] Deep-dive slides respect the same 700px max-height / scroll rule as top-level slides (nested selector added).
- [x] Term links are visually distinct (dotted underline) but don't overwhelm prose.
- [x] Navigator row sits below the slide body, separated by a dashed muted divider; doesn't overflow at default font size.
- [x] Slide-number badge shows a sensible value for deep-dives (Reveal.js default `N.v` format is fine).

## Verification

```bash
# 1. Catch broken term links: every `href="#/h/v"` must match an existing section count
grep -oE 'href="#/[0-9]+/[0-9]+"' presentation/index.html | sort -u

# 2. All four deep-dives have navigators
grep -c 'class="deepdive-nav"' presentation/index.html   # expect 4

# 3. i18n parity check: every `dd.*` key appears in all three dicts
grep -oE '"dd\.[a-z_]+(\.[a-z0-9_]+)*"' presentation/index.html | sort | uniq -c
# each count should be 4 → once in HTML fallback + once per dict (EN/ES/PT)

# 4. Canonical-command drift guard (unchanged discipline)
ls ~/.claude/plugins/cache/every-marketplace/compound-engineering/*/commands/workflows/

# 5. Red heading check — class applied on slide 15
grep -n 'class="cons" data-i18n="s15.title"' presentation/index.html
```

**Manual visual smoke test (Chrome headless or browser):**
- `python3 -m http.server 8421 -d presentation/` then:
  - `#/6/0`, `#/6/1`, `#/6/2` — main + both deep-dives for Work & Review
  - `#/10/0`, `#/10/1`, `#/10/2` — main + both deep-dives for Tech Stack
  - `#/12/0` — Cons slide item 5 must link to `#/10/2`
  - `#/14/0` — When NOT heading is red
- Toggle dark/light and EN/ES/PT on each.

## Out of Scope

- Adding a "deep-dives index" TOC slide. Only 4 deep-dives right now; a TOC is overkill.
- Animating the navigator or adding breadcrumb chips ("You are on: Tech Stack → MCP (1 of 2)"). The title prefix `Deep-dive:` on each child slide already signals context.
- Deep-dives for `/lfg`, `/deepen-plan`, or other meta-commands. Scope creep — do a follow-up plan if wanted.
- Adding additional cons to slide 13 or adjusting its copy. This plan only links item 5 to the MCP limitations deep-dive.
- Refactoring existing clickable workflow boxes to use anchor hrefs instead of `onclick="Reveal.slide(N)"`. Mixed pattern is acceptable for now; revisit later if inconsistency bites.

## Drift-Prevention Rules Carried Forward

From `docs/solutions/documentation-drift/presentation-advertised-unshipped-slash-command.md`:

1. **Verify every named feature** before it appears on a slide — this applies to the `dhh-rails-reviewer` agent name, the `/workflows:work` worktree claim, the MCP server shipping claim, and any specific command names cited on deep-dives.
2. **Grep all surfaces** — after adding the deep-dives, ensure term links in slides 7, 11, 13 actually resolve to real sections.
3. **No renumbering of existing i18n keys** — new keys use the `dd.*` prefix exclusively.
4. **Aspirational content needs explicit visual vocabulary** — if a deep-dive describes a not-yet-shipped feature, that feature's wording must say "coming soon" or the deep-dive doesn't ship in this PR.

## Stakeholder Notes

- **Presenter (Joaquin)** — gains an opt-in way to explain foundational concepts without bloating the main deck.
- **Audience** — can follow the main flow linearly; experts skip the deep-dives; newcomers tap the term links.
- **Future contributor** — has a clear pattern (term-link + vertical-nested deep-dive + navigator) to extend with new concepts.

## Risks

- **Keyboard confusion** — audience members used to pressing `→` may get stuck if they arrive on a deep-dive and expect `→` to advance within the stack. Navigator labels + the `↓ more` hint mitigate this; worth a 30-second presenter tip at the start.
- **Reveal.js slide-number badge shows `11.1`** format on deep-dives. Low-risk; consistent with Reveal convention.
- **Overflow on long deep-dives** — five bullets each should fit comfortably under the 700px max. If any bullet needs expanding, the nested-overflow CSS rule already allows scroll.
- **Cross-stack link from slide 13 to slide 11/v2** — users who click "MCP limitations" on Cons land on a deep-dive of a *different* parent. Back button on that deep-dive returns to slide 11, not slide 13. This is acceptable (the deep-dive *belongs* to Tech Stack semantically) but worth noting in the navigator copy on that specific deep-dive.

## MVP pseudocode

### `presentation/index.html` — nested-section CSS

```css
/* After the existing L73 rule */
.reveal .slides > section > section {
  overflow-y: auto !important;
  max-height: 700px !important;
  scrollbar-width: thin;
  scrollbar-color: var(--muted) transparent;
}
```

### `presentation/index.html` — term-link + navigator CSS

```css
.term-link { color: var(--accent); text-decoration: underline dotted; text-underline-offset: 3px; cursor: pointer; }
.term-link:hover { text-decoration-style: solid; }

.deepdive-nav { display: flex; justify-content: center; gap: 12px; flex-wrap: wrap; margin-top: 30px; padding-top: 16px; border-top: 1px dashed var(--muted); font-size: 0.5em; }
.deepdive-nav button, .deepdive-nav a { background: transparent; border: 1px solid var(--muted); color: var(--text-primary); padding: 6px 14px; border-radius: 999px; cursor: pointer; text-decoration: none; transition: border-color 0.2s, color 0.2s; font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Helvetica, Arial, sans-serif; }
.deepdive-nav button:hover, .deepdive-nav a:hover { border-color: var(--accent); color: var(--accent); }
.deepdive-nav .nav-back { border-color: var(--accent); color: var(--accent); }

.deepdive-more-hint { display: block; text-align: center; font-size: 0.5em; color: var(--muted); margin-top: 10px; }
.reveal .slides > section > section:last-child .deepdive-more-hint { display: none; }
```

### `presentation/index.html` — nested Slide 7 structure (skeleton)

```html
<section>
  <section><!-- main Work & Review, existing content + 2 term-links --></section>
  <section data-deepdive-parent="7"><!-- git worktree deep-dive + navigator --></section>
  <section data-deepdive-parent="7"><!-- Rails/DHH deep-dive + navigator --></section>
</section>
```

### `presentation/index.html` — nested Slide 11 structure (skeleton)

```html
<section>
  <section><!-- main Tech Stack, existing content + 1 term-link --></section>
  <section data-deepdive-parent="11"><!-- MCP-what-is-it deep-dive + navigator --></section>
  <section data-deepdive-parent="11"><!-- MCP-limitations deep-dive + navigator --></section>
</section>
```

### `presentation/index.html` — slide 15 one-liner

```html
<h2 class="cons" data-i18n="s15.title">When NOT to Use It</h2>
```

## Post-Ship Follow-Up (2026-04-21)

After PR #2 (`9cf2407`) was merged and demoed, the term links rendered as plain accent-colored text with **no visible underline**. Root cause: Reveal.js's bundled `black.css` theme defines `.reveal a { text-decoration: none; }` at specificity `(0,1,1)`, which outranks the unscoped `.term-link` rule at `(0,1,0)`. The theme's rule silently stripped `text-decoration: underline dotted` from every `<a class="term-link">`. The anchors still worked (color, cursor, click), but the "this is a link" affordance was invisible.

**Fix (PR #3, `fix/term-link-specificity`):** Scope the selector as `.reveal a.term-link` to match the theme's specificity and win by source order. Also bump `text-decoration-thickness` to 2px and `text-underline-offset` to 4px so the underline is clearly distinguishable at body font size.

```diff
-    .term-link {
+    /* Scope under .reveal to beat Reveal theme's `.reveal a { text-decoration: none; }` */
+    .reveal a.term-link {
       color: var(--accent);
       text-decoration: underline dotted;
+      text-decoration-thickness: 2px;
-      text-underline-offset: 3px;
+      text-underline-offset: 4px;
       cursor: pointer;
       font-weight: inherit;
     }
-    .term-link:hover, .term-link:focus { text-decoration-style: solid; outline: none; }
+    .reveal a.term-link:hover, .reveal a.term-link:focus { text-decoration-style: solid; outline: none; }
```

**Lesson to carry forward.** This presentation uses a vendored Reveal.js theme (`presentation/vendor/black.css`). Any `.reveal`-child selector we write for a new clickable element needs to match or exceed the theme's specificity. Unscoped class-only selectors will lose. Rule of thumb: **scope interactive-element CSS under `.reveal` whenever the theme has a matching element selector.** Worth capturing as a solutions doc in `docs/solutions/ui-features/` as a durable reminder for the next time we add a styled anchor, button, or input to the deck.

## References

### Internal

- Presentation: `presentation/index.html`
- Related solutions: `docs/solutions/ui-features/revealjs-presentation-with-theming-i18n-branding.md`
- Related solutions: `docs/solutions/documentation-drift/presentation-advertised-unshipped-slash-command.md`
- Recent plans (same area): `docs/plans/2026-04-17-feat-align-presentation-with-every-canonical-flow-plan.md`, `docs/plans/2026-04-17-fix-remove-unshipped-ideate-command-plan.md`, `docs/plans/2026-04-14-feat-clickable-workflow-cycle-boxes-plan.md`

### External

- Reveal.js vertical slides: https://revealjs.com/vertical-slides/
- Reveal.js API (`Reveal.slide`, `Reveal.left`, `Reveal.right`, `Reveal.up`, `Reveal.down`): https://revealjs.com/api/
- MCP spec / Anthropic docs: https://modelcontextprotocol.io
- Git worktree docs: https://git-scm.com/docs/git-worktree
- Rails: https://rubyonrails.org
- DHH / 37signals: https://37signals.com
