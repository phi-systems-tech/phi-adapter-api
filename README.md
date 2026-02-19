# phi-adapter-api

Header-only API for phi adapter development (Qt plugin).

## Package Dependency Model

`phi-adapter-api-dev` is intentionally kept slim.

- It provides public headers and CMake package files for adapter development.
- It does **not** provide a full packaging/build toolchain (`cmake`, `ninja`, `debhelper`, etc.).

For PHI package builders, install `phi-packaging-dev` on build hosts to get the
complete baseline toolchain.

## Adapter README Template

Use the common adapter documentation template from:

- `ADAPTER_README_TEMPLATE.md`

All `phi-adapter-*` repositories should follow this structure to keep documentation consistent.

## Adapter Development Rules

### Enum Mapping Policy

For all adapter implementations:

- Numeric enums must keep real device values (do not normalize to `1..N`).
- Text enums may use stable internal mapping when cross-device normalization is needed.
- Keep raw <-> internal mapping metadata so write-back always sends the original protocol value.

Rationale:

- UI preselection remains correct for current values.
- Automations operate on real device semantics.
- Different devices with different numeric ranges stay unambiguous.

### Runtime and Transport Design

General rules from production findings across adapters:

- Separate control and polling paths:
  - control commands must not be blocked by periodic polling.
  - prioritize control over background refresh work.
- Prefer event-driven I/O over busy-wait loops:
  - use signal/callback based socket handling with explicit timeouts.
  - avoid tight `waitForReadyRead`/poll loops in hot paths.
- Keep polling non-reentrant:
  - allow only one poll worker at a time.
  - queue/coalesce pending poll triggers instead of running overlapping polls.
- Debounce post-write refresh:
  - after successful `set`, trigger targeted readback for that device.
  - use staged/delayed refresh and skip redundant runs when regular poll is imminent.
- Treat acknowledgements and state payloads differently:
  - command ACK may arrive before full state update.
  - do not assume immediate telemetry change after ACK.
- Distinguish full snapshots from partial/delta responses:
  - only remove devices on verified full snapshots.
  - never prune inventory based on partial response payloads.
- Make shared runtime caches thread-safe:
  - protect cross-thread mutable state (scores, sessions, capability caches) with proper synchronization.
- Build observability in from day one:
  - log structured transport outcomes (connect, tx/rx, ack, timeout, fallback).
  - track counters for retries, skipped polls, and post-set refresh effectiveness.

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
