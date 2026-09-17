# Getting Started with the MSSV API

The MSSV Machine API provides programmatic access to customer-account and hosted-service operations.

Official website: https://mssv.ir/

Base URL:

```text
https://api.mssv.ir/api/machine/v1
```

## 1. Create a Machine API token

Sign in to your MSSV client account and create a Machine API token from the API token management page. Token creation requires a current-password check. The token is displayed once, so store it securely.

When creating the token, select only the scopes your integration needs and configure at least one allowed IPv4/IPv6 address or CIDR range. The IP allowlist is mandatory.

You can later edit the token name, scopes and allowed IP/CIDR list from the authenticated MSSV client area without rotating the token. Allowlist reconciliation at the API origin is automatic.

## 2. Authenticate from an allowed source address

Send the token using the standard Bearer scheme:

```http
Authorization: Bearer YOUR_MSSV_API_TOKEN
Accept: application/json
```

Example:

```bash
curl -sS https://api.mssv.ir/api/machine/v1/me \
  -H 'Authorization: Bearer YOUR_MSSV_API_TOKEN' \
  -H 'Accept: application/json'
```

Requests sent from an IP address that is not allowed for that token are rejected.

## 3. Understand scopes

Every Machine API token has a scope list. An endpoint returns HTTP `403` with `insufficient_scope` when the token is valid but does not have the required permission.

See [Authentication and scopes](authentication.md) for the current scope catalog and IP enforcement model.

## 4. Use idempotency for supported writes

Most state-changing Machine API operations use an `Idempotency-Key` header. Reusing the same key with the same request returns the stored response; reusing it with a different request returns a conflict.

```bash
curl -sS -X POST https://api.mssv.ir/api/machine/v1/services/123/action \
  -H 'Authorization: Bearer YOUR_MSSV_API_TOKEN' \
  -H 'Content-Type: application/json' \
  -H 'Idempotency-Key: service-123-restart-001' \
  --data '{"action":"restart"}'
```

## 5. Pagination

Collection endpoints use `page` and `per_page`. The API commonly supports page sizes of `25`, `50`, or `100`, depending on the endpoint.

Typical paginated response fields are:

```json
{
  "ok": true,
  "data": [],
  "page": 1,
  "per_page": 50,
  "total": 0,
  "pages": 1
}
```

## 6. Handle errors

Errors are JSON responses, commonly in this form:

```json
{
  "ok": false,
  "error": "insufficient_scope"
}
```

Read [Errors and idempotency](errors.md) before building retries.

## Machine API rate limiting

MSSV does not currently apply a requests-per-minute limiter to the public Machine API. Mandatory token/IP restrictions, scopes, ownership checks and idempotency controls still apply.

## Next steps

- [API reference](api-reference.md)
- [OpenAPI specification](../openapi/openapi.yaml)
- [cURL examples](../examples/curl.md)

For current MSSV product and service information, visit https://mssv.ir/.
