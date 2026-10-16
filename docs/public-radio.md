# Public Radio API

The MSSV Public Radio API is a small, read-only surface on `https://api.mssv.ir`. It is intentionally separate from the authenticated Machine API under `/v1/`.

No Bearer token is required for these two routes.

## `GET /radio/endpoints`

Returns the public Radio endpoint directory as a JSON array of endpoint filenames only.

```json
[
  "station-one.mp3",
  "station-two.mp3"
]
```

The response does **not** include domains, protocols, full stream URLs, display labels, listener counts, service IDs, customer/account identifiers, node identifiers, IP addresses, or infrastructure topology.

Example:

```bash
curl -sS https://api.mssv.ir/radio/endpoints \
  -H 'Accept: application/json'
```

## `GET /radio/listeners`

Returns the current public listener total and a per-public-endpoint count map.

```json
{
  "total": 12,
  "endpoints": {
    "station-one.mp3": 7,
    "station-two.mp3": 5
  }
}
```

The listener API does **not** expose listener/source IPs, whitelisted IPs, custom domains, service IDs, customer/account identifiers, node identifiers, or private topology.

Example:

```bash
curl -sS https://api.mssv.ir/radio/listeners \
  -H 'Accept: application/json'
```

## Caching and freshness

Both routes use short public cache lifetimes. MSSV also uses internal Redis/APCu hot caches so public reads do not repeatedly query the service database or recompute listener state.

Consumers should treat listener counts as near-real-time snapshots rather than transactional counters.

## Stream URLs

The endpoint API deliberately does not publish a domain or protocol. Applications that already know an approved Radio stream origin may combine that origin with an endpoint filename according to their own integration contract.

The MSSV public Radio page uses the canonical public stream origin internally. Customer-specific domain/protocol selection remains an authenticated customer-area concern and is not part of the Public Radio API.

## Privacy boundary

Detailed customer analytics, including per-whitelisted-IP and per-custom-domain listener information, are private and authenticated. They are not available through this public API.

## Superseded public paths

The current contract does not retain compatibility aliases for the superseded Radio public paths. In particular, integrations should not use:

- `https://www.mssv.ir/api/radio/endpoints`
- `/radio-stream-listeners`

Use the two `api.mssv.ir` routes documented above.

## OpenAPI

The dedicated OpenAPI 3.1 contract for this unauthenticated surface is [`openapi/public-radio.yaml`](../openapi/public-radio.yaml). The authenticated Machine API remains documented separately in [`openapi/openapi.yaml`](../openapi/openapi.yaml).
