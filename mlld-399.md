---
id: mlld-399
status: open
deps: []
links: []
created: 2025-12-17T08:30:10.46621-08:00
type: feature
priority: 2
tags: [size-m, complexity-m, risk-m, impl-partial]
---
# Add 'did you mean' checks for template path resolution

Users get confused about path resolution in mlld: 'tmp/file.txt' vs './file.txt' vs '@base/tmp/file.txt'. When ANY file is not found (templates, imports, file loads, etc.), check for similar paths in common locations and suggest alternatives. Add an explanatory error about how paths work in mlld (relative to execution context, use @base for project root). 

Examples:
- Template: 'File not found: tmp/prompt.att. Did you mean: @base/tmp/prompt.att?'
- Import: 'Module not found: utils.mld. Did you mean: ./utils.mld or @base/utils.mld?'
- File load: 'File not found: data/input.json. Did you mean: @base/data/input.json?'

Should apply to all file operations: /import, /exe template, <file>, etc.


