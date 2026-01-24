---
id: mlld-6fr
status: closed
deps: []
links: []
created: 2025-12-18T10:20:55.059639-08:00
type: bug
priority: 1
---
# Pipeline: Builtin transformers (@upper, @lower, @trim) not working

Builtin transformers like @upper, @lower, @trim fail in pipeline expressions with 'Pipeline command not found' error.

**Reproduction:**
```mlld
/var @x = "hello" | @upper
/show @x
```

**Error:** `Pipeline command upper not found`

**Root cause:** Two different pipeline code paths exist:
- unified-processor.ts checks isBuiltinTransformer() and includes builtins
- executor.ts:636 uses resolveCommandReference() which doesn't check builtin transformers

When pipelines execute via /show or /var, they hit executor.ts path which only looks for user-defined executables.

**Fix needed:** resolveCommandReference() should check isBuiltinTransformer(name) before returning null.

**Workaround:** Define transformations explicitly:
```mlld
/exe @upper(text) = js { return text.toUpperCase(); }
```

GitHub issue: #537


