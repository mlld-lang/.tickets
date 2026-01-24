---
id: mlld-3jk
status: closed
deps: []
links: []
created: 2025-12-09T04:53:12.510232-08:00
type: bug
priority: 2
---
# MJS bundle: Dynamic require of fs error in Node 24 ESM projects

**Reported by:** partydev during SDK dogfooding

**Environment:**
- Node 24
- ESM project (type: module in package.json)
- Using `import { execute } from 'mlld'`

**Error:**
`Dynamic require of fs is not supported` when importing from the MJS bundle.

**Workaround:**
Remove `type: module` from package.json and let tsx handle ESM imports, or use createRequire with the CJS bundle.

**Root cause (suspected):**
The MJS bundle is pulling in a CJS-only dependency that uses `require('fs')` or similar. Our tsup build generates both .mjs and .cjs outputs but the MJS bundle may not be fully tree-shaken or may include CJS-style imports.

**To investigate:**
1. Check tsup build config for ESM externals
2. Look for dynamic requires in dependencies
3. Test with Node 24 ESM project


