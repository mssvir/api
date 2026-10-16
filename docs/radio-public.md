# MSSV Public Radio API

MSSV exposes two small, unauthenticated Radio read APIs on `api.mssv.ir`. They are intentionally separate from the authenticated, versioned Machine API under `/v1/`.

## Endpoint directory

`GET https://api.mssv.ir/radio/endpoints`

The response is a JSON array containing only public Radio endpoint file names. No hostname, protocol, full URL, listener count, customer/service identifier, node information, IP address, or domain is included.

Example:

```json
[
  "radio-one.mp3",
  "radio-two.mp3"
]
```

Endpoint names match `^[A-Za-z0-9_-]{1,80}\.mp3$`.

## Listener counts

`GET https://api.mssv.ir/radio/listeners`

The response contains only the aggregate active-listener count and active-listener counts by public endpoint.

Example:

```json
{
  "total": 18,
  "endpoints": {
    "radio-one.mp3": 11,
    "radio-two.mp3": 7
  }
}
```

The public listener API never publishes whitelisted IP addresses, custom domains, service/customer/account identifiers, node identity, or infrastructure topology. Customer-specific endpoint/IP/domain breakdowns are available only inside the authenticated MSSV client area for the account-owned Radio service.

## Caching and freshness

These read APIs are served through MSSV's hot-cache path. Endpoint discovery uses APCu and Redis before falling back to the application database on a cache miss. Listener data is read from the live Redis/APCu listener cache. HTTP responses also include short public cache windows suitable for frequently refreshed status UIs.

Consumers should treat listener counts as near-real-time rather than transactional data.

## Website integration

`https://www.mssv.ir/radio-stream.html` consumes these two APIs directly: the endpoint API provides the public Radio list and the listener API supplies the live counters.

## Removed legacy public paths

The following older website-facing Radio paths are not part of the current public contract and are removed rather than kept as compatibility aliases:

- `https://www.mssv.ir/api/radio/endpoints`
- `https://www.mssv.ir/radio-stream-listeners`

Integrations should use the two `api.mssv.ir/radio/*` endpoints documented above.

## Machine API distinction

The authenticated Machine API remains under `https://api.mssv.ir/v1/` and uses Bearer tokens, scopes, source-IP controls, and account ownership checks. For example, `/v1/services/{id}/radio/endpoints` is an account-owned service operation and is not the same contract as the public `/radio/endpoints` directory.
