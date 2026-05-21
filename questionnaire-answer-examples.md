# Questionnaire Answer Examples

Template answers for the 12 questions that come up on almost every customer security questionnaire.
Paste, edit to fit your specifics, attach the matching evidence from your
`evidence-folder-template/`.

> **House rules:**
>
> 1. Don't bluff. If a control doesn't exist, write "We do not currently enforce this; we plan to by
>    [date]" instead of inventing one.
> 2. Always attach the evidence. Answer-only questionnaire responses get follow-up requests;
>    evidence-attached responses close the loop.
> 3. Keep answers short. Procurement teams skim. The evidence file is where the detail lives.

---

## Access controls

### 1. Do you enforce MFA for all administrative access to production systems?

> Yes. MFA is enforced via {{IdP — e.g. Okta / Google Workspace /
> Microsoft Entra}} for all users with administrative roles in our identity provider, cloud
> consoles, and code-repository organization. Enforcement is policy-based at the IdP, not opt-in. We
> review the per-user enforcement status quarterly.
>
> **Evidence:** see `01-access-controls/mfa-enforcement.md` and the attached IdP policy export.

### 2. How do you manage privileged access?

> Privileged access is granted via named roles in our IdP (no shared credentials, no permanent admin
> assignments to individual users where avoidable). Membership in privileged roles is reviewed
> quarterly and recorded in `01-access-controls/iam-quarterly-review.md`. Production infrastructure
> access for engineers is granted via {{your access
> system — e.g. AWS SSO / GCP IAM groups / Teleport / Tailscale}}.

### 3. How do you handle offboarding?

> Within {{N hours}} of a departure, the offboarding checklist revokes access across the IdP, all
> cloud providers, all code repositories, and SaaS tools. The completed checklist is filed in
> `01-access-controls/offboarding-checklist.md` per departure. Quarterly we sample 3 recent
> offboardings and verify no orphaned credentials remain.

---

## Logging and monitoring

### 4. How long do you retain audit logs?

> Production cloud audit logs are retained for {{N}} days in {{S3 / GCS / Storage Account}} with
> object-lock / bucket-lock applied to prevent tampering. Application logs are retained for {{N}}
> days in {{Datadog / Honeycomb / Loki / native}}.
>
> **Evidence:** see `02-logging-monitoring/retention-policy.md` for the storage lifecycle
> configuration and a sample query showing the oldest available log.

### 5. Do you have intrusion detection / security monitoring?

> Yes. Security-relevant events from cloud audit logs, IdP audit logs, and code-repository security
> alerts route to our on-call rotation via {{PagerDuty / Opsgenie}}. Alert rules and routing are
> documented in `02-logging-monitoring/alert-routing.md`. We do not operate a dedicated SOC; alerts
> are triaged by the on-call engineer against the runbook in
> `05-backups-recovery/incident-runbook.md`.

---

## CI/CD and change management

### 6. How do you control changes to production?

> Production deploys are gated by:
>
> 1. Branch protection on the production branch ({{main / production}}) — at least one required PR
>    review by a CODEOWNER.
> 2. GitHub Environments protection on the production environment — requires explicit approval by an
>    authorized reviewer.
> 3. Required passing CI checks (lint, type-check, tests, security scans).
>
> **Evidence:** see `03-cicd-change-management/branch-protection.md` (rule export),
> `deploy-approvals.md` (environment config), and `release-evidence.md` (last 30 production deploys
> with author and approver).

### 7. Can you produce a change history for production?

> Yes. Every production deploy is associated with a Pull Request, CODEOWNER review, and CI build.
> The full history is available via `git log` on the production branch and our CI/CD platform's
> deploy history. The most recent 30 deploys are exported quarterly to
> `03-cicd-change-management/release-evidence.md`.

---

## Vulnerability and secrets hygiene

### 8. Do you scan for vulnerabilities and secrets?

> Yes:
>
> - **Dependencies:** {{Dependabot / Renovate / Snyk}} scans every pull request and continuously
>   against `main`. Critical/high CVEs are remediated per the SLA in
>   `04-vulnerability-secrets/dependency-scanning.md`.
> - **Container images:** {{Trivy / Artifact Registry / ECR}} scanning runs in CI; deploys are
>   blocked on critical CVEs unless explicitly exempted via the documented exception process.
> - **Secrets in repositories:** GitHub Advanced Security secret scanning is enabled
>   organization-wide with push protection. Hits are triaged within {{N hours}} per
>   `04-vulnerability-secrets/secret-scanning.md`.

### 9. Have you had a penetration test?

> {{Yes — vendor and date}}, or:
>
> > We have not engaged a third-party pen-test as of {{date}}. We plan to in {{quarter / year}}. Our
> > current security posture is validated through automated scanning (dependency, container,
> > secret), code review on every change, and {{any in-house red-team exercises}}.
>
> Don't fake a pen-test you didn't do.

---

## Backups, recovery, and availability

### 10. How are production databases backed up, and have you tested restore?

> {{RDS / Cloud SQL / native}} automated backups run {{daily /
> continuously}} with {{N}}-day retention. Point-in-time recovery is enabled. Restore tests are run
> quarterly to a non-production environment; the most recent test result (date, restored row counts,
> wall-clock time, integrity-check outcome) is in `05-backups-recovery/restore-test.md`.

### 11. What's your RPO / RTO?

> Recovery Point Objective (RPO): {{N minutes / hours}}, established by the continuous backup
> capability above.
>
> Recovery Time Objective (RTO): {{N hours}}, validated by the quarterly restore test which measures
> wall-clock time from snapshot selection to fully populated target instance.
>
> The incident runbook in `05-backups-recovery/incident-runbook.md` includes a documented
> major-incident recovery procedure.

---

## AI workload (if applicable)

### 12. How do you handle AI / LLM-specific security and abuse?

> Our AI features operate under documented controls:
>
> - **Endpoint auth:** all model inference endpoints sit behind per-tenant API authentication.
>   Public unauthenticated access is not possible.
> - **Prompt and tool-call logging:** logged centrally per the policy in
>   `06-ai-workload-controls/prompt-tool-call-logging.md`, with retention aligned to our customer
>   DPA.
> - **Tenant isolation:** retrieval indices, embeddings, and any stored AI artifacts are partitioned
>   per-tenant. See `06-ai-workload-controls/data-retention.md`.
> - **Spend / abuse controls:** per-tenant rate limits and spend caps are enforced in front of the
>   model endpoints; alerts route to on-call. See `06-ai-workload-controls/spend-caps.md`.
> - **Customer data and training:** we do not use customer data to train or fine-tune base models.
>   {{Adjust if not true.}}

---

## When the questionnaire asks something not on this list

Common follow-ups:

| Topic                           | Where to look                                            |
| ------------------------------- | -------------------------------------------------------- |
| Vendor / sub-processor list     | Internal vendor inventory                                |
| Data residency                  | Cloud region selection + storage policy                  |
| Encryption at rest / in transit | Cloud-provider defaults + custom KMS config              |
| Employee security training      | HR/training records                                      |
| Background checks               | HR records                                               |
| SOC 2 / ISO report              | Engage an auditor — this kit does not substitute for one |

If a question genuinely doesn't map to anything you have, **don't invent the control**. Write the
honest version: "We do not currently enforce this. We assess the risk as {{LOW / MEDIUM / HIGH}}. We
plan to implement this by {{date}}." That answer is fine for many buyers; it's the bluff that gets
caught.
