---
id: mlld-33zl
status: closed
deps: []
links: []
created: 2026-01-15T10:05:32.938618-08:00
type: task
priority: 2
---
# Needs gating: update failing tests

Run: MLLD_DISABLE_NEEDS_GUARD=false TESTFAST=false npm test (also captured via vitest json report /tmp/vitest-needs.json). Failing tests with needs-related errors:\n\nDetected needs-related failures: 13 files, 85 tests.

interpreter/debug-dynamic-import.test.ts
  - debug dynamic import events emits debug event with provenance for dynamic modules

interpreter/fuzzy-local-imports.test.ts
  - Fuzzy Local File Imports Case-insensitive imports should import files with different case
  - Fuzzy Local File Imports Case-insensitive imports should handle uppercase variations
  - Fuzzy Local File Imports Whitespace normalization should import files with spaces using dashes
  - Fuzzy Local File Imports Whitespace normalization should handle nested paths with mixed separators
  - Fuzzy Local File Imports Extension inference should find .mld files without extension
  - Fuzzy Local File Imports Configuration should work with case-correct but normalized whitespace
  - Fuzzy Local File Imports Integration with relative paths should work with current directory notation

interpreter/interpreter.fixture.test.ts
  - Mlld Interpreter - Fixture Tests should handle feat/comments/inline
  - Mlld Interpreter - Fixture Tests should handle feat/import-liberal-syntax
  - Mlld Interpreter - Fixture Tests should handle feat/module-system/command-ref-imports
  - Mlld Interpreter - Fixture Tests should handle feat/module-system/directive-guard
  - Mlld Interpreter - Fixture Tests should handle feat/module-system/security-exe-roundtrip
  - Mlld Interpreter - Fixture Tests should handle feat/module-system/template-interpolation
  - Mlld Interpreter - Fixture Tests should handle feat/policy/import-policy
  - Mlld Interpreter - Fixture Tests should handle feat/policy/union
  - Mlld Interpreter - Fixture Tests should handle feat/strict-mode/import-alias (smoke test)
  - Mlld Interpreter - Fixture Tests should handle feat/strict-mode/import-all (smoke test)
  - Mlld Interpreter - Fixture Tests should handle feat/strict-mode/import-explicit-export (smoke test)
  - Mlld Interpreter - Fixture Tests should handle feat/strict-mode/import-explicit-export-wildcard (smoke test)
  - Mlld Interpreter - Fixture Tests should handle feat/strict-mode/import-imported-array-behavior (smoke test)
  - Mlld Interpreter - Fixture Tests should handle feat/strict-mode/import-namespace (smoke test)
  - Mlld Interpreter - Fixture Tests should handle feat/strict-mode/import-namespace-shorthand (smoke test)
  - Mlld Interpreter - Fixture Tests should handle feat/strict-mode/import-selected (smoke test)
  - Mlld Interpreter - Fixture Tests should handle feat/strict-mode/import-triple-colon-template (smoke test)
  - Mlld Interpreter - Fixture Tests should handle feat/with/combined
  - Mlld Interpreter - Fixture Tests should handle feat/with/needs-node
  - Mlld Interpreter - Fixture Tests should handle integration/exe-invocation-module
  - Mlld Interpreter - Fixture Tests should handle integration/exe-shadow-env-import
  - Mlld Interpreter - Fixture Tests should handle integration/modules/auto-export
  - Mlld Interpreter - Fixture Tests should handle integration/modules/explicit-export
  - Mlld Interpreter - Fixture Tests should handle integration/modules/metadata
  - Mlld Interpreter - Fixture Tests should handle slash/import/alias
  - Mlld Interpreter - Fixture Tests should handle slash/import/all-variable
  - Mlld Interpreter - Fixture Tests should handle slash/import/all
  - Mlld Interpreter - Fixture Tests should handle slash/import/directory-index-exported-vars
  - Mlld Interpreter - Fixture Tests should handle slash/import/directory-index-import-skipdirs-empty
  - Mlld Interpreter - Fixture Tests should handle slash/import/directory-index-import
  - Mlld Interpreter - Fixture Tests should handle slash/import/explicit-export-success
  - Mlld Interpreter - Fixture Tests should handle slash/import/explicit-export-wildcard
  - Mlld Interpreter - Fixture Tests should handle slash/import/import-triple-colon-template
  - Mlld Interpreter - Fixture Tests should handle slash/import/imported-array-behavior
  - Mlld Interpreter - Fixture Tests should handle slash/import/mixed
  - Mlld Interpreter - Fixture Tests should handle slash/import/mld-auto-directive
  - Mlld Interpreter - Fixture Tests should handle slash/import/namespace-shorthand
  - Mlld Interpreter - Fixture Tests should handle slash/import/namespace-special-chars
  - Mlld Interpreter - Fixture Tests should handle slash/import/selected
  - Mlld Interpreter - Fixture Tests should handle slash/import/suppress-output
  - Mlld Interpreter - Fixture Tests should handle slash/import/type-static
  - Mlld Interpreter - Fixture Tests should handle slash/import/type-static-inferred
  - Mlld Interpreter - Fixture Tests should handle slash/import/url
  - Mlld Interpreter - Fixture Tests should handle slash/import/url-angle-cached
  - Mlld Interpreter - Fixture Tests should handle slash/output/security-imported-exec
  - Mlld Interpreter - Fixture Tests should handle valid/feat/exe-chain-import

interpreter/output-formats.test.ts
  - Integration Scenarios Import Scope Precedence should use imported variables (first import wins immutable design)
  - Integration Scenarios Import Scope Precedence should import only selected variables
  - Integration Scenarios Variable Immutability should allow namespace imports where module has same-named internal variables

interpreter/url-support.test.ts
  - URL Support Import with URLs should import from URL

sdk/execute.test.ts
  - execute injects payload and state dynamic modules

sdk/index.test.ts
  - Mlld API processMlld should handle import directives

tests/integration/policy-config.test.ts
  - Policy config integration imports policies defined in mlld-config.json

tests/interpreter/policy-directive.test.ts
  - /policy directive scopes with-clause policy overrides to import execution

tests/sdk/dynamic-modules.test.ts
  - dynamic module imports imports selected values from object-backed modules
  - dynamic module imports supports namespace imports with nested dynamic data
  - dynamic module imports parses string-backed dynamic modules without filesystem paths
  - dynamic module imports parses string dynamic modules in strict mode by default
  - dynamic module imports allows text in string dynamic modules with explicit markdown mode
  - @payload optional field access ternary returns default when field missing from namespace import
  - @payload optional field access ternary returns value when field exists in namespace import
  - @payload optional field access handles multiple optional fields with defaults
  - @payload optional field access destructuring import succeeds for present fields

core/resolvers/__tests__/import-validation.test.ts
  - Import Content Type Validation Module Imports should accept module imports from .mld files
  - Import Content Type Validation Module Imports should accept module imports with auto-export
  - Import Content Type Validation Module Imports should accept module imports with explicit export
  - Import Content Type Validation Non-Module Import Behavior should import markdown files without mlld directives as empty namespace

interpreter/eval/import/executable-in-object.test.ts
  - Executable in Object Property should preserve executable as proper Variable when stored in object property
  - Executable in Object Property should handle nested objects with executables

interpreter/eval/import/import-types.test.ts
  - Import type handling imports local files with explicit static type
  - Import type handling passes cached duration metadata to URL fetches
  - Import type handling infers cached type for URL imports without keyword
  - Import type handling supports cached URL imports with angle-bracket syntax
  - Import type handling imports local files with inferred static type
  - Import type handling handles quoted resolver paths with liberal syntax
  - Import type handling imports directories by loading each subdirectory index.mld
  - Import type handling allows overriding directory skip patterns via with { skipDirs: [] }


