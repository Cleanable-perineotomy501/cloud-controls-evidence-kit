---
title: Branch protection
control: 3.1
owner: Engineering leads
cadence: Quarterly
---

# Branch protection

The rule that prevents direct pushes to the production branch and requires reviewed PRs. Evidence is
the **actual rule definition** plus the CODEOWNERS file that determines who reviews what.

## Evidence to keep here

1. **Rule export** — JSON of the branch protection or ruleset configuration on the production
   branch.
2. **CODEOWNERS file** — copy of the canonical CODEOWNERS at the time of review.
3. **Verification screenshot** — Settings → Rules / Branch Protection showing the rule is active and
   admins can't bypass.

## How to gather it

### GitHub — via API

```bash
# Branch protection (legacy)
gh api repos/<owner>/<repo>/branches/main/protection > branch-protection-main.json

# OR Rulesets (newer, more flexible)
gh api repos/<owner>/<repo>/rulesets > rulesets.json
gh api repos/<owner>/<repo>/rulesets/<ruleset-id> > ruleset-prod.json
```

Save the JSON. Also save CODEOWNERS:

```bash
cp <repo>/.github/CODEOWNERS codeowners-2026-05-15.txt
```

### GitLab

```bash
glab api projects/<id>/protected_branches > protected-branches.json
```

## Required rule contents

For SOC 2-friendly evidence, the rule should enforce:

- [ ] Pull request required for changes to `main` (no direct push)
- [ ] At least one review from a CODEOWNER (or designated team)
- [ ] Required status checks before merge (CI, tests, security scans)
- [ ] Cannot approve your own PR
- [ ] Linear history (optional but auditor-friendly)
- [ ] Required signed commits (optional, but a credibility booster)
- [ ] Cannot be bypassed by admins (GitHub: "Do not allow bypassing")

If any of these is off, document why with compensating control or accept it as a known gap and
target a fix date.

## Sample answer for the questionnaire

> The production branch `main` is protected by a GitHub Ruleset (see `ruleset-prod-<date>.json`)
> requiring:
>
> - PR review from a CODEOWNER
> - Passing CI status checks (lint, type-check, tests, security scans)
> - No bypass — admins included
>
> CODEOWNERS file is at `<repo>/.github/CODEOWNERS` and reviewed quarterly during the IAM review.

## Example filenames

```
branch-protection-main-2026-05-15.json
ruleset-prod-2026-05-15.json
codeowners-2026-05-15.txt
branch-protection-screenshot-2026-05-15.png
```

## Refresh

Quarterly. Re-export the rule; verify CODEOWNERS doesn't reference former employees.
