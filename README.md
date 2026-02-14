# phi-adapter-api

Header-only API for phi adapter development (Qt plugin).

## Adapter README Template

Use the common adapter documentation template from:

- `ADAPTER_README_TEMPLATE.md`

All `phi-adapter-*` repositories should follow this structure to keep documentation consistent.

## Debian Package Versioning Policy

`phi-adapter-api` is the single source of truth for Debian versioning rules used by all `phi-adapter-*` packages.

### Rules

1. Use Git tags as the primary source.
- Tag format: `vMAJOR.MINOR.PATCH`
- Debian upstream version: `MAJOR.MINOR.PATCH`
- Example: `v1.4.2` -> `1.4.2-1`

2. If no matching release tag exists, use a snapshot version.
- Format: `0.0~gitYYYYMMDD.<shortsha>-1`
- Example: `0.0~git20260214.a1b2c3d-1`

3. Debian revision (`-N`) tracks packaging-only changes.
- `-1` for first Debian release of an upstream version
- `-2`, `-3`, ... for packaging updates without upstream source changes

4. Versioning is independent per repository/package.
- `phi-adapter-api-dev` version comes from the `phi-adapter-api` repository
- Each `phi-adapter-<name>-dev` package version comes from its own repository

5. Adapter packages must depend on API compatibility ranges.
- Use: `phi-adapter-api-dev (>= X.Y), phi-adapter-api-dev (<< X.(Y+1))`

6. Every package release must include a `debian/changelog` entry.
- The entry must reference the source tag or commit used.

7. Do not use manual/freeform versioning.
- Versions must always be derived from tag or snapshot rule above.

### Scope

This policy applies to:
- `phi-adapter-api-dev`
- all `phi-adapter-*-dev` packages
- related adapter meta-packages
