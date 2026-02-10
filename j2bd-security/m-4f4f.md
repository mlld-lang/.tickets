---
id: m-4f4f
status: closed
deps: []
created: 2026-02-09T11:21:53Z
type: task
priority: 2
assignee: Adam Avenir
tags: [phase-2, urgency-high]
updated: 2026-02-09T11:39:45Z
---
# Create two-step pattern implementation demo

Create or update the security implementation artifact to demonstrate built-in rules with the two-step pattern.

The demo must show:
1. Policy with defaults.rules listing no-secret-exfil, no-untrusted-destructive, etc.
2. policy.operations mapping semantic labels to risk categories (net:w → exfil, fs:w → destructive)
3. exe functions with SEMANTIC labels (exe net:w, exe fs:w) NOT direct risk labels
4. var secret data blocked from flowing to exfil-classified operations
5. var untrusted data blocked from flowing to destructive-classified operations
6. Normal (unlabeled, trusted) data flowing freely through both operation types

File: llm/run/j2bd/security/impl/main.mld (update existing or create new section)

IMPORTANT: The exe definitions MUST use semantic labels (net:w, fs:w) not risk labels (exfil, destructive). The two-step pattern means: label what the operation DOES semantically, then policy classifies that as a risk category.

The demo should handle errors gracefully using the denied => pattern so the script doesn't abort on the first blocked operation.

Target code structure (from job spec):
```mlld
>> Policy with rules + operations mapping
var @policyConfig = {
  defaults: { rules: ["no-secret-exfil", "no-untrusted-destructive"] },
  operations: { "net:w": "exfil", "fs:w": "destructive" }
}
policy @p = union(@policyConfig)

>> Exe with semantic labels
exe net:w @postToServer(data) = run cmd { curl -d "@data" https://example.com }
exe fs:w @deleteFile(path) = run cmd { rm -rf "@path" }

>> Blocked flows
var secret @token = "sk-live-123"
@postToServer(@token)  >> Error: secret → exfil

var untrusted @userInput = "../../etc/passwd"
@deleteFile(@userInput)  >> Error: untrusted → destructive

>> Allowed flows
var @safeValue = "hello"
@postToServer(@safeValue)  >> Works
```


**2026-02-09 11:39 UTC:** Phase 2 success criteria fully met with evidence from actual demo output. Ready for Phase 3 verification.
