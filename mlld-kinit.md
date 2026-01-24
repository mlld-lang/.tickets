---
id: mlld-kinit
status: closed
deps: []
links: []
created: 2026-01-20T08:37:12.211987-08:00
type: feature
priority: 1
---
# Project name in mlld-config.json (mlld init)

## Summary

Add project name to mlld-config.json, set during `mlld init`.

## Purpose

Project name is used for keychain namespacing: `mlld-env-{projectname}`

This ensures:
- Credentials are isolated per project
- Scripts can only access their project's credentials
- No cross-project credential leakage

## Changes to mlld init

1. Prompt for project name (suggest directory name)
2. Write to mlld-config.json:
   ```json
   {
     "project": {
       "name": "my-project"
     }
   }
   ```

## Validation

- Alphanumeric, dashes, underscores only
- No spaces or special characters
- Max 64 characters

## Exit Criteria

- [ ] `mlld init` prompts for project name
- [ ] Project name written to mlld-config.json
- [ ] Validation rejects invalid names
- [ ] Existing projects without name prompted on first keychain use


