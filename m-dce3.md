---
id: m-dce3
status: open
deps: []
links: [m-3662, mlld-bgl]
created: 2026-02-09T06:20:24Z
type: feature
priority: 3
assignee: Adam Avenir
tags: [security, needs-human-design, module]
---
# Design: Filesystem boundary guards (@mlld/guards/fs)

## Task

Design a standard guard module for filesystem path validation outside of sandboxed environments. This is a DESIGN task — produce a module spec, not an implementation.

## Layer

Standard library module (@mlld/guards/fs), NOT core engine. Environments already handle path restrictions for sandboxed execution. This module covers non-sandboxed scripts that still need path boundary enforcement.

## Problem

Path traversal appeared in 6+ real-world AI agent repos. The universal pattern is normalize-resolve-enforce: resolve symlinks, normalize the path, confirm the resolved path is within allowed boundaries. mlld's `env` blocks handle this with `fs: { read, write }`, but scripts not running in a sandboxed environment have no path boundary enforcement.

Note: there's an existing ticket mlld-bgl for policy-based working directory restrictions. This ticket is about a broader filesystem guard module that covers read/write paths, not just cwd.

## Evidence

openclaw, browser-use, langflow, semantic-kernel, llama_index, SWE-agent all have path traversal defense patterns. The browser-use pattern (basename normalization) and openclaw pattern (resolve-then-verify) are representative.

## Design Questions

1. **Guard API** — Strawman:
   ```mlld
   import { @fsBoundary } from "@mlld/guards/fs"
   guard @restrictFs before op:fs = @fsBoundary({
     read: ["/app", "/tmp"],
     write: ["/tmp"],
     resolveSymlinks: true
   })
   ```
2. **Symlink resolution** — Resolve before boundary check? This is critical — without it, a symlink from /tmp/safe → /etc/passwd bypasses the check. Should resolution be default-on?
3. **Glob patterns vs prefix matching** — `"/app/**"` (glob) vs `"/app"` (prefix)? Globs are more expressive but prefix matching is simpler and harder to misconfigure.
4. **Deny patterns** — Should the guard support explicit deny in addition to allow? E.g., deny `~/.ssh/**`, `~/.gnupg/**`, `/etc/shadow` regardless of other rules.
5. **Relationship to env fs config** — Should the guard's API mirror environment `fs: { read, write }` syntax for consistency?
6. **Relationship to mlld-bgl** — That ticket covers cwd restrictions in policy. This covers read/write path restrictions as a guard module. Should they share syntax/semantics? Should this module subsume mlld-bgl's scope?
7. **Taint interaction** — If an untrusted-labeled value is used as a file path, should the guard apply extra scrutiny? Or is the path itself the concern regardless of taint?

## Deliverable

Module API spec: guard signatures, configuration options, symlink handling, and relationship to environment fs config.

