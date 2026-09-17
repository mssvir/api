# MSSV Machine API v1 Reference

Official website: [www.mssv.ir](https://www.mssv.ir)

Base URL: `https://api.mssv.ir/v1/`

All endpoints below use Bearer authentication unless explicitly documented otherwise. Every active token requires at least one allowed source IP/CIDR. The public Machine API is account-scoped: customer resources are checked against the account associated with the token.

## Account

| Method | Path | Scope | Idempotency |
|---|---|---|---|
| GET | `/me` | `account:read` | No |

`GET /me` returns account information plus token metadata such as token id, name and scopes.

## Profile

| Method | Path | Scope | Idempotency |
|---|---|---|---|
| GET | `/profile` | `profile:read` | No |
| POST | `/profile` | `profile:update` | Required |

Profile update accepts owner identity/contact fields including `first_name`, `last_name`, `phone`, `email`, `city`, `state` and `postcode`. Changing phone or email can make verification necessary again.

## Products and orders

| Method | Path | Scope | Idempotency |
|---|---|---|---|
| GET | `/products` | `products:read` | No |
| POST | `/orders` | `orders:write` | Required |

Product results include available billing models, prices, zones and availability information. Order creation supports product, billing cycle, zone/deployment selection and supported product-specific fields.

Typical order fields:

```json
{
  "product_id": 1,
  "cycle": "monthly",
  "zone_id": 1,
  "billing_model": "monthly"
}
```

## Services

| Method | Path | Scope | Idempotency |
|---|---|---|---|
| GET | `/services` | `services:read` | No |
| GET | `/services/{id}` | `services:read` | No |
| GET | `/services/{id}/history` | `services:read` | No |
| POST | `/services/{id}/action` | `services:operate` | Required |
| GET | `/services/{id}/secret` | `services:secrets` | No |
| POST | `/services/{id}/change/preview` | `services:manage` | No |
| POST | `/services/{id}/change` | `services:manage` | Required |
| POST | `/services/{id}/billing-model/preview` | `services:manage` | No |
| POST | `/services/{id}/billing-model` | `services:manage` | Required |
| POST | `/services/{id}/auto-renew` | `services:manage` | Required |
| POST | `/services/{id}/renew` | `services:manage` | Required |
| POST | `/services/{id}/terminate-payg` | `services:manage` | Required |

`GET /services` supports `page`, `per_page` and optional `status` filtering.

`GET /services/{id}` returns service information, masked custom fields, recent usage samples, automation policy and `available_actions`.

Runtime actions depend on service component and current runtime state. Supported actions can include `start`, `stop`, `restart`, `reset`, `repair` and `change_password`. Always use the returned `available_actions` instead of assuming an action is available.

Example action request:

```json
{
  "action": "restart"
}
```

Reset operations require the endpoint-specific confirmation string returned/documented by the service workflow. Termination also requires explicit confirmation.

To reveal a supported service secret:

```text
GET /services/{id}/secret?key=SECRET_KEY_NAME
```

Only request known keys required by your integration. Secret responses should never be logged.

## Invoices and billing

| Method | Path | Scope | Idempotency |
|---|---|---|---|
| GET | `/invoices` | `invoices:read` | No |
| GET | `/invoices/{id}` | `invoices:read` | No |
| GET | `/billing/preference` | `billing:manage` | No |
| POST | `/billing/preference` | `billing:manage` | Required |

Invoice detail includes invoice items. Billing preference currently exposes the official-invoice preference and related state.

## Wallet and payments

| Method | Path | Scope | Idempotency |
|---|---|---|---|
| GET | `/wallet` | `wallet:read` | No |
| GET | `/payments` | `wallet:read` | No |
| GET | `/payments/{id}` | `wallet:read` | No |

Machine API tokens cannot perform wallet top-ups, invoice payment gateway operations or token administration. Those capabilities require an authenticated browser session in the [MSSV client area](https://my.mssv.ir/).

## Security sessions and password

| Method | Path | Scope | Idempotency |
|---|---|---|---|
| GET | `/security/sessions` | `security:manage` | No |
| POST | `/security/sessions/{hash}/revoke` | `security:manage` | Required |
| POST | `/security/sessions/revoke-all` | `security:manage` | Required |
| POST | `/security/password` | `security:manage` | Required |

Password change requires `current_password`, `new_password` and `confirm_password`. The current implementation requires a new password of at least 10 characters and revokes existing user sessions after a successful change.

## Verification

| Method | Path | Scope | Idempotency |
|---|---|---|---|
| GET | `/verification` | `verification:manage` | No |
| POST | `/verification/send` | `verification:manage` | No |
| POST | `/verification/verify` | `verification:manage` | No |

Verification supports `sms` and `email` channels. Sending a verification returns challenge information; verification consumes a `challenge_id` and verification `code`.

Example send body:

```json
{
  "channel": "email"
}
```

Example verify body:

```json
{
  "channel": "email",
  "challenge_id": "CHALLENGE_ID",
  "code": "VERIFICATION_CODE"
}
```

## Support tickets

| Method | Path | Scope | Idempotency |
|---|---|---|---|
| GET | `/tickets` | `tickets:read` | No |
| GET | `/tickets/{id}` | `tickets:read` | No |
| POST | `/tickets` | `tickets:write` | Required |
| POST | `/tickets/{id}/reply` | `tickets:write` | Required |
| POST | `/tickets/{id}/close` | `tickets:write` | Required |

Ticket creation requires a non-empty `title` and `message`. `service_id` is optional but, when present, must belong to the token's account.

Example:

```json
{
  "title": "Service question",
  "message": "Please review my service.",
  "service_id": 123,
  "priority": "Medium"
}
```

## Team management

| Method | Path | Scope | Idempotency |
|---|---|---|---|
| GET | `/team` | `team:read` | No |
| POST | `/team` | `team:write` | Required |
| POST | `/team/{id}` | `team:write` | Required |
| POST | `/team/{id}/remove` | `team:write` | Required |
| POST | `/team/{id}/transfer-owner` | `team:write` | Required |

`GET /team` returns the account permission catalog and member list.

Creating a new member accepts `email` and `permissions`. If the email is not already an MSSV user, `first_name`, `last_name` and a password of at least 10 characters are also required by the current implementation.

The account owner cannot be removed through the member-removal endpoint. Ownership transfer requires an explicit confirmation value tied to the target user id.

## Source-IP requirement

The authenticated request must arrive from an IP address or CIDR configured on the same Machine API token. The customer can edit this allowlist from [Machine API token management](https://my.mssv.ir/machine-users/). The origin Nginx layer maintains an aggregate allowlist, while the application repeats the exact token-to-IP check.

MSSV does not currently apply a requests-per-minute limiter to the public Machine API.

## Response conventions

Success responses generally contain:

```json
{
  "ok": true
}
```

Failure responses generally contain:

```json
{
  "ok": false,
  "error": "error_code"
}
```

For exact machine-readable operation metadata, see [openapi/openapi.yaml](../openapi/openapi.yaml).
