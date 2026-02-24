---
id: m-d94b
status: closed
deps: []
created: 2026-02-12T20:57:43Z
type: feature
priority: 1
assignee: Adam
tags: [structured-value, data-model]
updated: 2026-02-24T13:35:11Z
---
# StructuredValue: expose .mx.text and .mx.data for direct value access

StructuredValues carry both raw text and parsed data internally, but users must pipe through @json to access the parsed form. Expose these directly as .mx.text and .mx.data on any StructuredValue, eliminating the need for | @json in most cases.

Use the .mx namespace to avoid polluting user field access — .text and .data are extremely common field names that users will have in their own data.

Examples:
  var @result = <response.json>
  show @result.stance             # auto-resolves through .mx.data
  show @result.mx.data.stance     # explicit equivalent
  show @result.mx.text            # raw text form

  # no longer needed in most cases:
  var @parsed = @result | @json
  show @parsed.stance

This makes the StructuredValue wrapper transparent — users get the value type they need without an explicit parse step, while .mx.* stays out of the way of user-defined fields.

Coupled with m-c8ab (@json → @parse rename): together these two changes mean | @parse becomes a deliberate explicit parse, while .mx.data is the zero-ceremony path for values that are already structured internally.

Design notes:
- .mx.text returns the raw text representation
- .mx.data returns the parsed/structured data
- .mx namespace already established for system metadata on values (@file.mx.filename etc)
- Plain field access auto-resolves through .mx.data: @result.stance == @result.mx.data.stance
- This means dotted access on a StructuredValue looks into the parsed data first, making the wrapper invisible for typical usage
- .mx.text and .mx.data are escape hatches for when you need to be explicit about which representation you want
- All other context rules follow existing StructuredValue semantics (see docs/dev/DATA.md): string interpolation uses .text, computation uses .data, display uses .text
- .mx.text and .mx.data work for all value types — a string where both are "hello" is fine, no special cases needed

What's new vs already working:
- Field access on .data is already documented behavior (DATA.md: "Field access (.foo, .bar) operates on .data"). So @result.stance may already work for values where .data is populated (loaded JSON, auto-parsed command output).
- The actual new work here is: (1) exposing .mx.text and .mx.data as explicit user-facing accessors, and (2) ensuring auto-resolution is consistent across all contexts, not just some.

Collision rule:
- .mx on a StructuredValue ALWAYS means system metadata, never auto-resolves into data
- If user data has an "mx" key, access it via @val.mx.data.mx
- This is consistent with .mx being reserved system namespace on all values

Implementation note — .mx.text/.mx.data resolver mapping:
- @val.mx.text and @val.mx.data must map to the StructuredValue's TOP-LEVEL .text and .data properties, not to keys inside the .mx metadata object
- The internal .mx object doesn't have .text or .data fields today, but the resolver must be explicit about this: @val.mx.text means "raw text representation", not "look up .text inside metadata"
- If .mx metadata ever gains a .text or .data field, the user-facing accessors still win — they're the defined interface

Acceptance criteria:
- @val.mx.text returns raw text on any StructuredValue
- @val.mx.data returns parsed data on any StructuredValue
- @val.foo auto-resolves to @val.mx.data.foo for any StructuredValue
- Plain .text and .data remain available as user field names without collision
- Existing | @json / | @parse continues to work (not removed)
- Tests covering direct access vs pipe access equivalence
- Tests covering auto-resolution: @sv.field == @sv.mx.data.field


## Notes

**2026-02-24T11:34:27Z** Ensure all relevant docs/src/atoms are updated
