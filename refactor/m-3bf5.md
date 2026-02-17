---
id: m-3bf5
status: closed
deps: [m-53cb]
created: 2026-02-12T03:22:30Z
type: task
priority: 0
assignee: Adam
updated: 2026-02-12T05:27:30Z
---
# Consolidation Program: Manual verification gate for Phase 4 namespace/import medium-risk changes

Manual verification gate required before any Phase 4 implementation. Validate namespace display and import serialization behavioral differences and define exact allowed edits.

## Acceptance Criteria

- Manually compare namespace-display vs JSON formatter behavior and record required output invariants.
- Manually compare ObjectReferenceResolver vs VariableImporter serialization behavior and record allowed shared extraction boundaries.
- Explicitly record Go/No-Go decision for Phase 4 in ticket notes.
- If this gate phase includes a commit, run `npm test` and `npm run build`; both must pass before commit.


**2026-02-12 05:27 UTC:** --dir=refactor Manual verification complete (Phase 4 namespace/import gate).

Compared namespace display paths:
- interpreter/core/interpreter/namespace-display.ts (cleanNamespaceForDisplay)
- interpreter/core/json-formatter.ts (JSONFormatter.stringifyNamespace)

Required output invariants:
1) cleanNamespaceForDisplay keeps the namespace-display contract: stable {frontmatter?, exports:{variables,executables}} shape and variable-wrapper unwrapping via .value.
2) JSONFormatter.stringifyNamespace keeps formatter contract for show/interpolation: formatter-driven JSON rendering and section-pruning behavior (removes empty frontmatter/exports).
3) Executable placeholder formatting stays equivalent (<function(...)>) across both paths.

Compared import serialization paths:
- interpreter/eval/import/ObjectReferenceResolver.ts
- interpreter/eval/import/VariableImporter.ts + variable-importer/ModuleExportSerializer.ts

Required serialization invariants:
1) ModuleExportSerializer remains the source of truth for top-level module export serialization and metadata map emission.
2) ObjectReferenceResolver remains focused on nested object reference resolution and embedded executable payload conversion.
3) Captured module-env recursion guard behavior in VariableImporter/ModuleExportSerializer must remain intact.
4) Do not unify executable payload field sets where behavior differs unless parity tests are explicitly updated/approved.

Approved Phase 4 scope (GO):
- shared helper extraction for pure/common utility logic only (e.g., namespace executable label formatting helpers, shadow-env map serialization helper), while preserving each path’s output/serialization contract.
- no contract-level behavior changes.

Out of scope / No-Go for this phase:
- changing namespace JSON shape parity between cleanNamespaceForDisplay and JSONFormatter.stringifyNamespace.
- changing top-level export serializer authority, metadata emission flow, or recursion handling strategy.

Manual gate decision: GO for Phase 4 within approved scope above.

Verification evidence:
- npx vitest run interpreter/core/interpreter/namespace-display.test.ts interpreter/core/interpreter.characterization.test.ts interpreter/core/json-formatter.test.ts (pass)
- npx vitest run tests/interpreter/import/object-reference-resolver.test.ts interpreter/eval/import/variable-importer.characterization.test.ts interpreter/eval/import/variable-importer/module-export-serializer.test.ts interpreter/eval/import/variable-importer/executable-rehydration-matrix.test.ts interpreter/eval/import/variable-importer/final-composition-parity.test.ts (pass)
