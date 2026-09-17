# Authentication and Scopes

The public MSSV Machine API uses Bearer tokens.

Base URL: `https://mssv.ir/api/machine/v1`

## Authorization header

```http
Authorization: Bearer YOUR_MSSV_API_TOKEN
```

Machine API tokens are issued from the authenticated MSSV client area and are shown only once at creation time.

## Token controls

A token can include:

- a name
- a set of scopes
- an optional expiration time
- an optional IPv4/IPv6 allowlist, including CIDR ranges
- a per-token requests-per-minute limit

The effective rate limit is the lower of the platform-wide limit and the token-specific limit.

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
| 403 | `api_ip_not_allowed` | Request IP is outside the token allowlist |
| 429 | `api_rate_limited` | Effective API rate limit was exceeded |

## Security recommendations

Use the narrowest possible scope set and configure an IP/CIDR allowlist for fixed-server integrations. Never place a real token in GitHub, client-side JavaScript, screenshots, public logs or support messages.

Official MSSV website: https://mssv.ir/
