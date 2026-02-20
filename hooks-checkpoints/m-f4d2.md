---
id: m-f4d2
status: open
deps: []
created: 2026-02-20T16:05:53Z
type: task
priority: 2
assignee: Adam Avenir
tags: [severity-medium, pre-ga]
updated: 2026-02-20T16:05:59Z
---
# Document mlld module / mlld mod command

## Problem

The mlld module (alias: mlld mod) command for creating modules is registered in CommandDispatcher.ts but entirely absent from docs/user/cli.md. Related to H5 (m-c7d0) where init docs incorrectly describe module creation flags, but this is the separate gap of module having no docs at all.

## Work

Add a mlld module / mlld mod section to docs/user/cli.md documenting:
- Purpose: create a new mlld module
- Options: --name, --author, --about, --version, --keywords, --homepage, --skip-git, --force
- Examples

