# API Versioning Policy

The public MSSV Machine API is versioned in the URL.

Current base URL:

```text
https://api.mssv.ir/v1/
```

`/v1/` is the only supported public Machine API namespace. Unsupported historical or alternate paths are not aliases and are expected to return `404` unless MSSV explicitly documents a compatibility mechanism in a future release.

## Compatibility within v1

Within `v1`, MSSV aims to keep documented integrations stable when adding new optional fields, response properties, endpoints, actions or enum values. This does not create alternate URL namespaces or legacy-path redirects.

Clients should:

- ignore unknown response properties
- avoid depending on undocumented fields
- use documented scopes and endpoint contracts
- use the service's returned `available_actions` rather than hard-coding runtime assumptions

## Breaking changes

A change that requires existing clients to change request structure, authentication behavior or core response semantics should be documented before release. A new compatibility layer or alternate version/path is not introduced unless MSSV explicitly decides to provide one.

## Documentation updates

Public API implementation changes should update, in the same release cycle:

1. `openapi/openapi.yaml`
2. the relevant Markdown documentation
3. `CHANGELOG.md`

The official MSSV website is [www.mssv.ir](https://www.mssv.ir).
