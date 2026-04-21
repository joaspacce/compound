---
title: "docs: Research brief on compound-engineering plugin MCP architecture & stdio-vs-HTTP tradeoffs"
type: docs
status: active
date: 2026-04-21
brainstorm: docs/brainstorms/2026-04-21-mcp-stdio-and-compound-plugin-brainstorm.md
---

# docs: Research brief on compound-engineering plugin MCP architecture & stdio-vs-HTTP tradeoffs

## Overview

Source brainstorm: `docs/brainstorms/2026-04-21-mcp-stdio-and-compound-plugin-brainstorm.md`. Deliverable: one solutions doc at `docs/solutions/integration-issues/compound-plugin-mcp-stdio-vs-http.md`. Target: re-readable in under 5 minutes before the Kelsus Camp 2026 talk; durable reference afterward.

Scope: two angles only.

1. **Compound plugin architecture — what it actually ships.** Focus on how agents / commands / skills relate to the single `.mcp.json` entry, and what role MCP plays (hint: secondary).
2. **Security / deployment model — why stdio is often the "safe default."** Grounded in the MCP spec's explicit "Clients SHOULD support stdio whenever possible" + the HTTP transport's security warnings.

No slide edits in this pass. Current slide 11 deep-dives (`dd.mcp.*`, `dd.mcp_limits.*`) are accurate enough; the solutions doc is what the presenter re-reads, not what the audience sees.

## Consolidated Research Findings

All five brainstorm open questions resolved during the plan phase. Findings are embedded here so `/workflows:work` doesn't need to redo the research.

### Q1 — What does context7 give the plugin?

Two tools, HTTP transport, read-only:

- `resolve-library-id` — fuzzy match a library name to a canonical ID
- `query-docs` — fetch framework documentation for that ID

Used in exactly three places inside the plugin:

- `commands/deepen-plan.md` (L290–291) — invoked by `/deepen-plan` research agents
- `skills/create-agent-skills/workflows/verify-skill.md` (L87–100)
- `skills/create-agent-skills/workflows/create-domain-expertise-skill.md` (L139–140)

**Not load-bearing.** No fallback is documented, but commands continue without the docs lookup — they just produce less framework-specific research. All 29 agents, 22 commands, 19 skills function without context7.

### Q2 — Streamable HTTP vs SSE — which one does context7 use, and does Claude Code support it?

- context7 declares `"type": "http"` in `.mcp.json` → **Streamable HTTP** (not SSE, not stdio).
- Per MCP spec (modelcontextprotocol.io/docs/concepts/transports): Streamable HTTP **replaces** the legacy SSE transport (protocol version 2024-11-05). SSE is deprecated as of protocol 2025-03-26.
- Claude Code supports **all three transports** today (stdio, Streamable HTTP, SSE) via `claude mcp add --transport <type>`. The official docs explicitly mark SSE as deprecated: *"Use HTTP servers instead, where available."*
- **So on Claude Code: this plugin's context7 integration just works.** The stdio limitation on slide 13 is a general MCP-ecosystem warning, not a problem for this plugin on its primary target host.

### Q3 — What breaks on stdio-only hosts?

Two layers to the answer:

1. **Hosts that parse `.mcp.json` but don't support HTTP transport** — the context7 server silently doesn't initialize. The plugin itself loads fine; `/deepen-plan` just doesn't get framework docs. Graceful degradation.
2. **Separate known friction** — even on Claude Code, the plugin's README lists a known issue: *"MCP Servers Not Auto-Loading"* (README.md lines 209–226). Recommended workaround: manually add context7 to `.claude/settings.json`. This is a plugin-loading-mechanism issue, not a transport issue, but it's worth mentioning because it's the more likely reason a user sees "nothing happens."

### Q4 — Is stdio actually "safer"? (With citations)

Yes, per the MCP spec itself, which uses normative RFC-2119 language:

- *"Clients **SHOULD** support stdio whenever possible."* — MCP transport spec
- Streamable HTTP's explicit security requirements (same spec page):
  - *"Servers **MUST** validate the `Origin` header on all incoming connections to prevent DNS rebinding attacks"*
  - *"When running locally, servers **SHOULD** bind only to localhost (127.0.0.1) rather than all network interfaces (0.0.0.0)"*
  - *"Servers **SHOULD** implement proper authentication for all connections"*
  - *"Without these protections, attackers could use DNS rebinding to interact with local MCP servers from remote websites."*
- Stdio's security model: client spawns the server as a subprocess over stdin/stdout pipes. No network surface. Process-level isolation by the OS. Nothing to validate, no auth flow needed, no CORS, no DNS.

**Takeaway for the talk**: stdio is "safer" in the boring, real way — it has no network attack surface. HTTP MCP is safe *when correctly implemented* but introduces every web-security concern (origin validation, auth, session management, DNS rebinding). That's the policy reason some hosts choose stdio-only.

### Q5 — Who are the stdio-only hosts?

No comprehensive matrix publicly maintained. Data we have:

- **Claude Code** → supports all three (primary plugin target).
- **Cursor** → has its own `.cursor-plugin/plugin.json` at the plugin root; MCP support in Cursor is well-documented.
- **OpenCode / Codex** → plugin CHANGELOG marks them **experimental** (v1.0.0). Plugin outputs a tool-agnostic `compound-engineering.local.md` for these hosts.
- **Gemini CLI** → v2.35.0 added a converter that maps MCP servers to `.gemini/settings.json`. Converter support, not runtime support.
- **Factory Droid** → v1.2.0 added converter support; no transport documentation.
- **Windsurf / Kiro / GitHub Copilot** → no references in plugin docs.

**Honest framing for the doc**: "we target Claude Code + Cursor as primaries; every other host is either a converter target or marked experimental. If you run this plugin in a context7-incompatible host, the plugin still works — you just lose the `/deepen-plan` docs lookup." That's the answer to give an audience member.

## Proposed Solution — Solutions doc outline

File: `docs/solutions/integration-issues/compound-plugin-mcp-stdio-vs-http.md`

Target length: 500–700 words. Narrative flow, not reference chunks. Frontmatter follows the pattern from `docs/solutions/documentation-drift/presentation-advertised-unshipped-slash-command.md`.

```yaml
---
title: "How the compound-engineering plugin uses MCP, and why stdio limitations matter"
date: 2026-04-21
category: integration-issues
tags:
  - mcp
  - compound-engineering
  - claude-code
  - stdio
  - streamable-http
  - context7
  - transport
  - security
  - plugin-architecture
modules:
  - ~/.claude/plugins/cache/every-marketplace/compound-engineering/*/
severity: P3
status: resolved
---
```

### Sections (in order)

1. **TL;DR** (3–4 sentences) — the plugin ships one MCP server (context7) over Streamable HTTP; MCP is a secondary docs-lookup helper, not load-bearing; on stdio-only hosts you lose the lookup, not the plugin; stdio is "safer" for a concrete protocol reason, not a vibe.
2. **Plugin architecture at a glance** — the one-screen tree: 29 agents, 22 commands, 19 skills, 1 MCP entry. One paragraph + a minimal tree. MCP is **one out of four** integration layers.
3. **What context7 actually does** — two tools, three callsites, used by `/deepen-plan` + a skill. Describe "framework docs lookup" plainly.
4. **Transport choice: why HTTP?** — context7 is a remote service hosted by a third party; stdio would mean shipping a local subprocess that proxies HTTP anyway. HTTP is honest about that.
5. **Why stdio is the MCP spec's preferred default** — quote the SHOULD language, list the HTTP security requirements verbatim, name the attack vector (DNS rebinding). Two short paragraphs.
6. **What breaks on stdio-only hosts** — graceful degradation; plugin loads; `/deepen-plan` just produces less framework-aware research. Mention the separate "MCP Servers Not Auto-Loading" known issue as a distinct source of friction.
7. **Presenter Q&A crib** — three likely audience questions with one-paragraph answers each. (Candidates: *"Does this work in Cursor / OpenCode?"*, *"Is it unsafe that it calls out to the internet?"*, *"What happens if mcp.context7.com is down?"*)

### Drift discipline (mandatory before ship)

Every factual claim in the doc gets verified against the plugin on disk at ship time:

- Plugin path: `~/.claude/plugins/cache/every-marketplace/compound-engineering/2.35.0/`
- `.mcp.json` content still matches (single HTTP entry).
- No new MCP references in `*.md` that would change the story.
- Claude Code docs still list HTTP as recommended (URL: `https://code.claude.com/docs/en/mcp`).

Per `docs/solutions/documentation-drift/presentation-advertised-unshipped-slash-command.md`: no aspirational claims; every "the plugin does X" has a path + line number in the research notes.

## Acceptance Criteria

- [ ] `docs/solutions/integration-issues/compound-plugin-mcp-stdio-vs-http.md` exists with the 7 sections above.
- [ ] TL;DR is ≤4 sentences and accurately summarizes the doc.
- [ ] Total length 500–700 words (excluding frontmatter + code fences).
- [ ] At least one direct quote from the MCP spec on transport security (Origin validation, localhost binding, or DNS rebinding).
- [ ] At least one direct quote on stdio preference (*"Clients SHOULD support stdio whenever possible"*).
- [ ] Architecture section cites the 29 / 22 / 19 / 1 numbers from the actual plugin tree (grep-verified at ship time, not copied from this plan).
- [ ] Presenter Q&A crib covers at minimum: (a) Cursor / OpenCode compatibility, (b) security concern about calling out to the internet, (c) what happens if context7 is down.
- [ ] Brainstorm doc's 5 open questions are moved to the "Resolved Questions" section in the brainstorm file, with one-line answers + a link to the solutions doc.
- [ ] All external URLs resolve and are the canonical ones (spec page, Claude Code MCP docs, plugin repo).
- [ ] No slide edits unless the research turns up a factual error in `dd.mcp.*` or `dd.mcp_limits.*` keys.

## Verification

```bash
# Plugin state at ship time (run these, paste outputs into a verification log)
cat ~/.claude/plugins/cache/every-marketplace/compound-engineering/2.35.0/.mcp.json
ls ~/.claude/plugins/cache/every-marketplace/compound-engineering/2.35.0/{agents,commands,skills} | wc -l
grep -rni 'context7\|mcp.context7' ~/.claude/plugins/cache/every-marketplace/compound-engineering/2.35.0/ | wc -l

# Doc length check (500–700 words excluding code fences)
wc -w docs/solutions/integration-issues/compound-plugin-mcp-stdio-vs-http.md

# Markdown link check — all URLs in the doc resolve
grep -oE 'https?://[^ )]+' docs/solutions/integration-issues/compound-plugin-mcp-stdio-vs-http.md | sort -u | while read u; do
  curl -sS -o /dev/null -w "%{http_code}  $u\n" "$u"
done
```

## Out of Scope

- Transport-mechanics deep-dive (stdio framing details, Streamable HTTP session management, `Mcp-Session-Id`, resumability). Covered by the linked MCP spec; the solutions doc links out for depth.
- Client compatibility matrix across every AI editor in the ecosystem. Honest "we don't have a public matrix" + converter/experimental list is sufficient.
- Slide 11 v2 deep-dive rewrite. Current content is accurate at the audience-facing level; the solutions doc is the presenter-level one.
- Code changes to the plugin (e.g., stdio fallback for context7). That's a separate plan if it ever becomes a real need.

## Risks

- **MCP spec text evolves.** Quoted RFC-2119 language might change between now and the talk. Low risk — the transport concepts are stable.
- **Claude Code docs URL might move.** It already redirected from `docs.claude.com` → `code.claude.com` during research. Use the canonical `code.claude.com` URL; the redirect works for readers if Anthropic changes it again.
- **Plugin version bumps.** Solutions doc cites v2.35.0 numbers. Note the version in the doc so readers know the snapshot.

## MVP pseudocode — solutions doc skeleton

```markdown
---
title: ...
date: 2026-04-21
category: integration-issues
tags: [...]
severity: P3
status: resolved
---

# How the compound-engineering plugin uses MCP, and why stdio limitations matter

## TL;DR
<4 sentences>

## The plugin's four integration layers
<tree + one paragraph>

## What context7 actually does
<two tools, three callsites>

## Why context7 uses Streamable HTTP
<hosting model; subprocess would just proxy HTTP anyway>

## Why stdio is the MCP spec's preferred default
<quoted SHOULD; security warnings>

## What breaks on stdio-only hosts
<graceful degradation; separate known-issue framing>

## Presenter Q&A crib
### "Does this work in Cursor / OpenCode?"
...
### "Is it a security concern that it calls the internet?"
...
### "What happens if mcp.context7.com is down?"
...

## References
<plugin paths, MCP spec URL, Claude Code MCP docs URL, brainstorm link>
```

## References

### Internal

- Brainstorm: `docs/brainstorms/2026-04-21-mcp-stdio-and-compound-plugin-brainstorm.md`
- Presentation: `presentation/index.html` (slide 11 verticals — reference only, not edited in this pass)
- Drift discipline: `docs/solutions/documentation-drift/presentation-advertised-unshipped-slash-command.md`
- Reveal.js patterns: `docs/solutions/ui-features/revealjs-presentation-with-theming-i18n-branding.md`

### External

- MCP transports spec: https://modelcontextprotocol.io/docs/concepts/transports
- Claude Code MCP docs: https://code.claude.com/docs/en/mcp
- MCP Python quickstart: https://modelcontextprotocol.io/docs/develop/build-client
- Plugin repo: https://github.com/EveryInc/compound-engineering-plugin
- context7 project: https://context7.com (referenced by the plugin's `.mcp.json`)
