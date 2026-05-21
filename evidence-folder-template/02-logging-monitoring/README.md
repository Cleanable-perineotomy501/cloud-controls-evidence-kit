# 02 · Logging and monitoring

Evidence that you can answer "what happened in production?" and "did anyone notice?" — with logs
that are complete, retained long enough, and routed somewhere that responds.

## What goes here

- Cloud audit log configuration (CloudTrail / Cloud Audit Logs / Azure Activity Log)
- Retention policy + lifecycle config
- Alert routing config (PagerDuty, Opsgenie, etc.) + sample fires
- Tamper-resistance config (object-lock / bucket-lock / WORM)

## Owner

Typically **Platform** for log configuration and **Security** for alert content. Some shops merge
these.

## Common gotchas

- **Per-account audit trails instead of org-level.** A trail only in one account leaves other
  accounts un-logged. Org-level CloudTrail (or GCP Org-level audit log sink) is the only acceptable
  answer for multi-account orgs.
- **Logs in the same account as the workload.** If the workload is compromised, the logs are too.
  Store in a dedicated security / logging account or project with limited write access.
- **Retention "until disk fills."** SOC 2 expects you can pull the full review-period audit trail
  (typically 1 year). Explicit lifecycle policy is the evidence.
- **No tamper resistance.** Audit logs in mutable storage are worth less. S3 Object Lock, GCS Bucket
  Lock, or equivalent.
- **Alerts but no one paged.** "We have alerts" without a routing destination is a
  documentation-only control. Verify that alerts actually fire to a person (or runbook) within
  minutes.

## Cross-references

- Controls map: rows **2.1 – 2.5** in `../../controls-map.md`
- Platform guides: see `../../platforms/` for where to find each piece of evidence per cloud
- Questionnaire answers: questions 4-5 in `../../questionnaire-answer-examples.md`
