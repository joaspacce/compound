---
title: "Brainstorm: MCP stdio limitation & how the compound-engineering plugin actually uses MCP"
topic: mcp-stdio-compound-plugin
date: 2026-04-21
status: active
next: research + write solutions doc (see Handoff)
---

# Brainstorm: MCP stdio limitation & how the compound plugin works

## What We're Building

**Not code. Two research artifacts for Kelsus Camp 2026 talk prep:**

1. This brainstorm doc — scope, decisions, and open questions.
2. A single consolidated solutions doc — `docs/solutions/integration-issues/compound-plugin-mcp-stdio-vs-http.md` — that the presenter can re-read before the talk and that stays discoverable afterward.

The presenter (Joaquín) already has the main deck built. The slide 11 MCP deep-dive (`dd.mcp.title` + `dd.mcp_limits.title`) is accurate at a surface level but leans abstract. This investigation turns the abstract "some platforms only support stdio" claim into a grounded answer about (a) what *this specific plugin* actually ships and (b) why stdio is frequently picked as the "safe default."

## Why This Approach

Picked Approach B over a brainstorm-only write-up because the investigation surfaced a load-bearing fact that will be useful beyond the talk: **the compound-engineering plugin's only declared MCP server is `context7`, transport `http`, URL `https://mcp.context7.com/mcp`.** That single finding changes the framing — the stdio limitation isn't academic; it directly gates whether this plugin's context7 integration works on a given client. Future-me will want to grep for that, and `docs/brainstorms/` is the wrong home for it.

## Key Decisions

- **Scope two angles, not four.** Talk-prep emphasis: (1) compound plugin architecture — what it actually ships; (2) security / deployment model — why stdio is often the "safe default." Transport-mechanics deep detail and client-compatibility matrix are explicitly deferred as stretch.
- **Deliverable: one solutions doc, narrative flow.** Structure: "what the plugin ships → which parts are MCP vs agents/skills/commands → transport choice → security reasoning → what fails on stdio-only hosts → presenter Q&A crib." Target length ~500–700 words. Aims for re-readability in under 5 minutes.
- **No slide changes in this pass.** The current deep-dive isn't *wrong*; it's just thin. Only edit it if the investigation turns up a factual error. Slide edits, if any, are a follow-up plan.
- **Research sources.** Plugin on disk (`~/.claude/plugins/cache/every-marketplace/compound-engineering/2.35.0/`) is primary. Official MCP spec / Anthropic docs are secondary. Client-side docs (Claude Code, Cursor, etc.) are consulted only if client compatibility comes up in the answer.
- **Drift discipline.** Per `docs/solutions/documentation-drift/presentation-advertised-unshipped-slash-command.md`: every claim in the solutions doc is verified against the plugin source before shipping. No aspirational "the plugin ships X" statements without grep proof.

## Open Questions

1. **What exactly does context7 give the plugin?** Need to read context7's tool surface (docs lookup / resolve-library-id tools were surfaced earlier in this session via MCP server instructions). Is this the plugin's *only* external tool integration or are there others I missed?
2. **Does "Streamable HTTP" have quirks on common clients?** MCP HTTP transport has two variants in the wild — SSE (legacy) and Streamable HTTP (current). Which one is context7 using? Which one does Claude Code support? (Affects the transport-mechanics angle even if deferred.)
3. **What breaks on stdio-only hosts?** When a host doesn't support HTTP MCP, does the plugin silently degrade (context7 tools just don't appear) or does it error at plugin-load time? This is the Q an attendee is most likely to ask.
4. **Is stdio actually "safer"?** Beyond process-isolation intuition, are there published security incidents or advisories that make the "safe default" story concrete? If yes, one citation is worth a paragraph of theory.
5. **Who are the stdio-only hosts?** Deferred per scope, but worth at least one line: "Gemini CLI / Codex / X is stdio-only today" vs. "all major hosts support HTTP" changes the weight of the limitation claim entirely.

## Resolved Questions

*(None yet — will move items from Open → Resolved as the research doc gets written.)*

## Links & Follow-Ups

- Target solutions doc: `docs/solutions/integration-issues/compound-plugin-mcp-stdio-vs-http.md` (to be created)
- Current slide deep-dive (for reference, not yet edited): slide 11 verticals in `presentation/index.html` (`dd.mcp.*` and `dd.mcp_limits.*` i18n keys)
- Plugin on disk: `~/.claude/plugins/cache/every-marketplace/compound-engineering/2.35.0/`
- Plugin MCP config: `~/.claude/plugins/cache/every-marketplace/compound-engineering/2.35.0/.mcp.json`
- Related solution — drift discipline: `docs/solutions/documentation-drift/presentation-advertised-unshipped-slash-command.md`
- MCP spec: https://modelcontextprotocol.io
