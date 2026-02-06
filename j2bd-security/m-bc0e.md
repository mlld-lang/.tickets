---
id: m-bc0e
status: closed
deps: []
created: 2026-02-05T14:15:29Z
type: task
priority: 2
assignee: Adam
tags: [phase-4, adversarial]
updated: 2026-02-05T14:21:52Z
---
# Phase 4 adversarial: prove env module exit criteria

## Objective

Red-team test the environment module implementation to PROVE exit criteria hold.

## Exit Criteria to Verify

1. **User can create packaged env module** - The module at examples/env-modules/claude-dev/index.mld must demonstrate:
   - exports @spawn and @shell executables
   - profile system with full/readonly variants
   - credential flow using policy.auth (not hardcoded)

2. **Another user can import and use it** - demo-usage.mld must work:
   - imports from the module
   - uses @spawn with proper credential handling

## Adversarial Tests Required

1. **Syntax validity**: Run `mlld validate` on both files, confirm no errors
2. **Import resolution**: Verify the import path resolves correctly
3. **Profile selection**: Verify profiles {} block is recognized (check AST or interpreter)
4. **Credential flow**: Verify `using auth:claude` syntax in exe definition works
5. **Attempt violations**:
   - Try to access credentials directly (should fail if sealed path works)
   - Try to import non-exported internals (should fail)
6. **Documentation accuracy**: Verify atoms describe reality (env-overview, policy-auth actually match implementation)

## Expected Results

Return `status: verified` with `exit_criteria_results` array showing PASS/FAIL for each criterion.


**2026-02-05 14:21 UTC:** Phase 4 complete. All four phases executed (docs, impl, verification, adversarial). Job ready for completion.
