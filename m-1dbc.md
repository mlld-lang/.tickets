---
id: m-1dbc
status: open
deps: []
created: 2026-02-16T17:17:36Z
type: task
priority: 1
assignee: Adam
tags: [docs, p1, qa-driven]
updated: 2026-02-16T17:17:40Z
---
# doc: add prominent angle bracket file-detection callout in escaping-basics

## Problem

The rule that <tag @var> triggers file-loading detection is documented in escaping-basics but buried. QA agents spent 6+ experiments trying template approaches to HTML attributes before discovering it.

## Fix

Add prominent callout early in escaping-basics explaining that angle brackets containing @, ., /, or * are treated as file references, with pointer to js/node workaround.

## File
docs/src/atoms/syntax/escaping-basics.md

## Source
QA run: qa/runs/2026-02-16-0/topics/escaping-basics/

