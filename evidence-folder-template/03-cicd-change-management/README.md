# 03 · CI/CD and change management

Evidence that code changes to production go through review, approval, and leave an audit trail. SOC
2 CC8.1 territory; customer security questionnaires ask for this routinely.

## What goes here

- Branch protection configuration (the rule that gates the production branch)
- Deploy approval configuration (GitHub Environments or equivalent)
- Release evidence — last 30 production deploys with author and approver
- Rollback procedure documentation

## Owner

**Engineering leads** typically own the policy; **Platform** owns the CI/CD plumbing.

## Common gotchas

- **Required reviews can be bypassed by admins.** Set "Do not allow bypassing the above settings" on
  GitHub branch protection — admins included.
- **Stale CODEOWNERS file.** Reviewers listed who left the company. Audit quarterly.
- **Production deploys from forks.** Without explicit environment protection, a fork PR can run
  production-context workflows. Use GitHub Environments + restrict secrets to the production
  environment.
- **Approval by author.** "Required PR review" should disallow approving your own PR. Confirm in the
  rule.

## Cross-references

- Controls map: rows **3.1 – 3.5** in `../../controls-map.md`
- Platform guide for GitHub: `../../platforms/github.md`
- Questionnaire answers: questions 6-7 in `../../questionnaire-answer-examples.md`
