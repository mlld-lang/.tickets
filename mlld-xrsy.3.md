---
id: mlld-xrsy.3
status: closed
deps: []
links: []
created: 2026-01-22T07:59:44.280443-08:00
type: task
priority: 3
parent: mlld-xrsy
---
# Extend mlld-lock.json for Python

Add Python package tracking to existing mlld-lock.json.

- Add `python` section alongside existing Node entries
- Track: version, resolved hash, source (pypi/local), integrity
- Support wheel and sdist references
- Compatible with existing lock file format


