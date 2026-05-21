# Evidence folder template

Drop this directory structure into your private compliance/evidence repo and adapt it to your stack.
Each numbered folder maps to a control category in `../controls-map.md`.

## Structure

```
evidence-folder-template/
  01-access-controls/
  02-logging-monitoring/
  03-cicd-change-management/
  04-vulnerability-secrets/
  05-backups-recovery/
  06-ai-workload-controls/
```

Each category folder contains:

- A `README.md` explaining what evidence belongs there and who owns it.
- Three to four `*.md` templates for the most common evidence files.
- Supporting attachments (screenshots, exports, CSVs) when you add them.

## Naming conventions

When a customer security review starts pulling files, name them so the procurement team doesn't have
to ask follow-ups:

| Naming pattern               | Example                            | Why                            |
| ---------------------------- | ---------------------------------- | ------------------------------ |
| `<topic>-<date>.png`         | `mfa-policy-2026-Q2.png`           | Screenshot of a console policy |
| `<topic>-<period>.md`        | `quarterly-iam-review-2026Q1.md`   | Periodic review minutes        |
| `<topic>-config.{json,yaml}` | `cloudtrail-org-config.json`       | Configuration export           |
| `<topic>-inventory.csv`      | `service-account-keys-2026-05.csv` | Inventory export               |

Date format: ISO-8601 (`2026-05-21`) or quarter (`2026Q2`). Don't use American or European date
formats — they're ambiguous globally.

## Refresh cadence

Each evidence file template includes a recommended cadence at the top. The default rule of thumb:

| Type                                                       | Refresh                         |
| ---------------------------------------------------------- | ------------------------------- |
| Policy configuration (MFA, branch protection, retention)   | Quarterly                       |
| Operational logs / exports (deploy history, IAM inventory) | Quarterly                       |
| Restore tests, DR drills                                   | Quarterly                       |
| Vendor / third-party access reviews                        | Annually                        |
| Incident runbook, on-call rotation                         | Annually unless a real incident |

Set a recurring calendar event for the quarterly review. Walk the folder, refresh anything older
than 90 days, archive what's no longer relevant.

## Privacy

This template assumes the evidence folder lives in a **private** repository or storage. Some
evidence files (IAM exports, key inventories, deploy histories with author names) contain
identifiers. Don't make this folder public.

If you need to share evidence with a customer or auditor, copy the specific files into a shared
evidence portal (Vanta Trust Center, Drata Trust Page, Conveyor, OneTrust, or your own shared drive)
— don't grant access to the full evidence repo.

## What this folder is NOT

- A substitute for a SOC 2 audit. An auditor still needs to inspect controls and issue a report.
  This folder makes that inspection efficient; it doesn't replace it.
- A policy library. Policies (acceptable-use, security, privacy) live in your policy repo. See
  StrongDM's [Comply](https://github.com/strongdm/comply) for a markdown-based policy template
  framework.
- A compliance platform. Vanta / Drata / Secureframe pull evidence automatically from cloud APIs;
  this folder is the manual complement (and the source of truth when automation misses something).
