---
id: m-eeca
status: closed
deps: [m-ee81, m-c965]
created: 2026-02-05T03:10:06Z
type: bug
priority: 0
assignee: Adam
updated: 2026-02-05T05:54:39Z
---
# defaults.rules parsed but doesn't generate guards

**Problem**: Policy `defaults.rules` array is parsed and stored, but rules don't generate enforcement guards.

**What exists**:
- Rules list: no-secret-exfil, no-sensitive-exfil, no-untrusted-destructive, no-untrusted-privileged, untrusted-llms-get-influenced
- Rules are checked during label flow analysis
- Some rules work (influenced label gets added)

**What's missing**:
- No guard generation from rules
- `generatePolicyGuards()` doesn't create guards based on `defaults.rules`
- Rules like "no-secret-exfil" should generate guards that block secret → exfil flow

**Evidence**:
- Adversarial test 5: `defaults.rules: ["no-secret-exfil"]` doesn't block `show @secret`
- `checkBuiltinPolicyRules()` has some rule logic but incomplete
- Spec Part 3.2 describes rules as generating automatic protection

**Expected**: `defaults: { rules: ["no-secret-exfil"] }` should automatically create guards that prevent secrets from flowing to exfil operations.

**Related**: This is the core missing piece that makes policy declarative instead of imperative.

**Files**: `core/policy/guards.ts`, `interpreter/policy/label-flow.ts`

## Plan

**How rules work**: Rules check label flow between **user-labeled data** and **user-labeled operations**.

Example:
```mlld
var secret @apiKey = "sk-..."        // User labels data as "secret"
exe exfil @send(d) = run cmd { ... } // User labels exe as "exfil"
@send(@apiKey)                        // Should be blocked by no-secret-exfil rule
```

**Implementation:**

- Extend `generatePolicyGuards()` in `core/policy/guards.ts` to handle `defaults.rules`
- For each enabled rule, generate guards that check label flow using **taint** (labels + provenance), not just labels:
  - `no-secret-exfil`: Block if input taint includes "secret" AND operation labels include "exfil"
  - `no-sensitive-exfil`: Block if input taint includes "sensitive" AND operation labels include "exfil" (NOT requires untrusted - rule name suggests sensitive alone is sufficient)
  - `no-untrusted-destructive`: Block if input taint includes "untrusted" AND operation labels include "destructive"
  - `no-untrusted-privileged`: Block if input taint includes "untrusted" AND operation labels include "privileged"
  - `untrusted-llms-get-influenced`: Auto-labels only (no enforcement) - see m-7178
- Risk labels (exfil, destructive, privileged) are **user-applied** on operations - no auto-classification
- Decide enforcement location: either generate guards OR use existing label-flow checks, but not both (avoid double-denies)
- If keeping both paths, ensure one is source of truth for denial messages
- Guards should be privileged and fire on appropriate operations
- Add tests showing each rule blocks the intended flow with user-labeled operations
- Update policy docs to clarify: users must label their operations (exe exfil, exe destructive, etc.) for rules to enforce
