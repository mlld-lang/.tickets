---
id: m-5acf
status: open
deps: []
links: []
created: 2026-02-24T10:19:31Z
type: task
priority: 2
assignee: Adam
tags: [docs, atoms]
---
# Make atom ordering less brittle

CATEGORIES config in docs/build/categories.js hardcodes every atom filename. When atoms get renamed/added/removed, the config breaks silently. Options: (1) add order field to atom frontmatter and derive grouping from parent field, (2) keep CATEGORIES but add build-time validation that all atoms in each category dir are accounted for, or (3) both.

