---
id: mlld-og8
status: closed
deps: []
links: []
created: 2025-12-14T12:15:10.594452-08:00
type: task
priority: 1
---
# Guard/when action keywords need standout color (allow/deny/continue/retry/done)

Action keywords in => position of guard/when blocks need distinct highlighting (could be pink like var/exe):
- allow
- deny
- continue
- retry
- done

Example: guard before label = when [ @condition == 1 => allow @action() ]
The 'allow' keyword should be pink or another standout color.

These are special control flow actions, distinct from regular keywords.


