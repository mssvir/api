# Changelog

All notable public MSSV API documentation changes are recorded here.

The public API is served from `https://api.mssv.ir/v1/`.

## 2026-09-25 — 53-operation Production contract

- Promoted the accepted TeaSpeak/Radio extension surface into the single current Production OpenAPI contract, for **53 total operations**.
- Removed the obsolete alternate preview OpenAPI/documentation files after Production acceptance.
- Made `openapi/openapi.yaml`, the API reference, authentication/error documentation and current Production implementation one synchronized contract.
- Adopted a current-contract-only policy: approved API changes replace obsolete behavior; backward-compatibility aliases, legacy fields, fallback paths and duplicate deprecated endpoints are not retained unless explicitly approved.
- Added/updated contract CI so stale 41-operation/preview labeling fails validation.

## 2026-09-22 — D13 API preview documentation

- Added a separately labeled **candidate** OpenAPI contract for 12 D13 customer service operations, bringing the preview to 53 operations while leaving the 41-operation production specification unchanged.
- Documented TeaSpeak state/metrics, projected clients/channels, port state/change, customer whitelist CRUD, Radio allowed-IP change, Zone Radio endpoint discovery and capability discovery.
- Documented strict account ownership, source-IP/scopes, public field projection, exact pagination validation and pending idempotency reconciliation semantics.
- Kept admin, Agent, infrastructure, Radio upload and payment-gateway interfaces outside the public Machine API.
- This entry records documentation of a source-reviewed candidate only; it does **not** claim the preview endpoints are deployed.

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
