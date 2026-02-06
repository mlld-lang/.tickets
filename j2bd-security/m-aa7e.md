---
id: m-aa7e
status: closed
deps: []
created: 2026-02-01T23:18:14Z
type: task
priority: 2
assignee: Adam
tags: [urgency-high, capstone]
updated: 2026-02-02T09:48:10Z
---
# Write pattern-audit-guard atom

Create the capstone documentation showing the full multi-agent audit pattern with signed instructions.

## Purpose
Demonstrate the complete audit guard pattern that uses signing/verification to protect auditor LLM instructions from prompt injection. This is the culminating example that ties together all the signing concepts.

## Key Concepts to Cover
- Full pipeline: first agent processes untrusted data, outputs get influenced label
- Auditor LLM with signed audit criteria template
- Verification flow: auditor calls verify, compares against context
- Action gating based on audit approval
- Detection of tampered instructions

## Example from Job Spec
```mlld
>> Step 1: Sign the audit template at authoring time
var @auditCriteria = template "./prompts/audit-criteria.att"
sign @auditCriteria by "security-team" with sha256

>> Step 2: First agent processes untrusted data
exe llm @processData(input) = run cmd { claude -p "@input" }
var @mcpData = @mcp.github.listIssues({ repo: "untrusted-repo" })
var @processed = @processData(@mcpData)
>> @processed now has 'influenced' label

>> Step 3: Auditor with signed instructions
exe llm @audit(content, criteria) = [...]

>> Step 4: Run audit
var @auditResult = @audit(@processed, @auditCriteria)

>> Step 5: Act only if approved
when @auditResult.approved => [...]
```

## What This Prevents
- Instruction injection
- Instruction modification
- Verification bypass
- Skip verification

## Dependencies
Builds on signing-overview, sign-verify, autosign-autoverify, and labels-influenced atoms.

## Location
docs/src/atoms/security/pattern-audit-guard.md

