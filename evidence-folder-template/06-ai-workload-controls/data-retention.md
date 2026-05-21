---
title: AI data retention and tenant isolation
control: 6.4 + 6.5
owner: AI / Legal
cadence: Annually (policy), Quarterly (review)
---

# AI data retention and tenant isolation

Evidence that retrieval indices, embeddings, prompt logs, and other AI-derived data are partitioned
per-tenant and retained per customer-DPA terms.

## Evidence to keep here

1. **Tenant-isolation policy** — how each tenant's data is partitioned in the AI stack.
2. **Retention policy** — what's kept, for how long, and where.
3. **DPA alignment doc** — mapping of contract terms to actual retention windows.

## Tenant-isolation policy

For each AI-derived data store, document:

### Vector / retrieval index

| Tenant model                 | How                                                                                                                                                          |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Index-per-tenant**         | Separate Pinecone index / Weaviate class / pgvector schema per tenant. Strongest isolation.                                                                  |
| **Namespace-per-tenant**     | Single index with tenant-scoped namespaces / metadata filters. Simpler ops; weaker isolation; the query must enforce the namespace at the application layer. |
| **Multi-tenant with filter** | Single index, filter at query. Lowest isolation; requires bulletproof filter logic + regular testing.                                                        |

Evidence: the actual configuration plus a test that proves cross-tenant queries return zero results.

### Embedding cache

If you cache embeddings to reduce model spend, keep per-tenant caches. A cross-tenant cache can leak
content shape.

### Conversation / session history

Stored where? With what tenant scoping? Retention window?

## Retention policy

| Data                              | Retention                              | Justification                 |
| --------------------------------- | -------------------------------------- | ----------------------------- |
| Inference request logs (metadata) | 365 days                               | Operational + audit           |
| Sampled inference content         | 30 days                                | Quality + safety review       |
| Flagged-for-review content        | 90 days                                | Active incident investigation |
| Vector / RAG index                | Indefinite (tied to product use)       | Functional                    |
| Conversation history              | Per customer setting (default 30 days) | Product UX + privacy          |

## DPA alignment

For each enterprise customer with a custom DPA, file a one-page crosswalk:

```markdown
# DPA crosswalk — {{Customer name}} — signed 2026-MM-DD

| DPA term                     | Our default      | Honored by                                                 |
| ---------------------------- | ---------------- | ---------------------------------------------------------- |
| Logs retention ≤ 30 days     | 365 days default | Shortened to 30d for this tenant via per-tenant log policy |
| No training on customer data | Same             | We don't train on customer data org-wide                   |
| Data residency: EU           | Multi-region     | Routed to EU-only region for this tenant                   |
| Right to delete: 7-day SLA   | N/A default      | Manual process documented in `delete-runbook.md`           |
```

## How to gather the evidence

- Vector store config: export from Pinecone / Weaviate / pgvector showing the partitioning model.
- Negative test: pick two tenants A and B; query A's namespace with B's tenant ID and verify zero
  results.
- DPA crosswalks: one per enterprise customer with custom terms.

## Sample answer for the questionnaire

> Per-tenant data in our AI stack is partitioned via {{model:
> namespace / separate index / filter}}. Inference request metadata is retained 365 days; sampled
> content is retained 30 days; flagged-for-review content is retained 90 days with
> security-team-only access. We do **not** use customer data to train or fine-tune base models.
> Retention is aligned to customer DPA terms — stricter customer terms override our defaults. See
> `data-retention.md`.

## Example filenames

```
vector-store-tenant-isolation-2026-05-15.md
ai-retention-policy-2026.md
dpa-crosswalk-customer-acme-2026-03-15.md
```

## Refresh

Policy: annually. Tenant-isolation test: quarterly. DPA crosswalks: per signed contract.
