---
id: m-f3c6
status: closed
deps: []
created: 2026-02-25T19:15:37Z
type: chore
priority: 3
assignee: codex
updated: 2026-02-27T06:48:40Z
---
# Refactor FileProcessor to reduce duplication

FileProcessor.ts mixes concerns and has duplicated interpreter setup across multiple code paths (file, stdin, piped modes). Extract a shared createInterpreter(options) helper to consolidate setup logic. Low urgency but reduces regression risk.

