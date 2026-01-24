---
id: mlld-yd9
status: closed
deps: [mlld-76c]
links: []
created: 2025-12-13T05:32:41.102029-08:00
type: bug
priority: 0
---
# VERIFY FIXED: Function declaration position with datatype labels

Function declarations like exe @evaluateAgent(agent, msg, turnP) have multiple issues:
1. Function name highlighting starts 2 characters late
2. Opening/closing parentheses not highlighted
3. Function arguments not tokenized/highlighted

Example: exe @evaluateAgent(agent, msg, turnP) = [...]
- @evaluateAgent highlighting offset by 2 chars
- ( and ) not highlighted
- agent, msg, turnP not highlighted as parameters

## Notes

Fixed handleVariableDeclaration to search for @ position instead of calculating offset. Now handles 'exe datatype @func()' correctly. Need to verify in editor.


