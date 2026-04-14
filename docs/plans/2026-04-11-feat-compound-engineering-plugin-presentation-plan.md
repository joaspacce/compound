---
title: Compound Engineering Plugin Presentation
type: feat
status: completed
date: 2026-04-11
---

# Compound Engineering Plugin Presentation

## Overview

Create a simple, concise presentation explaining the [Compound Engineering Plugin](https://github.com/EveryInc/compound-engineering-plugin) by Every Inc. The presentation should be accessible to engineers and non-engineers alike, covering what it is, how it works, and an honest assessment of pros and cons.

## Problem Statement / Motivation

The Compound Engineering Plugin is a powerful but dense project (14k+ stars, 94 releases). Team members and stakeholders need a quick, digestible overview to understand its value proposition without reading the full README or exploring the repo.

## Proposed Solution

Create a slide-based HTML presentation using [reveal.js](https://revealjs.com/) (single-file, no build step) inside a `presentation/` subfolder at the project root. The presentation should be ~16 slides, simple, visual, and self-contained.

## Presentation Structure

### Slide 1: Title
- "Compound Engineering Plugin"
- Subtitle: "Making each unit of work easier than the last"
- By Every Inc | github.com/EveryInc/compound-engineering-plugin

### Slide 2: What Is It?
- A plugin for AI coding editors (Claude Code, Cursor, etc.)
- Adds specialized agents, skills, and workflows
- 14,000+ GitHub stars, 94 releases, MIT licensed

### Slide 3: The Core Philosophy
- "Each unit of engineering work should make subsequent units easier — not harder"
- Inverts the traditional technical debt trajectory
- 80% Planning & Review / 20% Execution

### Slide 4: The Workflow Cycle (Diagram)
- Visual: Ideate (optional) → Brainstorm → Plan → Work → Review → Compound → Repeat
- Full cycle as recommended by the docs

### Slide 5: Key Commands
- `/ce:ideate` · `/workflows:ideate` · `/compound-engineering:workflows:ideate` — Surface high-impact improvements
- `/ce:brainstorm` · `/workflows:brainstorm` · `/compound-engineering:workflows:brainstorm` — Explore requirements interactively
- `/ce:plan` · `/workflows:plan` · `/compound-engineering:workflows:plan` — Convert ideas into technical plans
- `/ce:work` · `/workflows:work` · `/compound-engineering:workflows:work` — Execute with worktrees & task tracking
- `/ce:review` · `/workflows:review` · `/compound-engineering:workflows:review` — Multi-agent code review
- `/ce:compound` · `/workflows:compound` · `/compound-engineering:workflows:compound` — Document learnings for reuse

### Slide 6: How It Works — Brainstorm & Plan
- Brainstorm refines ideas through Q&A
- Plan converts requirements into actionable specs
- Research agents gather context automatically

### Slide 7: How It Works — Work & Review
- Work uses Git worktrees for isolated execution
- Review deploys multiple specialized reviewer agents (DHH, security, performance, etc.)
- Tasks tracked throughout

### Slide 8: How It Works — Compound
- Documents solutions and learnings
- Stored in `docs/solutions/` for future reference
- AI agents reference past learnings in future work

### Slide 9: In Practice
- Documentation flow: Ideate (optional) → Brainstorm → Plan → Work → Review → Compound
- My flow: Plan → Work → Review → Compound (when needed)
- You don't need every step every time

### Slide 10: Multi-Platform Support
- Primary: Claude Code, Cursor
- Experimental: OpenCode, Codex, Gemini CLI, GitHub Copilot, Kiro, Windsurf, and more
- CLI converts plugin format between platforms

### Slide 11: Tech Stack
- TypeScript (76.4%), Python, JavaScript, Shell, Ruby
- Bun runtime
- MCP Server integration
- MIT License

### Slide 12: Pros
- **Knowledge compounds over time** — documented learnings accelerate future work
- **Quality preservation** — rigorous review prevents tech debt accumulation
- **Multi-agent review** — catches issues from multiple perspectives (security, performance, architecture)
- **Platform-agnostic** — works across many AI editors, not just Claude Code
- **Open source (MIT)** — free to use, modify, and contribute
- **Active development** — 619 commits, 94 releases, strong community (14k stars)

### Slide 13: Cons
- **Heavy upfront investment** — 80/20 planning-to-execution ratio slows initial velocity
- **Dependency-heavy** — requires agent-browser, gh, jq, vhs, silicon, ffmpeg for full functionality
- **Experimental cross-platform** — non-Claude editors are experimental and may break
- **Learning curve** — 6 workflow commands with specific sequencing to internalize
- **MCP limitations** — some platforms only support stdio MCP servers
- **Opinionated workflow** — enforces a specific way of working that may not suit all teams

### Slide 14: When to Use It
- Teams doing AI-assisted development
- Projects where quality and documentation matter
- Codebases that will be maintained long-term
- When you want each sprint to be faster than the last

### Slide 15: When NOT to Use It
- Quick prototypes or throwaway scripts
- Solo side projects with no future maintenance
- Teams not using AI coding tools
- When you need to ship fast with minimal process

### Slide 16: Getting Started
- Install: `/plugin marketplace add EveryInc/compound-engineering-plugin`
- Setup: `/ce-setup`
- Start: `/ce:brainstorm` · `/workflows:brainstorm` · `/compound-engineering:workflows:brainstorm`
- Link: github.com/EveryInc/compound-engineering-plugin

## Technical Considerations

### Architecture
- Single HTML file using reveal.js CDN (no build step, no dependencies)
- Self-contained in `presentation/` subfolder at project root
- Opens in any browser

### File Structure
```
presentation/
├── index.html          # Main presentation (reveal.js, all slides)
└── README.md           # How to open/present
```

### Design
- Clean, minimal slides with dark theme
- Mermaid or CSS-based workflow diagram on slide 4
- Consistent color scheme matching the plugin's branding
- Large text, few bullets per slide

## Acceptance Criteria

- [x] `presentation/` folder created at project root
- [x] `presentation/index.html` — complete reveal.js presentation with all 16 slides
- [x] `presentation/README.md` — brief instructions on how to open and present
- [x] Presentation is self-contained (CDN-based, no local dependencies)
- [x] Slides are simple, concise, and visually clean
- [x] Pros and cons are balanced and honest
- [x] Workflow diagram is clear and readable
- [x] Opens correctly in a browser with `open presentation/index.html`

## MVP

### presentation/index.html

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>Compound Engineering Plugin</title>
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/reveal.js@5/dist/reveal.css">
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/reveal.js@5/dist/theme/black.css">
  <style>
    .reveal h1 { font-size: 2.2em; }
    .reveal h2 { font-size: 1.6em; color: #58a6ff; }
    .reveal li { font-size: 0.8em; margin-bottom: 0.3em; }
    .pros { color: #3fb950; }
    .cons { color: #f85149; }
    .workflow-box {
      display: inline-block;
      padding: 10px 20px;
      margin: 5px;
      border: 2px solid #58a6ff;
      border-radius: 8px;
      font-size: 0.7em;
    }
    .arrow { color: #58a6ff; font-size: 1.5em; }
  </style>
</head>
<body>
  <div class="reveal">
    <div class="slides">
      <!-- All 15 slides here following the structure above -->
    </div>
  </div>
  <script src="https://cdn.jsdelivr.net/npm/reveal.js@5/dist/reveal.js"></script>
  <script>Reveal.initialize();</script>
</body>
</html>
```

## References & Research

### External References
- Repository: https://github.com/EveryInc/compound-engineering-plugin
- reveal.js: https://revealjs.com/
- 14,000+ stars, 94 releases (v2.65.0), MIT License

### Key Stats
- TypeScript 76.4%, Python 10.9%, JavaScript 6.4%
- 619 commits, 36 open issues, 23 PRs
- Supports 12+ AI coding platforms
