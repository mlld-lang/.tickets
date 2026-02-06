---
id: m-a1c6
status: closed
deps: []
created: 2026-02-03T21:08:09Z
type: task
priority: 2
assignee: Adam
tags: [urgency-high, security]
updated: 2026-02-03T21:15:33Z
---
# Add secret-protection guards to sandbox-demo.mld

## Problem

sandbox-demo.mld claims credential protection (Step 5, Summary section #5) but doesn't include guard definitions. The adversarial worker confirmed that `var secret @x = '...'` followed by `show @x` outputs the secret without blocking unless explicit guards are defined.

The adversarial worker also confirmed (Test 4) that user-defined guards DO work correctly for secret protection.

## Fix

Add guard definitions to sandbox-demo.mld BEFORE the env block (Step 4 area). Insert after the policy definition (Step 3):

```mlld
>> Secret protection guards
>> Block show/log of secret-labeled data
guard @noShowSecrets before op:show = when [
  @mx.labels.includes("secret") => deny "Cannot display secret-labeled data"
  * => allow
]

>> Block interpolation of secrets into commands
guard @noSecretCmd before op:cmd = when [
  @mx.labels.includes("secret") => deny "Cannot interpolate secret into command"
  * => allow
]
```

Then add a test demonstrating the protection works:

```mlld
>> Demonstrate secret protection
var secret @testKey = "sk-test-never-shown"
var @showResult = try [ show @testKey ] catch @e [ => @e.message ]
log "Secret protection test: @showResult"
```

Update the Summary section #5 to reference the guard definitions:
- Change from 'Secret never becomes interpolatable string' to accurately reflect that guards provide the protection
- Make it clear that guards are required for label enforcement until defaults.rules is implemented

## Do NOT remove or weaken claims

The credential injection via `using auth:name` claims are correct. The sealed path claims are correct. Only add the guards to make the secret label claims provably true.

## Test

Run `mlld run j2bd/security/impl/sandbox-demo.mld` - should complete without error and the secret protection test should show the guard blocking message.

