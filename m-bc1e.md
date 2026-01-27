---
id: m-bc1e
status: closed
deps: []
links: []
created: 2026-01-25T12:43:18Z
type: bug
priority: 1
assignee: Adam Avenir
tags: [interpreter, parallel, for-loop]
---
# output directive silently fails inside for parallel blocks

When using `output` inside a `for parallel` block, files are not written but no error is thrown.

## Reproduction

```mlld
var @items = ["x"]
var @results = for parallel(1) @item in @items [
  var @obj = { topic: @item, complete: false }
  output @obj to "./tmp/parallel-json-@item.json"
  => @item
]
show \`Done: @results.length\`  // Shows 1
// But ./tmp/parallel-json-x.json does NOT exist
```

## Expected
File should be written.

## Actual  
File is not created, no error thrown. Loop returns correct count.

## Impact
This breaks qa.mld which relies on output inside parallel for loops to write session.json and transcript files.


## Notes

**2026-01-25T12:55:25Z**

## Investigation Findings

Partially confirmed. The output directive runs inside for parallel, but there may be serialization issues with inline object literals.

The agent found that inline object literals like `output { item: @item } to "file"` may produce `[object Object]` instead of JSON.

Workaround: Use a let binding first:
```mlld
let @data = { item: @item }
output @data to "file"
```

Root cause likely in `/Users/adam/dev/mlld/interpreter/eval/output.ts` in `evaluateOutputSource` function.

Note: Our manual testing showed files not being created at all in some cases, which may indicate additional issues beyond serialization.

**2026-01-25T18:29:11Z**

## Fixed

The output issue was related to the same `isExpression` context suppression as the show bug. With the fix for m-3e35 (adding `allowEffects` flag), output now works correctly inside for-expression blocks.

Verified working:
- output creates directories automatically (mkdir -p behavior)  
- output writes JSON files correctly
- Works in both arrow and block forms
- Works in parallel for loops
