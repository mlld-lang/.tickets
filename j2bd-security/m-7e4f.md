---
id: m-7e4f
status: closed
deps: []
created: 2026-02-04T21:55:05Z
type: task
priority: 2
assignee: Adam
tags: [adversarial, urgency-high]
updated: 2026-02-04T22:14:59Z
---
# Adversarial verification: security feature exit criteria

## Purpose

Run adversarial verification to PROVE all exit criteria hold for the security feature documentation job.

## Exit Criteria to Verify

From the success criteria:

### Phase 1: Documentation
- [ ] signing-overview atom exists with working code example
- [ ] sign-verify atom exists with working code example  
- [ ] autosign-autoverify atom exists with working code example
- [ ] labels-influenced atom exists with working code example
- [ ] pattern-audit-guard atom exists with working code example

### Phase 2: Implementation
- [ ] Signed audit template that can be verified
- [ ] First agent processes untrusted data (outputs get `influenced` label)
- [ ] Auditor verifies its own instructions before trusting them
- [ ] Guard blocks action if audit fails or verification fails
- [ ] End-to-end flow: untrusted input → influenced output → audit → action/rejection

### Phase 3: Verification
- [ ] Run target example code end-to-end
- [ ] Verify `influenced` label is applied to LLM outputs
- [ ] Verify `sign` and `verify` directives work
- [ ] Verify `autoverify` policy triggers verification automatically
- [ ] Test injection attack is detected (tampered instructions don't verify)

## Verification Mechanisms

1. Run `mlld validate` on all atom code examples
2. Run `mlld` on the implementation file and check output
3. Run relevant unit tests: `npm test -- --testPathPattern="sign-verify|autoverify|builtin-rules"`
4. Attempt adversarial scenarios:
   - Try to bypass verification
   - Try to modify signed template after signing
   - Verify `influenced` label propagates correctly

## Expected Results

All mechanisms pass. Document specific evidence for each exit criteria item.


**2026-02-04 22:14 UTC:** Verification results: (1) All 5 atoms validated with working examples (2) sign/verify directives work correctly (3) autoverify injects MLLD_VERIFY_VARS (4) influenced label applied to LLM outputs (5) Tampered content fails verification (6) Guard blocks on audit failure. Adversarial attacks attempted: signature forging, verification bypass, influenced label prevention, trusted override - all properly blocked by the security model.
