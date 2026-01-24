---
id: mlld-8lbq
status: open
deps: []
links: []
created: 2026-01-12T20:38:22.56588-08:00
type: bug
priority: 1
---
# Polish flywheel: merge phase not finding verified fixes

## Summary

After apply phase completes 28 fixes, merge phase shows 'Merging 0 verified fixes'. The filter query isn't matching.

## Filter Logic
```mlld
let @needsMerge = for @f in @freshFixed
  when @f.applied == true
    && @f.verified == true
    && !@f.merged
```

## Fixed.json Structure
```json
{
  "applied": true,
  "verified": true,
  "merged": null
}
```

The `merged: null` should work with `!@f.merged` but might have edge case issues with null vs undefined vs false.

## Additional Bug: Infinite Loop
After the merge phase, the flywheel loop repeats 'Iteration 1' endlessly with workDone=0 instead of exiting.


