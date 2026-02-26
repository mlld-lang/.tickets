---
id: m-a3fe
status: open
deps: []
created: 2026-02-20T01:13:04Z
type: task
priority: 2
assignee: Adam Avenir
---
# Create llm/lib/tickets.mld shared library

## Problem

Ticket operations are needed by multiple orchestrators but are either duplicated or missing. Polish (`llm/run/polish/lib/tickets.mld`, 209 lines) has a comprehensive implementation with 20+ functions. J2BD has a simpler `@loadTickets()` in its context. The conventions doc (`llm/CONVENTIONS.md:166-172`) specifies a shared `@lib/tickets.mld` with 3 core functions.

## Current implementations

**Polish** (`llm/run/polish/lib/tickets.mld`) — comprehensive, includes:
- Core query: `@getOpenTickets()`, `@getTicket(id)`
- Mutation: `@createTicket()`, `@addTags()`, `@addEnrichNote()`
- Tag helpers: `@hasTagPrefix()`, `@getTagValue()`, `@isStale()`, `@isImplDone()`, `@needsHumanDesign()`, `@getSize()`, `@getComplexity()`, `@getRisk()`, `@getImplStatus()`
- Enrichment: `@wasRecentlyEnriched()`

Most of Polish's helpers are specific to Polish's tag conventions (size-*, complexity-*, risk-*, impl-*, stale-check-*). These should stay in Polish.

## Required change

### Step 1: Create `llm/lib/tickets.mld`

The shared library provides **generic** ticket operations per CONVENTIONS.md. It does NOT include Polish-specific tag helpers.

```mlld
>> Ticket system integration
>> Import: import { @loadTickets, @loadTicketsByIds, @formatTickets } from @lib/tickets

needs { sh }

>> Load all ready (open + unblocked) tickets from a tk directory
exe @loadTickets(dir) = [
  let @dirArg = @dir ? `--dir=@dir` : ""
  let @result = sh {tk ready $dirArg --json 2>/dev/null || echo "[]"}
  => @result
]

>> Load specific tickets by ID array
exe @loadTicketsByIds(ids) = [
  let @results = for @id in @ids [
    let @ticket = sh {tk show "$id" --json 2>/dev/null || echo "null"}
    when @ticket && @ticket != "null" => @ticket
  ]
  => for @t in @results when @t => @t
]

>> Format ticket array as markdown context string for LLM prompts
exe @formatTickets(tickets) = [
  let @lines = for @t in @tickets => `### @t.id: @t.subject\n\nStatus: @t.status | Priority: @t.priority\n\n@t.description\n`
  => @lines.join("\n---\n\n")
]

/export { @loadTickets, @loadTicketsByIds, @formatTickets }
```

**Note:** The exact `tk` CLI flags (`--json`, `ready`, `show`) need to be verified against the actual `tk` CLI. Check `tk help`, `tk ready --help`, `tk show --help` for correct flags. The implementations above are best-guess based on existing usage patterns.

### Step 2: Update Polish

Polish's `lib/tickets.mld` should import the core query functions from `@lib/tickets` and keep its Polish-specific helpers locally:

```mlld
import { @loadTickets, @loadTicketsByIds, @formatTickets } from @lib/tickets
>> ... Polish-specific helpers remain here (@hasTagPrefix, @getTagValue, etc.)
```

Replace `@getOpenTickets()` with `@loadTickets()` (or re-export as alias if Polish phases already use `@getOpenTickets`).

### Step 3: Update J2BD

Replace J2BD's inline ticket loading in `llm/run/j2bd/lib/context.mld` with an import from `@lib/tickets`.

## Acceptance criteria

- `llm/lib/tickets.mld` exists with `@loadTickets`, `@loadTicketsByIds`, `@formatTickets` exported.
- Polish imports core ticket functions from `@lib/tickets` and keeps its tag helpers locally.
- J2BD imports from `@lib/tickets`.
- `tk` CLI integration works (test with `tk ls` or equivalent).

