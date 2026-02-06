---
id: m-2d1f
status: closed
deps: []
created: 2026-01-29T21:42:58Z
type: bug
priority: 1
assignee: Adam
updated: 2026-01-30T00:29:03Z
---
# output directive includes structured value wrapper in JSON output

When objects are written via `output @obj to "path"`, the JSON includes the internal structured value wrapper ({type, text, data, metadata}) instead of just the actual data.

**Reproduction:**
In execute.mld, results array contains objects that get wrapped:
```json
{
  "type": "object",
  "text": "{...}",
  "data": { "ticket_id": "m-452a", "status": "needs-review", ... },
  "metadata": { "isStructuredValue": true, ... }
}
```

**Expected:** Only the `.data` contents should be written to JSON.

**Works correctly:**
- `append` to JSONL files writes clean data
- `events.jsonl` has no wrapping

**Breaks:**
- `output @results to "path"` includes wrapper

**Likely location:** interpreter/output/normalizer.ts

**Impact:** Summary counts in polish pipeline show 0 because filters check `.ticket_id` which is nested inside `.data` instead of at top level.

