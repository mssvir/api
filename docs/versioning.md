# API Versioning and Current-Contract Policy

The public MSSV Machine API uses the canonical URL namespace:

```text
https://api.mssv.ir/v1/
```

`/v1/` is the only supported public Machine API namespace.

## Current contract only

The contract documented on `main` is the contract currently accepted in Production. **No backward-compatibility layer is retained** when an approved API change replaces an older contract.

This means MSSV does not keep, unless an explicit exception is approved:

- legacy or alternate URL aliases
- deprecated duplicate endpoints
- obsolete request or response fields
- old enum/value fallbacks
- redirects to historical API paths
- parallel preview specifications after Production acceptance
- runtime branches whose only purpose is compatibility with an obsolete contract

Git history preserves previous contracts. It is not a supported runtime compatibility surface.

## Client responsibility

Clients should:

- follow the current OpenAPI and Markdown documentation
- ignore unknown response properties where the current schema permits them
- avoid depending on undocumented fields
- use documented scopes and endpoint contracts
- use returned `available_actions` instead of hard-coding runtime assumptions

An approved breaking change can require clients to update. MSSV updates the implementation and documentation as one current release rather than carrying an obsolete behavior path forward.

## Release synchronization

A public API change is not complete until the same current contract is reflected in:

1. the accepted MSSV Production implementation and route registries
2. `openapi/openapi.yaml`
3. the relevant Markdown reference/authentication/error documentation
4. `CHANGELOG.md`
5. the cross-repository synchronization guard in `mssvir/mssv_private`

The official MSSV website is [www.mssv.ir](https://www.mssv.ir).
