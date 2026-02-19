---
id: m-5e1b
status: open
deps: []
created: 2026-02-19T06:17:50Z
type: task
priority: 2
assignee: Adam Avenir
---
# ## Problem

Phase 3 (Revise) generates a '## Revision Citations' section at the end of each revised doc. When Phase 4 writes back to the original location, this section pollutes the actual documentation.

## Options

1. **Strip during write-back**: In Phase 4, regex-strip everything from '## Revision Citations' to EOF before copying. Simple, no prompt changes needed.

2. **Write citations to separate JSON report**: Have the revise prompt write citations to a `@runDir/citations/@flatTarget.json` file instead of appending to the doc. More useful for auditing but requires prompt changes.

3. **Both**: Strip from the doc AND write a separate report.

## Recommendation

Option 3. The citations are valuable for audit but don't belong in the docs. The revise prompt should write a separate citations file, AND the write-back should strip as a safety net in case the prompt doesn't comply.

