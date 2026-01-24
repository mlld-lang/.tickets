---
id: mlld-76c
status: closed
deps: [mlld-ghp, mlld-7t7, mlld-ktr, mlld-kks, mlld-08i, mlld-514, mlld-ddn, mlld-drm]
links: []
created: 2025-12-12T18:10:20.873684-08:00
type: epic
priority: 0
---
# LSP Semantic Token Highlighting - Major Progress

MASSIVE progress this session:

✅ Completed:
- Enhanced validator with requireExactType flag (96.2% coverage, 51 fixtures passing)
- Fixed variable position bugs (off-by-one/two, bracket expressions, exports)
- Parameters now gold colored and working everywhere
- Function call arguments highlighting (fixed computed property call bug)
- var/exe/guard/policy → pink italic (modifier semantic type)
- exe declarations → purple bold (function token type)
- var declarations → light blue bold (variable token type)
- Variables → light blue, Functions → purple italic (swapped from original)
- Embedded code → italic with variable interpolation support
- Language labels (js/py/sh/cmd) → teal (type semantic type)
- Security labels → Special color, tokenize for exe blocks

⚠️ Remaining P1 issues:
- mlld-og8: Action keywords (allow/deny/continue/retry/done)
- mlld-72k: Pipeline functions should highlight as functions
- mlld-6vh: 'with' keyword highlighting
- mlld-ccf: Guard condition highlighting
- mlld-r2c: run/show/output directives color distinction
- mlld-7v5: Language labels final color
- mlld-70b: Security labels final color
- mlld-6gn: orchestrate.mld remaining gaps
- mlld-6fq: Column-based calculation audit

Validator improvements documented in LSP-TOKEN-VALIDATOR.md

## Notes

Reopening with new issues found in production testing:

Progress:
- ✅ Variables now highlighting (added highlight groups)
- ✅ Diagnostic system built (shows WHY tokens missing)
- ✅ Basic tokenization working (100% on fixtures)
- ⚠️ Production files reveal nuance and quality issues

New blocking issues created:
- mlld-8sn: Parameter highlighting lost [P0]
- mlld-yd9: Function declarations off-by-two [P0]  
- mlld-sp9: Import variables not tokenized [P0]
- mlld-cvf: => operator needs distinction [P1]
- mlld-k53: cmd blocks not tokenized [P1]
- mlld-rxs: datatype labels need prominence [P1]
- mlld-a1h: Brackets need distinction [P1]
- mlld-buc: String color inconsistent [P1]
- mlld-cyz: Comment inconsistency [P2]
- mlld-3s8: parallel keyword highlighting [P1]
- mlld-6gn: orchestrate.mld coverage [P0]

Diagnostic system enables rapid fixes - each issue shows exact root cause.


