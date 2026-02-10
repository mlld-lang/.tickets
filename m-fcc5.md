---
id: m-fcc5
status: open
deps: []
links: [m-6602, m-29d1, m-46d6]
created: 2026-02-09T06:18:51Z
type: feature
priority: 1
assignee: Adam Avenir
tags: [security, needs-human-design, core]
---
# Design: Error taint propagation

## Task

Design how labels propagate through error paths. This is a DESIGN task — produce a spec section, not an implementation.

## Layer

Core engine (taint propagation model).

## Problem

Labels propagate through all forward data transformations — this is mlld's central claim. But errors are a reverse data channel the model doesn't cover. If a secret-labeled value causes an exception, and the error message includes or derives from that value ("Cannot parse: sk-abc123..."), the error leaks the secret through a path that bypasses every flow rule.

Example: a guard blocks `secret → op:show`. But if code tries to JSON-parse a secret and fails, the error message contains the secret and gets displayed anyway.

## Evidence

Multiple real-world repos (langflow, dify, openclaw, OpenHands) have explicit "error normalization / information hiding" patterns — stripping internals from error messages to prevent leaks. They're solving this at the application layer because their frameworks don't handle it.

## Design Questions

1. **Error objects as labeled values** — When an operation throws and the error message contains a labeled value, should the error object inherit those labels? This seems like the natural extension of propagation semantics.
2. **Which labels propagate?** — All labels, or only security-critical ones (secret, sensitive, pii)? Propagating src:mcp through errors could be noisy.
3. **Display of labeled errors** — If an error inherits `secret`, and `secret → deny op:show`, does the error get suppressed entirely? Redacted? Replaced with a generic message? The developer needs to debug, but the user shouldn't see the secret.
4. **Error boundary guards** — Should there be an `on-error` guard type that can intercept, sanitize, or re-label errors before they propagate?
5. **Stack traces** — Stack traces can contain argument values. Does taint propagation extend into stack frame metadata?
6. **Interaction with audit ledger** — Should error events that involve labeled data be logged with the labels? (Probably yes — forensic value.)
7. **Performance** — Error paths are cold paths, so overhead is less concerning, but error construction in hot loops could matter.

## Deliverable

A spec section covering error taint semantics, display behavior, guard interaction, and audit integration.

