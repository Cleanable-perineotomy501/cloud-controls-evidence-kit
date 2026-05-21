# Alert routing

Evidence that security-relevant events route to a human (or documented runbook) within minutes. "We
have alerts configured" is not enough — the questionnaire wants proof of routing and
acknowledgement.

## Evidence to keep here

1. **Alert rules** — list of security-relevant alerts with their triggers, severities, and
   destinations.
2. **Routing config** — PagerDuty / Opsgenie / native cloud alert integration that shows messages
   reaching a person.
3. **Sample fires** — 1-3 anonymized examples of alerts that actually fired, were acknowledged, and
   resolved. Demonstrates the chain works.

## Alerts that customer security reviews expect

At minimum:

| Trigger                                      | Severity | Destination   |
| -------------------------------------------- | -------- | ------------- |
| Root / break-glass account login             | Critical | Page on-call  |
| New IAM admin role assignment                | High     | Page on-call  |
| New service-account key created in prod      | High     | Page on-call  |
| Audit logging configuration changed          | Critical | Page on-call  |
| Multiple failed sign-ins on admin account    | High     | Page on-call  |
| Public S3 bucket / GCS bucket created        | High     | Page on-call  |
| Secret-scanning alert (committed credential) | High     | Page security |
| Container image with critical CVE deployed   | High     | Page platform |

Add more for your stack. Each item should have:

- A configured alert rule (link / file path)
- A clear destination (specific channel / person / rotation)
- A documented runbook for the on-call to follow

## How to gather the evidence

For each alert, screenshot or export:

- The rule definition in {{Datadog / CloudWatch / Sentinel / GCP Alerting}}
- The destination configuration in {{PagerDuty / Opsgenie}}
- A sample firing (anonymize sensitive data)

Save as `alert-<short-name>-<date>.{png,json}`.

## Sample answer for the questionnaire

> Security-relevant events from cloud audit logs, IdP audit logs, and code-repository security
> alerts route to our on-call rotation via {{PagerDuty / Opsgenie}}. Alert rules and runbooks are
> listed in `alert-routing.md`. Triage during on-call follows the playbook in
> `../05-backups-recovery/incident-runbook.md`.

## Refresh

- Alert config: annually unless you add new alerts.
- Sample fires: include any meaningful examples that show the chain works (don't manufacture them,
  but if real alerts fired, file an anonymized sample).
