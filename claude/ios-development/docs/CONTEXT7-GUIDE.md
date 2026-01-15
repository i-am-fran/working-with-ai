# Context7 MCP Server - Quick Reference

This project uses the context7 MCP server to access **current, accurate documentation** for Apple frameworks. This guide explains how and when to use it.

---

## Why Context7?

**Problem**: Apple APIs evolve rapidly. SwiftUI, SwiftData, and iOS frameworks change between versions. Training data becomes outdated.

**Solution**: Context7 provides up-to-date documentation directly from Apple's official sources.

---

## When to Use Context7

### ALWAYS Use Context7 When:

- ✅ Working with SwiftUI (views, modifiers, property wrappers)
- ✅ Designing SwiftData models or relationships
- ✅ Using Swift concurrency (async/await, actors, Sendable)
- ✅ Implementing iOS framework features (Core Location, PhotosPicker, HealthKit)
- ✅ Checking API availability for iOS 17.0+
- ✅ Verifying accessibility patterns
- ✅ Looking up current Apple HIG guidelines

### NEVER Rely On:

- ❌ Training data for Apple framework APIs
- ❌ Memory of how something "used to work"
- ❌ Assumptions about current best practices

---

## How to Use Context7

### Step 1: Resolve Library ID

```
Tool: mcp__context7__resolve-library-id

Parameters:
- libraryName: "SwiftUI", "SwiftData", "Swift", "Combine", etc.

Example:
mcp__context7__resolve-library-id(libraryName="SwiftUI")

Returns: Context7-compatible ID like "/apple/swiftui"
```

### Step 2: Fetch Documentation

```
Tool: mcp__context7__get-library-docs

Parameters:
- context7CompatibleLibraryID: (from step 1)
- topic: Specific topic to search
- mode: "code" or "info"
  - "code" → API references, code examples
  - "info" → Conceptual guides, explanations

Example:
mcp__context7__get-library-docs(
  context7CompatibleLibraryID="/apple/swiftui",
  topic="State management property wrappers",
  mode="code"
)
```

---

## Common Workflows

### Check SwiftData Relationships

```
1. mcp__context7__resolve-library-id(libraryName="SwiftData")
   → Returns: "/apple/swiftdata"

2. mcp__context7__get-library-docs(
     context7CompatibleLibraryID="/apple/swiftdata",
     topic="Relationship delete rules cascade nullify",
     mode="code"
   )
   → Returns: Current documentation with examples
```

### Verify SwiftUI Property Wrappers

```
1. mcp__context7__resolve-library-id(libraryName="SwiftUI")
   → Returns: "/apple/swiftui"

2. mcp__context7__get-library-docs(
     context7CompatibleLibraryID="/apple/swiftui",
     topic="State Binding ObservedObject StateObject",
     mode="code"
   )
   → Returns: Latest property wrapper usage
```

### Look Up Swift Concurrency

```
1. mcp__context7__resolve-library-id(libraryName="Swift")
   → Returns: "/swift/swift"

2. mcp__context7__get-library-docs(
     context7CompatibleLibraryID="/swift/swift",
     topic="async await actors TaskGroup",
     mode="code"
   )
   → Returns: Current async patterns
```

### Check Apple HIG Guidelines

```
1. mcp__context7__resolve-library-id(libraryName="Apple Human Interface Guidelines")
   → Returns: HIG library ID

2. mcp__context7__get-library-docs(
     context7CompatibleLibraryID="[HIG-ID]",
     topic="modal presentation navigation",
     mode="info"
   )
   → Returns: Current HIG recommendations
```

---

## Common Topics for [my-app]

### SwiftData Topics
- "Model macro attributes"
- "Relationship delete rules"
- "Query descriptor predicate"
- "ModelContext insert delete"
- "Schema migration"

### SwiftUI Topics
- "State management property wrappers"
- "NavigationStack NavigationPath"
- "List LazyVStack performance"
- "Sheet presentation modes"
- "TabView styling"
- "Form TextField validation"
- "PhotosPicker PhotosPickerItem"

### Swift Language Topics
- "async await structured concurrency"
- "actors actor isolation"
- "Sendable protocol"
- "TaskGroup concurrent operations"
- "Result error handling"
- "Optional chaining nil coalescing"

### Accessibility Topics
- "accessibilityLabel accessibilityHint"
- "Dynamic Type font scaling"
- "VoiceOver rotor actions"
- "Accessibility traits"

---

## Agent Usage

### ios-swift-expert
- Queries context7 for SwiftData schema design
- Verifies iOS architecture patterns
- Checks iOS 17.0+ API availability

### swift-expert
- Queries context7 for Swift language features
- Verifies concurrency patterns
- Checks for deprecations

### ios-ux-reviewer
- Queries context7 for SwiftUI component APIs
- Verifies accessibility patterns
- Checks HIG compliance

---

## Best Practices

### DO:
- ✅ Query context7 proactively
- ✅ Use mode="code" for API lookups
- ✅ Use mode="info" for conceptual understanding
- ✅ Verify iOS 17.0+ compatibility
- ✅ Check for deprecations
- ✅ Use specific topics (not vague queries)

### DON'T:
- ❌ Guess about current APIs
- ❌ Trust training data for Apple frameworks
- ❌ Skip context7 to save time
- ❌ Use outdated patterns from memory
- ❌ Make assumptions about deprecations

---

## Quality Standard

**Every iOS implementation should:**
1. Query context7 for relevant APIs
2. Verify current best practices
3. Check iOS 17.0+ compatibility
4. Use non-deprecated patterns
5. Follow latest Apple guidelines

**If uncertain → Query context7 FIRST**

---

## Examples of Proactive Usage

### ✅ GOOD: Proactive Query

```
User: "Let's add a navigation stack to the routes view"

Agent:
1. Queries context7 for "NavigationStack NavigationPath"
2. Reviews current API
3. Implements using latest pattern
4. Notes iOS 17.0+ requirement
```

### ❌ BAD: Guessing from Memory

```
User: "Let's add a navigation stack to the routes view"

Agent:
1. Uses NavigationView (deprecated!)
2. Implements outdated pattern
3. Doesn't check current API
```

---

## Troubleshooting

**Problem**: Library ID not found
**Solution**: Try variations:
- "SwiftUI" vs "Apple SwiftUI"
- "Swift" vs "Swift Standard Library"
- Check spelling and capitalization

**Problem**: Topic returns no results
**Solution**:
- Be more specific: "State" → "State property wrapper SwiftUI"
- Try related terms: "navigation" → "NavigationStack NavigationPath"
- Use mode="info" for broader conceptual searches

**Problem**: Documentation seems incomplete
**Solution**:
- Try page=2, page=3 for more results
- Refine topic to be more specific
- Switch between mode="code" and mode="info"

---

## Summary

Context7 is **mandatory** for [my-app] iOS development. It ensures:
- Current APIs (not deprecated)
- iOS 17.0+ compatibility
- Latest best practices
- Accurate documentation

**Golden Rule**: If it's an Apple framework, query context7 first.
