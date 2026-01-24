---
id: mlld-y6ah
status: open
deps: []
links: []
created: 2026-01-11T14:23:06.91241-08:00
type: feature
priority: 2
---
# Better error when comments used inside array/block literals

Comments (>>) inside arrays or blocks cause parse errors with no helpful message. Users expect inline comments to work. Either support them or provide a clear error explaining comments must be before the construct, not inside.


