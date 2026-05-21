---
title: Release evidence
control: 3.4
owner: Engineering leads
cadence: Quarterly (export)
---

# Release evidence

The audit trail of "who shipped what to production, when, and who approved it." This is the
most-requested piece of change-management evidence on questionnaires.

## Evidence to keep here

A quarterly export of the last 30+ production deploys with:

- Date / time
- Commit SHA + PR link
- Author
- Approving reviewer (from branch protection)
- Approving deployer (from environment protection)
- Deploy outcome (success / rolled back)

## How to gather it

### GitHub — combined query

```bash
gh run list \
  --workflow=deploy.yml \
  --branch=main \
  --limit=50 \
  --json databaseId,displayTitle,headSha,headBranch,createdAt,actor,conclusion,event \
  > recent-runs.json
```

For each run, get the PR + reviewers:

```bash
gh api repos/<owner>/<repo>/commits/<sha>/pulls --jq '.[0]' > pr.json
gh api repos/<owner>/<repo>/pulls/<pr-number>/reviews \
  --jq '[.[] | select(.state=="APPROVED")]' > approvers.json
```

Combine into a single CSV / table:

| Date       | SHA    | PR    | Author | PR approver | Deploy approver | Outcome |
| ---------- | ------ | ----- | ------ | ----------- | --------------- | ------- |
| 2026-05-15 | a1b2c3 | #1234 | alice  | bob         | carol           | success |

Save as `release-evidence-2026-Q2.csv`.

## Sample answer for the questionnaire

> Every production deploy is associated with a Pull Request, a CODEOWNER review, and a
> deploy-approver. The most recent 30 production deploys are exported quarterly to
> `release-evidence-<quarter>.csv`. The full history is reconstructable from `git log` on the `main`
> branch combined with the CI/CD platform's deploy history.

## Bonus: signed commits

If your CODEOWNERS sign their commits and your branch rule requires signature verification, mention
that — it's a credibility booster and not a heavy lift to adopt.

```bash
# Verify
gh api repos/<owner>/<repo>/commits/<sha> --jq '.commit.verification'
```

## Example filenames

```
release-evidence-2026-Q2.csv
recent-runs-2026-05-15.json
```

## Refresh

Quarterly. Re-run the export against the last 90 days.
