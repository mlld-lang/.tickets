---
id: mlld-ghie
status: open
deps: []
links: []
created: 2026-01-11T16:35:02.991603-08:00
type: task
priority: 2
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


