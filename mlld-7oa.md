---
id: mlld-7oa
status: open
deps: []
links: []
created: 2025-12-18T08:58:34.850001-08:00
type: task
priority: 3
tags: [size-s, complexity-s, risk-s, impl-none]
---
# Better error/docs for template collection export limitations

Users try to export template collections from modules and get confusing errors:

```mlld
/import templates from "./prompts" as @prompts(msg, ctx)
/export { @prompts }  // Error: Variable reference @msg not found
```

The error is misleading - the issue is that template collections with parameters can't be exported from modules because the parameters aren't available at import time.

**Solutions:**
1. Better error message explaining this limitation
2. Documentation showing the right pattern (import templates at top level, not in modules)
3. Or support wrapping in /exe for export:
   ```mlld
   /exe @prompts.greeting(msg, ctx) = @templates["greeting"](@msg, @ctx)
   /export { @prompts.greeting }
   ```

Identified by @partydev during scaffold work.


