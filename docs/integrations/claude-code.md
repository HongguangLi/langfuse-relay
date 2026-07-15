# Claude Code → AgentTap

Which route to use depends on how your Claude Code authenticates.

## claude.ai subscription (OAuth login) — use native OTel

A custom `ANTHROPIC_BASE_URL` disables Claude Code's subscription OAuth (it
then demands an API key), so the proxy route does **not** work for
subscription users. Use Claude Code's built-in OpenTelemetry instead — it
keeps your normal login untouched and reports each API request to AgentTap:

```bash
export CLAUDE_CODE_ENABLE_TELEMETRY=1
export OTEL_LOGS_EXPORTER=otlp
export OTEL_METRICS_EXPORTER=otlp
export OTEL_EXPORTER_OTLP_PROTOCOL=http/protobuf
export OTEL_EXPORTER_OTLP_ENDPOINT=http://127.0.0.1:4318
# optional: capture prompt text too (off by default for privacy)
# export OTEL_LOG_USER_PROMPTS=1
claude
```

Claude Code emits its per-request telemetry as OTLP **log events**;
AgentTap's `/v1/logs` endpoint turns each `claude_code.api_request` into an
LLM span with model, input/output tokens, cost, and latency, grouped by
session and by user. Metrics are accepted and ignored. You'll see Claude
Code under the `claude-code` service in the dashboard.

## API-key billing — use the LLM tracing proxy (full content)

If your Claude Code uses a pay-as-you-go `ANTHROPIC_API_KEY` (not a
subscription), point it at the proxy to capture full request/response text:

```bash
export ANTHROPIC_BASE_URL=http://127.0.0.1:4318/proxy/anthropic
claude
```

The key passes through untouched; streaming works transparently. This route
captures prompt and completion content, which the OTel/logs route does not.
