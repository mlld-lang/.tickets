---
id: m-daa4
status: closed
deps: []
links: []
created: 2026-02-22T05:44:32Z
type: bug
priority: 1
assignee: Adam
updated: 2026-02-24T13:35:11Z
---
# import from @input should resolve missing fields to null instead of throwing

When importing specific fields from @input (e.g. import { @MLLD_FOO } from @input), if the field is not set, it throws 'Export not found in resolver @input'. It should resolve to null so modules can check for the value and provide defaults. Fix applied in InputResolver.ts — always include requested field in exports (null for missing). Needs a fixture test.

