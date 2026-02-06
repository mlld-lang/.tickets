---
id: m-ab0a
status: closed
deps: [m-bb95, m-cd57]
created: 2026-02-05T03:10:07Z
type: feature
priority: 2
assignee: Adam
updated: 2026-02-05T09:08:41Z
---
# Security event log: only conflict events logged

**Problem**: Audit log infrastructure exists but only 1 of 6 event types is actually logged.

**What works**:
- `AuditLogger.appendAuditEvent()` writes to .mlld/sec/audit.jsonl ✓
- Trust conflict events are logged ✓

**What's missing** (5 event types):
- sign/verify events (not logged in sign-verify.ts)
- label/bless events (not logged in label-modification.ts)
- write events (not logged in output.ts)

**File taint tracking missing**:
- Write should record: file path + taint + writer
- Read should inherit taint from file

**Expected**: All security events logged to JSONL. Users query with standard tools (jq, grep) or read from scripts.

**Files**: Missing calls in sign-verify.ts, label-modification.ts, output.ts

## Plan

- Expand `AuditEvent` type to support all 6 event types with consistent fields:
  - `sign`: var, hash, by
  - `verify`: var, result, caller
  - `label`: var, add, remove, by (include add/remove arrays for non-bless modifications)
  - `bless`: var, add, remove, by (for trusted/untrusted changes via guards)
  - `conflict`: var, labels, resolved
  - `write`: path, taint, writer
- Consider adding version field (`v: 1`) for schema evolution or keep implicit
- Add `appendAuditEvent()` calls in:
  - `interpreter/eval/sign-verify.ts` for sign/verify events
  - `interpreter/eval/label-modification.ts` for label/bless events (currently only logs conflicts)
  - `interpreter/eval/output.ts` for write events with taint
- Implement file taint tracking:
  - Record `write` events with file path + taint state
  - Wire reads in `interpreter/eval/content-loader.ts` and `interpreter/env/ImportResolver.ts` for local files
  - On file read, lookup most recent write event and inherit taint
  - Use in-memory index refreshed on audit log changes to avoid scanning JSONL on every read
  - Apply to local filesystem reads and resolver reads when materialized to local paths
- Add tests verifying all 6 event types are logged with correct fields
- Update docs showing JSONL format and examples of querying with jq/grep or reading from scripts
- No CLI commands needed - users use standard JSONL tools
