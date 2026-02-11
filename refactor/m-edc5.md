---
id: m-edc5
status: closed
deps: [m-5cd8]
links: []
created: 2026-02-09T06:19:05Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-ef4e
tags: [refactor, environment, phase-1, bootstrap]
updated: 2026-02-10T20:30:32Z
---
# Refactor Program: Modularize interpreter/env/Environment.ts - Phase 1: Extract bootstrap and dependency wiring

Objective:
Move constructor bootstrap logic into focused initialization helpers while preserving construction behavior.

Instructions for the implementing agent:
- Extract root bootstrap responsibilities from `Environment` constructor into dedicated modules/factories, including:
  - path context normalization
  - security/registry/project-config setup
  - resolver manager creation and default resolver registration
  - import resolver dependency construction for root vs child
  - variable manager dependency construction
- Keep constructor overload and public constructor signature unchanged.
- Keep initialization order unchanged where behavior depends on sequencing.
- Add unit tests for extracted factory/helper functions where practical.

## Wave 2 Phase Guardrails

Required design constraints for this phase:
- Do not create runtime modules under 60 lines unless the file is a pure type-definition file or re-export barrel.
- Do not duplicate shared logic across sibling modules in this area; extract a shared helper/service when behavior overlaps.
- Do not introduce constructor callback-lambda injection as a service-composition strategy; use interface-typed service objects.
- Define shared result/context/types/type-guards once per extraction area and import them instead of re-declaring.
- Keep module dependency direction acyclic in the touched area before closing the phase.

Evidence required in the phase note before close:
- Touched runtime modules with line counts and a short ownership note per module.
- Targeted test commands run for this phase and pass/fail status.
- Explicit statement that no new sub-60 runtime module was introduced (or list allowed type/barrel exceptions).
- Explicit statement that no callback-lambda service injection was introduced (or justify a recursion-only exception).

## Acceptance Criteria

1. Constructor body is materially smaller and delegates bootstrap steps to cohesive helpers.
2. Root and child initialization behavior remains unchanged (validated by phase-0 characterization tests).
3. Public constructor signatures stay stable.
4. Exit criteria: full test gate passes with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples


**2026-02-10 20:30 UTC:** Phase 1 extraction complete: bootstrap/dependency wiring

What changed
- Extracted constructor bootstrap/dependency wiring to new module:
  - `interpreter/env/bootstrap/EnvironmentBootstrap.ts` (296 LOC)
- Refactored `Environment` constructor to delegate path normalization, root bootstrap, variable-manager dependency wiring, and import-resolver dependency wiring:
  - `interpreter/env/Environment.ts` (3051 LOC; reduced from 3158)
- Added helper unit tests:
  - `interpreter/env/bootstrap/EnvironmentBootstrap.test.ts` (166 LOC)

Checklist coverage
1) Path context normalization extraction
- Implemented via `normalizeEnvironmentPathContext(...)`.

2) Security/registry/project-config bootstrap extraction
- Implemented via `initializeRootBootstrap(...)`.

3) Resolver manager creation + default registration extraction
- Implemented via `createResolverManager(...)` and invoked from root bootstrap.

4) Import resolver dependency construction extraction (root/child)
- Implemented via `buildImportResolverDependencies(...)`.
- Applied in constructor root path and in `setEphemeralMode` ImportResolver recreation.

5) Variable manager dependency construction extraction
- Implemented via `buildVariableManagerDependencies(...)`.
- Constructor now builds dependencies through extracted helper and passes to `VariableManager`.

Behavior/scope notes
- Public constructor overload and signature remain unchanged.
- Constructor initialization sequencing is preserved (path context -> parent context wiring -> root bootstrap -> utility managers -> variable manager -> reserved init -> import resolver -> builtins/command factory).
- Root/child behavior remains unchanged and is validated by characterization + integration suites.

Targeted phase validation
- Build: `npm run build` (pass)
- Targeted suites: `npm test -- interpreter/env/bootstrap/EnvironmentBootstrap.test.ts interpreter/env/Environment.characterization.test.ts interpreter/env/tool-scope.test.ts interpreter/env/debug.test.ts interpreter/env/variable-passing-integration.test.ts interpreter/env/NodeShadowEnvironment.test.ts interpreter/env/primitive-variable.test.ts interpreter/env/variable-proxy-integration.test.ts interpreter/env/executors/ShellCommandExecutor.test.ts` (pass)
  - Run summary: `312` files passed, `0` failed; `3652` tests passed, `0` failed.

Full gate validation
- Final successful run: `npm run build && npm test && npm run test:tokens && npm run test:examples` (pass)
  - Build: pass
  - Main suite: `312` files passed, `0` failed; `3652` tests passed, `0` failed.
  - Tokens: `6` files passed, `0` failed; `331` tests passed, `0` failed.
  - Examples: `1` file passed, `0` failed; `1353` tests passed, `0` failed.
- Note: an earlier run had one transient timing miss in `tests/parallel/shell-commands-parallel.test.ts`; immediate rerun passed without code change.

Wave 2 guardrail attestations
- Touched runtime modules and ownership:
  - `interpreter/env/bootstrap/EnvironmentBootstrap.ts` (296): bootstrap and dependency-builder services for constructor orchestration.
  - `interpreter/env/Environment.ts` (3051): facade/orchestration; delegates extracted bootstrap responsibilities.
- No new runtime sub-60 modules introduced.
- No duplicated bootstrap logic introduced across new sibling runtime modules.
- No callback-lambda constructor service-composition strategy introduced; extraction uses explicit helper/service functions.
- No cyclic dependency introduced in touched env/bootstrap area.
