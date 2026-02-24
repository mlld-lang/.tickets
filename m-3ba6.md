---
id: m-3ba6
status: closed
deps: []
links: []
created: 2026-02-22T06:48:48Z
type: feature
priority: 0
assignee: Adam
updated: 2026-02-22T07:10:09Z
---
# Standalone auth directive with short form and env fallback

## Problem

Modules that need API keys (e.g. @mlld/bravesearch) currently require callers to `import policy @p` just to activate auth config. This leaks internal concerns — the caller shouldn't need to know how the module gets its credentials.

## Proposal

### 1. `auth` as a standalone top-level directive

Auth becomes its own directive rather than always living inside `policy = { auth: { ... } }`. Auth declarations travel with exported executables — the module is self-contained.

**Short form** (covers 90% of cases):

```mlld
auth @brave = "BRAVE_API_KEY"
```

Expands by convention to:
- keychain: `mlld-env-{projectname}/BRAVE_API_KEY`
- env fallback: `process.env.BRAVE_API_KEY`  
- target env var: `BRAVE_API_KEY`

**Long forms** for custom sources:

```mlld
auth @brave = { from: "keychain", as: "BRAVE_API_KEY" }
auth @brave = { from: "keychain:custom-svc/custom-acct", as: "BRAVE_API_KEY" }
auth @brave = { from: "env:SOME_OTHER_VAR", as: "BRAVE_API_KEY" }
auth @brave = { from: "op://vault/item/field", as: "BRAVE_API_KEY" }
```

The `from: "op://..."` pattern opens the door to pluggable credential providers (1Password, Vault, etc.).

**Policy interaction**: Policy can still restrict auth (keychain.deny, capabilities), but you don't need a policy just to declare credentials. Existing `policy = { auth: { ... } }` continues to work and composes with standalone auth.

### 2. Env var fallback for keychain sources

When `from: "keychain:..."` fails (entry missing), check `process.env[as]` before throwing. Resolution order:

1. Try keychain lookup
2. If entry missing → check process.env.BRAVE_API_KEY
3. If neither → throw

This makes modules portable: keychain on dev machines, env vars in CI/Docker/MCP serving. Silent fallback — where the credential came from doesn't change security properties.

### 3. Auth travels with exported executables

Module-defined auth binds to the exe at definition time. When a caller imports the exe, `using auth:brave` resolves against the module's auth declarations.

```mlld
>> In @mlld/bravesearch
auth @brave = "BRAVE_API_KEY"
exe net:r @search(q: string) = js { ... } using auth:brave
export { @search }

>> Caller — just works
import { @search } from @mlld/bravesearch
var @r = @search("mlld")
```

## Implementation scope

- Grammar: new `auth` directive (short form: string, long form: object)
- Interpreter: auth evaluation, binding to exe, resolution at call time
- Auth injection: env fallback when keychain entry missing
- Policy: compose with standalone auth declarations
- Tests and docs

## Design questions

- Should standalone `auth` imply `danger: ["@keychain"]`? The module is declaring intent by using `auth`. Current policy-based auth requires explicit danger opt-in.
- Pluggable providers (`op://`, etc.): implement now or leave the `from` field extensible for later?


## Notes

**2026-02-22T06:51:35Z** - Existing policy.auth should align with the same resolution behavior: keychain → env fallback. The `from: "keychain:..."` path in policy.auth should also try process.env[as] before throwing when the keychain entry is missing. This isn't a new feature for policy.auth, it's the same env fallback applied consistently.

- Rename docs/src/atoms/security/keychain.md → auth.md. The atom should cover the full auth story: standalone auth directive, short/long forms, env fallback, keychain details, and how policy.auth composes with standalone auth. Keychain becomes a section within auth rather than the top-level framing.

**2026-02-22T06:53:26Z** Auth + policy composition rules:

- Module `auth` + no caller policy: module auth just works
- Module `auth` + caller `policy.auth` same name: caller overrides (lets caller remap credential source)
- Module `auth` + caller `policy.auth` different names: union, both available
- Caller `policy.keychain.deny` / `policy.capabilities`: still enforced against module auth at runtime

Principle: `auth` declares sources, `policy` restricts access. Auth merges by union, policy overrides by intersection (most restrictive wins) — consistent with existing policy composition via union().

**2026-02-22T06:54:20Z** ### policy.auth short form alignment

policy.auth should accept the same forms as standalone auth:

```mlld
policy @p = {
  auth: {
    brave: "BRAVE_API_KEY",                                      >> short form (new)
    claude: { from: "keychain", as: "ANTHROPIC_API_KEY" },       >> bare keychain (new)
    custom: { from: "keychain:svc/acct", as: "X" }              >> full form (existing)
  }
}
```

`from: "keychain"` (bare, no path) expands to `keychain:mlld-env-{projectname}/<as-value>` — same convention as standalone auth short form.

### Auth binding mechanism

When an exe with `using auth:name` is defined in a module that has `auth @name`, the auth config is captured in the executable's metadata at definition time (similar to how labels are captured). When the exe is called from an importing context, auth resolution checks:

1. The exe's captured auth bindings (from the defining module)
2. The caller's policy.auth (if any — can override)
3. The caller's standalone auth (if any — can override)

Caller overrides module bindings (same name = replace). This lets callers remap credential sources without the module needing to know.

### Design decisions (resolved)

- Standalone `auth` implies keychain access intent. No `danger: ["@keychain"]` required for standalone auth — the directive IS the declaration of intent. Policy-based auth keeps existing behavior for backward compat; standalone auth is the new ergonomic path.
- Pluggable providers: leave `from` field extensible. Only implement `keychain:`, `env:`, and bare `keychain` for now. Unknown prefixes throw a clear error ("unsupported auth provider: op://"). Future work can add provider plugins.

### Target state — @mlld/bravesearch after implementation

```mlld
auth @brave = "BRAVE_API_KEY"

exe net:r @search(query: string, count: number) = js {
  const limit = count || 5;
  const res = await fetch(
    `https://api.search.brave.com/res/v1/web/search?q=${encodeURIComponent(query)}&count=${limit}&spellcheck=0`,
    { headers: { 'X-Subscription-Token': process.env.BRAVE_API_KEY } }
  );
  if (!res.ok) {
    return JSON.stringify({ error: true, status: res.status, message: await res.text() });
  }
  const data = await res.json();
  return JSON.stringify(data.web?.results?.map(r => ({
    title: r.title, url: r.url, snippet: r.description
  })) || []);
} using auth:brave with { description: "Search the web using Brave Search API" }

export { @search }
```

Caller:
```mlld
import { @search } from @mlld/bravesearch
var @results = @search("mlld scripting language", 3)
```

Works if BRAVE_API_KEY is in keychain OR in environment. No policy import needed.
