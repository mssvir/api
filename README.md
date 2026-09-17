# MSSV API — Official Developer Documentation

[![Website](https://img.shields.io/badge/Website-www.mssv.ir-0b7285)](https://www.mssv.ir/)
[![API](https://img.shields.io/badge/API-api.mssv.ir%2Fv1-2f9e44)](https://api.mssv.ir/v1/)
[![License](https://img.shields.io/badge/License-Apache--2.0-blue)](LICENSE)

Official English documentation and OpenAPI specification for the **MSSV API**.

MSSV provides hosting and service-management automation through a versioned Machine API. The API can be used to integrate customer systems with MSSV for account information, products, orders, hosted services, billing, wallet history, support tickets, team management, account security, and verification workflows.

**Official website:** [www.mssv.ir](https://www.mssv.ir/)

**API hostname:** `https://api.mssv.ir`

**API base URL:** `https://api.mssv.ir/v1/`

## Documentation

- [Getting started](docs/getting-started.md)
- [Authentication and scopes](docs/authentication.md)
- [API reference](docs/api-reference.md)
- [Errors and idempotency](docs/errors.md)
- [Versioning policy](docs/versioning.md)
- [cURL examples](examples/curl.md)
- [OpenAPI 3.1 specification](openapi/openapi.yaml)
- [Changelog](CHANGELOG.md)
- [Security policy](SECURITY.md)

## Quick start

Create a Machine API token from [Machine API token management](https://my.mssv.ir/machine-users/), assign at least one allowed IPv4/IPv6 address or CIDR range to that token, then send it as a Bearer token from an allowed source address:

```bash
curl -sS https://api.mssv.ir/v1/me \
  -H 'Authorization: Bearer YOUR_MSSV_API_TOKEN' \
  -H 'Accept: application/json'
```

A successful response uses JSON and includes `"ok": true`.

## API security model

The public Machine API currently uses:

- a dedicated `api.mssv.ir` hostname behind MSSV's CDN edge
- Bearer-token authentication
- fine-grained permission scopes
- mandatory per-token IPv4/IPv6 allowlists, including CIDR ranges
- trusted CDN real-client-IP validation
- an aggregate edge allowlist in Nginx plus an exact token-to-IP check in the API application
- no MSSV Machine API requests-per-minute limiter
- account ownership checks for customer resources
- `Idempotency-Key` protection on supported state-changing operations
- JSON responses
- versioned paths under `/v1/`

Customers can update the allowed IP/CIDR list for an active Machine API token from [Machine API token management](https://my.mssv.ir/machine-users/). Origin allowlist reconciliation is automatic.

## Public API scope

This repository documents the public customer-facing Machine API. Internal MSSV control-plane, node-agent, administrative, infrastructure and production-only interfaces are intentionally excluded.

## Website and product information

For MSSV services, account access and current product information, visit **[www.mssv.ir](https://www.mssv.ir/)**.

## Source of truth

The documentation is maintained against the MSSV production API implementation. When a public API contract changes, the OpenAPI specification, reference documentation and changelog should be updated in the same release cycle.

## License

Documentation, examples and the OpenAPI specification in this repository are licensed under the [Apache License 2.0](LICENSE).
