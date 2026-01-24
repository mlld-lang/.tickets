---
id: mlld-k4k
status: closed
deps: [mlld-5jz, mlld-9qq, mlld-04k, mlld-cgb, mlld-o2u]
links: []
created: 2025-12-08T19:57:11.921576-08:00
type: feature
priority: 0
---
# Strict mode for .mld files

Implement a strict execution mode for .mld files that eliminates LLM-unfriendly ambiguities:

**Problem:**
- Current mlld requires `/` prefix on directive lines, which creates ambiguity
- Non-directive lines become implicit content output, which LLMs can accidentally trigger
- This makes mlld harder for LLMs to generate correctly

**Solution:**
Two modes based on file extension:
- `.mld` → strict mode: bare directives, text lines error, blank lines ignored
- `.mld.md` → markdown mode: `/` required, text becomes content (current behavior)

**Key decisions:**
- Optional `/` prefix in strict mode for backward compatibility
- Blank lines are no-ops in strict mode (formatting whitespace)
- Raw strings in SDK default to strict mode
- Mode included in AST cache keys

**Line handling by mode:**

| Line Type          | Strict Mode   | Markdown Mode   |
|--------------------|---------------|-----------------|
| `/directive`       | ✓ Execute     | ✓ Execute       |
| `directive` (bare) | ✓ Execute     | Text content    |
| Blank              | ✓ Ignore      | Content         |
| Text               | ✗ Error       | Content         |

See child issues for implementation breakdown.

IMPORTANT: Ensure compatibility with 
1. current .mld.md module format which mixes ```mlld``` and ```mlld-run``` in markdown + frontmatter files—this should be valid.
2. `/for`...`/end` loops in templates


