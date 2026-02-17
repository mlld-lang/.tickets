---
id: m-f1bb
status: closed
deps: []
created: 2026-02-15T08:10:38Z
type: chore
priority: 2
tags: [qa-2026-02-14, docs, templates]
updated: 2026-02-15T10:38:52Z
---
# Docs: templates-external fix syntax errors and pipe clarification

Fix factual errors in templates-external docs:
- Loop syntax shows for/end instead of /for//end
- Claims "for/when blocks" but .att only supports /for
- .mtt comparison table missing loops row

Pipes: .att templates SHOULD support pipes with condensed syntax (@var|@pipe) to avoid ambiguity. This is a feature gap -- doc should note condensed pipe syntax is needed in templates.

QA topic: templates-external

## Acceptance Criteria

Examples use correct /for//end syntax, comparison table accurate, pipe support clarified

**2026-02-15 10:38 UTC:** Updated template docs to use /for ... /end syntax, removed .att when-block claim, documented condensed template pipes (@var|@pipe), added .att/.mtt loops+pipes comparison rows, and synced generated docs fixture descriptions for content-and-data line shifts. Validation: npm test -- tests/cases/docs/content-and-data/ (suite passed: 385 files, 4163 tests). Commit: fe28bd3a9.
