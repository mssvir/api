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

## Verified actual 10.5.61 source/route inventory — 2026-11-17

[Read-only existing Main self-hosted run 44153052545](https://github.com/mssvir/mssv_private/actions/runs/44153052545) **SUCCESS**. It observed actual installed Core **10.5.61**, 406 PHP router declarations in `src/Core/App.php` and no root-level public manifest/service-worker filename candidates. It only read exact named source files, never customer rows, credentials, token values or Production-mutating paths.

Read-only source counts (literal signatures, **not** proof that every call is an AJAX operation): `public/assets/app.js` 6 `fetch(`, `mssv-radio-v13.js` 2, `mssv-radio-u61.js` 2, and `views/client/service.php` 7. These currently include same-origin Radio operations; no new `api.mssv.ir` calls were observed inside those JavaScript assets. PublicSiteController already has APCu/Redis source markers, but a source marker is not evidence of optimal cache behavior across all pages.

**Version 62 entry gate:** Complete route/caller classification, ownership, response sensitivity and caching contracts before migrating a single AJAX call. Existing public `GET /radio/endpoints` and `GET /radio/listeners` remain the only documented accepted no-token Radio reads. Do not publish new history, display-name or token-optional endpoints as already available. Each new public route must prove anonymous rate-limit 429+Retry-After and separately prove authenticated token+IP enforcement for private data.

The current `mssvir/api` default-branch GitHub workflow `.github/workflows/api-docs-contract.yml` still specifies `ubuntu-latest`; **do not invoke that GitHub-hosted workflow**. Before any PR or main push to `mssvir/api`, replace its trigger/runner policy with a trusted available self-hosted source-only validation path, or keep documentation edits only on the inert feature branch.


## Source-only evidence after Main61 baseline (2026-11-17)

This section documents **unshipped** implementation-stage source evidence; public `mssvir/api@main` remains the accepted Machine API 53-operation contract plus two existing public Radio reads. Do not publish the new endpoints as live yet.

- Readonly actual Main61/API/AJAX/PWA inventory [44153052545](https://github.com/mssvir/mssv_private/actions/runs/44153052545), public-router exact SHA anchor inventory [44153864283](https://github.com/mssvir/mssv_private/actions/runs/44153864283).
- `feature/mssv-u10562-public-radio-api-20261117` stages host-locked anonymous `GET /radio/directory` and `GET /radio/history` plus `OPTIONS` for browser optional Authorization, separate IP rate limits, public-only visible directory and 11 actual history ranges. Eight nonroot tests [44154900180](https://github.com/mssvir/mssv_private/actions/runs/44154900180) and real61 three PHP output source-only parse [44155292358](https://github.com/mssvir/mssv_private/actions/runs/44155292358) passed. **Not deployed**.
- The same branch stages three separate customer/admin/radio manifest-and-worker generators. Eleven nonroot tests and JavaScript syntax checks of three generated workers [44156077774](https://github.com/mssvir/mssv_private/actions/runs/44156077774) passed. **No PWA is installed or admin settings activated yet**.
- Required next: migrate public frontend fetch/CSP/edge first, then private AJAX with scoped token/IP authorization and CSRF, native-like SPA, per-PWA admin settings, cache lifecycle, self-managed notifications, end-to-end tests, independently verified Production62 release. Never trigger the existing public repository `ubuntu-latest` workflow; it is a **known hosted-runner blocker** until replaced by an authorized non-Production self-hosted runner.


## Stage F 2026-11-18 — browser/API auth bridge measured evidence (not live)

- Verified actual **10.5.61** Core auth source on matched Main self-hosted: [read-only run 44196857554](https://github.com/mssvir/mssv_private/actions/runs/44196857554). It checks SHA-256 of 13 files including `Auth`, `Session`, `Csrf`, `RateLimiter`, `MachineAuth`, `MachineApiController` and `MachineServiceApiController`, without exposing customer rows, tokens or passwords.
- Reviewed the previous local source-only auth decision prototype and committed an improved **inert** policy gate `ops/production/u10562_api_auth_gate.php` and negative-policy tests in `mssvir/mssv_private:feature/mssv-u10562-public-radio-api-20261117`. Tightened server-resolved owner binding and rejected unrestricted Trusted Proxy CIDRs, separate from the token's own IP allowlist. [52 nonroot isolated assertions passed](https://github.com/mssvir/mssv_private/actions/runs/44197430455). No routing or Production changes.
- Public API GETs remain genuinely anonymous, publicly projected and rate-limited. The customer/admin browser flow must use verified short-lived session-bound credentials, token-scope validation, token-specific approved IP after audited trusted-edge normalization, account/service ownership, CSRF on writes, idempotency and precise admin permission checks.
- Existing `/v1/` Machine API bearer + aggregate edge/IP policy is a separate supported consumer contract. Do **not** weaken those protections or pass browser passwords, long-lived Machine bearer tokens or token secrets into PWA CacheStorage/localStorage.
- This is **not** an active OpenAPI route addition. A policy decision class does not issue or verify tokens or implement Redis. The real authentication adapters, CORS, mobile-IP behavior and negative browser tests remain unresolved. Keep live API documents and the 53 accepted Machine operations unchanged until full implementation and independent Production verification.
- Self-hosted runners only. The public API repo's hosted-runner docs workflow must not be triggered by PR or main commits.
