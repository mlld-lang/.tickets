---
id: mlld-nwu
status: closed
deps: []
links: []
created: 2025-12-07T20:51:28.696466-08:00
type: task
priority: 1
---
# sh/bash executor not emitting CHUNK events to StreamBus for format adapters

When using 'stream sh { ... }' the FormatAdapterSink doesn't receive CHUNK events. Works with 'stream cmd { ... }'. FormatAdapterSink.handle never called for sh/bash executors.


