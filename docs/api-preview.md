# MSSV Machine API v1 — D13 preview contract

This page documents the **candidate** D13 customer API extension. These 12 operations are source-reviewed and tested in the private MSSV release candidate, but they are **not production endpoints yet**. The current live contract remains the 41-operation API documented in [API reference](api-reference.md) and [openapi/openapi.yaml](../openapi/openapi.yaml).

Candidate OpenAPI: [openapi/openapi-preview.yaml](../openapi/openapi-preview.yaml)

## Coverage

The preview raises the public operation count from **41 to 53** without adding admin/control-plane interfaces. It reuses existing Machine API authentication, source-IP policy, account ownership and scopes.

| Method | Path | Scope |
|---|---|---|
| GET | `/capabilities` | `account:read` |
| GET | `/services/{id}/teaspeak` | `services:read` |
| GET | `/services/{id}/teaspeak/clients` | `services:read` |
| GET | `/services/{id}/teaspeak/channels` | `services:read` |
| GET | `/services/{id}/teaspeak/ports` | `services:read` |
| POST | `/services/{id}/teaspeak/ports` | `services:manage` |
| GET | `/services/{id}/teaspeak/whitelist` | `services:read` |
| POST | `/services/{id}/teaspeak/whitelist` | `services:manage` |
| POST | `/services/{id}/teaspeak/whitelist/{rule_id}` | `services:manage` |
| DELETE | `/services/{id}/teaspeak/whitelist/{rule_id}` | `services:manage` |
| POST | `/services/{id}/radio/ip` | `services:manage` |
| GET | `/services/{id}/radio/endpoints` | `services:read` |

## Contract rules

All service operations load the resource by both service id and token account id before calling the domain layer. A resource from another account is therefore returned as `404 not_found`, not as a cross-account disclosure.

TeaSpeak observability is projected through explicit public allowlists. Node records, Agent payloads, credentials, raw exception text, client IP addresses and unique client identifiers are excluded. A running server with a failed/incomplete live query returns `503` rather than an empty-success response.

Port changes accept only integer `voice_port` and `query_port`; file port remains read-only. The request is delegated to the existing guarded reservation/preflight/queue flow with admin override disabled. Pagination accepts only `per_page=25|50|100`; malformed numeric strings such as `50x` are rejected.

Whitelist rules are IPv4-only and use `voice` or `query` port kinds. `limit_mode=limited` requires `max_connections`; `limit_mode=unlimited` forbids a numeric ceiling. GET responses are explicitly projected so future private domain fields cannot leak into the public contract. DELETE is strictly zero-length: an empty body does not require a `Content-Type` header, but non-zero `Content-Length`, `Transfer-Encoding`, or a parsed body is rejected.

Radio IP changes accept only public IPv4 addresses; private and reserved ranges are rejected. Active Radio changes are remote-first, so an uncertain timeout, local commit failure, or concurrent local change returns `503 radio_ip_reconciliation_required` with `reconciliation_required=true` instead of inviting an unsafe retry. Radio endpoint lists also reject credential-bearing URLs such as `https://user:pass@host/...` even when the URL is otherwise syntactically valid.

Authentication failures are distinguished from authentication-backend failures: a missing or invalid token remains `401 unauthorized`, an IP allowlist rejection remains `403 api_ip_not_allowed`, and an unexpected authentication subsystem failure is redacted as `503 authentication_unavailable`.

All preview writes require `Idempotency-Key`. A key is claimed before the domain side effect. JSON object keys are canonicalized before request hashing, so reordering object properties does not create a false idempotency conflict. Completed responses are replayed for 24 hours. A claim that remains pending after an uncertain outcome is **not** automatically expired or re-executed; operators reconcile it and clients must keep the same key instead of forcing a second side effect.

## Rollout state

Promotion requires a fresh Main source/DB contract capture, MariaDB concurrency/crash tests for the claim table, a bounded release candidate diff, additive migration, cutover/rollback assets, authenticated ownership smoke tests and independent post-deploy verification. Until those gates pass, integrations must not assume the preview operations exist on `api.mssv.ir`.
