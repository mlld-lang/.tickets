---
id: mlld-bm80
status: closed
deps: []
links: []
created: 2025-12-20T20:18:42.889962-08:00
type: bug
priority: 2
---
# Fixed: .length now works as property on strings

.length was only working as a method (.length()) but not as a property (.length) on strings. Now both work, matching JavaScript behavior.

Fix: Added string .length handling in interpreter/utils/field-access.ts

Reported by @party-dev in mm chat.


