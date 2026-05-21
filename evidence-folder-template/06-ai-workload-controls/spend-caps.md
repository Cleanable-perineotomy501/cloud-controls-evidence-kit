# Spend caps and abuse handling

Evidence that AI features have per-tenant rate-limits and spend caps so a single tenant (or a
misbehaving feature) can't burn through monthly model budget, and that there's a documented runbook
for abuse and incidents.

## Evidence to keep here

1. **Spend-cap configuration** — per-tenant or per-org quotas at the gateway.
2. **Alert routing** — what fires when caps are hit.
3. **Abuse runbook** — what to do when a customer reports prompt-injection, data leakage, or hostile
   use.

## Spend-cap configuration

The gateway in front of model endpoints should enforce:

| Tier       | Per-minute rate limit | Daily token cap | Monthly spend cap |
| ---------- | --------------------- | --------------- | ----------------- |
| Free tier  | 60 req/min            | 50k tokens      | $0 (hard stop)    |
| Paid tier  | 600 req/min           | 1M tokens       | per contract      |
| Enterprise | 6000 req/min          | 10M tokens      | per contract      |

Evidence to keep:

- Rate-limit / quota config (gateway / cloud-provider quotas)
- Logs showing limits actually firing for at least one tenant
- Alert rules that fire to on-call when:
  - A single tenant exceeds 80% of monthly cap
  - Org-wide spend exceeds budget threshold
  - Unusual spike (e.g., >5σ above tenant's baseline)

## Provider-level guardrails (defense in depth)

Even with your gateway, set caps at the model-provider level too — in case the gateway is bypassed:

- **OpenAI**: Org → Usage limits → Hard / Soft caps per project
- **Vertex AI**: Quotas → API requests per minute, tokens per project
- **Bedrock**: Service quotas + CloudWatch billing alarms

Save the config exports.

## Abuse runbook

```markdown
# AI Abuse Response Runbook

## Triggers

1. **Spend anomaly** — alert fires for spike or cap-approach
2. **Customer report** — "your AI said something inappropriate" / "your AI leaked another customer's
   data" / "prompt injection attack succeeded"
3. **Internal review flag** — content moderation or safety review surfaces something

## Response (Sev1 — data leak or breach)

1. Acknowledge within 15 minutes
2. **Throttle or disable** the affected endpoint immediately if data leakage is suspected (better to
   break the feature than leak more)
3. Pull recent inference logs for the affected tenant + the complaining customer
4. Engage security escalation + legal escalation
5. Customer notification per breach policy

## Response (Sev2 — misuse / abusive prompts)

1. Acknowledge within 1 hour
2. Identify the offending tenant / user
3. Apply rate-limit reduction or temporary block
4. Document with a per-incident write-up

## Response (Sev3 — spend anomaly only, no leakage)

1. Acknowledge within 4 hours
2. Identify the runaway feature or tenant
3. Apply quota reduction; investigate root cause (loop? compromised key?)
4. File a postmortem if the root cause is engineering-side
```

## Sample answer for the questionnaire

> AI features run behind a gateway that enforces per-tenant rate limits and monthly spend caps.
> Quotas are documented in `spend-caps.md`. When caps are approached or anomalies are detected,
> alerts page on-call. Abuse and misuse responses follow the runbook in `spend-caps.md` (also in the
> abuse section).

## Example filenames

```
gateway-quotas-2026-05-15.yaml
provider-spend-limits-2026-05-15.json
abuse-runbook-2026.md
incident-log-2026-Q2.md
```

## Refresh

Quarterly. Confirm caps still match contract tiers; review any incident reports.
