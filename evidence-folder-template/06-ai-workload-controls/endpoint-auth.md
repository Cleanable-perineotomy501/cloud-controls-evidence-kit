# Model endpoint authentication

Evidence that every inference endpoint requires authentication, that unauthenticated requests are
denied, and that the auth mechanism ties requests back to a tenant for downstream controls (logging,
spend caps, abuse handling).

## Evidence to keep here

1. **Endpoint inventory** — every model-serving URL / route.
2. **Auth configuration** — gateway / API key / OAuth / IAM config per endpoint.
3. **Denial proof** — sample request without auth showing a 401/403.
4. **Tenant attribution** — how each authenticated request maps to a tenant ID.

## How to gather it

### If using a cloud provider's hosted model (OpenAI, Vertex AI, Bedrock)

The customer-facing endpoint is _your_ application API, not the provider's API. The application API
does the auth; the provider's API gets called server-side with your single org key.

Evidence:

```bash
# Sample denied request to your inference route
curl -i https://api.example.com/v1/generate -d '{"prompt": "test"}'
# → 401 Unauthorized
```

Plus the auth middleware code or config:

```
# In your API gateway or framework, the auth requirement
# Save as middleware-config.md
```

### If self-hosting (Triton, vLLM, Ollama)

The model server must NOT be publicly reachable. Run it behind:

- Private VPC subnet only
- An API gateway that authenticates the caller
- IP allowlist for known traffic origins

Evidence: the network configuration (security group / firewall rules) + the auth gateway config.

### Tenant attribution

Each authenticated request should carry a tenant identifier into logging and spend tracking. Example
log line shape:

```json
{
  "ts": "2026-05-15T14:00:00Z",
  "tenant_id": "tenant_abc123",
  "user_id": "user_456",
  "endpoint": "/v1/generate",
  "model": "gpt-4o-mini",
  "input_tokens": 1234,
  "output_tokens": 567,
  "request_id": "req_xyz"
}
```

The `tenant_id` is what later controls (spend caps, abuse runbook, data retention) key off.

## Sample answer for the questionnaire

> All model inference endpoints require authentication via {{API key / OAuth / IAM}} at our
> application gateway. Unauthenticated requests receive HTTP 401. Each authenticated request is
> tagged with a tenant identifier that propagates into logging and quota systems. The provider model
> endpoints (OpenAI / Vertex AI / Bedrock) are called server-side using infrastructure credentials
> only — they are never exposed to end users.

## Example filenames

```
endpoint-inventory-2026-05-15.md
gateway-auth-config-2026-05-15.json
denied-request-sample-2026-05-15.txt
```

## Refresh

Quarterly. New endpoints get added; old ones can drift. Audit the inventory.
