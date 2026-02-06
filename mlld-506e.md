---
id: mlld-506e
status: closed
deps: []
links: []
created: 2026-01-14T17:12:08.313793-08:00
type: task
priority: 2
tags: [stale-check-9]
updated: 2026-01-30T12:12:05Z
---
# Grammar may need update to support unquoted @resolver in import syntax

Adam noted (msg-crl18at4) that import syntax should be `/import { get } from @keychain` not `/import { get } from "@keychain"`. Currently parser requires quotes. Need to check if grammar supports unquoted @resolver paths and update if needed. Related to mlld-esjy keychain import pattern.


