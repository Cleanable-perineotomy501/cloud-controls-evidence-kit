# 06 · AI workload controls

The AI-specific controls customer procurement teams have started asking about: who can call the
model endpoints, what gets logged, how data is partitioned, how spend is capped, and what happens
when something goes wrong.

If your product doesn't ship AI features, skip this folder.

## What goes here

- Endpoint authentication evidence
- Prompt / tool-call logging policy
- Per-tenant spend caps + abuse runbook
- Data retention + tenant isolation for retrieval / embeddings

## Owner

**AI team** (or platform team where AI work lives) for technical controls; **Security** for policy;
**Legal** for retention and DPA alignment.

## Common gotchas

- **Public model endpoints with no auth.** Even in pre-launch, a public inference URL without
  rate-limiting or auth is a spend and abuse risk. Auditors increasingly catch this.
- **Logging that captures full prompts and outputs without a retention policy.** Inference content
  can contain customer data — keep it, but bound it.
- **Tenant data crossing in RAG.** Multi-tenant vector indices without namespace partitioning leak
  embeddings across tenants. Either index per-tenant or filter at query time and prove the filter is
  enforced.
- **No spend cap on the underlying model.** A misbehaving feature or compromised key can burn
  through monthly budget overnight. Per-tenant quota at the gateway is the answer.
- **No abuse playbook.** What do you do when a prompt-injection attack happens? When a customer
  reports their data appeared in another customer's response? Write the playbook before you need it.

## Mapping to standards

NIST AI RMF and ISO/IEC 42001 are the emerging references for AI governance. The controls here map
roughly to NIST AI RMF **MANAGE** and **MEASURE** functions.

## Cross-references

- Controls map: rows **6.1 – 6.6** in `../../controls-map.md`
- Questionnaire answer: question 12 in `../../questionnaire-answer-examples.md`
