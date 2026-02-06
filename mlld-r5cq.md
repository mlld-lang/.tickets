---
id: mlld-r5cq
status: closed
deps: []
links: []
created: 2026-01-11T16:33:38.877871-08:00
type: task
priority: 2
tags: [stale-check-9]
updated: 2026-01-30T11:58:15Z
---
# Negation ! doesn't parse in for-when conditions with method calls

**Problem**: Using `!` to negate a method call in a for-when condition fails to parse.

**Fails**:
```mlld
var @topics = for @f in @files when !@f.fm.id.startsWith("_") => @f.fm.id
>> Error: Missing condition list in when expression
```

**Also fails with parens**:
```mlld
var @topics = for @f in @files when !(@f.fm.id.startsWith("_")) => @f.fm.id
>> Same error
```

**Workaround** (unintuitive):
```mlld
var @topics = for @f in @files when @f.fm.id.startsWith("_") == false => @f.fm.id
>> Works!
```

**Works fine in other contexts**:
```mlld
when !@flag => show "flag is false"  >> works
var @x = !@condition  >> works
```

**Impact**: Filtering with negated conditions requires awkward `== false` pattern. Not discoverable - users will assume `!` works.

**Grammar investigation needed**: The for-when clause may have a more restrictive expression grammar than other contexts. Check if UnaryExpression with `!` is allowed in ForWhenCondition.

**Discovered in**: qa.mld refactoring - tried to filter out underscore-prefixed IDs.


