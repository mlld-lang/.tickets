---
id: mlld-wmzl.2
status: closed
deps: []
links: []
created: 2026-01-16T07:17:41.047683-08:00
type: task
priority: 0
parent: mlld-wmzl
---
# Phase 2: PolicyConfig.labels and auth type/parsing

## Goal

Add policy label flow rules and auth configuration to PolicyConfig. This enables label flow checking and sealed credential injection.

## PolicyConfig Shape

PolicyConfig keeps top-level allow/deny and adds:

```typescript
export type AuthConfig = {
  from: string;
  as: string;
};

export type LabelFlowRule = {
  deny?: string[];
  allow?: string[];
};

export type PolicyConfig = {
  default?: 'deny' | 'allow';
  auth?: Record<string, AuthConfig>;
  allow?: Record<string, string[] | true> | true;
  deny?: Record<string, string[] | true> | true;
  labels?: Record<string, LabelFlowRule>;
  limits?: PolicyLimits;
};
```

No allowEnvTo or inject in label rules. Default:deny semantics live in `mlld-wmzl.15`.

## Parsing

Update `grammar/directives/policy.peggy` to parse:

```mlld
policy {
  auth: {
    claude: { from: "keychain:mlld-env/claude", as: "ANTHROPIC_API_KEY" }
  },
  labels: {
    secret: { deny: [op:cmd, op:show, op:output] },
    "src:mcp": { deny: [op:cmd:git:push], allow: [op:cmd:git:status] }
  }
}
```

Support quoted keys and arrays.

## Normalization and Merge

`core/policy/union.ts`:
- normalize auth and labels
- merge auth with last-wins semantics
- merge label rules with deny union, allow intersection
- keep allow/deny at top level

## Exit Criteria

- [ ] PolicyConfig has auth and labels types
- [ ] Grammar parses auth and labels sections
- [ ] Normalize/merge logic for auth and labels
- [ ] Tests cover parsing and merge behavior



