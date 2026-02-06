---
id: m-e76e
status: closed
deps: []
created: 2026-02-04T00:31:50Z
type: task
priority: 2
assignee: Adam
tags: [urgency-high, doc-fix]
updated: 2026-02-04T00:33:47Z
---
# Fix inaccurate claims in sandbox-demo.mld

The final review (m-09aa) found two factual inaccuracies in llm/run/j2bd/security/impl/sandbox-demo.mld:

**Issue 1: Lines 156-158 overclaim tools enforcement**
Current text:
```
1. TOOL RESTRICTIONS
   - tools: ["Read", "Write"] limits available tools
   - Agent cannot use Bash or other unrestricted tools
   - Enforced at runtime by mlld execution layer
```
Reality: The `tools` field is only enforced for MCP tool routing. Direct command execution (`run cmd`, `run sh`) is NOT checked against tools. The actual mechanism for blocking commands is `capabilities.deny/allow` on policy.

Fix: Rewrite to accurately reflect that tool scoping works with MCP providers and that command restrictions use policy capabilities:
```
1. TOOL RESTRICTIONS
   - tools: ["Read", "Write"] scopes MCP tool routing
   - capabilities.deny: ["sh"] blocks shell access via policy
   - capabilities.allow list controls which commands can run
```

**Issue 2: Line 177 says defaults.rules is not implemented**
Current text:
```
   - Guards are required until defaults.rules is implemented
```
Reality: defaults.rules IS implemented. The gap is that `operations` classification (mapping op:show to exfil label) isn't wired up yet, so the built-in rules don't fire on show/log. Guards or explicit policy.labels.secret.deny are the working approaches.

Fix: Replace with accurate explanation:
```
   - policy.labels.secret.deny blocks show/log/cmd for secrets
   - Guards provide additional protection for custom exe calls
```

Also check lines 17 and 113-114 which say 'Bash is NOT available' and 'Bash commands would be blocked' - these should clarify that shell blocking comes from policy deny:['sh'], not from the tools field.

