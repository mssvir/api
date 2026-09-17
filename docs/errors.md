# Errors, Rate Limits and Idempotency

Official website: https://mssv.ir/

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
| 403 | Valid token without permission, or IP allowlist rejection |
| 404 | Account-scoped resource not found |
| 409 | State conflict, such as idempotency-key reuse with a different request |
| 422 | Validation or business-rule failure |
| 429 | API rate limit exceeded |
| 503 | Temporary cutover/synchronization barrier during a controlled platform release |

Common error codes include `unauthorized`, `insufficient_scope`, `api_ip_not_allowed`, `api_rate_limited`, `not_found`, `idempotency_key_required`, `idempotency_key_too_long`, `idempotency_key_conflict`, `action_not_allowed`, `runtime_actions_disabled`, `ticket_closed`, `confirmation_required` and endpoint-specific validation codes.

## Rate limiting

Machine tokens have a configured requests-per-minute limit. The MSSV platform also has a global API limit. The effective limit is the lower of those two values.

A rejected request returns HTTP `429` and:

```json
{
  "ok": false,
  "error": "api_rate_limited"
}
```

Clients should use bounded retry with backoff. Do not retry validation, permission or authentication errors as if they were transient.

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
