---
id: m-f57c
status: open
deps: []
created: 2026-02-06T02:20:30Z
type: j2bd-security,urgency-high,security-fix
priority: 2
assignee: Adam
---
# Redact secret values in guard error messages

j2bd-security


**2026-02-06 02:20 UTC:** ## Problem

Guard error messages leak secret-labeled variable values through three channels:
1. inputPreview field - shows the raw value of secret variables
2. guardInput field - includes the full Variable object with its value  
3. guardContext snapshot - includes input and output variables that carry secret values

## Root Cause

buildVariablePreview() at interpreter/hooks/guard-pre-hook.ts:278 reads the variable .value property without checking for secret labels.

## Fix

1. buildVariablePreview(): Check if variable.mx.labels includes secret. Return [REDACTED].
2. buildInputPreview(): Check perInput.labels for secret before calling buildVariablePreview().
3. buildDecisionMetadata(): When extras.inputVariable has secret label, redact metadata.guardInput.
4. cloneGuardContextSnapshot(): When cloning input/output, redact if they carry secret labels.

Pattern: Array.isArray(variable.mx?.labels) && variable.mx.labels.includes('secret')
Also check for 'sensitive' label.
