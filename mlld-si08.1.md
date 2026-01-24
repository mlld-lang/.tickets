---
id: mlld-si08.1
status: closed
deps: []
links: []
created: 2026-01-19T22:23:18.745534-08:00
type: task
priority: 1
parent: mlld-si08
---
# Signing primitives (sign/verify)

## Goal

Implement the signing primitives from spec v4 Part 14.

## Syntax

```mlld
sign @var with sha256              // Sign with hash
sign @var by "alice"               // Explicit signer identity
sign @var by "alice" with sha256   // Both

verify @var                        // Verify signature
```

## What Gets Signed

- The content of the variable at signing time
- For templates: the template text with placeholders (not interpolated)

## Storage

```
.mlld/sec/sigs/
  <varname>.sig       # { hash, method, signedby, signedat }
  <varname>.content   # Original signed content (for comparison)
```

## Verify Returns

```json
{
  "verified": true,
  "template": "Evaluate @input and determine if safe...",
  "hash": "sha256:abc123...",
  "signedby": "developer",
  "signedat": "2026-01-19T10:30:00Z"
}
```

## Implementation Tasks

1. Grammar for `sign` and `verify` directives
2. Signature storage in `.mlld/sec/sigs/`
3. Hash computation (sha256)
4. Verify command (`mlld verify`)
5. Integration with interpreter

## Exit Criteria

- [ ] `sign @var with sha256` works
- [ ] `sign @var by "signer"` works
- [ ] `verify @var` returns signature info
- [ ] Signatures stored in `.mlld/sec/sigs/`
- [ ] `mlld verify` CLI command works

## References

- Spec v4 Part 14.3: Signing Primitives
- Spec v4 Part 14.4: Verification Primitives

## Estimated Effort

~6 hours



