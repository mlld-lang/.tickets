---
id: mlld-emu
status: closed
deps: []
links: []
created: 2025-12-09T07:50:59.202687-08:00
type: feature
priority: 2
---
# CLI: add --payload alias for --inject flag

**Reported by:** partydev during SDK dogfooding

**Request:**
Add `--payload` as an alias for `--inject` in the CLI for consistency with the SDK.

**Rationale:**
- SDK uses `execute(file, payload)`
- CLI uses `--inject`
- For consistency, `mlld file.mld --payload '{...}'` would be more intuitive

**Implementation:**
Add alias in ArgumentParser - straightforward change.


