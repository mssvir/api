# Errors and Idempotency

Official website: [www.mssv.ir](https://www.mssv.ir)

API base URL: `https://api.mssv.ir/v1/`

## Error format

Machine API errors are JSON and normally use this shape:

```json
{
  "ok": false,
  "error": "error_code"
}
```

Common status codes include:

| HTTP | Typical meaning |
|---:|---|
| 400 | Invalid request shape in endpoint-specific cases |
| 401 | Missing or invalid authentication |
| 403 | Valid token without permission, source IP rejected, or edge access rejected |
| 404 | Account-scoped resource not found |
| 409 | State conflict, such as idempotency-key reuse with a different request |
| 422 | Validation or business-rule failure |
| 503 | Temporary cutover/synchronization barrier during a controlled platform release |

Common error codes include `unauthorized`, `insufficient_scope`, `api_ip_not_allowed`, `not_found`, `idempotency_key_required`, `idempotency_key_too_long`, `idempotency_key_conflict`, `action_not_allowed`, `runtime_actions_disabled`, `ticket_closed`, `confirmation_required` and endpoint-specific validation codes.

## Source-IP rejection

Every active Machine API token requires at least one IPv4/IPv6 address or CIDR range. Requests from an address outside the token's configured allowlist are rejected.

The API hostname also uses an aggregate Nginx allowlist at the origin. The application repeats the check against the specific token, so an address associated with one API token cannot authenticate another token unless it is explicitly configured there as well.

## Rate limiting

MSSV does not currently apply a requests-per-minute limiter to the public Machine API. Clients should nevertheless avoid accidental request floods and should use idempotency for supported write operations.

## Idempotency

Most state-changing Machine API operations require:

```http
Idempotency-Key: UNIQUE_KEY_FOR_THIS_OPERATION
```

The current implementation stores the request hash and response for 24 hours.

If the same key is repeated with the same request, the stored response is returned. If the same key is reused with a different method/path/body combination, the API returns HTTP `409` with `idempotency_key_conflict`.

Keys are limited to 190 characters.

Recommended key style:

```text
my-system:service-123:restart:20260917T120000Z
```

Never use secrets or personal data inside an idempotency key.

## Temporary release barriers

During a controlled MSSV cutover, state-changing requests can temporarily return `503` with an error such as `cutover_syncing` or `cutover_rollback` and may include `Retry-After`. Clients should respect the retry interval and retry safely with the same idempotency key where the operation supports idempotency.
