# Audit log retention policy

Buyers and auditors want logs that cover the full review period (typically 12 months) AND are
tamper-resistant. The evidence is the storage-lifecycle config + a query showing the oldest
available record.

## Evidence to keep here

1. **Lifecycle policy** — the rule on the storage bucket that retains audit logs for ≥ 365 days.
2. **Object-lock / immutability config** — proof that logs can't be tampered with even by an admin.
3. **Oldest-record query** — a query against the actual log storage showing the earliest available
   record date. This is the most-compelling evidence because it proves you didn't just set the
   policy yesterday.

## How to gather it

### AWS — S3 Object Lock + lifecycle

```bash
# Confirm object lock is enabled
aws s3api get-object-lock-configuration --bucket <log-bucket>

# Confirm lifecycle keeps records for retention period
aws s3api get-bucket-lifecycle-configuration --bucket <log-bucket>

# Oldest object in the bucket
aws s3api list-objects-v2 --bucket <log-bucket> \
  --query 'sort_by(Contents, &LastModified)[0]'
```

### GCP — GCS Bucket Lock + retention

```bash
# Bucket retention policy + locked status
gsutil retention get gs://<log-bucket>

# Oldest object
gsutil ls -l gs://<log-bucket>/** | sort -k 2 | head -1
```

### Azure — Storage immutability + retention

```bash
az storage account blob-service-properties show \
  --account-name <storage-account>
```

## Sample answer for the questionnaire

> Production cloud audit logs are retained for **{{N}} days** in {{S3 / GCS / Storage Account}} with
> **object-lock / bucket-lock applied** to prevent tampering even by storage admins. The oldest
> available record dates from **{{date from your query}}**, covering the full review period.

## Example filenames

```
s3-object-lock-config-2026-05-15.json
s3-lifecycle-policy-2026-05-15.json
oldest-record-query-2026-05-15.txt
```

## Refresh

- Lifecycle / object-lock config: annually, or any time you change it.
- Oldest-record query: quarterly (it should keep moving forward as old logs age out per the
  lifecycle).
