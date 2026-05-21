# Audit log configuration

Evidence that audit logging is enabled across **every** production account / project / subscription
— not just one. The most common auditor finding in this category is "logs missing from one of N
accounts."

## Evidence to keep here

1. **Config export** — the actual trail / sink configuration, in JSON or Terraform.
2. **Coverage proof** — a list of all production accounts / projects showing each is covered by the
   trail.
3. **Sample query result** — a query against the actual log storage returning records from each
   production account, proving logs are actually flowing (not just configured).

## How to gather it

### AWS

Org-level CloudTrail is the right answer:

```bash
# Confirm org-level trail exists and is logging
aws cloudtrail describe-trails --query 'trailList[?IsOrganizationTrail]'
aws cloudtrail get-trail-status --name <trail-name>

# Confirm every member account is covered
aws organizations list-accounts --query 'Accounts[?Status==`ACTIVE`].[Id,Name]' --output table
```

Save the JSON output of `describe-trails` as `cloudtrail-org-config.json`.

### GCP

Org-level audit log sink to BigQuery or GCS:

```bash
gcloud logging sinks list --organization=<org-id>
gcloud logging sinks describe <sink-name> --organization=<org-id>

# Verify Data Access logs are enabled where needed
gcloud projects get-iam-policy <project> --flatten='auditConfigs[]'
```

Save the sink config and IAM-policy audit-config as `gcp-audit-log-sink-config.json` and
`gcp-data-access-logs.json`.

### Azure

Diagnostic Settings or Activity Log Profile at the subscription / management-group level:

```bash
az monitor diagnostic-settings subscription list \
  --subscription <subscription-id>
```

Save the output as `azure-activity-log-config.json`.

## Sample query proof

For each cloud, run a query that returns recent log events from each production account/project, and
save a screenshot or CSV. This proves logs are flowing, not just that the config exists.

Example (CloudTrail / Athena):

```sql
SELECT accountid, COUNT(*) AS events, MIN(eventtime), MAX(eventtime)
FROM cloudtrail_logs
WHERE eventtime > date_add('day', -7, current_timestamp)
GROUP BY accountid;
```

Save the result as `cloudtrail-coverage-2026-MM-DD.csv`.

## Example filenames

```
cloudtrail-org-config-2026-05-15.json
gcp-audit-log-sink-config-2026-05-15.json
azure-activity-log-config-2026-05-15.json
log-coverage-by-account-2026-05-15.csv
```

## Refresh

Quarterly. Re-export the configs and re-run the coverage query. Drift (a new account that didn't get
added to the trail) is the common failure mode.
