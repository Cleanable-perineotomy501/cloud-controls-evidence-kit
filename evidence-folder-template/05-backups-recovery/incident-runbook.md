---
title: Incident runbook
control: 5.3
owner: Engineering leads
cadence: Annually unless an incident
---

# Incident runbook

The "what to do when something is on fire in production" document. Auditors and customer security
reviews ask for it; the bigger value is that writing it forces the team to think through worst cases
before they happen.

## Evidence to keep here

1. **This runbook** — kept current.
2. **Recent postmortems** (if any) — sanitized for sharing externally if needed.
3. **On-call rotation** — current schedule + escalation path.

## Master runbook

````markdown
# Incident Runbook

## Severity definitions

| Sev  | Definition                                                        | Response time                            |
| ---- | ----------------------------------------------------------------- | ---------------------------------------- |
| SEV1 | Customer-facing service down, major data loss, or security breach | Page immediately; war room within 15 min |
| SEV2 | Significant degradation for many customers; potential data issue  | Page on-call; war room within 1 hour     |
| SEV3 | Limited customer impact OR internal-only issue                    | Ticket; address next business day        |

## On-call rotation

- Current schedule: {{PagerDuty rotation link}}
- Primary on-call: see PagerDuty
- Secondary / escalation: see PagerDuty
- Engineering lead escalation: {{name}}
- Security escalation: {{name}}

## SEV1 response procedure

1. **Acknowledge the page** within 5 minutes.
2. **Declare an incident** — post in {{#incidents channel}} with one-line description + severity.
3. **Spin up a war room** — {{Slack huddle / Zoom / dedicated
   incident channel}}. Bring in: on-call, eng lead, anyone with relevant context.
4. **Assign roles**:
   - **Incident commander** — coordinates, decides
   - **Operator** — drives the actual fix
   - **Comms** — customer notifications, status page, internal updates
5. **Triage**:
   - What's broken? (specific service / API / data store)
   - Blast radius? (customer count, data category)
   - Is data being lost or exposed? (security implication?)
6. **Stabilize first, root-cause later**:
   - Roll back to last known good if possible
   - Failover to standby region if applicable
   - Enable read-only mode if write-side is corrupt
7. **Notify** customers via {{StatusPage / email / in-app}} per the threshold:
   - SEV1: notify within 30 min
   - SEV2: notify within 2 hours
8. **Resolve** and confirm: monitoring back to baseline; spot-check the affected flows
9. **Postmortem** — schedule within 5 business days; sanitize and file in this folder

## Special-case runbooks

### Production database corruption / data loss

1. Take application offline (read-only mode or 503 page)
2. Identify last known good snapshot
3. Follow `restore-test.md` procedure with production target
4. Validate restored DB
5. Bring application back up
6. Communicate data-loss window to affected customers

### Security incident (suspected breach / credential exposure)

1. Revoke the suspect credential immediately (don't wait to investigate further)
2. Rotate all related credentials (derive risk: what could have been accessed?)
3. Pull cloud audit logs for the affected time window
4. Engage security escalation: {{name}}
5. If customer data potentially exposed: notify legal + privacy/comms within 24 hours
6. Document everything; do not delete evidence

### Major cloud-provider outage

1. Confirm the outage on the provider's status page (rule out our own bug)
2. Switch traffic to standby region if multi-region
3. Notify customers via status page
4. Wait + retry plan documented for stateful services

## Postmortem template

```markdown
# Incident Postmortem — YYYY-MM-DD

**Severity:** SEV1 / SEV2 / SEV3 **Duration:** {{start time}} → {{end time}} ({{X minutes}})
**Customer impact:** {{description}}

## Timeline

| Time (UTC) | Event                 |
| ---------- | --------------------- |
| HH:MM      | Alert fired           |
| HH:MM      | On-call acknowledged  |
| HH:MM      | War room opened       |
| HH:MM      | Root cause identified |
| HH:MM      | Mitigation applied    |
| HH:MM      | Confirmed recovered   |

## Root cause

{{What broke and why}}

## What worked

{{Things the team did well}}

## What didn't

{{Things to improve}}

## Action items

| Item | Owner | Due |
| ---- | ----- | --- |
| ...  | ...   | ... |
```
````

## Refresh

Annually unless a real incident happens (in which case the postmortem informs runbook updates).
