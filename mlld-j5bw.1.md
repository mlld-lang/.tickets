---
id: mlld-j5bw.1
status: closed
deps: []
links: []
created: 2026-01-14T21:36:39.659668-08:00
type: task
priority: 1
parent: mlld-j5bw
---
# C1a: Add /needs + /wants to existing modules and env capture template

## Summary

Prepare existing modules for C1 enforcement by adding explicit /needs and /wants declarations to all importable modules (registry + templates).

## Rationale

C1 enforcement breaks existing modules unless they declare /needs and /wants. This migration keeps behavior stable while enabling the capability contract.

## Scope

- Add `/needs { }` + `/wants [ ]` to every registry module under `registry/modules/` that lacks them
- Update env capture template in `cli/commands/env.ts` to emit `/needs` + `/wants`
- Ensure helper modules in D1 include declarations

## Exit Criteria

- [ ] All registry modules include /needs + /wants declarations
- [ ] Env capture template includes /needs + /wants
- [ ] Tests/fixtures updated as needed

## Notes

Block enabling C1 enforcement until this completes.


