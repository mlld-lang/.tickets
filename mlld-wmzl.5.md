---
id: mlld-wmzl.5
status: closed
deps: [mlld-wmzl.4]
links: []
created: 2026-01-16T07:18:53.192916-08:00
type: task
priority: 1
parent: mlld-wmzl
---
# Phase 5: src:* source labels at entry points

## Goal

Auto-apply `src:*` labels based on data provenance at all entry points. This tracks where data CAME FROM.

## Source Label Mapping

| Entry Point | Label | Current Status |
|-------------|-------|----------------|
| MCP tool calls | `src:mcp` | ✅ Working (`FunctionRouter.ts`) |
| Dynamic modules | `src:dynamic` | ✅ Working (`ImportDirectiveEvaluator.ts`) |
| Command execution output | `src:exec` | ❌ Add |
| File reads | `src:file` | ❌ Add |
| Network fetches | `src:network` | ❌ Add |
| User input | `src:user` | ❌ Add |
| Jail output paths | `src:jail` | ❌ Add |

## Implementation

### 1. Command Execution Output

**File**: `interpreter/eval/run.ts`

After command execution, tag output with `src:exec`:

```typescript
const result = await executeCommand(command);
return {
  value: result.stdout,
  security: makeSecurityDescriptor({
    labels: [],
    taint: ['src:exec'],
    sources: [\`cmd:\${command}\`]
  })
};
```

### 2. File Reads

**File**: `interpreter/eval/file.ts` (or wherever file loading happens)

```typescript
const content = await readFile(path);
return {
  value: content,
  security: makeSecurityDescriptor({
    labels: [],
    taint: ['src:file'],
    sources: [\`file:\${path}\`]
  })
};
```

### 3. Network Fetches

**File**: `interpreter/url-support.ts` or `ImportResolver.ts`

```typescript
const response = await fetch(url);
return {
  value: response.text,
  security: makeSecurityDescriptor({
    labels: [],
    taint: ['src:network'],
    sources: [\`url:\${url}\`]
  })
};
```

### 4. Jail Output Paths (via guard)

Data read from jail output directories should be labeled:

```mlld
/guard before op:file = when [
  @mx.op.path.match("/agents/*/outbox/*") =>
    allow @input with { addLabels: ["src:jail"] }
  * => allow
]
```

This requires Phase 6 (guard label modification) to be complete.

## Runtime Introspection

Ensure `@value.mx.labels` and `@value.mx.sources` are accessible for debugging:

```mlld
show @data.mx.labels   // ["secret", "pii"]
show @data.mx.sources  // ["mcp:fetchData", "cmd:git"]
```

**File**: `interpreter/eval/template.ts`

The template interpolation logic needs to expose `.mx` properties on values.

## Exit Criteria

- [ ] Command output tagged with `src:exec`
- [ ] File content tagged with `src:file`
- [ ] Network responses tagged with `src:network`
- [ ] `@value.mx.labels` accessible in templates
- [ ] `@value.mx.sources` accessible in templates
- [ ] Tests verify source labels propagate through transforms

## References

- Spec Section 1.2: Source Labels (Auto-Applied)
- Spec Section 1.4: Data Label Context
- Plan Phase 5: `todo/plan-security-2026-v3.md` lines 493-530

## Estimated Effort
~3 hours


