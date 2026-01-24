---
id: mlld-6gn
status: closed
deps: [mlld-76c]
links: []
created: 2025-12-12T21:19:49.360719-08:00
type: bug
priority: 0
---
# orchestrate.mld tokenization gaps - retest with new validator

Need to retest with improved validator (requireExactType now working).

Previous: 15 gaps at 93.2% coverage
After validator improvements: Need to rerun to see real gaps vs false positives

The enhanced validator now correctly distinguishes:
- Error gaps (missing tokens) vs warning gaps (wrong token type)
- Only counts error gaps in coverage percentage
- Shows exact diagnostic info for why tokens are missing

Run: npm run dump:tokens orchestrate.mld -- --diagnostics

## Notes

Improved from 93.2% → 97.7% coverage using diagnostic system. Remaining 5 gaps: function args not visited (1), variables in run{} shell strings (4 - not in AST). Diagnostic system shows exact root causes.


