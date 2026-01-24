---
id: mlld-xrsy.1
status: closed
deps: []
links: []
created: 2026-01-22T07:59:23.617019-08:00
type: task
priority: 3
parent: mlld-xrsy
---
# PythonPackageManager abstraction

Create PythonPackageManager interface with pip and uv implementations.

- Interface: install(), list(), checkAvailable(), getDependencies(), resolveVersion()
- PipPackageManager implementation
- UvPackageManager implementation  
- Auto-detection logic: prefer uv if available, fall back to pip
- Allow explicit manager selection via config


