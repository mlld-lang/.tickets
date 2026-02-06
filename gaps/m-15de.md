---
id: m-15de
status: open
deps: []
created: 2026-02-05T21:35:19Z
type: feature
priority: 2
assignee: Adam
---
# Environment-mediated MCP via @mcpConfig() and profiles

**Current State**: MCP integration works via direct imports with automatic src:mcp tainting

MCP tools can be imported directly into scripts:
```mlld
import tools from mcp "node /path/to/server.cjs" as @github
var @issues = @github.listIssues({ repo: "myrepo" })
// @issues.mx.taint = ["src:mcp"] ✅
```

This works and includes:
- Direct MCP imports via `import tools from mcp` syntax (tests: mcp-import.test.ts)
- Automatic src:mcp tainting on tool outputs
- Policy restrictions on src:mcp data (labels.src:mcp.deny)
- Guards on op:exe that check input taint

**The Gap**: No profile-adaptive MCP configuration via environment modules

Spec v5 (Part 6.3, lines 1351-1395) describes environment-mediated MCP:

```mlld
// In environment module
profiles {
  full: { requires: { network } },
  readonly: { requires: { } }
}

exe @mcpConfig() = when [
  @mx.profile == "full" => {
    servers: [
      { module: "@github/issues", tools: "*" }
    ]
  },
  @mx.profile == "readonly" => {
    servers: []  // No MCP in readonly mode
  }
]

export { @spawn, @mcpConfig }
```

Then when spawning agents:
```mlld
import { @spawn } from "@local/myenv"

// MCP availability adapts to profile
env @config with { profile: "readonly" } [
  @spawn("Summarize issues")  // Gets limited MCP tools
]
```

**Why This Matters**:

Current direct imports are static - same MCP tools everywhere. Can't:
1. Give different MCP access based on trust level (sandbox-agent use case)
2. Adapt MCP availability to security policy
3. Integrate MCP with environment isolation patterns
4. Control MCP tools through profiles (full/readonly/etc)

Profile-adaptive pattern enables:
- Sandboxed agents with limited MCP access
- Policy-driven tool availability
- Composable security contexts
- Dynamic MCP configuration

**What Needs Implementation**:

1. **@mx.profile runtime value**
   - Environment must expose current profile via @mx.profile
   - Set during environment initialization based on policy match
   - Available to all code running in that environment

2. **@mcpConfig() calling in environment lifecycle**
   - When environment with @mcpConfig export is created
   - Call @mcpConfig() function to get server configuration
   - Use result to spawn MCP servers (spec 6.4, steps 1-4)

3. **Profile matching against policy**
   - Match profiles.*.requires against active policy
   - Select best-fit profile
   - Set @mx.profile before calling @mcpConfig()

4. **MCP server spawning from config**
   - Take @mcpConfig() result: { servers: [...] }
   - For each server: spawn mlld mcp subprocess
   - Filter tools per configuration
   - Route through guards (already works)
   - Aggregate via MCP proxy

5. **Integration with environment primitives**
   - env @config [ ... ] blocks should use environment's MCP config
   - Agents spawned in environment get configured MCP tools
   - Profile selection happens at environment creation

**Implementation Notes**:

- Current direct imports should still work (backward compat)
- @mcpConfig() pattern is opt-in via environment modules
- Profile selection uses existing policy matching logic
- MCP server spawning can reuse existing MCP infrastructure

**Related**:
- j2bd/security/jobs/wrap-mcp-tools.md describes target usage
- j2bd/security/jobs/sandbox-agent.md needs this for MCP sandboxing
- Spec v5 Part 6.3 (lines 1351-1395) defines architecture
- Spec v5 Part 6.4 (lines 1382-1395) defines lifecycle

**Files**:
- interpreter/env/Environment.ts (add @mx.profile)
- interpreter/env/EnvironmentManager.ts (call @mcpConfig)
- New: interpreter/env/profile-matcher.ts (profile selection)
- New: interpreter/mcp/config-spawner.ts (spawn from @mcpConfig result)

## Acceptance Criteria

**Must have:**

1. **@mx.profile is available in environment blocks**
   ```mlld
   import { @spawn } from "@local/myenv"
   env @cfg [ show @mx.profile ]  // Shows: "full" or "readonly"
   ```

2. **@mcpConfig() is called during environment setup**
   - Environment module exports @mcpConfig function
   - Function is called when environment is created
   - Return value determines MCP server configuration

3. **Profile-based MCP availability works**
   ```mlld
   // Environment module
   profiles {
     full: { requires: { network } },
     readonly: { requires: { } }
   }

   exe @mcpConfig() = when [
     @mx.profile == "full" => { servers: [...] },
     @mx.profile == "readonly" => { servers: [] }
   ]

   // Usage
   env @cfg with { profile: "full" } [
     // Gets MCP servers
   ]

   env @cfg with { profile: "readonly" } [
     // No MCP servers
   ]
   ```

4. **MCP tools from config work like direct imports**
   - Same automatic src:mcp tainting
   - Same policy enforcement
   - Same guard integration
   - Backward compatible with direct imports

5. **Tests prove it works**
   - Test profile selection based on policy
   - Test @mcpConfig() calling and server spawning
   - Test @mx.profile value in environment
   - Test MCP tool calls through configured servers

**Nice to have:**

- Tool filtering works: `tools: ["list_issues"]` only allows specified tools
- Error messages when @mcpConfig() missing or malformed
- Documentation in security atoms updated to show both patterns

## Success Criteria

This ticket is complete when j2bd/security/jobs/wrap-mcp-tools.md target example runs successfully:
- Environment module with profiles and @mcpConfig()
- Agent spawned in environment gets configured MCP tools
- Profile changes affect MCP availability
- All existing MCP tests still pass

