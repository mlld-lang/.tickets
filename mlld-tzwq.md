---
id: mlld-tzwq
status: closed
deps: []
links: []
created: 2026-01-18T11:07:48.079624-08:00
type: task
priority: 1
tags: [size-m, complexity-m, risk-s, impl-done]
parent: mlld-si08
updated: 2026-01-30T06:46:53Z
---
# Review v4 spec for implementation gaps

## Summary

Spec v4 (`todo/spec-security-2026-v4.md`) supersedes v3. This bead's scope is now to review v4 for gaps between spec and implementation.

## v4 Status

The v4 spec is complete and addresses most of the original v3 audit findings:

- ✓ Guard export uses regular module system (no special syntax)
- ✓ `/needs` is dependencies-only (no keychain gating)
- ✓ Capability inference marked as runtime guards (not AST)
- ✓ Policy DSL design complete (`defaults` object)
- ✓ Signing & verification design complete

## New Implementation Work (Separate Beads)

The v4 spec introduces new features that need implementation:

| Feature | Bead |
|---------|------|
| Signing primitives | (new) |
| Auto-sign | (new) |
| Auto-verify for LLM exes | (new) |
| Label modification syntax | (new) |
| `influenced` propagation | (new) |
| Audit ledger | (new) |
| `.mx.tools` namespace | (new) |

## Remaining v4 Review Tasks

1. **Compare v4 spec against current implementation**
   - Which v4 features are already implemented?
   - Which need new implementation?
   - Which have partial implementation?

2. **Identify spec clarifications needed**
   - Any ambiguities in v4?
   - Any missing edge cases?

3. **Update inline code comments**
   - Ensure code references v4 spec sections
   - Remove stale v3 references

## Exit Criteria

- [ ] v4 spec reviewed against implementation
- [ ] Gap analysis documented
- [ ] Code comments updated to reference v4
- [ ] Any spec clarifications added to v4

## References

- `todo/spec-security-2026-v4.md` (authoritative spec)
- `todo/spec-security-2026-v3.md` (superseded)

## Estimated Effort

~2 hours



