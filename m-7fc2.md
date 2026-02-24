---
id: m-7fc2
status: closed
deps: []
links: [m-2449, m-f035]
created: 2026-02-22T23:00:05Z
type: task
priority: 1
assignee: Adam
updated: 2026-02-23T05:02:49Z
---
# Validate guard-mcp-tools example end-to-end with @mlld/gh-issues

The guard-mcp-tools website example now uses the published `@mlld/gh-issues` module. Validate the full flow works as expected with real MCP tools.

**Current example:**

```mlld
import { @getIssue, @closeIssue, @labelIssue,
         @createIssue, @commentOnIssue } from @mlld/gh-issues

var tools @triageTools = {
  read:     { mlld: @getIssue, labels: ["untrusted"] },
  close:    { mlld: @closeIssue },
  label:    { mlld: @labelIssue },
  create:   { mlld: @createIssue, labels: ["publishes"] },
  comment:  { mlld: @commentOnIssue, labels: ["publishes"] }
}

>> Publishing after reading untrusted issues? Verify first.
guard before publishes = when [
  @mx.taint.includes("untrusted") && !@mx.tools.calls.includes("verify")
    => deny "Must verify instructions before publishing"
  * => allow
]

exe llm @agent(tools, task) = env with { tools: @tools } [
  => cmd { claude -p "@task" }
]

>> Agent reads issues (untrusted), triages, tries to comment
>> Close/label: fine. Comment: BLOCKED (untrusted + unverified)
var @reply = @agent(@triageTools, "Triage issues in project X")
```

**Known issues to fix:**
- No repo configured — @mlld/gh-issues likely needs a repo parameter (org/repo) somewhere. Check how the module expects repo configuration and update the example accordingly.
- Verify the exact export names from @mlld/gh-issues match what we're importing (@getIssue, @closeIssue, @labelIssue, @createIssue, @commentOnIssue)
- Verify tool labels flow correctly when tools come from a module import vs locally defined exes

**Validation checklist:**
- [ ] Import resolves correctly against published @mlld/gh-issues
- [ ] All 5 tool names match module exports
- [ ] Repo configuration added (env var, payload, or import param)
- [ ] `untrusted` label on @getIssue output propagates through LLM call
- [ ] Guard blocks @createIssue and @commentOnIssue when taint includes untrusted + no verify
- [ ] Guard allows @closeIssue and @labelIssue regardless of taint
- [ ] Guard allows publishing after verify has been called
- [ ] `env with { tools }` scopes tools correctly
- [ ] Update website example file (website/src/examples/guard-mcp-tools.mld) with final version
- [ ] Add mocked fixture test paired with skip-live.md live test per docs/dev/TESTS.md

## Acceptance Criteria

- Example runs end-to-end against a real GitHub repo with MLLD_LIVE=1
- Guard behavior confirmed for all 5 tools
- Website example updated and validates
- Mocked + live test pair added


## Notes

**2026-02-22T23:01:53Z** Live test requirement: create tests/cases/security/guard-mcp-tools-live/ with skip-live.md. Test should import @mlld/gh-issues, configure a real repo, and verify:
- @getIssue returns data with untrusted taint
- Guard blocks @commentOnIssue when untrusted + unverified
- Guard allows @closeIssue and @labelIssue with untrusted taint
- Guard allows @commentOnIssue after verify is called
Pair with mocked equivalent for normal test suite.

**2026-02-22T23:34:58Z** Mocked fixture tests added and passing:
- tests/cases/security/guard-mcp-tools-taint-deny/ — untrusted data blocked from publishes-labeled exe
- tests/cases/security/guard-mcp-tools-taint-allow/ — clean data passes through guard
- tests/cases/security/guard-mcp-tools-live/ — live test with skip-live.md (requires MLLD_LIVE=1)

Website example updated (website/src/examples/guard-mcp-tools.mld):
- Fixed @commentOnIssue → @addComment (matches @mlld/gh-issues exports)
- Removed @labelIssue (not exported by @mlld/gh-issues)

Module exports verified: @listIssues, @getIssue, @createIssue, @addComment, @closeIssue, @searchIssues, @tools

**2026-02-23T01:44:07Z** Replaced weak mocked tests with proper defense composition tests:
- guard-mcp-tools-env-taint-propagation: exe labels propagate taint through env with tools blocks
- guard-mcp-tools-guard-deny-publishes: influenced label from exe llm + untrusted input, guard blocks publishes
- guard-mcp-tools-tools-calls-verify: @mx.tools.calls tracking enables verify-gate pattern
- guard-mcp-tools-live: real @mlld/gh-issues with skip-live.md
All 3 mocked tests pass, live test properly skipped.
