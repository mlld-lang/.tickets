---
id: mlld-8jer
status: closed
deps: []
links: []
created: 2026-01-02T23:56:45.554119-08:00
type: bug
priority: 2
---
# Import version syntax @module@version not parsing in strict mode

Version specifier syntax in imports like `import { @helper } from @alice/utils@1.0.0` fails to parse in strict mode with 'Text content not allowed' error at the version suffix.

The grammar appears to have support for this (id.hash in import.peggy line 616) but it's not working correctly. The `@1.0.0` part is being parsed as prose/text.

Documented in docs/user/modules.md - currently converted to table format as workaround.

Repro:
```bash
echo 'import { @helper } from @alice/utils@1.0.0' > /tmp/test.mld
mlld /tmp/test.mld
# Parse Error at column 37
```


