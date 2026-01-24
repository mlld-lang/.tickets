---
id: mlld-4yq
status: closed
deps: []
links: []
created: 2025-12-17T08:30:09.986607-08:00
type: feature
priority: 1
---
# Better error for missing @ prefix in pipeline filters

When user writes '| json' instead of '| @json', the parse error isn't clear. We should detect this pattern (| followed by a bare word that matches a known filter name) and provide a helpful error like: 'Did you mean | @json? Filters require the @ prefix.'


