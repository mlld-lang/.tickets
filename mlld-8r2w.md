---
id: mlld-8r2w
status: closed
deps: []
links: []
created: 2026-01-18T11:07:21.970858-08:00
type: task
priority: 1
parent: mlld-si08
---
# Remove /needs keychain gating (sealed paths only)

## Summary

Remove the `/needs { keychain }` security gating. Credential access should ONLY go through the sealed `policy.auth` + `using auth:*` path.

## Background

From 2026-01-18 audit: The `/needs { keychain }` check was added to gate direct keychain access via `/import { get } from @keychain`. However, this is unnecessary if all credentials flow through the sealed path:

1. `policy.auth` defines credential sources (keychain or env)
2. `using auth:name` injects credentials to env vars
3. Modules NEVER see actual secret values

## Changes Required

1. **Remove gating checks:**
   - `interpreter/eval/import/ImportDirectiveEvaluator.ts:389-402`
   - `interpreter/utils/auth-injection.ts:124-130`

2. **Remove or restrict direct keychain import:**
   - Either remove `/import { get } from @keychain` entirely
   - Or restrict to env-provider context only
   - Decision: Probably remove - env providers can use `policy.auth` too

3. **Update tests:**
   - Remove/update `tests/cases/exceptions/keychain-needs-required/`
   - Verify sealed path still works

4. **Update spec:**
   - Remove `/needs { keychain }` references
   - Clarify `/needs` is purely for dependencies

## Exit Criteria

- [ ] `/needs { keychain }` check removed
- [ ] Direct keychain import removed or restricted
- [ ] All credential access goes through `policy.auth` + `using auth:*`
- [ ] Tests pass
- [ ] Spec updated

## Related

- Spec: `todo/spec-security-2026-v3.md` Part 2.4
- Design discussion: 2026-01-18 audit session


