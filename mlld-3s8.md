---
id: mlld-3s8
status: closed
deps: [mlld-76c]
links: []
created: 2025-12-13T05:33:53.486058-08:00
type: bug
priority: 1
---
# parallel keyword and arguments need unique highlighting

The 'parallel' keyword in for loops should be highlighted distinctly:

Example: for parallel(3) @agent in @agents => ...

Issues:
1. 'parallel' should be highlighted uniquely (not as directive, not as datatype label)
2. Parentheses ( ) not highlighted  
3. Numeric argument (3) not highlighted

parallel is a modifier keyword that affects execution semantics and should stand out.


