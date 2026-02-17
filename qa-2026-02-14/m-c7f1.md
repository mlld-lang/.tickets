---
id: m-c7f1
status: closed
deps: [m-1926, m-2e74]
created: 2026-02-15T08:11:17Z
type: chore
priority: 3
tags: [qa-2026-02-14, docs, guards]
updated: 2026-02-15T11:02:44Z
---
# Docs: security-before-guards/guards-basics - timing comparison

before LABEL (fires at data creation, once) vs before op:TYPE (fires at operation time, each use) distinction not emphasized. security-before-guards doc is a stub.

Denied handler limitation: can't catch before LABEL denials because data doesn't exist yet at that point. This is inherent to the design, not a bug -- just needs to be documented.

QA topics: security-before-guards, security-guards-basics

## Acceptance Criteria

Timing comparison table added, denied handler scope documented, stub filled or redirected
