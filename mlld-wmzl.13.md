---
id: mlld-wmzl.13
status: closed
deps: [mlld-wmzl.4]
links: []
created: 2026-01-16T07:51:05.894572-08:00
type: task
priority: 1
parent: mlld-wmzl
---
# Phase 2.5: Right-size /needs for dependencies (not security)

## Goal

Right-size \`/needs\` to be purely about **dependency management**, not security gating. 

## Background

\`/needs\` originally existed for declaring dependencies (from YAML frontmatter \`needs: [...]\`). Recent work incorrectly expanded it into a security gating mechanism. We need to:

1. Keep \`/needs\` for its original purpose (dependencies)
2. Remove any security gating behavior
3. Ensure it works for package versions + command availability

## What /needs IS for

```mlld
needs {
  node: [express@^4.0.0, lodash@^4.17],
  python: [requests>=2.28],
  cmd: [git, jq, curl]
}
```

- **Package dependencies with versions** - Can't be inferred from code
- **Command availability** - Checked at load time ("is git installed?")
- **Documentation** - Makes dependencies explicit for humans

## What /needs is NOT for

- Security gating (removed - see Phase 0)
- Capability declarations (inferred from AST)
- Controlling what operations can execute (that's policy)

## Current State

**Needs investigation** - An agent will review the current implementation and report:
1. What grammar exists for \`/needs\`
2. What the evaluator does
3. What's been unwound vs what remains
4. What changes are needed to align with spec

## Expected Changes

Based on investigation, likely need to:
1. Ensure grammar supports \`node: [pkg@version]\`, \`python: [...]\`, \`cmd: [...]\`
2. Evaluator checks availability at load time (not security gating)
3. Remove any \`isNeedsGatingDisabled()\` references that remain
4. Update tests to verify dependency checking (not security)

## Exit Criteria

- [ ] \`/needs\` parses package dependencies with versions
- [ ] \`/needs\` parses command requirements
- [ ] Load-time check: "Are these packages/commands available?"
- [ ] Clear error if dependency unavailable
- [ ] NO security gating behavior
- [ ] Tests verify dependency checking works

## References

- Spec Section 2.4: Dependencies (\`/needs\`)
- Original YAML frontmatter design

## Estimated Effort
~3 hours (depends on investigation findings)


