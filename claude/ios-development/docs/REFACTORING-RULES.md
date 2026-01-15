# Refactoring Rules for [my-app]

These are non-negotiable rules for any code changes. Claude Code must follow these strictly.

## Model Integrity

- **NEVER** change SwiftData model properties without explicit approval
- **NEVER** modify relationships between models (Route ↔ Photo, Route ↔ Location)
- **ALWAYS** preserve existing model migrations
- **ALWAYS** maintain model property types (String stays String, Date stays Date, etc.)

## User Workflows Must Not Break

Before any refactor, these workflows must be tested and continue working:

### Routes

1. Create new route → Save → Appears in list
2. View route detail → All data displays correctly
3. Edit route → Save → Changes persist
4. Delete route → Confirm → Route removed, photos handled correctly
5. Add photo to route → Photo displays and persists
6. Mark holds on route photo → Annotations save and reload correctly

### Locations

1. Create location → Save → Appears in list
2. View location on map → Map displays correctly
3. Link routes to location → Relationship persists
4. Edit location → Changes save

### Navigation

1. Sidebar navigation → All items accessible
2. Back navigation → Returns to correct view
3. Deep linking (future) → Must not break

## Code Quality Standards

- **Simpler is better** - if refactor adds complexity, it's wrong
- **Readable over clever** - code should be obvious, not impressive
- **Comments for "why"** - not "what" the code does
- **No premature optimisation** - correct first, fast later
- **Extract reusable components** - but only after pattern appears 2-3 times

## Explanation Requirements

After EVERY code change, Claude Code must explain:

1. What changed (file by file)
2. Why it changed (what problem it solved)
3. What to test manually
4. Any new Swift patterns introduced
5. Any risks or side effects

## Safety Practices

- **One feature at a time** - no simultaneous changes to multiple systems
- **Commit after each logical change** - Git is your safety net
- **Test immediately** - manual testing before moving to next change
- **Preserve existing comments** - especially UX decisions and TODOs
- **Never remove code without explanation** - even if it seems unused

## Fran's Learning Goals

Every refactor is also a teaching opportunity:

- Explain Swift patterns in plain English
- Compare "before/after" and explain why "after" is better
- Point out best practices being applied
- Flag any "magic" that needs understanding

## Forbidden Changes (Without Explicit Permission)

- Changing navigation structure
- Modifying SwiftData schema
- Removing features (even incomplete ones)
- Changing model property names (breaks persistence)
- Adding external dependencies
- Implementing "clever" solutions that are hard to understand

## When Refactoring Goes Wrong

If manual testing reveals issues:

1. Git revert immediately
2. Analyse what broke and why
3. Document the lesson learned
4. Try again with smaller scope

## Future Considerations

As [my-app] grows, these areas will need careful handling:

- CloudKit sync implementation
- Photo storage and management
- Route drawing coordinate system
- Premium feature gating
- Data export/import

---

**Remember:** [my-app] is a solo project by a UX designer learning Swift. The goal is maintainable, understandable code that works reliably. Not impressive code that's fragile.
