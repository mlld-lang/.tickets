---
id: mlld-jfvq
status: open
deps: []
links: []
created: 2025-12-20T15:17:12.57973-08:00
type: feature
priority: 3
---
# /prompt directive for SDK user input coordination

Add a `/prompt` directive that allows mlld scripts to request user input during execution, coordinated with the SDK.

Context from @adam: When an agent workflow reaches a blocking state where user input is needed, the script needs a way to surface this to the SDK without bailing out entirely.

Proposed design:
- `/prompt @userResponse = "What should we do next?"` 
- SDK receives a PromptRequest effect
- SDK can handle via UI, terminal prompt, or callback
- Script execution pauses until response provided
- Response flows back as the variable value

SDK integration pattern:
```typescript
const handle = interpret(script, { mode: 'stream' });
handle.on('prompt:request', async (event) => {
  const answer = await getUserInput(event.message);
  handle.respond(event.promptId, answer);
});
await handle.done();
```

Alternative: Could use state writes + SDK callbacks instead of pausing execution, but true pausing might be more intuitive for script authors.


