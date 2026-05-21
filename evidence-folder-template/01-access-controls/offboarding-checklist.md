---
title: Offboarding checklist
control: 1.4
owner: HR + IT
cadence: Per departure
---

# Offboarding checklist

Auditors and customer security reviews ask for evidence that access is revoked promptly when someone
leaves. The standard evidence is: (a) a documented checklist, (b) completed instances of recent
offboardings, (c) a periodic spot-check that no orphaned access remains.

## Evidence to keep here

1. **This checklist** — the authoritative procedure.
2. **3–5 most recent completed instances** — one file per departure, showing each step completed
   with timestamp.
3. **Quarterly orphan check** — a quick scan confirming none of the recently-departed have active
   credentials anywhere.

## The checklist (master template)

Within **{{N hours}}** of HR signaling a departure, IT completes:

- [ ] Disable IdP account (Okta / Google / Entra) — wipes SSO access
- [ ] Revoke active sessions on IdP
- [ ] Remove from any admin / privileged role groups
- [ ] Cloud providers:
  - [ ] AWS: deactivate IAM user / remove SSO assignment
  - [ ] GCP: remove from organization IAM policies
  - [ ] Azure: remove from Entra groups + role assignments
- [ ] Code repositories:
  - [ ] GitHub: remove from org (or convert to outside collaborator if needed temporarily)
  - [ ] Revoke any active Personal Access Tokens issued to them
- [ ] SaaS criticals:
  - [ ] Datadog, Sentry, PagerDuty, Linear, Notion, Slack, etc.
- [ ] Production secrets:
  - [ ] If they had access to a vault (1Password / Vault / KMS), rotate any shared secrets they knew
- [ ] Hardware:
  - [ ] Laptop returned and wiped
  - [ ] YubiKey returned (or revoked from registration)
- [ ] Email forwarded / archived per data-retention policy
- [ ] Final attestation: `git log --author="<name>"` + IdP audit log show no activity after
      revocation timestamp

## Per-instance template

For each offboarding, copy this template to `offboarding-<initials>-<YYYY-MM-DD>.md`:

```markdown
# Offboarding: {{Initials}}, departed 2026-MM-DD

| Step                    | Done at          | By            |
| ----------------------- | ---------------- | ------------- |
| IdP disabled            | 2026-MM-DD HH:MM | {{IT person}} |
| Sessions revoked        | 2026-MM-DD HH:MM | {{IT person}} |
| AWS access removed      | 2026-MM-DD HH:MM | {{Platform}}  |
| GCP access removed      | 2026-MM-DD HH:MM | {{Platform}}  |
| GitHub access removed   | 2026-MM-DD HH:MM | {{Eng leads}} |
| PATs revoked (N=...)    | 2026-MM-DD HH:MM | {{Eng leads}} |
| SaaS tools              | 2026-MM-DD HH:MM | {{IT person}} |
| Shared secrets rotated  | 2026-MM-DD HH:MM | {{Platform}}  |
| Hardware returned/wiped | 2026-MM-DD HH:MM | {{IT person}} |
| Final attestation       | 2026-MM-DD       | {{Reviewer}}  |

Notes: {{anything atypical, e.g. retained advisor relationship}}
```

## Quarterly orphan check

Walk this list each quarter:

- IdP: list users not logged in for 90+ days → confirm each is intentional
- GitHub: outside collaborators list → confirm each is current
- AWS access keys / GCP service-account keys: any owned by a former employee → rotate immediately
- Datadog / Sentry / Linear / Slack: external/inactive users

File the result as `orphan-check-<YYYY-QN>.md`.

## Refresh

Per departure (mandatory). Plus quarterly orphan check.
