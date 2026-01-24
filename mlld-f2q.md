---
id: mlld-f2q
status: closed
deps: []
links: []
created: 2025-12-11T04:11:07.083517-08:00
type: task
priority: 2
---
# Harden path resolution story across the board for monorepo setups

Inconsistent where various parts of the codebase think things are rooted

We need to find the way different parts of the codebase root paths and what assumptions and fallbacks they use

Then we need to make sure these work completely consistently in cases where a dir is inside another dir that possesses qualities that might make other parts of the codebase infer paths differently.

Having `/monorepo/.git` seems to set where `mlld-config.json` gets written if `mlld setup` is run in `/monorepo/subproject` -- instead of writing/updating the new mlld-config.json in subproject, it writes/updates it at the root or at leaset SEEMS to.

This will be a research task first, then a discussion to decide how to reconcile and standardize for reasonably expected UX.


