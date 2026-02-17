---
id: m-0a7c
status: closed
deps: []
created: 2026-02-12T12:21:14Z
type: task
priority: 0
assignee: Adam
updated: 2026-02-12T20:55:29Z
---
# Design: review P0 tickets needing design decisions before implementation

Several P0 tickets need design decisions before agents can work on them. Review each and make the call so they can be unblocked.

## m-0ccb — Runtime errors in for-loop bodies silently become data
Decision needed: Should error propagation (throw) be the default, with opt-in error wrapping? Or keep current behavior with opt-out throwing? This is a breaking change if we switch defaults. The ticket suggests throw-by-default but existing scripts may rely on error-as-data behavior.

## m-3b36 + m-595f — let/var scoping and imported function scope isolation
These are deeply related. m-3b36 says 'let shadows var instead of erroring' and m-595f says 'imported function internals clash with caller scope'. The real question is: should mlld have proper scope isolation for imported functions (so their internal lets are invisible to callers), or should it error on name collisions? Proper scope isolation fixes both tickets. Erroring is a band-aid that makes users rename variables. Coordinated decision needed.

## m-c731 — sh block parameters: error on undefined variables
Tagged needs-human-design. The '@' character appears in both variable references (@var) and plain text (user@example.com). The ticket proposes: error when undefined @var is used as a function argument, pass through in template text. This distinction (function arg position vs template text) seems right but needs confirmation.

## m-1138 — When expression '2 condition errors' crash
Can't reproduce — every attempt gives '1 condition error' instead of '2'. May be a symptom of m-3b36 (let shadowing causing stale state). Block on m-3b36 resolution. If m-3b36 fix doesn't resolve it, need to add debug instrumentation to when-expression.ts to capture the exact state that causes 2 simultaneous failures.

## mlld-ghie — template directive executes code blocks in template files
Three options, no decision: (1) document as intentional, (2) add literal template mode, (3) require explicit opt-in for executable blocks. Current behavior is surprising — .att templates with markdown code examples execute those examples. Need to decide if templates are 'smart' (execute code) or 'dumb' (string interpolation only).

## m-8420 — Parameter shadowing warning in validate
The answer to this depends on the scoping model decision (m-3b36/m-595f). With proper scope isolation, same-module parameter shadowing is standard lexical scoping — `exe @fn(result) = [...]` when `var @result` exists is fine because the function has its own scope. In that world, a validate WARNING is appropriate (catches accidental shadowing). Without proper scope isolation, shadowing is dangerous because parameters leak into caller scope, and it should be an ERROR. So: decide scoping model first, then the validate behavior follows.

## mlld-iox7 — Method chaining across multiple lines
Significant grammar feature, not a bug fix. Multi-line method chaining (@val.replace().replace()) requires grammar changes to handle line continuations after '.'. Complexity: how does this interact with the existing newline-as-statement-terminator model? Should this be P1 design instead of P0?

