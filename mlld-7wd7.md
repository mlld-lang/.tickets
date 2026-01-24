---
id: mlld-7wd7
status: open
deps: []
links: []
created: 2025-12-20T15:39:58.981545-08:00
type: feature
priority: 2
---
# Allow /policy to configure cross-project file access

Allow /policy to configure path access restrictions.

As of commit 3a47575d8, path restrictions are relaxed by default - scripts can access files outside the project root.

The /policy module should enable configurable path restriction defaults:
- Restrict to project root only
- Allow specific external directories
- Block specific paths/patterns

This is the inverse of the original behavior: now permissive by default, with /policy providing the mechanism to lock down access when needed.


