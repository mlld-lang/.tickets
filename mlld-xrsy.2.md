---
id: mlld-xrsy.2
status: closed
deps: [mlld-xrsy.1]
links: []
created: 2026-01-22T07:59:33.801048-08:00
type: task
priority: 3
parent: mlld-xrsy
---
# VirtualEnvironmentManager

Manage Python virtual environments for mlld projects.

- Create/detect/activate venvs
- Track venv config in mlld-config.json:
  ```json
  {
    "python": {
      "venv": ".venv",
      "manager": "uv"
    }
  }
  ```
- Resolve site-packages path for imports
- Support both project-local and system Python


