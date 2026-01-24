---
id: mlld-6vh
status: closed
deps: []
links: []
created: 2025-12-14T12:15:12.496767-08:00
type: task
priority: 1
---
# 'with' keyword should highlight like for/when/while prepositions

The 'with' keyword in pipeline configurations should highlight the same as for/when/while (light teal italic keyword color):

Example: @data | @transform with { parallel: 3 }
The 'with' should be light teal italic like 'for'/'when'

Currently: Not tokenized or using wrong type
Should: Same as other preposition keywords (keyword semantic type)


