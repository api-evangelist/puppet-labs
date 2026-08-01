---
name: Find and download a Puppet Forge module
description: Search the Puppet Forge, inspect a module and its releases, and download a release tarball.
api: openapi/puppet-labs-forge-openapi-original.json
operations: [getModules, getModule, getReleases, getRelease, getFile]
auth: none required for read endpoints
---

# Find and download a Puppet Forge module

Base URL: `https://forgeapi.puppet.com`. All read operations below are public — no API key needed.

## Steps

1. **Search for modules** — `getModules` (`GET /v3/modules`). Pass `query=<term>`, optionally
   `owner=<username>`, `tag=<tag>`, `with_pdk=true`. Use `limit` and `offset` to page; read the
   `pagination` object (`total`, `next`) to continue.
2. **Inspect a module** — `getModule` (`GET /v3/modules/{module_slug}`). The `module_slug` is
   `<owner>-<name>` (e.g. `puppetlabs-apache`). Read `current_release`, `deprecated_at`, and
   supported operating systems.
3. **List releases** — `getReleases` (`GET /v3/releases`) with `module=<slug>` to see all versions,
   or read a specific one with `getRelease` (`GET /v3/releases/{release_slug}`) where
   `release_slug` is `<owner>-<name>-<version>`.
4. **Download the tarball** — `getFile` (`GET /v3/files/{filename}`) using the `file_uri`/filename
   from the release metadata.

## Rules

- Pagination is limit/offset; never assume all results fit one page.
- Respect `deprecated_at` — warn before installing a deprecated module.
- Errors are plain HTTP status codes (see `errors/puppet-labs-problem-types.yml`): `404` = unknown
  slug, `400` = malformed query.
