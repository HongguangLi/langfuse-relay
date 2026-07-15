# Show HN post

## Title (pick one — keep under ~80 chars)
- Show HN: AgentTap – local-first LLM tracing for Claude Code/Codex, one process
- Show HN: Trace your coding agent's LLM calls locally, no containers (npx agenttap)

## URL
https://github.com/HongguangLi/agenttap

## Text (the first comment — post this yourself right after submitting)

I kept wanting to see what my coding agents (Claude Code, Codex, OpenClaw) were
actually doing under the hood — every LLM call, tool run, token count and cost —
but the good option, self-hosting Langfuse, means running Postgres + ClickHouse +
Redis + MinIO. That's a lot of infrastructure for one developer tracing local
agents.

So I built AgentTap: the same trace / session / cost views, but in one Node
process with one SQLite file and a single dependency. `npx agenttap` and open
localhost:4318.

How it captures, with no code changes to the agent:
- OTLP ingest (/v1/traces, /v1/logs) for anything that exports OpenTelemetry —
  OpenClaw via NVIDIA's NeMo Relay, Codex, Claude Code's native telemetry.
- A built-in LLM tracing proxy: point a provider base URL at it and every
  request is forwarded verbatim (your key passes through) and recorded on the side.

It normalizes three semantic conventions (OTel GenAI, OpenInference, NeMo Relay)
into one unified view, so input/output/tokens show up regardless of dialect. The
dashboard has waterfall + tree + mind-map trace views, sessions, per-user and
per-model breakdowns.

It's deliberately local-first: binds to loopback, prompts never leave your
machine, and if you outgrow it you can re-point the same OTLP exporter at
Langfuse/Phoenix/Jaeger — no lock-in.

Apache-2.0, Node >= 22 (uses the built-in node:sqlite). Would love feedback,
especially on the capture side and which agents you'd want first-class support for.

## Timing / tips
- Submit on a weekday, ~8-10am US-Pacific.
- Post the text above as the first comment immediately.
- Reply to every comment for the first few hours; engagement drives ranking.
- Don't ask for upvotes anywhere (against HN rules).
