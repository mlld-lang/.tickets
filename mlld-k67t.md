---
id: mlld-k67t
status: closed
deps: []
links: []
created: 2026-01-06T22:54:45.256501-08:00
type: bug
priority: 3
---
# Go SDK README mentions DurationMs but field doesnt exist

Go SDK README shows execResult.DurationMs but the actual ExecuteResult struct has Metrics.TotalMs instead. Also CLI structured output doesnt include metrics. Need to fix README to match actual API. Reported by fray-opus.


