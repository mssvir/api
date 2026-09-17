# Security Policy

Security matters for every MSSV API integration.

Official website: [www.mssv.ir](https://www.mssv.ir/)

## Reporting a vulnerability

Please do not publish API tokens, customer data, credentials, secrets, session identifiers, private infrastructure details, or exploitable security findings in a public GitHub issue.

Use the support channels available through [www.mssv.ir](https://www.mssv.ir/) to report security-sensitive issues privately.

When reporting a vulnerability, include only the minimum information needed to reproduce the issue safely. Redact tokens and customer data.

## API credential safety

- Treat Machine API tokens as secrets.
- Never commit tokens to source control.
- Prefer the minimum scopes required for an integration.
- Keep the mandatory IP/CIDR allowlist restricted to the smallest practical source range.
- Manage and revoke tokens that are no longer needed from [Machine API token management](https://my.mssv.ir/machine-users/).
- Use a unique `Idempotency-Key` for supported state-changing requests.
- Do not log full Authorization headers.

## Public documentation boundary

This repository covers the customer-facing Machine API. Internal control-plane, node-agent, administrative and production-only interfaces are not part of the public API contract.
