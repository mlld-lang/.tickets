---
id: m-71bc
status: closed
deps: []
created: 2026-02-18T06:56:12Z
type: task
priority: 2
assignee: Adam Avenir
updated: 2026-02-18T07:04:11Z
---
# Commit d2d2655ce changed the alias resolver from `bash -ic` to `BASH_ENV=~/.bashrc bash -c` to fix test hangs (interactive bash calls tcsetpgrp() which sends SIGTTIN in non-foreground process groups like vitest workers, causing 'zsh: suspended (tty input)'). But this broke alias/PATH resolution for zsh users — `~/.bashrc` doesn't contain zsh PATH entries, so commands like `claude` at `~/.local/bin/claude` aren't found. Real tension: `bash -i` hangs tests, `BASH_ENV=~/.bashrc` doesn't work for zsh. Need a solution that handles both — options include: (1) detect user shell and source appropriate rc file, (2) probe common paths directly, (3) use `command -v` in a login shell, (4) inherit the parent process PATH instead of resolving aliases. Original fix: f184ecdde. Regression: d2d2655ce. Symptoms: 'Command not found: claude' when sh {} blocks try to use commands only available via user's zsh PATH.


**2026-02-18 07:39 UTC:** Root cause found: NOT alias resolution. Two issues: (1) grammar rejected 'when first' as hard parse error, breaking @mlld/claude module — fixed by accepting silently. (2) The actual review-docs failure was `@claude()` in verify.att template being interpolated as an executable invocation instead of literal text — fixed with \@ escape. Opened m-b046 (p0) for template validation in mlld validate to catch this class of bug statically.
