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

Ticket loading helpers exist only in j2bd/lib/context.mld but are needed by the review pipeline and potentially other orchestrators. Polish also has ticket helpers in polish/lib/tickets.mld.

## Work

1. Create llm/lib/tickets.mld with:
   - @loadTickets(dir) — all ready tickets from a tk directory
   - @loadTicketsByIds(ids) — specific tickets by ID via tk show
   - @formatTickets(tickets) — format ticket array as markdown context string
2. Export all helpers
3. Update j2bd and polish to import from @lib/tickets where applicable

