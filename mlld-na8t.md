---
id: mlld-na8t
status: open
deps: []
links: []
created: 2025-12-20T15:14:51.922726-08:00
type: task
priority: 3
tags: []
updated: 2026-01-30T12:18:02Z
---
# DataValueEvaluator errors need better context in error messages

When DataValueEvaluator catches an error, the error message shown to users is truncated to 'DataValueEvaluator error:' without the actual cause. The errorContext object with the actual details (errorMessage, errorStack) is logged but may not be visible to users.

Reported by partydev - they saw:
```
Error: DataValueEvaluator error:
⚠️ 1 agent(s) failed
```

Without knowing which operation failed or why.

Potential improvements:
1. Include errorMessage in the thrown error
2. Show clearer context about which evaluation failed
3. Consider whether logger.error is the right logging level for debugging

## Notes

**2026-01-30**: Assessed as valid UX issue. Error messages should include the actual cause, not just "DataValueEvaluator error:".
