---
id: mlld-7ng
status: closed
deps: []
links: []
created: 2025-12-09T05:16:23.953965-08:00
type: task
priority: 1
---
# Tests: fix failing suites for strict-mode rollout

Failing test files after strict mode changes:
- cli/commands/mcp.test.ts
- interpreter/index.structured.test.ts
- interpreter/interpreter.fixture.test.ts
- interpreter/output-formats.test.ts
- interpreter/output-management.test.ts
- tests/heredoc.e2e.test.ts
- tests/integration/imports/basic-patterns.test.ts
- tests/integration/node-shadow-cleanup.test.ts
- tests/pipeline/parallel-runtime.test.ts

## interpreter.fixture.test.ts tests/cases


  - effects: append-jsonl-basic; append-pipeline-basic; exe-for-effects; exe-for-nested-
    effects; exe-for-pipeline-retry-effects; exe-when-basic; for-basic-effects; nested-
    contexts; output-directive-effects; pipeline-function-effects; pipeline-inline-log;
    pipeline-inline-output-file; pipeline-inline-output-stderr; pipeline-inline-output-
    stdout; pipeline-inline-retry-replay; pipeline-inline-show; pipeline-retry-effects;
    var-for-immediate-effects
  - exe: command-ref-array-preserve
  - alligator: alligator-glob-concat; ast-name-list-all/class/fn/glob/var; ast-selection;
    ast-selector-variable; ast-type-filter-all/class/fn/var; ast-wildcard-contains/
    prefix/single/suffix; edge-cases-boundary; file-references-legitimate; function-args-
    operators; glob-as-transform; json-autoparse; keep-in-loops; keep-metadata-access;
    metadata-file/json/url; pipe-transformations; pipeline-contexts; section-extraction;
    section-list-all/glob/h2/h3; security-labels-files; unified-json-file; unified-text-
    file; url-markdown-conversion; xml-html-literal
  - array-operations: slice-basic; slice-negative; slice-variable-indices
  - augmented-assignment
  - bash: heredoc-prevents-e2big; large-variable-heredoc
  - bracket-notation-comprehensive
  - builtin: typeof-function; builtin-methods-array/string/variables; builtin-type-
    checking
  - command-substitution-tty
  - comments/inline
  - exe: command-ref-arg-interpolation-basic; function-call-syntax; param-shadowing
  - expressions-operators; expressions-operators-parsing
  - file-reference-interpolation
  - for: directive-actions; exe-template-params; nested-basic/function-calls/output/
    triple; pipeline-multiple/single; run-command; template-interpolation; when-with-none
  - frontmatter: alias; basic
  - html-conversion: basic-article; complex-elements; edge-cases; heading-hierarchy;
    metadata-extraction; readability-extraction; url-section-extraction
  - input: env-vars-allowed; input-new-syntax
  - issue-fixes/issue-459-run-json-field-access
  - json-backslash-n-preserved
  - keep-structured
  - literals-in-function-args
  - load-content: js-edge-cases; js-unwrap; node-unwrap
  - method-chain-after-array-access
  - module-system: command-ref-imports; directive-guard; security-exe-roundtrip;
    template-interpolation
  - native-object: github-pattern; node-return; property-access
  - object-property-access in exec args/functions
  - object-spread
  - optional-slash-comprehensive
  - pipeline: chained-multiple; exe-command-ref-pipeline; exe-reusable-pipes; foreach-
    structured-value; inline-log-suppressed; inline-output-file; inline-show-basic/retry-
    replay; interpolation-contexts; native-json-stages; parallel-syntax-parity; pipeline-
    alias-p-negative-index; pipeline-array-indexing; pipeline-context-basic/edge-cases/
    preservation; pipeline-multi-stage-retry; pipeline-ordering-integrity; pipes-with-
    arguments; retry-attempt-tracking; retry-basic/best-of-n/complex-logic/conditional-
    fallback/hint-function-value/hint-interpolated/hint-object/hint-reception/when-
    expression/with-none; show-invocation-inline-retry; show-vs-with-basic/retry;
    vertical-stacked-pipes; when-actions-pipes; when-all-any-pipes; with-clause-pipes
  - policy: import-policy; union; wants-basic
  - reserved: input-variable; now-variable; now-variable-lowercase; projectpath-variable
  - resolver-contexts
  - run: command-escape-newline-in-quoted
  - security: guard-after-allow/deny-handled/transform/transform-chain; guard-allow;
    guard-always; guard-before-after-compose; guard-deny-handled; guard-multiple-
    handlers; guard-retry; expression-tracking; pipeline-taint; after-guard-retry
    variants (nonpipeline hints/success/transform; pipeline effects/success); before-
    guard-retry-success; guard-context-inside-guards; guard-overrides-disable-all/except/
    only; guard-structured-equality
  - shell-interpolation: array-from-js-function; array-of-mixed-types/objects-command/
    objects-stdin; array-with-nulls; booleans-in-objects; deeply-nested-structures;
    empty-array-command; exec-stdin-complex-array/object; exec-with-complex-param;
    foreach-complex-elements; jq-with-complex-stdin; json-transformer-output; loader-to-
    command; mixed-complex-array; mixed-wrapped-simple; multiple-complex-params; nested-
    arrays-command/stdin; numbers-in-nested-array; object-array-in-template; object-
    in-command; object-nested-properties; object-via-stdin; object-with-array-property;
    pipe-sugar-complex-array; pipeline-complex-data; simple-array-expansion/stdin;
    single-quoted-complex/+apostrophe; special-chars-in-object; structured-value-wrapped;
    three-level-nesting; undefined-variable-interpolation; user-full-scenario-435; user-
    scenario-435
  - template-inline: for-backtick; for-double-colon; inline-show-double-bracket; inline-
    show-triple; triple-alligator-literal
  - template-syntax-migration
  - transformers: chain; csv-basic; json-basic/fromlist/llm-extract-array/generic-fence/
    inline-prose/invalid/nested/no-json/with-comments; json-llm-extract; json-loose-
    syntax; json-strict-basic; md-basic; xml-basic
  - variable-methods: direct-rhs-indexing; method-chaining
  - when: when-preserve-type-through-pipeline; expression-none-with-vars
  - with: combined; needs-node; pipeline-basic/input/termination
  - integration: exe-invocation-module; exe-sh-module; modules/auto-export/explicit-
    export/metadata; pipeline/ctx-hint-flow-basic/ctx-hint-multistage-mixed-hints/ctx-
    hint-object/ctx-hint-template-code; executable-transform; formatted-input; hint-
    command-ref-interpolation; triple-colon-exec
  - regression: alligator-for-expressions; angle-bracket-literals
  - slash/exe: code-brackets; command-substitution-interactive/sh; double-colon-
    content; exe-node-console-log-capture; exe-node-gray-matter-access; exe-node-mlld-
    dependencies; exe-node-shadow-env-always-created; exe-parameterized-command; js-
    shadow-env-test; nested-executable-field-access/-var; node-shadow-env; node-
    undefined-params; optional-slash-run; param-at-syntax; param-interpolation; run-
    template-exec; shadow-env-undefined-params; shadow-environment/+simple
  - slash/for: exe-for-expression/nested; for-basic; var-for-function-composition
  - slash/import: alias; explicit-export-success/wildcard; import-triple-colon-template;
    imported-array-behavior; mixed; namespace-json/nested/shorthand/special-chars; stdin-
    json/live/shorthand/text/deprecated; type-static/type-static-inferred; url
  - slash/output: alligator-content; blank-line-normalization; command; document;
    file; literal; quoted-path; resolver; security-imported-exec; template-invocation;
    variable; when-action
  - slash/path: url; variable-assignment
  - slash/run: bash-env-vars/multiline/parameters/bash/bash-array-at-syntax; code-with-
    variables; command-bases-npm-run/ operators/special-patterns; file-content-escaping;
    no-reinterpret; pipe-operator; quoted-special-chars; run-cmd-syntax-consistency; run-
    command-bracket-nesting; run-node-console-log-no-capture; shell-escaping; stdin-
    support
  - slash/show: all-template-types; alligator-section-as-substring; backtick-template;
    backtick-with-colon
  - slash/var: data-array-ast-fix/data-array-path-disambiguation/data-array-valid-
    patterns/data-object-literals-in-arrays/data-primitive-values; exe-invocation-direct-
    data/direct-text; foreach-bash-env/foreach-text-template; now-basic-compat; text-
    assignment-run-slash; text-url; text-variable-copy
  - slash/when: add-variable-in-action; all-block-action; all-individual-actions; any-
    block-action; bare-block-action; bare-individual-actions; block-all; exe-conditions;
    exe-invocation-add/run/tail; exe-when-all-matches; exe-when-expressions/+operators;
    exe-wildcard-default; first-individual-actions; implicit-actions; let-local-
    var; negation/+bare-when/+block; none-vs-wildcard; operators-chained; operators-
    comparison; optional-slash-combined/output/run/show; simple-none; truthiness-edge-
    cases; var-complex-types; var-function-calls; when-literal-condition; when-negation-
    switch; when-switch; wildcard-always-true
  - warnings: bare-variable-reference; directive-in-text-line; template-syntax-in-text

Update fixtures/expectations as needed, prioritizing non-strict-text errors first.

## Notes

Plan for strict-mode test migration:
- Default most feature/integration fixtures to strict (.mld, optional slash), unless they assert prose/blank-line semantics.
- Keep markdown (.mld.md) only where prose behavior matters (content/blank lines/formatter/doc-style output).
- Duplicate or parametrize only when mode changes behavior: prose handling, bare directives vs text, formatter/prose outputs, LSP/token sensitivity.
- Use explicit mode in raw-string tests; SDK/CLI defaults stay strict unless prose is expected.
- Keep LOOSE_TESTMODE=1 as default for stability; add targeted strict runs/subsets to catch regressions.
- Migration order: 1) convert generic fixtures to strict; 2) add strict variants only for prose-sensitive cases; 3) wire a small strict subset run; 4) align docs/examples mode with intent.


