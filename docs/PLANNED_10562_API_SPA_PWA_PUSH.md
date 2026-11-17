# MSSV 10.5.62 — proposed unified API, three PWAs and self-managed notifications

> **Design candidate only. Not shipped.** The accepted public documentation still describes
> 53 authenticated Machine API operations (`/v1/`) plus two unauthenticated Radio reads
> (`/radio/endpoints`, `/radio/listeners`). Do **not** mistake this page for a live
> endpoint specification; update OpenAPI, examples, docs and the public version **only
> after** matching Production implementation and authenticated contract evidence.

## Objectives

- `https://api.mssv.ir` is the canonical JSON API for all website, sign-in, customer,
  administrator and public Radio AJAX operations. Inventory every live XMLHttpRequest
  and `fetch` first; do not remove legacy routes until all callers migrate.
- Public read-only operations for public Radio directory, Persian `display_name`,
  live canonical listener counts and eleven genuinely recorded history windows are
  callable **without** a bearer token. Public clients *may* send valid tokens, but
  neither the presence of a token nor a request-header trick may bypass authorization,
  resource filtering or response minimization. Unauthenticated rate limits vary by
  endpoint cost (short cached directory/count vs long sampled history), with
  `429`, `Retry-After` and strict anti-abuse behavior; tune from actual traffic.
- Private operations require validated, unexpired, unrecalled scoped tokens and a
  source IPv4/IPv6 address matching **that token's** configured permitted CIDRs.
  Trusted-edge real-IP resolution must precede allowlist evaluation, and arbitrary
  `X-Forwarded-For` from end users must never be trusted. Enforce customer ownership
  and administrator permissions in the application regardless of frontend controls.
- Login/forgot-password/token issuance are pre-authentication bootstrap operations:
  they cannot logically require an already-issued token. Apply strict origin, CSRF,
  credential-stuffing and rate-limit protection; private operations thereafter need
  valid session-bound tokens, provenance, allowed IP and authorization.
  Browser credentials must not be stored in localStorage, IndexedDB or service-worker
  caches. For mobile IP changes, revalidate/re-authorize rather than bypass token CIDR.
- `my.mssv.ir/`, `my.mssv.ir/admin/` and `www.mssv.ir/radio-stream.html`
  are separately installable PWAs with distinct `manifest.id`, `start_url`,
  `scope`, icons and independently configurable settings. For customer and admin,
  overlapping worker scope must be deliberately tested so admin is controlled by
  its specific `/admin/` worker. The radio app's service-worker scope is narrowly
  restricted to `/radio-stream.html`; never blindly claim the whole `www` origin.
- An admin-only settings backend manages each PWA's manifest metadata, icon assets,
  prompt/update policy, cache version, feature availability and rollouts with
  audited changes and immutable artifact versions.

## Required cache boundaries

| Tier | Permitted | Forbidden or special handling |
|---|---|---|
| Redis | validated, serialized, generation-tagged public API snapshots and narrowly scoped private data | cross-owner reuse, credentials, indefinite stale data |
| APCu | bounded per-process hot cache with generation/version invalidation | treating APCu as globally consistent or authoritative |
| HTTP CDN/browser | content-hashed immutable static JS/CSS/icons; short public Radio JSON with freshness metadata | caching private account/payment/admin JSON publicly |
| CacheStorage/PWA | versioned application shell and explicitly approved public GET resources | auth responses, bearer tokens, finance, private Radio IP/domain lists, password/session endpoints |
| IndexedDB | consented, bounded UI preferences and safe offline drafts | API secrets, live accounting authority, unauthorized data copies |

Use `Cache-Control: no-store` for credential and private finance/security responses.
On 10.5.62 activation, publish new content-addressed assets, atomically switch a
version manifest, have service workers announce an available update, and remove
previous app-cache generations only when new shell activation is verified.
Preserve unsent user drafts and avoid forcing an update during an in-flight payment.

## Three native-like, accessible interfaces

Mobile navigation must be touch sized, keyboard navigable, screen-reader readable,
safe with RTL and reduced-motion preferences, and usable in narrow viewport widths
(320/375/390/768 px). Browser back/forward, deep links, refresh, loading/error,
offline and expired-session states must work for customer/admin/Radio without
hydration flash, multi-player audio or duplicate POST. The Radio page must continue
to expose a genuine active station, accurate Persian display names and all 11
time windows. Design approval cannot replace real device tests.

## Self-managed notification system

- MSSV owns notification templates, API, permissions, audiences, scheduler,
  fan-out queue, subscription lifecycle, deduplication keys, expiring jobs,
  signed acknowledgement, localization and analytics. Private notification
  content must remain protected and audited, with opt-in, unsubscribe and
  per-channel/per-service preferences.
- Standard **background Web Push** delivery on browsers inevitably uses the
  endpoint selected by the browser's Push Service (e.g. browser-vendor operated).
  A fully self-hosted sender with MSSV VAPID keys does *not* remove that
  browser dependency. Do not promise offline push with zero external transport.
- When avoiding any third-party transport is mandatory, implement self-hosted
  SSE/WebSocket foreground notifications plus an internal inbox; clearly state
  that closed-browser background delivery is unavailable in that mode.
- Analytics must distinguish queued, submitted, provider-accepted, browser
  acknowledged, shown, opened and failed/expired; never label queued items
  as delivered or infer a user read from transport acknowledgement.

## Release acceptance

1. Enumerate existing routes and their actual PHP/JS callers. Preserve 53
   Machine API operations plus existing public read contracts until replacement
   is live and verified. Do not invent live paths or sample responses.
2. Implement + test new public history/directory contract, optional-token and
   no-token behavior, unauthenticated limits, token/IP constraints, CORS,
   CSRF, ownership isolation, invalid ranges and time-window semantics.
3. Migrate and verify AJAX callers one module at a time, with idempotent writes,
   audit and failure recovery. No changes to ledger, payments or existing
   customer data outside explicitly approved scope.
4. Implement cache generation, busting, stale-while-revalidate only on safe
   resources, observability and per-owner isolation tests.
5. Install and validate **three** separate manifests/workers and admin settings,
   then test update, mobile UX, controlled cache eviction and offline behavior.
6. Accept self-managed notification queues, opt-in flow and truthful statuses;
   specify explicitly whether background browser-vendor Push or entirely
   internal foreground-only messaging is selected.
7. Use only existing authorized **Self-hosted** runners. The current
   `mssvir/api` workflow uses `ubuntu-latest` and must not be executed for
   this project; migrate/disable it before any PR/main push that would trigger
   this hosted check. No `pull_request` opened for this planning branch.
8. Publish updated public OpenAPI/docs only after observed Production 10.5.62
   parity and independently verified scope/IP, security, cache and PWA behavior.

No provider keys, customer tokens or production secrets belong in this document.
