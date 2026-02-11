---
id: m-5cd8
status: closed
deps: [m-27ff]
links: []
created: 2026-02-09T06:19:05Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-ef4e
tags: [refactor, environment, phase-0]
updated: 2026-02-10T20:20:28Z
---
# Refactor Program: Modularize interpreter/env/Environment.ts - Phase 0: Baseline and characterization

Objective:
Establish a behavior baseline and refactor safety net for `Environment` before moving code.

Instructions for the implementing agent:
- Inventory `Environment` responsibilities and publish a method-to-responsibility map in ticket notes.
- Add/expand characterization tests that cover high-risk behavior clusters:
  - root vs child environment inheritance (reserved names, allowed tools, policy context, import guard scope)
  - resolver variable resolution/caching and reserved resolver behavior (`@debug`, `@input`, keychain denial)
  - effect emission behavior (doc suppression during import, break flushing, security capability attachment)
  - command/code execution wrappers (option merging, ambient `mx` injection for js/node only)
  - child creation + merge behavior (`createChild`, `createChildEnvironment`, block-scoped merge exclusions)
  - cleanup behavior for shadow envs, streaming bridge, and child environments
- Capture baseline risks and proposed extraction boundaries in this ticket.
- Do not perform structural refactors in this phase.

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

1. Characterization tests cover the high-risk clusters listed above and pass.
2. No intentional runtime semantic change is introduced.
3. Baseline architecture notes are attached to this ticket.
4. Exit criteria: full test gate passes with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples


**2026-02-10 20:18 UTC:** Phase 0 baseline + characterization update

What changed
- Added characterization suite: `interpreter/env/Environment.characterization.test.ts` (287 LOC).
- No structural refactor was performed in this phase.
- No runtime module behavior was intentionally changed.

High-risk behavior cluster coverage added
1) Root vs child inheritance
- Covers reserved resolver name inheritance, allowed tool inheritance, policy context propagation, and import-guard local scope behavior (`getIsImporting` isolation).

2) Resolver variable resolution/caching
- Covers non-reserved resolver lookup (`undefined`), keychain direct-access denial, explicit resolver caching behavior (single resolve call + cached variable reuse), and reserved `@debug`/`@input` behavior.

3) Effect emission behavior
- Covers doc suppression during import mode, break flush ordering before direct content emission, and capability/security attachment on emitted effects.

4) Command/code execution wrappers
- Covers default+override options merge for `executeCommand`, stream bus injection in wrapper context, and ambient `mx` injection only for js/node language paths.

5) Child creation + merge behavior
- Covers `createChild` path override + inherited policy/tool scope and `mergeChild` exclusions for block-scoped (`let`, `exe-param`) bindings.

6) Cleanup behavior
- Covers recursive child cleanup, shadow env cleanup, and stream-bridge unsubscribe lifecycle.

Method-to-responsibility map (baseline)
- Bootstrap/composition: constructor, `registerBuiltinResolvers`, `registerDynamicModules`, `configureLocalModules`.
- Path/runtime context: `getPathContext`, `getProjectRoot`, `getFileDirectory`, `getExecutionDirectory`, `resolvePath`, `getProjectPath`.
- Policy/security runtime: `setPolicyContext`, `getPolicyContext`, `setPolicyCapabilities`, `recordPolicyConfig`, `pushSecurityContext`, `popSecurityContext`, `recordSecurityDescriptor`, `recordStateWrite`, `getSecuritySnapshot`.
- Variable/resolver orchestration: `setVariable`, `updateVariable`, `getVariable`, `getVariableValue`, `hasVariable`, `getResolverVariable`, `setFrontmatter`.
- Output/effects/SDK bridge: `emitEffect`, `emitIntent`, `renderOutput`, `enableSDKEvents`, `emitSDKEvent`, stream-event mapping helpers.
- Execution wrappers: `executeCommand`, `executeCode`, `setOutputOptions`, streaming options/getters.
- Shadow env lifecycle: `setShadowEnv`, `getShadowEnv`, node/python getters, `captureAllShadowEnvs`, `hasShadowEnvs`.
- Import/file/url facade: `readFile`, `resolveModule`, `isImporting`, `beginImport`, `endImport`, URL helper methods.
- Scope lifecycle: `createChild`, `createChildEnvironment`, `mergeChild`, directive trace methods.
- Teardown: `cleanup`.

Baseline risks (for extraction planning)
- `Environment` combines many domains (policy, resolver, effects, execution wrappers, shadow env lifecycle), so accidental behavior coupling risk is high.
- Root/child inheritance semantics mix copy-on-create and parent fallback; extraction must preserve this boundary exactly.
- Effect emission carries subtle ordering and security metadata requirements; behavior drift risk is high.
- Execution wrappers (`executeCommand`/`executeCode`) include compatibility overload and selective ambient context injection; this is a high-regression seam.
- Cleanup behavior spans stream bridge, shadow env runtimes, and recursive child tracking; leaks/regressions are likely if ownership is unclear.

Proposed extraction boundaries
- `Environment` remains facade/composition root.
- Boundary A: bootstrap + dependency wiring.
- Boundary B: security/policy/state-write runtime.
- Boundary C: variable + resolver facade orchestration.
- Boundary D: effects + SDK/stream bridge orchestration.
- Boundary E: shadow env + execution wrapper orchestration.
- Boundary F: child lifecycle + merge + cleanup lifecycle.

Targeted test evidence (phase sub-checkpoint)
- Build: `npm run build` (pass)
- Targeted env suite run: `npm test -- interpreter/env/Environment.characterization.test.ts interpreter/env/tool-scope.test.ts interpreter/env/debug.test.ts interpreter/env/NodeShadowEnvironment.test.ts interpreter/env/primitive-variable.test.ts interpreter/env/variable-proxy-integration.test.ts interpreter/env/variable-passing-integration.test.ts interpreter/env/executors/ShellCommandExecutor.test.ts` (pass)
  - Result summary from run: `311` files passed, `0` failed; `3646` tests passed, `0` failed.

Wave 2 guardrail attestations
- Touched runtime modules with ownership notes: none in this phase (test-only change; no runtime module edits).
- No new runtime sub-60 modules introduced.
- No callback-lambda service injection introduced.
- No duplicated shared runtime logic introduced.
- No new cyclic dependency introduced in touched area.

**2026-02-10 20:20 UTC:** Phase close validation

Full gate command
- `npm run build && npm test && npm run test:tokens && npm run test:examples`
- Status: pass

Full gate evidence summary
- Build: pass
- Main test suite: `311` files passed, `0` failed; `3646` tests passed, `0` failed.
- Token suite: `6` files passed, `0` failed; `331` tests passed, `0` failed.
- Examples suite: `1` file passed, `0` failed; `1353` tests passed, `0` failed.

Acceptance criteria check
1. Characterization coverage added for all listed high-risk clusters: pass.
2. No intentional runtime semantic change introduced (test-only phase): pass.
3. Baseline architecture notes (method map, risks, boundaries) added: pass.
4. Full gate completed successfully: pass.
