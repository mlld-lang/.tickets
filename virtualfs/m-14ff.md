---
id: m-14ff
status: open
deps: [m-0c26]
created: 2026-02-18T17:12:20Z
type: task
priority: 2
assignee: Adam Avenir
updated: 2026-02-18T17:12:20Z
---
# Phase 4: Integrate VirtualFS into SDK and interpreter surfaces

Context:
- Source plan: plan-virtualfs.md (Phase 4)

Goal:
Integrate VirtualFS into SDK/interpreter surfaces and ensure public access via package exports.

Implementation tasks:
- Export VirtualFS from SDK public surface (sdk/index.ts) and package export map per contract.
- Verify processMlld/execute workflows with VirtualFS instances.
- Validate virtual-FS-sensitive interpreter paths remain correct:
  - ModuleContentProcessor isVirtual() behavior,
  - DirectoryImportHandler directory merging/stat behavior.
- Preserve default NodeFileSystem behavior for callers that do not opt into VirtualFS.

## Design

Integration anchors:
- sdk/index.ts
- sdk/execute.ts
- package.json exports
- interpreter/eval/import/ModuleContentProcessor.ts
- interpreter/eval/import/DirectoryImportHandler.ts

Compatibility requirements:
- No breaking change for existing default SDK usage.
- Public import path for VirtualFS must be documented and tested.

## Acceptance Criteria

Exit criteria:
- Added test coverage:
  - Extend/add sdk/index.test.ts and sdk/execute.test.ts with VirtualFS workflows.
  - Add interpreter integration tests for virtual mode parsing and directory import behavior.
  - Add export/import smoke test for public VirtualFS import path.
- All tests pass:
  - full regression gate.
- Docs updates:
  - Update CHANGELOG.md for SDK/interpreter integration.
  - Update relevant SDK docs references to public VirtualFS entry point.
- Commit made:
  - Commit with message: feat(vfs): expose VirtualFS in sdk and integrate interpreter paths

