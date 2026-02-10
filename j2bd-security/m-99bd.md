---
id: m-99bd
status: closed
deps: []
created: 2026-02-09T18:55:39Z
type: task
priority: 2
assignee: Adam Avenir
tags: [phase-4, adversarial]
updated: 2026-02-09T19:10:45Z
---
# Phase 4: Adversarial verification of sandbox restrictions

Red team the sandbox implementation. For each exit criterion, write mlld code that ATTEMPTS to violate the restriction and prove it's blocked.

## Exit Criteria to Verify

1. **Artifact runs without error**: Run `mlld llm/run/j2bd/security/impl/sandbox-demo.mld` and confirm clean completion

2. **Shell access BLOCKED**: Write code with `policy.capabilities.deny: ["sh"]` then attempt `run sh { echo pwned }`. Must be blocked by mlld policy.

3. **Command restrictions BLOCKED**: Write code with `capabilities.allow: ["cmd:git:*"]` then attempt `run cmd { rm -rf / }`. Must be blocked.

4. **Filesystem limits BLOCKED**: This is provider-enforced (Docker). Document that the `fs` config is passed to the Docker provider which creates mount restrictions. Since we can't run Docker in this context, verify the config structure is correct and document the enforcement layer.

5. **Network disabled**: This is provider-enforced (Docker `--network none`). Same as above - verify config, document enforcement layer.

6. **Credential protection**: Write code with `var secret @key = "test"` and attempt to `show @key`. Verify the guard blocks it. Also verify `@key` can't be interpolated into commands.

Each test MUST include:
- The exact mlld code run
- The expected behavior
- The actual output
- Which layer enforces it (mlld policy, provider, or structural)

Return `status: "verified"` with `exit_criteria_results` array, or `status: "failed"` with details.


**2026-02-09 19:10 UTC:** Worker returned status: verified. No friction points. No remediation needed.
