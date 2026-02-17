---
id: m-6a3f
status: open
deps: []
created: 2026-02-17T01:55:09Z
type: chore
priority: 0
assignee: Adam
---
# Audit and clean ALL-CAPS annotation comments and commented-out code

40+ ALL-CAPS annotation comments (// IMPORTANT:, // FIXED:, // GOTCHA:, // REMOVED:) throughout the codebase read like AI scaffolding. Also scattered commented-out code blocks in ErrorHandler.ts, EmbeddedLanguageService.ts, JavaScriptExecutor.ts, and others. Audit all instances and either convert to normal sentence-case comments where the comment adds value, or delete.

