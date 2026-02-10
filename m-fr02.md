---
id: m-fr02
status: closed
deps: []
created: 2026-02-09T14:31:26Z
type: task
priority: 1
assignee: Adam Avenir
updated: 2026-02-09T14:41:20Z
---
# Fix documentation accuracy issues from final review (m-fr01)

Final review found documentation inaccuracies. Fix all of these:

1. MAJOR: guards-privileged.md line 39 says 'Non-privileged guards attempting these on protected labels get PROTECTED_LABEL_REMOVAL error' — misleading. Code at interpreter/hooks/guard-utils.ts:53-71 blocks ALL label removal by non-privileged guards. Protected check only changes the error code. Rewrite to: 'Non-privileged guards cannot remove ANY labels. Attempting removal produces LABEL_PRIVILEGE_REQUIRED (or PROTECTED_LABEL_REMOVAL for protected labels like secret, untrusted, src:*).'

2. MAJOR: main.mld line 278 says 'Only policy-generated guards (not user-defined) can clear protected labels like untrusted/influenced.' The influenced label is NOT protected — protected labels are secret, untrusted, and src:*. Fix to say 'like secret and untrusted' or explain that ALL removal requires privilege.

3. MINOR: guards-privileged.md frontmatter related field references 'guards-basics' and 'policies' but actual atom IDs are probably different. Check what IDs exist and fix.

4. MINOR: main.mld uses 'guard @ensureVerified for llm' but the pattern-dual-audit atom uses 'guard @ensureVerified after llm'. Align the demo to use 'after llm' for consistency with the atom.

5. MINOR: pattern-dual-audit.md should note that autoverify only fires when signed variable is @-referenced in command template, not when passed as parameter. Add a brief note.

6. NIT: main.mld line 285 has backslash-escaped @ensureVerified in :: block. Check if backslash is needed; remove if :: blocks don't interpolate @references.


**2026-02-09 14:31 UTC:** Verified related link IDs. Correct IDs: security-guards-basics (not guards-basics), security-policies (not policies), label-modification (correct), pattern-audit-guard (correct).

**2026-02-09 14:41 UTC:** All 6 fixes verified in place: (1) guards-privileged.md line 39 rewritten for accuracy, (2) main.mld summary fixed re: influenced/protected, (3) frontmatter related links corrected, (4) guard timing changed to after llm, (5) autoverify trigger note added to pattern-dual-audit, (6) backslash removed from @ensureVerified in heredoc.
