# Reddit posts (softer, community-first tone — Reddit hates ads)

Lead with the problem and your own use, not the product. Answer questions,
don't just drop a link.

--------------------------------------------------------------------
## r/LocalLLaMA  (privacy / local angle resonates most here)
--------------------------------------------------------------------
Title: I built a local-first tracer for coding agents — see every LLM call without shipping prompts to a cloud

Body:
I run coding agents locally and wanted to see what they actually do — every
model call, tool run, token, cost — but I didn't want prompts leaving my machine
and didn't want to stand up a 6-container Langfuse install.

So I made AgentTap: one Node process + one SQLite file. `npx agenttap`, open
localhost:4318. It captures either via OTLP or a built-in tracing proxy (point a
provider base URL at it), and shows traces/sessions/cost on a local dashboard.
Everything stays on your box.

Apache-2.0: github.com/HongguangLi/agenttap — curious what other local-agent
folks want tracked. Screenshot in comments.

--------------------------------------------------------------------
## r/ClaudeAI  (Claude Code users)
--------------------------------------------------------------------
Title: Made a tiny local dashboard to see Claude Code's token usage & cost per session

Body:
If you use Claude Code a lot and wonder where the tokens go: AgentTap reads
Claude Code's built-in OpenTelemetry (CLAUDE_CODE_ENABLE_TELEMETRY=1 → local
endpoint) and shows per-request model/tokens/cost, grouped by session and user,
on a local dashboard. No account, no cloud, `npx agenttap`.

Also works with Codex, OpenClaw, opencode, etc. Apache-2.0:
github.com/HongguangLi/agenttap

--------------------------------------------------------------------
## Awesome-list PRs (steady long-tail discovery)
--------------------------------------------------------------------
Submit a one-line entry to:
- awesome-claude-code
- awesome-llmops / awesome-llm-observability
- awesome-ai-agents

Entry:
- [AgentTap](https://github.com/HongguangLi/agenttap) — Local-first, single-process
  LLM observability (OTLP + tracing proxy) for coding agents; Langfuse-style
  dashboard, one SQLite file.
