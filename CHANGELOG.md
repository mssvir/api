# Changelog

All notable public MSSV API documentation changes are recorded here.

The public API is served from `https://api.mssv.ir/api/machine/v1`.

## 2026-09-17 — Dedicated API edge and mandatory source-IP policy

- Moved the documented production Machine API base URL to `https://api.mssv.ir/api/machine/v1`.
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
- Added cURL examples and links to the official MSSV website at `https://mssv.ir/`.

Future public API changes should update this file, `openapi/openapi.yaml`, and the relevant Markdown reference in the same release cycle.
