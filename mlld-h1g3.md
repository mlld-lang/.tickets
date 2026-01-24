---
id: mlld-h1g3
status: closed
deps: [mlld-0vps, mlld-3sc5]
links: []
created: 2026-01-03T02:59:04.184956-08:00
type: task
priority: 3
---
# Improve codebase audit cookbook example after bug fixes

Once these bugs are fixed, update the codebase audit cookbook example (Recipe 8) to use cleaner syntax:

## Current Status (2026-01-03)

**Partial fix implemented in commit 72d7b33c3:**
- Direct `@f.mx.relative` access now works in for loops (both sequential and parallel)
- The `isPipelineInput2` error is fixed

**Still broken:**
1. Object literals in for expressions: `for @f in @files => { file: @f.mx.relative }` fails
2. Exe function parameters: `@getFile(@f)` where exe accesses `@f.mx.relative` fails

## Investigation Details

### What Works Now

```mlld
var @files = <../core/utils/*.ts>

# Direct inline access - WORKS
var @paths = for @f in @files => @f.mx.relative
# Returns: ["./core/utils/file1.ts", "./core/utils/file2.ts"]

# Direct in parallel - WORKS
var @paths = for parallel(2) @f in @files => @f.mx.relative
# Returns: ["./core/utils/file1.ts", "./core/utils/file2.ts"]
```

### What Still Fails

```mlld
# Object literal in for expression - FAILS
var @reviews = for parallel(2) @f in @files => {
  file: @f.mx.relative,  # Error: Field "mx" not found in LoadContentResult
  review: `ok`
}

# Exe function parameter - FAILS
exe @getFile(f) = @f.mx.relative
var @r = for parallel(2) @f in @files => @getFile(@f)
# Returns file CONTENT instead of path - @f is stringified when passed to exe
```

## Root Cause Analysis

### The Data Flow

1. Glob `<*.ts>` creates a StructuredValue array where `.data` contains LoadContentResult objects
2. Each LoadContentResult has: `content`, `filename`, `relative`, `absolute`, `_extension`, etc.
3. The StructuredValue's `.mx` property mirrors these: `mx.relative`, `mx.filename`, etc.

### Where Metadata Gets Lost

**Path 1: normalizeIterableValue (for-utils.ts)**
- When iterating, `normalizeIterableValue()` processes the array
- For StructuredValue arrays, it calls `asData(value)` which extracts the `.data` array
- The `.data` array contains LoadContentResult objects (plain objects with file props)
- These objects are then passed to `ensureVariable()`

**Path 2: ensureVariable (for.ts:46-129)**
- Fixed to copy file metadata to `.mx` for LoadContentResult and file-like objects
- This works for direct `@f.mx.relative` access

**Path 3: Object literal evaluation**
- When evaluating `{ file: @f.mx.relative }`, the object literal is evaluated in a context
- The variable `@f` is resolved, but somewhere in this resolution chain, `.mx` is lost
- The error shows: `Field "mx" not found in LoadContentResult`
- This suggests the object literal evaluator gets the raw LoadContentResult, not the Variable wrapper

**Path 4: Exe function parameter passing**
- When `@f` is passed to an exe function, it gets stringified to the file content
- The exe function receives just the string, not the full object with metadata
- This is by design for "pass by value" semantics, but breaks metadata access

## Key Code Locations

1. **for.ts:46-129** - `ensureVariable()` - where Variables are created for loop iteration
2. **for.ts:340** - `const iterationVar = ensureVariable(varName, value, env)` - main call site
3. **for-utils.ts:22-26** - `normalizeIterableValue()` - where StructuredValue is unwrapped
4. **field-access.ts:215-220** - where `.mx` is resolved for Variables
5. **exec-invocation.ts** - where exe function parameters are processed

## Debug Traces That Helped

```
[for.ts] iterableArray length: 1
[for.ts] first entry key: 0 value type: object
[for.ts] first entry value keys: [ 'content', 'filename', 'relative', 'absolute', '_extension' ]
[for.ts] runOne entry, key: 0 value type: object
[for.ts] runOne value keys: [ 'content', 'filename', 'relative', 'absolute', '_extension' ]
[for.ts] BEFORE ensureVariable, value type: object
[for.ts] BEFORE ensureVariable, value keys: [ 'content', 'filename', 'relative', 'absolute', '_extension' ]
[for.ts] ensureVariable START, value type: object isVariable: false isLoadContentResult: true isStructuredValue: false
```

This shows the value is a LoadContentResult (not a Variable or StructuredValue) when it reaches ensureVariable.

## Potential Fix Approaches

### Approach 1: Fix object literal evaluation
- In the object literal evaluator, when resolving `@f.mx.relative`, ensure the Variable's `.mx` is used
- Location: likely in `interpreter/eval/object.ts` or similar

### Approach 2: Keep StructuredValue wrapper through iteration
- Instead of `asData(value)` in normalizeIterableValue, preserve the StructuredValue
- Risk: breaks other code that expects unwrapped data
- Tried this: caused test failures (keep-in-loops test expected text content, got full JSON)

### Approach 3: Fix exe parameter passing
- When passing a Variable to an exe function, preserve metadata
- Location: `exec-invocation.ts` parameter binding

### Approach 4: Document workaround pattern
- Current cookbook pattern works: extract metadata before parallel loop
- Document this as the recommended pattern until full fix

## Files Changed in Partial Fix

- `interpreter/eval/for.ts` - Added looksLikeFileData(), enhanced ensureVariable()
- `interpreter/utils/interpolation.ts` - Fixed isPipelineInput import path

## Test Commands

```bash
# Test direct .mx access (works)
echo 'var @files = <../core/utils/ansi-*.ts>
var @paths = for parallel(2) @f in @files => @f.mx.relative
show @paths' > tmp/test.mld
./dist/cli.cjs tmp/test.mld

# Test object literal (fails)
echo 'var @files = <../core/utils/ansi-*.ts>
var @r = for @f in @files => { file: @f.mx.relative }
show @r' > tmp/test.mld
./dist/cli.cjs tmp/test.mld

# Test exe function (fails - returns content instead of path)
echo 'exe @getFile(f) = @f.mx.relative
var @files = <../core/utils/ansi-*.ts>
var @r = for @f in @files => @getFile(@f)
show @r' > tmp/test.mld
./dist/cli.cjs tmp/test.mld
```

## Why Object Literal Fails But Direct Access Works

The key insight is that direct `@f.mx.relative` works because:
1. The for loop sets the Variable with `.mx` containing file metadata
2. When interpolating `@f.mx.relative`, field-access.ts properly resolves the Variable's `.mx`

But in object literal `{ file: @f.mx.relative }`:
1. The object is being constructed with evaluated expressions
2. Somewhere in this evaluation, `@f` is being resolved to its VALUE (LoadContentResult)
3. Then `.mx` is accessed on the LoadContentResult, not the Variable
4. LoadContentResult has `relative` at top level, not under `.mx`

The fix likely needs to ensure that when evaluating expressions inside object literals in for loops, variable references preserve their Variable wrapper rather than being unwrapped to raw values.

## Related Issues

- Depends on (now closed): mlld-3sc5 (isPipelineInput2 error), mlld-0vps (parallel for .mx)
- These are fixed, but the full cookbook improvement requires fixing object literal and exe param cases


