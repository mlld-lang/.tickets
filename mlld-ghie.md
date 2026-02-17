---
id: mlld-ghie
status: closed
deps: []
links: []
created: 2026-01-11T16:35:02.991603-08:00
type: task
priority: 0
tags: [size-m, complexity-m, risk-l, impl-none]
updated: 2026-02-12T22:02:07Z
---
# template directive executes code blocks in template files

**Problem**: The `template` directive parses and executes mlld code blocks inside .att template files, which is surprising when templates contain documentation examples.

**Surprising behavior**:
```mlld
exe @buildPrompt(topic) = template "prompts/qa.att"
```

If qa.att contains markdown with mlld code examples:
```markdown
## Example
\`\`\`mlld
import { @haiku } from @mlld/claude
var @response = @haiku("test")
\`\`\`
```

These code blocks are **parsed and executed** when the template is rendered, not treated as literal text.

**Workaround**: Use file load + JS string replacement:
```mlld
var @template = <prompts/qa.att>
exe @buildPrompt(tmpl, topic) = js {
  return tmpl.replace(/@topic/g, topic);
}
```

**Why it happens**: Template files are parsed with `TemplateBodyAtt` start rule which recognizes `TemplateInlineShow` nodes including code blocks. During interpolation, these are converted to directives and evaluated.

**This may be intentional** - templates are powerful and can contain executable content. But it's surprising when you just want string interpolation with example code blocks.

**Suggestion**: Either:
1. Document this behavior clearly in howto
2. Add a "literal" template mode that doesn't execute code blocks
3. Require \`\`\`mlld-run for executable blocks in templates (like literate modules)

**Discovered in**: qa.mld refactoring - qa-prompt.att had example code that got executed, importing @mlld/claude and outputting its docs.



**2026-02-12 20:55 UTC:** Design decision: mlld-run fence convention. Only mlld-run blocks execute in templates; plain mlld blocks are literal text. Consistent with literate .md module convention.

**2026-02-12 22:02 UTC:** Implemented mlld-run fence gating for template file parsing. Plain Error: [31mAn unexpected error occurred: Unknown option: blocks[39mmlld-run blocks continue to execute interpolation/executable references. Added regression fixture tests/cases/regression/template-file-mlld-fence-gating (including .att support files). Updated all template parse callsites (/exe template, autoverify template rendering, template collection import executable build, prose template parsing). Tests: npm run test:verbose -- interpreter/interpreter.fixture.test.ts -t template-file-mlld-fence-gating; npm test. Commit: 734aeb8ad.

**2026-02-12 22:02 UTC:** Implemented fence gating for template file parsing: plain mlld fenced blocks are treated as literal text, and mlld-run fenced blocks continue to execute interpolation/executable references.

Touched parse callsites:
- interpreter/eval/exe/definition-helpers.ts
- interpreter/eval/exec/command-handler.ts
- interpreter/eval/import/ModuleContentProcessor.ts
- interpreter/eval/prose-execution.ts
- new helper: interpreter/eval/template-fence-literals.ts

Added regression fixture:
- tests/cases/regression/template-file-mlld-fence-gating/

Validation:
- npm run test:verbose -- interpreter/interpreter.fixture.test.ts -t template-file-mlld-fence-gating
- npm test

Commit: 734aeb8ad
