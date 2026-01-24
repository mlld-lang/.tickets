---
id: mlld-08i
status: closed
deps: []
links: []
created: 2025-12-12T18:08:47.706368-08:00
type: bug
priority: 0
---
# Strings incorrectly flagged as invalid in strict mode

String literals like "⚠️ Will respond" and "📧 Invoking" marked as errors with red squiggly. Strict mode should allow strings/templates, only bare text lines are invalid. False positive from overly aggressive text detection. Seen in: orchestrate.mld lines 48, 50

## Notes

Fixed by same change as mlld-kks - checkStrictModeTextNodes now only checks top-level Text nodes


