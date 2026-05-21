---
title: Deploy approvals
control: 3.2
owner: Engineering leads
cadence: Quarterly
---

# Deploy approvals

Branch protection gates the merge; deploy approvals gate the actual production push. SOC 2 wants
both: code review (3.1) AND deployment approval (3.2). On GitHub, that means a protected Environment
with required reviewers.

## Evidence to keep here

1. **Environment config** — JSON or screenshot of the production environment's protection rules.
2. **Approver list** — who can approve a deploy to production. This is a sensitive list; review
   quarterly.
3. **Sample approval trail** — 3-5 recent production deploys showing the approver and approval
   timestamp.

## How to gather it

### GitHub Environments

```bash
gh api repos/<owner>/<repo>/environments/production > environment-prod.json
gh api repos/<owner>/<repo>/environments/production/deployment-protection-rules \
  > protection-rules-prod.json
```

Required reviewers list:

```bash
gh api repos/<owner>/<repo>/environments/production \
  --jq '.protection_rules[] | select(.type=="required_reviewers")'
```

### GitLab — Protected Environments

```bash
glab api projects/<id>/protected_environments > protected-envs.json
```

### Deploy approval trail

Pull the last 30 production deploys with author + approver:

```bash
gh run list --workflow=deploy.yml --branch=main --limit=30 \
  --json databaseId,displayTitle,event,createdAt,actor,conclusion \
  > recent-deploys.json
```

For each, the deploy review record is at:

```bash
gh api repos/<owner>/<repo>/actions/runs/<run-id>/approvals
```

Aggregate into a CSV or table for `release-evidence.md`.

## Required protection contents

- [ ] At least one required reviewer on `production` environment
- [ ] Reviewer can't be the PR/commit author (or document why if allowed)
- [ ] Production secrets scoped to the `production` environment only (not repository-wide)
- [ ] Deploy branch / tag restriction (only `main` or release tags)

## Sample answer for the questionnaire

> Production deploys are gated by a GitHub Environment named `production` with required reviewers.
> The approver list is in `deploy-approvals.md` and rotates with team changes. Production secrets
> are scoped to that environment only — they are not accessible from arbitrary workflows.

## Example filenames

```
environment-prod-2026-05-15.json
protection-rules-prod-2026-05-15.json
recent-deploys-2026-05-15.json
```

## Refresh

Quarterly. Re-export the environment config; verify the reviewer list against the IAM quarterly
review.
