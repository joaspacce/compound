---
title: "Kelsus Camp 2026 — Speaker Notes"
date: 2026-04-21
event: Kelsus Camp 2026
presenter: Joaquín Pacce
target_duration: 25 minutes
---

# Kelsus Camp 2026 — Speaker Notes

## How to use this doc

- Read top-to-bottom the night before for rehearsal.
- On stage, skim the section for the slide you just opened.
- Deep-dive and Q&A cues are called out inline.
- Sibling doc for context: `docs/presentation/build-case-study.md`.

## Global cues

- **Language toggle** (top-right dropdown): EN default. Flip to ES/PT only if the audience skews that way. Toggle doesn't pause or break state — safe to switch live.
- **Theme toggle** (☀/🌙): dark default. Flip to light only if the projector washes out dark backgrounds.
- **Font size** (A- / A+): bump up twice if the back row complains. Reset with the ↺ button.
- **Kelsus watermark** (top-left on every slide but 1): clickable to kelsus.com if you want to point at it mid-talk.
- **Back-arrow escape**: `Esc` opens slide overview. Use only if you get fully lost.

## Timing — target total 25 min

- Intro (slides 1–3): **3 min**
- Core mechanics (slides 4–9): **10 min**
- Plugin context (slides 10–13): **5 min**
- Fit & close (slides 14–18): **5 min**
- Buffer / Q&A: **2 min**

---

## Slide 1 — Title

- Kelsus logo + Camp 2026 + "Compound Engineering Plugin" + presenter name.
- **Demo cue:** the Kelsus + Camp 2026 cluster is clickable → kelsus.com (new tab). Don't demo this one — just know it works.
- Keep intro tight. ~20 seconds on this slide.

## Slide 2 — What Is It?

- "A plugin for AI coding editors (Claude Code, Cursor, more)."
- "It adds specialized agents, skills, and workflows."
- "Structures AI-assisted development into a repeatable cycle."
- 14k+ stars, 94 releases, MIT licensed, open source.
- Keep to 30 seconds. Don't linger.

## Slide 3 — The Core Philosophy

- Quote is the headline: *"Each unit of engineering work should make subsequent units easier — not harder."*
- Inverts traditional technical-debt trajectory.
- **80% Plan & Review / 20% Work & Compound** — verbatim from Every.to.
- Knowledge from each cycle feeds the next.
- **If asked:** *"Isn't 80/20 planning vs execution unrealistic for tight sprints?"*
  - **Answer:** It's a long-term average, not per-PR. Plans and reviews compound — you plan once and reuse the pattern; you review once and the solution doc saves future review time. On tight deadlines the ratio tilts; over a quarter it settles back.

## Slide 4 — The Workflow Cycle

- Visual: Brainstorm (dashed, optional) → Plan → Work → Review → Compound → (loop).
- Dashed Brainstorm is deliberate — Every.to treats it as optional pre-planning.
- **Demo cue:** every box is clickable. Tap **Plan** to jump to slide 6. Tap **Work** or **Review** to jump to slide 7. Tap **Compound** to jump to slide 8. Do this live — it's the deck's best "feel" moment.
- **If asked:** *"Why is Brainstorm dashed and the rest solid?"*
  - **Answer:** Every.to's canonical flow starts at Plan. Brainstorm is a pre-cursor tool for when you're not sure what to build. When you already know, you skip it. The dashed line + arrow signals that visually.

## Slide 5 — Key Commands

- Five commands: `/ce:brainstorm` (optional), `/ce:plan`, `/ce:work`, `/ce:review`, `/ce:compound`.
- Each has two aliases: `/workflows:<name>` and `/compound-engineering:workflows:<name>`.
- **Demo cue:** point at the "optional" pill on the brainstorm row — it ties back to slide 4's dashed box.
- **If asked:** *"Why no `/ce:ideate`?"*
  - **Answer:** Early versions of this deck listed it. It turns out the plugin doesn't ship an Ideate command — five workflow commands, not six. We caught the drift before the talk and documented it. The lesson is in `docs/solutions/documentation-drift/`.

## Slide 6 — Brainstorm & Plan

- Brainstorm refines ideas via interactive Q&A; short-circuits when the idea is already clear.
- Plan converts requirements into actionable technical specs.
- Research agents gather codebase context + best practices automatically.
- Plans saved as markdown in `docs/plans/`.
- **If asked:** *"What makes a good brainstorm vs diving straight to plan?"*
  - **Answer:** Brainstorm answers WHAT; Plan answers HOW. If the WHAT is ambiguous (multiple interpretations, no clear success criteria), brainstorm first. If you already know what you want and just need steps, go straight to plan.

## Slide 7 — Work & Review

- **Work** uses Git worktrees for isolated parallel execution.
- Tasks tracked with built-in todo system; incremental commits as logical units complete.
- **Review** deploys multiple specialized reviewer agents: Security · Performance · Architecture · Rails (DHH) · Simplicity.
- **Demo cue:** two term links on this slide — "**Git worktrees**" and "**Rails (DHH)**", dotted-underlined. Tap either if the audience wants depth. Otherwise press ↓ to reveal the deep-dive hint and let it land without detouring.
- **If asked:** *"How many reviewers run in parallel?"*
  - **Answer:** Up to ~14 specialized review agents depending on config. In this session we configured a leaner subset; the full list is in the plugin's `agents/review/` directory.

### Deep-dive 7.1 — Git worktrees

- `git worktree add <path> <branch>` creates a parallel working directory.
- Same `.git`, multiple checkouts — no duplicate storage.
- `/workflows:work` can run the agent in its own worktree, leaving your main checkout untouched.
- Enables parallel agent swarms: each teammate gets its own branch and directory.
- **Navigator cue:** `↑ Back to Work & Review` returns to slide 7 (top of stack). `← Previous` jumps to slide 6. `Next →` jumps to slide 8 — skips the sibling Rails/DHH deep-dive.

### Deep-dive 7.2 — Rails & DHH

- **Rails** — Ruby on Rails, server-rendered framework (2004→).
- **DHH** — David Heinemeier Hansson, Rails creator and 37signals cofounder.
- Opinionated: convention over configuration, REST purity, Hotwire, "majestic monolith."
- The **`dhh-rails-reviewer`** agent reviews Ruby/Rails code in his style — flags JS-framework contamination, anti-patterns, convention drift.
- **Navigator cue:** same pattern as 7.1.

## Slide 8 — Compound

- Documents solutions, gotchas, and learnings after each cycle.
- Stored in `docs/solutions/` for future reference.
- AI agents **automatically reference** past learnings in future work.
- Each cycle makes the next one faster and better informed.
- **If asked:** *"Does this really compound in practice or is it aspirational?"*
  - **Answer:** Concrete example: the Reveal.js theme CSS specificity bug we hit on this deck (fixed in PR #3) got compounded into `docs/solutions/ui-features/revealjs-theme-css-specificity.md`. The next time anyone in the team adds a styled anchor to a Reveal.js deck, they find the rule in 30 seconds instead of debugging for 20 minutes.

## Slide 9 — In Practice

- *Documentation flow*: Brainstorm (dashed) → Plan → Work → Review → Compound. Full cycle as recommended.
- *My flow*: Plan → Work → Review · Compound (when needed). Both boxes clickable.
- You don't need every step every time — compound is the one that turns a sprint into a platform, but it earns its keep over the long run.
- Transition slide — no Q, just move through.

## Slide 10 — Multi-Platform Support

- Primary: Claude Code, Cursor.
- Experimental: OpenCode, Codex, Gemini CLI, GitHub Copilot, Kiro, Windsurf, Factory Droid.
- CLI converts plugin format between platforms automatically.
- **If asked:** *"Does this work in Cursor / OpenCode?"*
  - **Answer:** Cursor — yes, full support. OpenCode / Codex — marked experimental in the plugin changelog. Gemini CLI gets converter support. For the most confident experience, use Claude Code or Cursor.

## Slide 11 — Tech Stack

- TypeScript 76.4% (core plugin), Python 10.9%, JS 6.4%, Shell 3.9%, Ruby 2.4%.
- Bun runtime & package manager.
- **MCP Server** integration (Model Context Protocol) — term link.
- MIT License, fully open source.
- **Demo cue:** "MCP Server" is a term link → jumps to deep-dive 11.1. Tap it if a developer in the room asks about the protocol.
- **If asked:** *"Why Bun?"*
  - **Answer:** Startup time. Plugin commands are invoked hundreds of times a session; Bun's cold-start beats Node's, which matters when the command wraps a 2-second agent call.

### Deep-dive 11.1 — What is an MCP?

- **Model Context Protocol** — open standard from Anthropic (Nov 2024) for AI-to-tool connections.
- AI is the client; an MCP server exposes **tools**, **resources**, and **prompts**.
- compound-engineering uses one MCP: **context7**, for framework documentation lookup.
- **Navigator cue:** `↑ Back to Tech Stack` returns to slide 11.

### Deep-dive 11.2 — MCP transports & limitations

- MCP has two transports: **stdio** (local subprocess) and **Streamable HTTP** (remote).
- **This plugin's MCP (`context7`) is HTTP-based** — it calls a remote server at `mcp.context7.com`.
- On hosts that only support stdio, context7 **silently doesn't load** — no crash, just no docs lookup.
- The rest of the plugin still works — MCP is secondary, used by `/deepen-plan` + 2 skills.
- Claude Code & Cursor support HTTP; OpenCode and Codex are experimental — verify before relying on context7.
- **Key reframe** to hold in your head: the "some platforms only support stdio" warning on slide 13 is **this plugin-specific**, not abstract MCP theory. If that research lands before the talk: `docs/solutions/integration-issues/compound-plugin-mcp-stdio-vs-http.md`.

## Slide 12 — Pros

- Knowledge compounds; quality preservation; multi-agent review; platform-agnostic; open source (MIT); active development.
- Tempo this slide. 6 bullets is a lot — don't read them all aloud. Pick 2–3 and narrate.
- **If asked:** *"What's the single biggest pro?"*
  - **Answer:** The knowledge-compounding, by a wide margin. The other pros are table stakes for modern dev tools; compounding is what turns a plugin into a platform.

## Slide 13 — Cons

- Heavy upfront investment; dependency-heavy; experimental cross-platform; learning curve; **MCP limitations** (term link); opinionated workflow.
- **Demo cue:** "MCP limitations" is a term link → lands on deep-dive **11.2** (cross-stack). Jumping there from here is a great moment to show off the deck's interactivity if you've got time.
- **If asked:** *"Does that MCP limitation affect me on Claude Code?"*
  - **Answer:** No. Claude Code supports all three transports (stdio, Streamable HTTP, SSE-legacy). The bite is on hosts that are stdio-only — OpenCode and Codex are experimental; verify before relying on context7.

## Slide 14 — When to Use It

- Teams doing AI-assisted development.
- Quality and documentation matter.
- Codebases maintained long-term.
- Want each sprint to be faster than the last.
- No Q likely — quick read, move on.

## Slide 15 — When NOT to Use It

- Heading is **red** on purpose — tonal contrast with slide 14.
- Quick prototypes or throwaway scripts.
- Solo side projects with no future maintenance.
- Teams not using AI coding tools.
- Need to ship fast with minimal process.
- **If asked:** *"So you'd skip compound engineering for prototypes?"*
  - **Answer:** Yes. Compound's payoff is over many cycles. For a throwaway script the overhead of brainstorm + plan + solutions doc exceeds the value. Use the right tool for the timescale.

## Slide 16 — Getting Started

- Installation command + `/ce:brainstorm` to kick off.
- Note the three aliases: `/ce:*`, `/workflows:*`, `/compound-engineering:workflows:*`.
- Link: github.com/EveryInc/compound-engineering-plugin.
- ~30 seconds max. Don't walk them through install live.

## Slide 17 — Questions

- Open the floor.
- **Likely topics (crib):** MCP/Cursor compat, cost of AI pair-programming, testing discipline, review-agent accuracy, how to get buy-in from a skeptical team, what breaks in production.
- If silence for 10 seconds: seed with "A common question is how this interacts with existing CI/CD — happy to take that if nobody else has one."

## Slide 18 — Thank You

- Thank audience.
- Call out links: kelsus.com, github.com/EveryInc/compound-engineering-plugin.
- 15 seconds. Don't over-extend; respect the Q&A buffer.

---

## Emergency fallbacks

- **Projector dies mid-talk:** the deck is fully offline — vendored Reveal.js, no CDN. Reboot and resume at the current slide via hash URL.
- **Live demo click fails:** every clickable element also has a navigate-by-arrow equivalent. If a term link doesn't respond, just use `→` to advance and narrate what would have happened.
- **Language toggle gets weird:** hit the ↺ reset button (top-right) — returns to dark / EN / default font size.
- **Audience asks something you don't know:** "Great question — not sure. Ping me on Slack/email and I'll write it up." *(This is the compound philosophy in action — the question becomes tomorrow's solutions doc.)*

## References

- Deck: `presentation/index.html`
- Build case study (sibling): `docs/presentation/build-case-study.md`
- Plans: `docs/plans/` (8 files, chronological record)
- Solutions: `docs/solutions/` — drift, i18n/theming, CSS specificity
- Sibling brainstorm (PR #5, may not be merged at talk time): `docs/brainstorms/2026-04-21-mcp-stdio-and-compound-plugin-brainstorm.md`
- Every.to canonical guide: https://every.to/guides/compound-engineering
- Plugin repo: https://github.com/EveryInc/compound-engineering-plugin
