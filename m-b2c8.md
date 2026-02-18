---
id: m-b2c8
status: closed
deps: []
created: 2026-02-14T22:17:06Z
type: feature
priority: 1
assignee: Adam
updated: 2026-02-17T21:11:32Z
---
# mlld skill install: cross-harness skill installation and lifecycle

## tldr

Unified command to install mlld authoring skills across coding harnesses (Claude Code, Codex, Pi, OpenCode). Includes postinstall suggestion, auto-update on npm update, and a plugin hook that nudges agents toward available skills.

## Components

### 1. `mlld skill install` command

Auto-detects installed harnesses and installs skills for all of them:

```
$ mlld skill install
Detected: Claude Code, Codex, Pi
  Claude Code: installing plugin via marketplace... done
  Codex: copying skills to ~/.codex/skills/mlld/... done
  Pi: copying skills to ~/.pi/agent/skills/mlld/... done
```

Also checks if Claude Code plugin is installed — if not, installs it (delegates to existing `mlld plugin install` logic).

Detection:
- Claude Code: `which claude`
- Codex: `which codex` or `~/.codex/` exists
- Pi: `which pi` or `~/.pi/` exists
- OpenCode: `which opencode` or `~/.config/opencode/` exists

Skill install paths:
- Claude Code: marketplace plugin (full plugin with skills, commands, hooks, LSP, MCP)
- Codex: copy `plugins/mlld/` to `~/.codex/skills/mlld/` (includes skills + examples)
- Pi: copy `plugins/mlld/` to `~/.pi/agent/skills/mlld/` (includes skills + examples)
- OpenCode: MCP server config + skills as instructions in `opencode.json`

Writes a `.version` marker file in each install location for update detection.

Flags:
- `--target <harness>` — install for a specific harness only
- `--scope user|project` — passed to Claude Code plugin install

`mlld skill uninstall` and `mlld skill status` for parity with plugin command.

### 2. npm postinstall message

In package.json postinstall script, detect available harnesses and print:

```
mlld installed successfully.

  Detected: Claude Code, Codex
  Run \`mlld skill install\` to add mlld authoring skills to your coding tools.
```

If nothing detected:
```
mlld installed successfully.

  Tip: if you use Claude Code, Codex, or Pi, run \`mlld skill install\`
  to add mlld authoring skills to your coding tools.
```

If skills already installed (update case): silently re-copy skills with updated content, print:
```
mlld updated. Skills refreshed for Claude Code, Codex.
```

First install = explicit opt-in via `mlld skill install`. Subsequent updates = automatic.

### 3. Plugin hook: skill nudge on `llm/` writes

Add `hooks/hooks.json` to the Claude Code plugin (`plugins/mlld/`) with a PostToolUse hook:

- Matcher: `Write|Edit`
- Script checks `tool_input.file_path` — only fires for `llm/` paths
- Output: one-liner mentioning available skills and examples
  e.g. `mlld skills: /mlld:orchestrator, /mlld:agents | examples: plugins/mlld/examples/`
- Script lives in `plugins/mlld/hooks/` and uses `${CLAUDE_PLUGIN_ROOT}` for paths

### 4. Developer docs

Create `docs/dev/SKILL-HOOKS-PLUGIN.md` documenting:
- Plugin structure and what each component does
- Skill install paths per harness
- Hook mechanism and how to add/modify hooks
- Update lifecycle (postinstall detection, version markers)
- How to extend for new harnesses

Follow docs/dev/DOCS-DEV.md principles: terse, no examples in dev docs, present tense only.

## Files to create/modify

- `cli/commands/skill.ts` (new) — skill install/uninstall/status command
- `cli/execution/CommandDispatcher.ts` — register skill command
- `cli/parsers/ArgumentParser.ts` — add 'skill' to subcommands
- `plugins/mlld/hooks/hooks.json` (new) — PostToolUse hook config
- `plugins/mlld/hooks/skill-nudge.sh` (new) — hook script
- `scripts/postinstall.js` (new) — npm postinstall script
- `package.json` — add postinstall script
- `docs/dev/SKILL-HOOKS-PLUGIN.md` (new) — developer docs

## Acceptance Criteria

1. `mlld skill install` detects installed harnesses and installs skills
2. `mlld skill install` also installs Claude Code plugin if Claude Code detected but plugin missing
3. `mlld skill status` reports which harnesses have skills installed and their versions
4. `mlld skill uninstall` removes skills from all harnesses
5. npm postinstall prints suggestion to install skills (first install) or silently updates (subsequent)
6. PostToolUse hook fires on writes to `llm/` and mentions available skills/examples
7. `docs/dev/SKILL-HOOKS-PLUGIN.md` documents the full system
8. .version marker files enable update detection

