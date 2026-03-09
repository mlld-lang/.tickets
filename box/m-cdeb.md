---
id: m-cdeb
status: closed
deps: [m-3bea]
links: []
created: 2026-03-04T01:39:57Z
type: epic
priority: 2
assignee: Adam
tags: [phase-8, git, vfs]
updated: 2026-03-04T04:39:46Z
---
# Phase 8: Git-based workspace hydration

Spec: `_specs/box.md` (Part 7: Git-based workspace hydration)

`files <@workspace/> = git "url"` syntax. Clone → VFS population. Taint labeling. Text-only fidelity. Built-in GitHub auth integration. Network policy enforcement.

## Syntax
```mlld
files <@workspace/> = git "https://github.com/user/repo" auth:@github-token
files <@workspace/> = git "https://github.com/user/repo" branch:"feature-x" auth:@token
files <@workspace/src/> = git "https://github.com/user/repo" path:"src/" auth:@token
```

## Options
| Option | Required | Description |
|--------|----------|-------------|
| URL | yes | HTTPS or SSH git URL |
| auth: | depends | Keychain reference for private repos |
| branch: | no | Branch, tag, or commit SHA (default: default branch) |
| path: | no | Subdirectory to clone (sparse checkout) |
| depth: | no | Clone depth (default: 1, shallow) |

## Implementation
- Shell out to `git` CLI for clone (handles SSH auth natively, no new dependency)
- Shallow clone to temp dir → walk file tree → read text files → write into VFS shadow layer → clean up temp
- Use `env.executeCommand()` or direct child_process for git operations

## Authentication Resolution Order (spec Part 7)
1. If `auth:` specified → use that keychain reference
2. Else if repo is GitHub → check built-in GitHub auth (service: `mlld-cli`, account: `github-token`)
3. Else if SSH URL → use SSH agent (no explicit auth needed)
4. Else → unauthenticated clone (public repos only)

## Fidelity Constraints (spec Part 7)
VirtualFS stores string content only:
- **Text files**: loaded into VFS as-is
- **Binary files**: skipped by default (emit warning)
- **Symlinks**: not supported (VFS has no symlink concept). Skipped with warning.
- **File permissions**: not preserved (VFS has no permission model). Executable bit lost.

For full fidelity, use `box { provider: "@mlld/docker" }` with a real filesystem.

## Taint
All git-sourced files carry:
- `src:git` label
- `src:git:{remote-url}` source entry for provenance

**Credential redaction**: all taint sources and provenance entries MUST redact credentials:
- `src:git:https://github.com/user/repo` (token removed from URL)
- `src:git:git@github.com:user/repo` (SSH key path not included)
- Guard logs and audit trails must not leak auth tokens

## Network Policy
Git clones are network operations and MUST respect the environment's network policy:
- If `net: { allow: [] }` (network disabled) → git clone fails unless explicitly allowed
- Policy should support git URL patterns: `net: { allow: ["github.com"] }`

## Grammar Changes
- `git` as a source expression in `files` RHS
- `auth:`, `branch:`, `path:`, `depth:` as named options

## Error Handling
| Condition | Behavior |
|-----------|----------|
| Clone fails (network, auth) | Runtime error with clear message |
| Binary file encountered | Skip, emit warning |
| Symlink encountered | Skip, emit warning |
| Empty repo | No files written, no error |

## Acceptance Criteria
- `files = git "url"` parses correctly with all option combinations
- Public repo clone → VFS population works
- Private repo clone with auth works
- Taint labels applied (src:git, src:git:{url})
- Credentials redacted in all taint/audit entries
- Binary files skipped with warning
- Symlinks skipped with warning
- Network policy enforced
- Shallow clone by default (depth: 1)
- branch: option works (tags, commits, branches)
- path: option works (sparse checkout)
- Grammar tests for git source syntax
- Evaluator tests for clone/populate/taint
- Integration test with real (or mocked) git repo
- Docs atoms for git hydration feature
- All existing tests pass
- Committed before proceeding to Phase 9
