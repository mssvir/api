# API Versioning Policy

The public MSSV Machine API is versioned in the URL.

Current base URL:

```text
https://mssv.ir/api/machine/v1
```

## Compatibility

Within `v1`, MSSV aims to keep existing integrations working when adding new optional fields, response properties, endpoints, actions or enum values.

Clients should:

- ignore unknown response properties
- avoid depending on undocumented fields
- use documented scopes and endpoint contracts
- use the service's returned `available_actions` rather than hard-coding runtime assumptions

## Breaking changes

A change that requires existing clients to change request structure, authentication behavior or core response semantics should be introduced through a new API version or a documented compatibility plan.

## Documentation updates

Public API implementation changes should update, in the same release cycle:

1. `openapi/openapi.yaml`
2. the relevant Markdown documentation
3. `CHANGELOG.md`

The official MSSV website is https://mssv.ir/.
