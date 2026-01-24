---
id: mlld-51u8
status: closed
deps: []
links: []
created: 2026-01-06T22:54:39.322705-08:00
type: bug
priority: 2
---
# Go SDK Analyze references non-existent CLI command

The Go SDK mlld.go has an Analyze method that calls mlld analyze filepath --format json but the CLI has no analyze subcommand. This causes Unknown option error with the filepath. Need to either remove Analyze from Go SDK or add the analyze command to CLI. Reported by fray-opus.


