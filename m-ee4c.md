---
id: m-ee4c
status: open
deps: []
created: 2026-02-19T06:09:38Z
type: task
priority: 2
assignee: Adam Avenir
---
# ## Goal

Replace the static manifest.json in review-docs with a bidirectional reference system where mappings live in the source files themselves.

## Design

### 1. Atom frontmatter gets `related` and `sources` fields

Each atom in `docs/src/atoms/` lists:
- **related**: user docs that cover this topic (`docs/user/*.md`, `website/src/examples/*.mld`, etc.)
- **sources**: code files that implement the feature this atom describes

Both must be accurate and complete.

### 2. User docs get `atoms` frontmatter field

Each doc in `docs/user/` (and website targets) lists which atoms are relevant to it. The review-docs orchestrator reads this instead of manifest.json.

### 3. One-off LLM script: `mlld run sync-atom-mappings`

Uses an LLM to audit both sides of the mapping:
- Finds atoms missing related docs (and vice versa)
- Finds atoms with stale/incorrect source code references
- Updates frontmatter in place
- Reports what changed

### 4. Deterministic two-way sync: `npm run sync:doc-refs`

A non-LLM script that enforces bidirectional consistency:
- If atom A lists doc D as related, doc D should list atom A
- If doc D lists atom A, atom A should list doc D as related
- Adds missing back-references automatically
- Reports conflicts or asymmetries

### 5. review-docs reads from frontmatter

Replace manifest.json loading with:
- Glob `docs/user/**/*.md` + `website/src/examples/*.mld` + `website/src/index.njk`
- Read each file's frontmatter for its `atoms:` list
- Skip files with no atoms (or flag them for sync-atom-mappings)

### Ordering

1. Add frontmatter fields to atoms and docs (can seed from current manifest.json)
2. Build `npm run sync:doc-refs` (deterministic, fast, runs in CI)
3. Build `mlld run sync-atom-mappings` (LLM-powered, run periodically)
4. Update review-docs to read from frontmatter instead of manifest.json
5. Delete manifest.json

