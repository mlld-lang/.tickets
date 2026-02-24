---
id: r-ce77
status: closed
deps: []
created: 2026-02-22T00:32:10Z
type: feature
priority: 0
assignee: Adam
tags: [marketplace, scaffold, command]
updated: 2026-02-22T00:48:05Z
---
# Fix command scaffold to generate Claude Code command format

The command module type (`mlld module command review`) currently generates index.mld as entry point. Claude Code commands are single .md files with YAML frontmatter. Update the scaffold to match.

## Design

Current scaffold output:
  .claude/commands/review/
    index.mld        ← wrong format for Claude Code
    module.yml
    README.md

Claude Code command format (from plugins/mlld/commands/scaffold.md):
  Single .md file with description in YAML frontmatter:
  ---
  description: Short description of what this command does
  ---
  Command instructions in markdown...

Target scaffold output:
  .claude/commands/review/
    commands/review.md     ← Claude Code command file
    .claude-plugin/
      plugin.json          ← plugin manifest (like skills)
    module.yml             ← entry: commands/review.md
    README.md

Implementation in cli/commands/init-module.ts:

1. Add a generateCommandContent(name, about) method similar to generateSkillContent()
   - Generates .md with YAML frontmatter (description field)
   - Includes placeholder instruction sections

2. In scaffoldDirectoryModule(), for moduleType === 'command':
   - Create .claude-plugin/ directory and generate plugin.json
   - Create commands/ directory and write {name}.md there
   - Set module.yml entry to commands/{name}.md
   - Don't generate index.mld

3. Update file listing and next steps output

4. Update generateReadmeContent() for command type to show new structure

Reference: plugins/mlld/commands/scaffold.md for command format example

Key files:
- cli/commands/init-module.ts (scaffoldDirectoryModule, generateDirectoryIndexContent, generateReadmeContent)
- plugins/mlld/commands/scaffold.md (reference for command .md format)

## Acceptance Criteria

- [ ] `mlld module command review` generates commands/review.md (not index.mld)
- [ ] Command .md has YAML frontmatter with description field
- [ ] .claude-plugin/plugin.json is generated
- [ ] module.yml entry field points to commands/review.md
- [ ] README shows correct structure
- [ ] Existing tests pass
- [ ] Command module is a valid Claude Code plugin (marketplace-compatible)


**2026-02-22 00:48 UTC:** Command scaffold generates commands/{name}.md with YAML frontmatter, .claude-plugin/plugin.json, proper README.
