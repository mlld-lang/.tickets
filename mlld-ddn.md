---
id: mlld-ddn
status: closed
deps: []
links: []
created: 2025-12-12T18:09:04.794716-08:00
type: bug
priority: 1
---
# Object property keys not highlighted

Object keys like 'agent:', 'replyPressure:', 'responseRequired:' should be highlighted as properties. Currently appearing as plain text. Example: { agent: @responder.agent, replyPressure: @replyP } - keys should be highlighted. orchestrate.mld lines 29, 51, 68

## Notes

Object keys are properly tokenized - verified with 100% coverage on test cases


