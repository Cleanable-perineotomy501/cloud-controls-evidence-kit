# IAM quarterly review

Periodic review of who has privileged access — to the IdP, cloud consoles, code repositories, and
production data. Catches drift: former contractors still in groups, engineers who got temporary
elevation and never lost it, service accounts that aren't used anymore.

## Evidence to keep here

1. **Review minutes** for each quarter — date, attendees, decisions made (who got removed, who got
   added, who got justified to stay).
2. **Membership export** at the time of review — IdP admin role members, cloud-console privileged
   roles, repo org owners / admins, key SaaS admin accounts.
3. **Sign-off** — name(s) of the reviewer who approved the resulting membership list.

## How to gather it

Quarterly:

1. Export current membership:
   - IdP: Okta / Google Workspace / Entra → admin role membership
   - AWS: `aws iam get-account-authorization-details` or AWS Identity Center assignments
   - GCP: `gcloud projects get-iam-policy <project>` for each prod project
   - GitHub: org → People → filter by `2fa_disabled` and `owners` role
   - SaaS critical tools: Snowflake admin users, Datadog admin users, etc.
2. Walk each list with the responsible owner. For each member, answer: "Is this person still in a
   role that requires this access?" Remove anyone who's a no.
3. Write up the diff: removed, added, kept-with-justification.
4. Both reviewers sign off (file in this folder).

## Template review minutes

```markdown
# IAM Review — 2026Q2 (2026-05-15)

**Reviewers:** {{name}} (Security), {{name}} (Platform)

## Removed

| System | Role                          | Member    | Reason                  |
| ------ | ----------------------------- | --------- | ----------------------- |
| AWS    | OrganizationAccountAccessRole | alice@... | Left company 2026-04-12 |
| GitHub | Owner                         | bob@...   | Moved to non-admin role |

## Added

| System   | Role        | Member    | Reason                    |
| -------- | ----------- | --------- | ------------------------- |
| GCP prod | roles/owner | carol@... | Promoted to Platform Lead |

## Kept (with justification)

| System | Role                | Member   | Justification                           |
| ------ | ------------------- | -------- | --------------------------------------- |
| AWS    | AdministratorAccess | dave@... | Sole on-call admin for prod break-glass |

## Sign-off

- Reviewed by {{name}}, {{name}} on 2026-05-15.
- Next review: 2026-08-15.
```

## Example filenames

```
quarterly-iam-review-2026Q2.md
iam-membership-snapshot-2026-05-15.csv
```

## Refresh

Quarterly. Set a recurring calendar event with both reviewers.
