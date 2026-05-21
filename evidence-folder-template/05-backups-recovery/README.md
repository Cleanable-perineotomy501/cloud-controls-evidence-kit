# 05 · Backups, recovery, and availability

Evidence that data can be recovered — verified by actual restore tests, not just backup-job success
messages. SOC 2 CC9.1 and A1.x; customer security reviews ask the "have you tested restore?"
question routinely.

## What goes here

- Backup configuration (managed database backups, retention)
- Restore-test runbook + most recent test report
- Incident runbook
- Status page / availability signal

## Owner

**Platform / SRE** owns backup configuration and restore tests. **Engineering leads** own the
incident runbook.

## Common gotchas

- **Backups confirmed but never restored.** "Backup succeeded last night" doesn't prove the backup
  is usable. Auditors want a restore test — actual data restored to a non-production environment,
  with row counts and runtime documented.
- **RPO / RTO defined in slides, not in code.** The recovery point + recovery time objectives need
  to be measurable against the actual restore test outcome.
- **No incident runbook for the major-incident case.** Every team has runbooks for routine stuff;
  few have the "production database is corrupt" version. Write the bad-day runbook before you need
  it.

## Cross-references

- Controls map: rows **5.1 – 5.4** in `../../controls-map.md`
- Questionnaire answers: questions 10-11 in `../../questionnaire-answer-examples.md`
