---
id: m-a9e5
status: closed
deps: []
links: []
created: 2026-02-23T23:37:26Z
type: task
priority: 4
assignee: Adam
tags: [docs, atoms]
updated: 2026-02-27T04:51:38Z
---
# Atom gap fill: streaming, resolvers, CLI, SDK

Ticket tracking known thin areas in the atom system. Fill organically as gaps are hit, not as bulk creation. Known thin areas: (1) Streaming: 1 atom (stream), could use atoms for format adapters, NDJSON parsing, executor support. (2) Resolvers: 1 atom (modules-resolvers), could expand for specific resolver types. (3) CLI commands: only cli-run and cli-file atoms exist. (4) SDK: 4 atoms exist but are sketches — docs/user/sdk.md has ~670 lines covering stream handle API, state:// protocol, multi-tenant patterns, metrics, error handling, options reference tables, analyzeModule interface. The SDK atoms need real expansion (this is a genuine gap, not speculative). Approach: add atoms when users hit gaps or features change. The audit estimated ~115 atoms but many 'gaps' were already covered within existing atoms. SDK is the one area where the user doc is genuinely more thorough than atoms.


## Notes

**2026-02-23T23:59:24Z** SDK language wrappers (Go, Python, Rust, Ruby, Elixir) have their own READMEs in sdk/*/README.md. The atom system should point to those, not try to duplicate them. docs/user/sdk.md already links to github.com/mlld-lang/mlld/tree/main/sdk/<lang> which is correct. When expanding SDK atoms, focus on the Node.js/TypeScript API (processMlld, interpret, execute, analyzeModule) which is the primary SDK. Language wrapper docs stay in their respective READMEs.
