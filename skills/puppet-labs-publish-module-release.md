---
name: Publish a module release to the Forge
description: Authenticate with an API key and publish, verify, or deprecate a Puppet Forge module release.
api: openapi/puppet-labs-forge-openapi-original.json
operations: [addRelease, getRelease, deprecateModule]
auth: API key required (Authorization Bearer)
---

# Publish a module release to the Forge

Base URL: `https://forgeapi.puppet.com`. These are write operations and **require an API key**.
Create one from your profile on https://forge.puppet.com and send it on every request:

```
Authorization: Bearer <api_key>
```

## Steps

1. **Publish the release** — `addRelease` (`POST /v3/releases`). Upload the built module tarball
   (produce it with `pdk build`). A `201` means the release was accepted; `409` means that version
   already exists; `403` means the key lacks permission to publish for that owner.
2. **Verify** — `getRelease` (`GET /v3/releases/{release_slug}`) with `<owner>-<name>-<version>` to
   confirm the metadata and download URI.
3. **Deprecate (optional)** — `deprecateModule` (`PATCH /v3/modules/{module_slug}`) to mark an
   older module deprecated, supplying the deprecation reason in the request body.

## Rules

- A missing `Authorization` header returns `401`; an invalid or under-privileged key returns `403`.
- Publishing is not idempotent — check with `getRelease` before retrying a failed `addRelease` to
  avoid a `409` on a partially-succeeded publish.
- Build and lint with `pdk` before publishing (see `cli/puppet-labs-cli.yml`).
- Error semantics: `errors/puppet-labs-problem-types.yml`.
