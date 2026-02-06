---
id: m-aede
status: closed
deps: []
created: 2026-02-03T21:35:01Z
type: task
priority: 2
assignee: Adam
tags: [urgency-high, phase-3]
updated: 2026-02-03T21:50:03Z
---
# Fix log/show effect label propagation for guard evaluation

## Problem

The `log` and `show` pipeline effects strip security labels from their arguments before guard evaluation. This means `guard before secret` never fires for `log @secretVar`, allowing secrets to leak via log.

**Adversarial evidence**: Tests 5b and 5b2 in m-ed11 showed `log "@testKey"` and `log @testKey` both output secret values despite `before secret` guard being registered.

## Root Cause

In `interpreter/eval/pipeline/builtin-effects.ts`:

1. `extractEffectGuardInputs` (line 158-166) calls `resolveEffectPayload` which returns a plain `string`
2. `resolveEffectPayload` calls `evaluateEffectArg` (line 48-74) which converts args to strings via `interpolate`, recording security descriptors on the env but losing them from the return value
3. `materializeGuardInputs([plainString])` at line 164 creates a Variable with no security labels
4. Guard pre-hook at line 235 receives label-less inputs, so `before secret` never matches

## Fix

Modify `extractEffectGuardInputs` to also extract Variable objects (with their labels) from the effect's AST args, for use as guard inputs. The string payload should still be computed for display, but the guard inputs need the original Variables.

**Pattern to follow**: `command-execution.ts:920-941` builds `guardInputCandidates` from actual Variable objects before passing to `materializeGuardInputs`. The effect code should do the same.

**Approach**: Add a function that walks effect.args looking for VariableReference nodes, resolves them to Variables via `env.getVariable()`, and returns those Variables. Use these as guard inputs instead of (or in addition to) the string payload.

Specifically:
1. In `extractEffectGuardInputs`, after computing `payload`, also extract Variables from `effect.args` by walking the AST nodes for VariableReference/TemplateVariable and resolving them
2. Pass these Variables to `materializeGuardInputs` instead of `[payload]`
3. This preserves the `secret` label on the Variable's mx metadata, so `before secret` guards fire

Also check: `stageOutput` at line 216 may need the same treatment when there are no explicit args (the stage output might carry labels that need to propagate).

**Test**: After the fix, `tmp/adversarial-test5b-log-secret.mld` and `tmp/adversarial-test5b2-log-secret-direct.mld` should both be blocked by the guard (exit code 1 with denial message) instead of leaking the secret.

**Scope**: This affects log, show, output, and append effects. All use the same `extractEffectGuardInputs` path.

