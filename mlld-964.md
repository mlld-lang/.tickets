---
id: mlld-964
status: closed
deps: []
links: []
created: 2025-12-17T17:31:29.710415-08:00
type: feature
priority: 3
---
# Add /prompt directive for SDK-coordinated user input

Enable scripts to request user input without bailing out, for SDK-based interactive workflows:

```mlld
/var @answer = prompt "Continue with deployment? (y/n)"
/when @answer == "y" => /run sh {./deploy.sh}
```

**Design:**
- /prompt emits a special effect (type: 'prompt') with the question
- SDK intercepts this, pauses execution
- SDK collects user input through its own UI/CLI
- SDK resumes execution with the user's response
- Script continues with @answer populated

**Use cases:**
- Agent workflows that need human confirmation
- Interactive deployment/migration scripts
- Multi-step processes with decision points

**NOT for:**
- Non-interactive batch execution (should fail gracefully)
- CLI-only usage (use shell read instead)

This enables SDK-based agents to coordinate with users without breaking out of the mlld script entirely.

Requested by @partydev for agent scaffold pattern.


