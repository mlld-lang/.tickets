---
id: m-b85f
status: closed
deps: []
created: 2026-02-16T06:35:55Z
type: task
priority: 0
assignee: Adam
updated: 2026-02-16T06:54:33Z
---
# Verify env config fs/net/limits field shapes are provider conventions not core types

The env-directive.md documents fs: { read: [...], write: [...] }, net: 'none', limits: { mem, cpu, timeout } — but these fields are passed through to the provider via the [key: string]: unknown index signature on EnvironmentConfig. They are not typed or validated by mlld core. Need to verify: (1) are these shapes provider conventions only? (2) if so, should the docs clarify that these are provider-specific? The existing atoms (env-overview.md, env-config.md) all use these same shapes, so this may be an intentional convention to document, but it should be explicit about where enforcement happens.

## Acceptance Criteria

Confirmed whether shapes are conventions or enforced. Docs updated if needed.


**2026-02-16 06:54 UTC:** Investigated: fs/net/limits are purely provider conventions. NOT typed in EnvironmentConfig (fall through [key: string]: unknown), NOT validated by core, NOT stripped by prepareProviderOptions(). Passed straight to provider @create(). Spec section 7.3 calls them 'recommended interface'. Docker provider in modules/llm/modules/docker.mld consumes them directly. env-directive.md already says '(passed to provider)' which is accurate. No doc fix needed. Closing.
