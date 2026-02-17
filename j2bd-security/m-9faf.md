---
id: m-9faf
status: closed
deps: []
created: 2026-02-09T20:15:07Z
type: task
priority: 2
assignee: Adam Avenir
tags: [urgency-high, doc-polish]
updated: 2026-02-12T11:34:01Z
---
# Polish: Fix cross-refs, add navigation, clarify union()

Excellence assessment found several documentation polish gaps that directly impair the new user experience. Fix all in one pass.

## 1. Fix 9 broken cross-reference IDs in atom frontmatter

The `related:` fields use non-prefixed IDs that don't match actual atom IDs:

**mcp-security.md** (line 8):
- `guards-basics` → `security-guards-basics`

**mcp-policy.md** (line 8):
- `policies` → `security-policies`

**mcp-guards.md** (line 8):
- `guards-basics` → `security-guards-basics`
- `before-guards` → `security-before-guards`
- `after-guards` → `security-after-guards`

## 2. Fix text references in atom bodies

**mcp-guards.md** line 51:
- `See \`guards-basics\`` → `See \`security-guards-basics\``

**mcp-policy.md** line 58:
- `See \`denied-handlers\`` → `See \`security-denied-handlers\``

**mcp-policy.md** line 60:
- `See \`policies\`` → `See \`security-policies\``

## 3. Add MCP security reading path to security index

Add an 'MCP Security' subsection to `docs/src/atoms/security/_index.md` with a brief reading path. Keep it short — 2-3 lines:

```
**MCP Security:** To secure MCP tools, start with `mcp-security` (automatic tainting), then `mcp-policy` (policy restrictions), and `mcp-guards` (custom guards). See `env-config` for profile-based tool availability.
```

## 4. Add brief union() note to mcp-policy

In mcp-policy.md, after the first code example (line 27), add a brief inline note explaining union():

`union()` activates the policy — see `policy-composition` for combining multiple sources.

## Files to modify:
- docs/src/atoms/security/mcp-security.md
- docs/src/atoms/security/mcp-policy.md
- docs/src/atoms/security/mcp-guards.md
- docs/src/atoms/security/_index.md

