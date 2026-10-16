# MSSV API — Official Developer Documentation

[![Website](https://img.shields.io/badge/Website-www.mssv.ir-0b7285)](https://www.mssv.ir)
[![API](https://img.shields.io/badge/API-api.mssv.ir-2f9e44)](https://api.mssv.ir/)
[![License](https://img.shields.io/badge/License-Apache--2.0-blue)](LICENSE)

Official English documentation and OpenAPI specifications for the **MSSV API**.

MSSV provides two public API surfaces on `api.mssv.ir`:

- the authenticated, versioned **Machine API v1** at `https://api.mssv.ir/v1/`;
- the unauthenticated, read-only **Public Radio API** at `https://api.mssv.ir/radio/*`.

**Official website:** [www.mssv.ir](https://www.mssv.ir)

**API hostname:** `https://api.mssv.ir`

## Documentation

- [Getting started](docs/getting-started.md)
- [Authentication and scopes](docs/authentication.md)
- [Machine API reference](docs/api-reference.md)
- [Public Radio API](docs/public-radio.md)
- [TeaSpeak and Radio service extensions](docs/service-extensions.md)
- [Errors and idempotency](docs/errors.md)
- [Versioning policy](docs/versioning.md)
- [cURL examples](examples/curl.md)
- [Machine API OpenAPI 3.1 specification](openapi/openapi.yaml)
- [Public Radio OpenAPI 3.1 specification](openapi/public-radio.yaml)
- [Changelog](CHANGELOG.md)
- [Security policy](SECURITY.md)

## Machine API quick start

Create a Machine API token from [Machine API token management](https://my.mssv.ir/machine-users/), assign at least one allowed IPv4/IPv6 address or CIDR range to that token, then send it as a Bearer token from an allowed source address:

```bash
curl -sS https://api.mssv.ir/v1/me \
  -H 'Authorization: Bearer YOUR_MSSV_API_TOKEN' \
  -H 'Accept: application/json'
```

The current Machine API Production contract remains **53 operations**.

## Public Radio quick start

The two Public Radio routes are read-only and do not require a token:

```bash
curl -sS https://api.mssv.ir/radio/endpoints -H 'Accept: application/json'
curl -sS https://api.mssv.ir/radio/listeners -H 'Accept: application/json'
```

`GET /radio/endpoints` returns only endpoint filenames. `GET /radio/listeners` returns only the public total and per-public-endpoint listener counts. Domains, protocols, customer/service/account/node metadata, whitelisted IPs and custom-domain analytics are intentionally excluded.

## API security model

The Machine API uses:

- a dedicated `api.mssv.ir` hostname behind MSSV's CDN edge;
- Bearer-token authentication;
- fine-grained permission scopes;
- mandatory per-token IPv4/IPv6 allowlists, including CIDR ranges;
- trusted CDN real-client-IP validation;
- an aggregate edge allowlist in Nginx plus an exact token-to-IP check in the API application;
- no MSSV Machine API requests-per-minute limiter;
- account ownership checks for customer resources;
- `Idempotency-Key` protection on supported state-changing operations;
- versioned paths under `/v1/`.

The Public Radio API is the explicit read-only exception to token/IP allowlisting. It remains behind the trusted MSSV CDN/origin boundary and exposes only the narrow public-safe fields documented in `docs/public-radio.md`.

## Public API scope

This repository documents public customer-facing interfaces. Internal MSSV control-plane, node-agent, administrative, infrastructure and production-only interfaces are intentionally excluded.

The Machine API specification in `openapi/openapi.yaml` is the current 53-operation authenticated Production contract. The separate `openapi/public-radio.yaml` describes only the two unauthenticated read-only Radio discovery routes and does not alter Machine API authentication semantics.

## Website and account access

For MSSV services and current product information, visit **[www.mssv.ir](https://www.mssv.ir)**. For account access and Machine API token management, use the [MSSV client area](https://my.mssv.ir/).

## Source of truth

This repository is the canonical public API documentation surface and is maintained against the accepted MSSV Production implementation in `mssvir/mssv_private`. API-affecting releases are incomplete until implementation and documentation agree.

## Current-contract-only policy

Approved API changes replace the previous contract. MSSV does not retain backward-compatibility aliases, legacy request/response fields, fallback paths, deprecated duplicate endpoints or alternate preview specifications unless an explicit exception is approved. Git history preserves prior contracts; `main` documents only the current supported API.

## License

Documentation, examples and OpenAPI specifications in this repository are licensed under the [Apache License 2.0](LICENSE).
