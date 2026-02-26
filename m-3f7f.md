---
id: m-3f7f
status: closed
deps: []
created: 2026-02-25T06:21:43Z
type: bug
priority: 0
assignee: Adam Avenir
updated: 2026-02-25T17:42:14Z
---
# mlld publish must read YAML frontmatter from module files

## Bug

`mlld publish` does not read existing YAML frontmatter from the module file being published. Users put metadata in their module's frontmatter, but publish ignores it and either uses defaults or requires CLI flags.

## Reproduction

Create a module with frontmatter:
```mlld
---
title: My Utility
description: Helper functions for string processing
version: 1.2.0
tags: [utils, strings]
author: someone
---
exe @trim(s) = js { return s.trim() }
export { @trim }
```

Run `mlld publish my-module.mld`. The published registry entry does not use the title, description, version, or tags from the frontmatter.

## Acceptance criteria

1. `mlld publish module.mld` reads YAML frontmatter from the module file before publishing
2. These frontmatter fields are used as defaults for registry entry metadata: `title`, `description`, `version`, `tags`, `author`
3. CLI flags override frontmatter values when both are present (CLI wins, frontmatter is the fallback)
4. If the module has no frontmatter, `mlld publish` behaves exactly as it does today (no regression)
5. All existing tests pass: `npm test`

## Where to look

The publish command implementation in `cli/commands/` (likely `publish.ts` or similar). Find where registry metadata is assembled and add frontmatter reading as a fallback source before that step.
