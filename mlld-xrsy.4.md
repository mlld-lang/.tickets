---
id: mlld-xrsy.4
status: closed
deps: []
links: []
created: 2026-01-22T07:59:54.514176-08:00
type: task
priority: 3
parent: mlld-xrsy
---
# Python module cache

Content-addressed cache for Python packages.

Structure:
```
~/.mlld/cache/python/
├── wheels/sha256/...
├── sdist/sha256/...
└── index.json
```

- Store wheels and source distributions
- Metadata includes Python version compatibility, ABI tags
- Quick lookup index by package name


