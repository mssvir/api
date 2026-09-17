# Authentication and Scopes

The public MSSV Machine API uses Bearer tokens on the dedicated API hostname.

Base URL: `https://api.mssv.ir/v1/`

Official website: [www.mssv.ir](https://www.mssv.ir)

## Authorization header

```http
Authorization: Bearer YOUR_MSSV_API_TOKEN
```

Machine API tokens are issued from [Machine API token management](https://my.mssv.ir/machine-users/) and are shown only once at creation time.

## Token controls

Every active Machine API token has:

- a name
- a set of scopes
- an optional expiration time
- a **mandatory** IPv4/IPv6 allowlist, including CIDR ranges

A token cannot be created or kept active without at least one allowed IP address or CIDR range.

The token owner can edit its name, scopes and IP/CIDR allowlist from [Machine API token management](https://my.mssv.ir/machine-users/). These changes are audited and the origin allowlist is reconciled automatically.

## Source IP enforcement

`api.mssv.ir` is served through MSSV's CDN. MSSV accepts real-client-IP information only through the trusted CDN path and passes the validated client address to the API application.

Requests are checked at multiple layers:

1. The origin Nginx configuration rejects source addresses that are not present in the aggregate allowlist of active Machine API tokens.
2. The Machine API performs an exact token-to-IP/CIDR check. An IP allowed for one token does not make it valid for another token.
3. Direct requests to the origin that do not arrive through a trusted CDN peer are rejected.

If the authenticated request IP is not allowed for that token, the API returns HTTP `403` with `api_ip_not_allowed`.

## Rate limiting

MSSV does not currently apply a requests-per-minute limiter to the public Machine API. Authentication, mandatory source-IP restrictions, scopes, ownership checks and idempotency controls still apply.

## Scope catalog

| Scope | Purpose |
|---|---|
| `account:read` | Read account identity and token metadata |
| `profile:read` | Read the owner profile |
| `profile:update` | Update owner profile fields |
| `products:read` | Read products, prices, zones and availability |
| `orders:write` | Create orders |
| `services:read` | List services, service details and history |
| `services:operate` | Start, stop, restart, reset, repair or change password where supported |
| `services:manage` | Change service product/zone, billing model, auto-renew, renewal and termination |
| `services:secrets` | Reveal supported service secrets |
| `invoices:read` | Read invoices and invoice items |
| `billing:manage` | Read/update billing preferences |
| `wallet:read` | Read wallet entries and payment history |
| `tickets:read` | Read support tickets |
| `tickets:write` | Create, reply to and close tickets |
| `team:read` | Read account team membership |
| `team:write` | Create/update/remove members and transfer ownership |
| `security:manage` | Manage owner sessions and password |
| `verification:manage` | Read and perform phone/email verification |

## Authentication-related errors

| HTTP | Error | Meaning |
|---:|---|---|
| 401 | `unauthorized` | Missing, malformed, expired, revoked or unknown token |
| 403 | `insufficient_scope` | Token lacks the endpoint's required scope |
| 403 | `api_ip_not_allowed` | Request IP is outside the token's mandatory allowlist |

## Security recommendations

Use the narrowest possible scope set. Prefer a single fixed server address or the smallest practical CIDR range for each integration. Never place a real token in GitHub, client-side JavaScript, screenshots, public logs or support messages.

Official MSSV website: [www.mssv.ir](https://www.mssv.ir)
