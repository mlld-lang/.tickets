---
id: m-fc23
status: closed
deps: []
created: 2026-02-17T23:13:04Z
type: task
priority: 2
assignee: Adam Avenir
updated: 2026-02-18T08:01:25Z
---
# Fold DYNAMIC-MODULES.md into SDK.md and RESOLVERS.md

Move dynamic module behavior into docs/dev/SDK.md and docs/dev/RESOLVERS.md.

## Acceptance Criteria

- Dynamic module architecture is canonical in SDK/RESOLVERS docs.\n- docs/dev/DYNAMIC-MODULES.md is ready for deletion by the remove ticket.


**2026-02-17 23:37 UTC:** Doc-audit input (source: `llm/run/doc-audit/tmp/results.json`) for this ticket.
This input MUST be verified with 100% confidence during implementation.

Files in scope to verify:
- `docs/dev/SDK.md` [outdated]: Several inaccuracies: cache is content-based not mtime-based, Ruby SDK is missing from wrappers list, dynamic module size limits are incomplete (missing MAX_TOTAL_NODES), and ExecutionEmitter does not subscribe to StreamBus.
- `docs/dev/RESOLVERS.md` [outdated]: Significant inaccuracies: wrong resolver names, non-existent classes/interfaces (MCPResolver, TTLCacheService as described, resolveAtReference, joinPathSegments), wrong file name for lock/config files, wrong capabilities for @now, wrong ProjectPathResolver name and capabilities, and wrong ResolverError constructor signature

Verification bar (required):
- Confirm or disprove each finding for the files above against current code/docs with 100% confidence.
- If disproved, record why with concrete file references.
- If confirmed, update docs in this ticket scope and keep links/frontmatter consistent.

**2026-02-18 00:30 UTC:** Verified against current SDK/resolver implementation. Decisive fold plan for `docs/dev/DYNAMIC-MODULES.md` -> `docs/dev/SDK.md` + `docs/dev/RESOLVERS.md`:

## `docs/dev/SDK.md`

- Keep SDK as canonical for API-level dynamic module usage and execution behavior; merge dynamic-module user-facing behavior from `DYNAMIC-MODULES.md` here.
- Fix confirmed SDK inaccuracies:
  - AST cache description must be content-based, not mtime-based:
    - `MemoryAstCache` reads file content and cache-hits only when cached `source === current source` (cache key is `filePath + mode`).
  - ExecutionEmitter/StreamBus architecture must be corrected:
    - `ExecutionEmitter` is an event hub (on/off/emit) and does **not** subscribe to `StreamBus` itself.
    - StreamBus subscription/bridging happens in environment SDK bridge (`enableSDKEvents` -> bus subscription -> mapped SDK events).
  - Language wrapper list must include `Ruby` (`sdk/ruby/`) alongside Go/Python/Rust.
  - Dynamic object-module limits must include full current constraints, especially `MAX_TOTAL_NODES`.
- Dynamic module limits section should reflect current resolver constraints:
  - max serialized size: 1MB
  - max depth: 10
  - max keys/object: 1000
  - max elements/array: 1000
  - max total nodes: 10000
- Keep SDK dynamic-module security behavior explicit:
  - dynamic modules are labeled/tainted with `src:dynamic` (+ optional `src:<source>`)
  - `execute()` injects `@payload` and optional `@state` via dynamicModules
  - `@payload/@state` registrations use literal-string mode for string values

## `docs/dev/RESOLVERS.md`

- Keep RESOLVERS as canonical resolver-internals doc and merge resolver-side dynamic-module architecture from `DYNAMIC-MODULES.md` here.
- Confirmed major drift: remove/replace non-existent abstractions and stale pseudo-APIs:
  - `MCPResolver` (as shown), `TTLCacheService` (as shown), `resolveAtReference(...)` helper flow, and `joinPathSegments(...)` examples are not current implementation artifacts.
- Replace interface/code snippets with current resolver contracts:
  - `Resolver` and `ResolverCapabilities` from `core/resolvers/types.ts`
  - actual resolver classes in use (`DynamicModuleResolver`, `ProjectPathResolver`, `RegistryResolver`, `LocalResolver`, `GitHubResolver`, `HTTPResolver`, plus built-in function resolvers).
- Correct file/config naming guidance:
  - canonical config/lock docs should center `mlld-config.json` and `mlld-lock.json` (legacy fallback names may be noted as compatibility, not primary architecture contract).
- Correct built-in resolver behavior details:
  - `@now` capability/context info must match `NowResolver` (including path-context support).
  - Project path resolver is `ProjectPathResolver` with resolver name `base` and alias `root`; do not document a `PROJECTPATH` resolver identity/API.
- Correct resolver error model examples:
  - align `ResolverError` constructor usage/signature with `core/errors/ResolverError.ts`.
- Add dynamic resolver internals section (migrated from DYNAMIC-MODULES, aligned to code):
  - exact-key matching (`canResolve` against injected map)
  - object-module serialization to `/var` + `/export` module source
  - source labeling and security taint (`src:dynamic`, optional source label)
  - update semantics when dynamic resolver already exists in resolver manager.

## `docs/dev/DYNAMIC-MODULES.md`

- After migration, reduce to pointer stub and mark ready for deletion by remove ticket.
- Do not preserve duplicate or stale resolution-order prose from this file in canonical docs; resolver ordering/capabilities should be documented from current resolver code paths.
