---
id: mlld-osxu
status: closed
deps: []
links: []
created: 2025-12-20T15:41:09.461873-08:00
type: task
priority: 2
---
# Security errors should show clear denial reason

When file access is denied for security reasons (path outside project root), the error message is too generic:

Current: 'Failed to load content: /path/to/file'
Should be: 'Access denied: path is outside project root (/path/to/file)'

The real error IS available internally (via DEBUG_CONTENT_LOADER=1) but gets wrapped in the generic message. Security denials should be explicit.

Related to mlld-7wd7 (policy-based file access).


