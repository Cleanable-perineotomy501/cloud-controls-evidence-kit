# MFA enforcement

The single most-requested control on customer security questionnaires. Buyers want to see that
**every** human with admin access uses MFA, and that the enforcement is policy-level (not opt-in).

## Evidence to keep here

1. **IdP MFA policy export** — the actual rule, not a screenshot of the policy-editor UI. JSON,
   YAML, or PDF depending on what your IdP exports.
2. **Per-user enforcement screenshot or report** — showing all admin users with an MFA status of
   "Enabled" (or equivalent). Include the date in the filename.
3. **Exception register** — if any accounts skip MFA (break-glass / service / legacy), list them
   with justification and compensating controls. Don't omit them; auditors find them.

## How to gather it

- **Okta**: Security → Authentication → Authentication Policies → export the policy. Also Reports →
  Users → MFA Usage.
- **Google Workspace**: Admin → Security → 2-Step Verification → Enforcement settings + Reports →
  Audit → Login (filter for `is_2sv = false` to find any holes).
- **Microsoft Entra (Azure AD)**: Entra ID → Security → Conditional Access → the policy enforcing
  MFA on the relevant role/group. Reports → Sign-in logs filtered by MFA result.

## Common buyer questions answered

> "Are all administrators MFA-enabled?"

Yes — see attached `mfa-policy-<date>.{json,pdf}` and `mfa-status-<date>.csv`.

> "Are there exceptions?"

{{Pick one:}}

- No exceptions.
- {{N}} exceptions: {{break-glass account / monitoring service}}. Justification in
  `mfa-exceptions-<date>.md`. Compensating controls:
  {{credential vault, hardware token, IP allowlist, etc.}}.

## Example filenames

```
mfa-policy-2026-05-15.json
mfa-status-export-2026-05-15.csv
mfa-exceptions-2026-05-15.md       # only if applicable
```

## Refresh

Quarterly. Set a recurring calendar event. The check takes <10 minutes once you've done it once.
