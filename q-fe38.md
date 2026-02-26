---
id: q-fe38
status: open
deps: []
created: 2026-02-25T19:45:58Z
type: task
priority: p3
assignee: Adam Avenir
---
# Doc test blocks that use file loading (<README.md>, <docs/*.md>) can't produce deterministic expectations because they depend on real filesystem content. Two approaches discussed: (1) Add @test resolver so examples use <@test/README.md>, (2) Add files.json config per hash dir that tells the build what files to make available. Option 2 is cleaner — doesn't clutter doc examples. For now these blocks are skipped.


**2026-02-25 19:46 UTC:** Skipped blocks: quickstart/bb9d887b (<docs/*.md>), quickstart/f9b0f1be (<README.md>, <package.json>), quickstart/e428efa5 (pwd/ls)

**2026-02-25 21:04 UTC:** Sibling ticket: m-63d5 (SDK payload injection for context-dependent blocks). q-fe38 covers file-loading deps, m-63d5 covers undefined context variable deps.
