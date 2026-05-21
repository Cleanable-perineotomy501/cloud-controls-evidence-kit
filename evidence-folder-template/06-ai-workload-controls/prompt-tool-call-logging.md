# Prompt / tool-call logging

Evidence that prompts, tool calls, and outputs are logged with a policy that bounds retention and
access — so investigations are possible without becoming a long-term data risk.

## Evidence to keep here

1. **Logging policy** — what's logged, retention, who can read it.
2. **Sample log entry** — sanitized.
3. **Access controls** — who can query the logs.

## Logging policy template

```markdown
# AI Inference Logging Policy

## What we log

For every inference request:

- Timestamp, request ID, tenant ID, user ID
- Endpoint, model, input/output token counts
- Latency, status code

For inference requests **flagged for review** (per the abuse runbook):

- Full prompt
- Full output (or hash, depending on customer DPA)
- Tool calls made and their results

For routine traffic:

- {{policy: store hashes of prompt + output, retain full content for N days, etc.}}

## What we don't log

- {{e.g. customer PII unless required for incident investigation}}
- {{e.g. authentication tokens, API keys}}

## Retention

| Log type                      | Retention                               |
| ----------------------------- | --------------------------------------- |
| Request metadata (no content) | 365 days                                |
| Sampled prompt+output content | 30 days                                 |
| Flagged-for-review content    | 90 days, with security-team access only |

## Access

- Application engineers: metadata logs only
- AI / safety team: metadata + sampled content
- Security: all logs, for active investigations

Reading the flagged-content store requires an access-review entry (who, when, why).

## Customer data alignment

This policy must align with our customer DPA terms. If a customer contract states "no AI training on
customer data" or "30-day retention," our policy must respect the stricter term.
```

## How to gather sample evidence

```bash
# Pull a sanitized sample log entry from your logging backend
# (Datadog / Honeycomb / Loki / native)
# Replace any actual content with [REDACTED] before saving
```

## Sample answer for the questionnaire

> Inference requests are logged centrally with retention bounded per the policy in
> `prompt-tool-call-logging.md`. Metadata (timestamps, token counts, tenant IDs) is retained for 365
> days; content is sampled for 30 days; flagged-for-review content is retained for 90 days with
> security-team-only access. Retention windows align with customer DPA terms; we do not retain
> customer prompts longer than contracted.

## Example filenames

```
inference-logging-policy-2026.md
sample-log-entry-sanitized-2026-05-15.json
log-access-roles-2026-05-15.md
```

## Refresh

Policy: annually unless changed. Sample log: quarterly.
