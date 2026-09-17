# MSSV API — Official Developer Documentation

[![Website](https://img.shields.io/badge/Website-mssv.ir-0b7285)](https://mssv.ir/)
[![API](https://img.shields.io/badge/API-v1-2f9e44)](https://mssv.ir/)
[![License](https://img.shields.io/badge/License-Apache--2.0-blue)](LICENSE)

Official English documentation and OpenAPI specification for the **MSSV API**.

MSSV provides hosting and service-management automation through a versioned Machine API. The API can be used to integrate customer systems with MSSV for account information, products, orders, hosted services, billing, wallet history, support tickets, team management, account security, and verification workflows.

**Official website:** https://mssv.ir/

**API base URL:** `https://mssv.ir/api/machine/v1`

## Documentation

- [Getting started](docs/getting-started.md)
- [Authentication and scopes](docs/authentication.md)
- [API reference](docs/api-reference.md)
- [Errors, rate limits and idempotency](docs/errors.md)
- [Versioning policy](docs/versioning.md)
- [cURL examples](examples/curl.md)
- [OpenAPI 3.1 specification](openapi/openapi.yaml)
- [Changelog](CHANGELOG.md)
- [Security policy](SECURITY.md)

## Quick start

Create a Machine API token from your MSSV client account, then send it as a Bearer token:

```bash
curl -sS https://mssv.ir/api/machine/v1/me \
  -H 'Authorization: Bearer YOUR_MSSV_API_TOKEN' \
  -H 'Accept: application/json'
```

A successful response uses JSON and includes `"ok": true`.

## API design

The public Machine API currently uses:

- Bearer-token authentication
- fine-grained permission scopes
- optional IPv4/IPv6 allowlists, including CIDR ranges
- per-token and platform-wide rate limiting
- account ownership checks for customer resources
- `Idempotency-Key` protection on supported state-changing operations
- JSON responses
- versioned paths under `/api/machine/v1`

## Public API scope

This repository documents the public customer-facing Machine API. Internal MSSV control-plane, node-agent, administrative, infrastructure and production-only interfaces are intentionally excluded.

## Website and product information

For MSSV services, account access and current product information, visit **[mssv.ir](https://mssv.ir/)**.

## Source of truth

The documentation is maintained against the MSSV production API implementation. When a public API contract changes, the OpenAPI specification, reference documentation and changelog should be updated in the same release cycle.

## License

Documentation, examples and the OpenAPI specification in this repository are licensed under the [Apache License 2.0](LICENSE).
