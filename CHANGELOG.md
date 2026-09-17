# Changelog

All notable public MSSV API documentation changes are recorded here.

The public API is served from `https://api.mssv.ir/v1/`.

## 2026-09-18 — Zone-only service placement contract

- Made `zone_id` the only public placement input for order creation.
- Removed the obsolete `deployment_target_id` request field from `POST /orders`.
- MSSV now resolves a compatible Node internally from the selected Zone, service type and current availability.
- No compatibility field or legacy placement alias is retained.
- Updated the OpenAPI document version to `1.2.0`; the public API namespace remains `https://api.mssv.ir/v1/`.

## 2026-09-17 — Canonical `/v1/` base path and client return flow

- Set the canonical public Machine API base URL to `https://api.mssv.ir/v1/`.
- Removed the unused `/api/machine/v1` compatibility redirect before any Machine API user existed; `/v1/` is the only supported public Machine API namespace.
- Standardized official website links to [www.mssv.ir](https://www.mssv.ir).
- Linked account-management references to the [MSSV client area](https://my.mssv.ir/) and API-token references to [Machine API token management](https://my.mssv.ir/machine-users/).
- Added safe server-side intended-destination handling so customers return to the originally requested internal page after successful login and any required verification.
- Updated the OpenAPI contract to remove the obsolete `429 RateLimited` response from the current no-RPM-limit Machine API policy.

## 2026-09-17 — Dedicated API edge and mandatory source-IP policy

- Initially staged the dedicated API edge while the public namespace was being finalized; the final production namespace is `https://api.mssv.ir/v1/`.
- Made the IPv4/IPv6/CIDR allowlist mandatory for every active Machine API token.
- Added customer self-service editing for token name, scopes and source-IP allowlist.
- Documented trusted CDN real-client-IP handling and fail-closed origin access.
- Added aggregate Nginx IP filtering in front of the exact token-to-IP application check.
- Removed the MSSV Machine API requests-per-minute limiter from the public API contract.
- Retained permission scopes, ownership checks and idempotency controls.

## 2026-09-17 — Initial documentation release

- Published the first official English MSSV API documentation.
- Added the OpenAPI 3.1 specification for the public Machine API v1.
- Documented Bearer authentication and permission scopes.
- Documented pagination, common errors and idempotency behavior.
- Documented public endpoints for account, profile, products, orders, services, invoices, billing, wallet, payments, security sessions, verification, tickets and team management.
- Added cURL examples and links to the official MSSV website at [www.mssv.ir](https://www.mssv.ir).

Future public API changes should update this file, `openapi/openapi.yaml`, and the relevant Markdown reference in the same release cycle.
