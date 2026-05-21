# Backup configuration

The configured backup schedule and retention for production data stores. Evidence is the actual
managed-service configuration plus a recent record of successful backups.

## Evidence to keep here

1. **Configuration export** — for each production data store (databases, object storage, message
   queues with state).
2. **Last 7 days of backup success records** — proves it's actually running, not just configured.
3. **Retention policy** — how long backups are kept and where they live.

## How to gather it

### AWS — RDS

```bash
aws rds describe-db-instances --db-instance-identifier <id> \
  --query 'DBInstances[0].{BackupRetentionPeriod:BackupRetentionPeriod, PreferredBackupWindow:PreferredBackupWindow, AutomatedBackupsArn:AwsBackupRecoveryPointArn}'

aws rds describe-db-snapshots --db-instance-identifier <id> \
  --snapshot-type automated --max-records 10
```

Save as `rds-backup-config-<id>-2026-05-15.json`.

### GCP — Cloud SQL

```bash
gcloud sql instances describe <instance> \
  --format='value(settings.backupConfiguration)'

# Backup history
gcloud sql backups list --instance=<instance> --limit=10
```

### Azure — SQL / Database

```bash
az sql db show --resource-group <rg> --server <server> --name <db> \
  --query 'currentBackupStorageRedundancy'

az sql db ltr-backup list --resource-group <rg> --server <server> --database <db>
```

## Retention guidance

For SOC 2-friendly evidence, typical answers:

- **Operational backups**: 7-35 days of point-in-time recovery (managed by your cloud database).
- **Long-term retention**: monthly or quarterly snapshots retained for 1+ year, stored in a separate
  region or account.
- **Encryption**: at rest (default for managed services) + at least cross-account/cross-project
  access controls so a compromised primary account doesn't lose backups too.

## Sample answer for the questionnaire

> Production databases are backed up automatically by {{RDS / Cloud SQL / Azure SQL}}. Point-in-time
> recovery is enabled with **{{N}}-day** retention; long-term snapshots are retained for **{{N}}
> year**. Backups are encrypted at rest and stored {{in a separate region / account}}. Backup
> success is monitored daily; restore is tested quarterly (see `restore-test.md` for the most recent
> test result).

## Example filenames

```
rds-backup-config-prod-db-2026-05-15.json
cloudsql-backup-history-2026-05-15.json
backup-success-log-2026-05-15.csv
```

## Refresh

Configuration: annually unless changed. Success log: quarterly spot-check.
