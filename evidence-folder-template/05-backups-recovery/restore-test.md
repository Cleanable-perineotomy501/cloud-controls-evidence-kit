# Restore test

The single piece of evidence that most teams don't have but most buyers ask for: **a documented
restore from backup**, recently, with measured outcomes. "We back up nightly" is not enough; an
auditor wants to see the proof you can restore.

## Evidence to keep here

For each quarterly test:

1. **Restore-test runbook** — the procedure followed.
2. **Test report** — date, source backup, target environment, row counts, integrity-check outcome,
   wall-clock time.
3. **Sign-off** — name of the person who validated the restore.

## Restore-test runbook (master template)

```markdown
# Restore Test Runbook — Production Database

## Pre-conditions

- Non-production environment available with capacity for full DB
- Test runner has read access to backups + write access to target
- Notifications: post in {{#engineering channel}} that a test is starting

## Steps

1. Choose source backup: most recent automated snapshot for prod DB
2. Target: dedicated `restore-test` environment in same cloud region
3. Initiate restore:
   - {{cloud-specific command}}
4. Wait for restore to complete; record wall-clock duration
5. Validate:
   - Row counts on key tables match expected within 5% (writes during snapshot interval can produce
     small skew)
   - Integrity checks: foreign-key constraint validation, application boot success against the
     restored DB
   - Spot-check a recent record (e.g., user signup from yesterday)
6. Tear down: delete the restored DB instance (cost control)
7. File the report

## Acceptance

- Restore completes in ≤ RTO ({{X hours}})
- Application boots against restored DB
- No data integrity errors
```

## Test report (per-test template)

```markdown
# Restore Test — 2026-05-15

**Tester:** {{name}}, **Reviewer:** {{name}}

| Item                | Value                                               |
| ------------------- | --------------------------------------------------- |
| Source backup       | Automated snapshot 2026-05-14T03:00Z                |
| Source DB           | prod-main (RDS / Cloud SQL)                         |
| Target environment  | restore-test (same region, same VPC)                |
| Restore initiated   | 2026-05-15T14:00Z                                   |
| Restore completed   | 2026-05-15T14:38Z                                   |
| **Wall-clock time** | **38 min**                                          |
| Row count: users    | 142,318 (expected ~142,300; delta within 0.05%)     |
| Row count: orders   | 1,084,221 (expected ~1,084,000; delta within 0.02%) |
| FK integrity        | PASS                                                |
| Application boot    | PASS — read-only smoke test against restored DB     |
| Tear-down           | Completed 2026-05-15T15:10Z                         |

## Outcome

PASS. Restore time 38 min, well within RTO (4 hours). No integrity issues.

## Issues / follow-ups

{{None / Issue list}}
```

## RPO / RTO answer

Once you have a real restore test:

> **RPO**: {{N}} minutes — point-in-time recovery is enabled, so the theoretical worst-case data
> loss is one continuous-backup interval.
>
> **RTO**: {{N}} hours — validated by the quarterly restore test (most recent: {{date}}, {{minutes}}
> wall-clock).

## Refresh

**Quarterly** is the standard for SOC 2 readiness. The test takes 1-2 hours; setting a recurring
calendar event is the cheapest durable improvement most teams can make.
