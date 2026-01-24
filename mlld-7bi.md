---
id: mlld-7bi
status: open
deps: []
links: []
created: 2025-12-17T08:30:10.232247-08:00
type: feature
priority: 2
---
# Detect and warn about --output-format json without streaming

When using --output-format json with the Claude CLI helper without streaming support, it causes JavaScript heap OOM. We should detect this configuration and either: (1) suggest adding 'stream' keyword to the function, or (2) provide a clear error explaining that large JSON responses need streaming. See docs/dev/ERRORS.md for error enhancement patterns.


