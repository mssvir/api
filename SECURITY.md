# Security Policy

Security matters for every MSSV API integration.

Official website: https://mssv.ir/

## Reporting a vulnerability

Please do not publish API tokens, customer data, credentials, secrets, session identifiers, private infrastructure details, or exploitable security findings in a public GitHub issue.

Use the official MSSV website and support channels at https://mssv.ir/ to report security-sensitive issues privately.

When reporting a vulnerability, include only the minimum information needed to reproduce the issue safely. Redact tokens and customer data.

## API credential safety

- Treat Machine API tokens as secrets.
- Never commit tokens to source control.
- Prefer the minimum scopes required for an integration.
- Use the optional IP/CIDR allowlist where practical.
- Revoke tokens that are no longer needed.
- Use a unique `Idempotency-Key` for supported state-changing requests.
- Do not log full Authorization headers.

## Public documentation boundary

This repository covers the customer-facing Machine API. Internal control-plane, node-agent, administrative and production-only interfaces are not part of the public API contract.
