---
id: m-a1d7
status: closed
deps: []
created: 2026-02-20T16:05:53Z
type: bug
priority: 3
assignee: Adam Avenir
tags: [severity-low, polish]
updated: 2026-02-24T19:46:50Z
---
# Fix default output format inconsistency between docs and code

## Problem

`docs/user/cli.md:25` says:

```
--format, -f <format> - Output format: `xml` (default), `md`
```

But the code defaults to `markdown`. Both `cli/parsers/OptionProcessor.ts:172` and `cli/parsers/ArgumentParser.ts:439` have:

```typescript
private normalizeFormat(format?: string): 'markdown' | 'xml' {
  if (!format) return 'markdown';
```

The code is correct — `markdown` is the intended default.

## Required change

Update `docs/user/cli.md:25` to say `md` (default) instead of `xml` (default):

```
--format, -f <format> - Output format: `md` (default), `xml`
```

Also check and fix the same claim if it appears in:
- `docs/llm/llms-cli.txt`
- `docs/src/` (website source atoms — check `docs/src/atoms/configuration/cli-run.md` or similar)
- `spec-hooks-checkpoints-resume.md`

## Acceptance criteria

- All docs that mention the `--format` flag's default value say `md`, not `xml`.
- No code changes needed — the code is already correct.

