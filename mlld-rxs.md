---
id: mlld-rxs
status: closed
deps: [mlld-76c]
links: []
created: 2025-12-13T05:33:08.56308-08:00
type: bug
priority: 1
---
# datatype labels need distinct highlighting - should stand out

Type labels in exe definitions like 'datatype' are currently highlighted as generic identifiers (same as variables).

Example: exe datatype @getReplyPressure(agent, msg) = [...]

The 'datatype' label declares return type and should be visually prominent - different from both variables and keywords. Should be tokenized as 'type' or 'label' token type.


