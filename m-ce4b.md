---
id: m-ce4b
status: closed
deps: []
links: []
created: 2026-03-04T18:58:55Z
type: feature
priority: 1
assignee: Adam
tags: [box, mcp, vfs, llm]
updated: 2026-03-04T19:37:53Z
---
# VFS-backed MCP bridge for LLM invocations in boxes

When an exe with the `llm` label runs `cmd {}` inside a VFS box, it fails because ShellSession (just-bash) has no system binaries. `claude -p` → "command not found."

## Goal
Make `@mlld/claude` (and any `exe llm` that shells out to a real binary) work inside VFS boxes by serving VFS-backed tools to the LLM process via MCP.

## What to build
An ephemeral VFS-backed MCP server that gets injected into LLM CLI invocations inside boxes.

### Detection
When `CommandExecutorFactory.executeCommand()` is called with an active workspace, and the command originates from an `llm`-labeled exe, use the MCP bridge path instead of ShellSession.

The `llm` label needs to be threaded through to `CommandExecutorFactory`. Currently `CommandExecutionContext` has `directiveType` and `sourceLocation` but not labels. Add label visibility so the factory can distinguish LLM invocations from plain shell commands.

### MCP bridge
1. Create a VFS-backed MCP server that exposes Read, Write, Bash (via ShellSession), Glob, Grep tools — all operating on the active workspace's VirtualFS
2. Start it on a Unix socket or stdio pair
3. Inject `--mcp-config` into the `claude` command pointing at the VFS-backed server
4. Run the command via real bash (not ShellSession) so the `claude` binary is found
5. On completion, tear down the ephemeral MCP server

### Tool restriction forwarding
The box's `tools` config restricts which tools are available. The MCP server should only expose tools that the box allows. If `tools: ["Read"]`, the MCP server serves only Read — Claude can't use Write or Bash.

## Existing infrastructure to use
- `mlld mcp` / `MCPServer` (cli/mcp/MCPServer.ts) — serves mlld functions as MCP tools over stdio
- `MCPOrchestrator` (cli/mcp/MCPOrchestrator.ts) — manages Unix sockets for MCP proxying
- `ShellSession` (services/fs/ShellSession.ts) — VFS-backed shell execution for the Bash tool
- `VirtualFS` / `VirtualFSAdapter` — the VFS layer
- `CommandExecutorFactory` (interpreter/env/executors/CommandExecutorFactory.ts) — the routing point (line 106-108) where workspace vs real execution is decided
- `BashExecutor` (interpreter/env/executors/BashExecutor.ts) — real bash execution path

## Acceptance criteria
1. Inside a VFS box, `@haiku("say hello")` from @mlld/claude produces an LLM response (not "command not found")
2. Claude's Read/Write/Bash tool calls inside that box operate on VFS files, not real FS
3. Files created by Claude inside the box appear in the workspace's VFS (visible via `<@ws/path>` resolver after box exits)
4. Box `tools` config restricts what Claude can do — `tools: ["Read"]` means Claude can't use Bash or Write
5. Box `mcps: []` default doesn't block the ephemeral VFS MCP server (it's infrastructure, not a user MCP server)
6. Non-`llm` commands in VFS boxes still route through ShellSession as before (no regression)

## Semantics (for context)
`cmd:@ws` contract is preserved. Simple commands run in ShellSession (just-bash). LLM invocations run the binary on real FS but their tool calls go through VFS via MCP. File operations always happen in the workspace regardless of execution path.
