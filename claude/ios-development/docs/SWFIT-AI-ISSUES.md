# Swift & SwiftUI: AI Code Quality Guidelines

**Based on advice by Paul Hudson (@twostraws)**
Source: [What to fix in AI-generated Swift code](https://www.hackingwithswift.com/articles/281/what-to-fix-in-ai-generated-swift-code)

---

## Critical Context

This document provides specific guidance for avoiding common mistakes that AI tools make when generating Swift and SwiftUI code. These guidelines assume you're targeting modern iOS versions (iOS 17+, with iOS 18+ preferred).

**Why this matters:**

- Swift and SwiftUI evolve rapidly
- AI training data often includes deprecated APIs
- Modern alternatives are simpler, faster, and more accessible
- Following these patterns improves VoiceOver support and accessibility

---

## Deprecated API Replacements (HIGH PRIORITY)

### 1. Foreground Colors

❌ **NEVER use:** `foregroundColor()`
✅ **ALWAYS use:** `foregroundStyle()`

**Why:** `foregroundColor()` is deprecated. `foregroundStyle()` supports advanced features like gradients and hierarchical colors.

```swift
// ❌ BAD
Text("Hello").foregroundColor(.blue)

// ✅ GOOD
Text("Hello").foregroundStyle(.blue)
```

---

### 2. Corner Radius

❌ **NEVER use:** `cornerRadius()`
✅ **ALWAYS use:** `clipShape(.rect(cornerRadius:))`

**Why:** `cornerRadius()` is deprecated. The new API supports uneven rounded rectangles and more advanced shapes.

```swift
// ❌ BAD
Rectangle().cornerRadius(12)

// ✅ GOOD
Rectangle().clipShape(.rect(cornerRadius: 12))
```

---

### 3. OnChange Modifier

❌ **NEVER use:** Single-parameter `onChange(of:)`
✅ **ALWAYS use:** Two-parameter or zero-parameter variant

**Why:** The old 1-parameter variant is unsafe and deprecated.

```swift
// ❌ BAD
.onChange(of: value) { /* oldValue not available */ }

// ✅ GOOD
.onChange(of: value) { oldValue, newValue in
    // Handle change
}

// ✅ ALSO GOOD (no parameters)
.onChange(of: value) {
    // Handle change
}
```

---

### 4. Navigation View

❌ **NEVER use:** `NavigationView`
✅ **ALWAYS use:** `NavigationStack` or `NavigationSplitView`

**Why:** `NavigationView` is deprecated. Use `NavigationStack` for simple navigation, `NavigationSplitView` for iPad/Mac multi-column layouts.

```swift
// ❌ BAD
NavigationView {
    List { ... }
}

// ✅ GOOD
NavigationStack {
    List { ... }
}
```

---

### 5. Task Sleep

❌ **NEVER use:** `Task.sleep(nanoseconds:)`
✅ **ALWAYS use:** `Task.sleep(for:)`

**Why:** The nanoseconds API is harder to read and error-prone.

```swift
// ❌ BAD
try await Task.sleep(nanoseconds: 1_000_000_000)

// ✅ GOOD
try await Task.sleep(for: .seconds(1))
```

---

## Accessibility & Modern APIs (HIGH PRIORITY)

### 6. Tap Gestures vs Buttons

❌ **AVOID:** `onTapGesture()` (except for special cases)
✅ **PREFER:** Actual `Button` views

**Why:** Buttons provide better VoiceOver support, eye tracking on visionOS, and semantic meaning.

**Exceptions:** Only use `onTapGesture()` when you need:

- Tap location (x, y coordinates)
- Number of taps (double-tap, triple-tap)

```swift
// ❌ BAD (unless you need tap location/count)
Text("Tap me").onTapGesture {
    doSomething()
}

// ✅ GOOD
Button("Tap me") {
    doSomething()
}
```

---

### 7. Button Labels with Images

❌ **AVOID:** Using just `Image` or verbose `Label` syntax
✅ **PREFER:** Inline Button API with systemImage

**Why:** Better VoiceOver experience and more concise.

```swift
// ❌ BAD (no text for VoiceOver)
Button(action: add) {
    Image(systemName: "plus")
}

// ❌ VERBOSE
Button(action: add) {
    Label("Add", systemImage: "plus")
}

// ✅ GOOD (iOS 15+)
Button("Add", systemImage: "plus", action: add)
```

---

### 8. Dynamic Type Support

❌ **AVOID:** Fixed font sizes with `.font(.system(size:))`
✅ **PREFER:** Semantic font styles that scale

**Why:** Respects user's accessibility preferences for larger text.

```swift
// ❌ BAD
Text("Title").font(.system(size: 24))

// ✅ GOOD
Text("Title").font(.title)

// ✅ ALSO GOOD (iOS 18+)
Text("Title").font(.body.scaled(by: 1.5))
```

---

### 9. Font Weight

⚠️ **BE CAREFUL:** `fontWeight(.bold)` vs `bold()`

**Why:** These do NOT always produce the same result. `bold()` is context-aware.

```swift
// These are NOT equivalent:
Text("Bold").fontWeight(.bold)  // Fixed weight
Text("Bold").bold()             // Context-aware
```

---

## State Management & Observation

### 10. ObservableObject → @Observable

❌ **AVOID:** `ObservableObject` protocol
✅ **PREFER:** `@Observable` macro

**Why:** Simpler code, better performance, more intelligent view invalidation.

**Exception:** Keep `ObservableObject` only if you specifically need Combine publishers.

```swift
// ❌ OLD WAY
class ViewModel: ObservableObject {
    @Published var name: String = ""
}

// ✅ NEW WAY
@Observable
class ViewModel {
    var name: String = ""
}
```

---

### 11. Computed Properties vs Extracted Views

❌ **AVOID:** Breaking up views into computed properties
✅ **PREFER:** Separate SwiftUI view structs

**Why:** With `@Observable`, only separate view structs benefit from intelligent view invalidation. Computed properties don't.

```swift
// ❌ BAD
struct ContentView: View {
    var body: some View {
        VStack {
            headerSection
            contentSection
        }
    }

    var headerSection: some View {
        Text("Header")
    }
}

// ✅ GOOD
struct ContentView: View {
    var body: some View {
        VStack {
            HeaderSection()
            ContentSection()
        }
    }
}

struct HeaderSection: View {
    var body: some View {
        Text("Header")
    }
}
```

---

## SwiftData Patterns

### 12. SwiftData Unique Attributes

⚠️ **WARNING:** `@Attribute(.unique)` does NOT work with CloudKit

**Why:** CloudKit sync will fail or behave unexpectedly with unique constraints.

```swift
// ⚠️ BREAKS CLOUDKIT SYNC
@Model
class Route {
    @Attribute(.unique) var name: String
}

// ✅ SAFE FOR CLOUDKIT
@Model
class Route {
    var name: String  // Handle uniqueness in app logic if needed
}
```

---

## Navigation Patterns

### 13. NavigationLink API

❌ **AVOID:** Old inline destination API in lists
✅ **PREFER:** `navigationDestination(for:)` or `NavigationLink(value:)`

**Why:** Better performance, type-safe navigation, cleaner code.

```swift
// ❌ OLD WAY
List(items) { item in
    NavigationLink(destination: DetailView(item: item)) {
        Text(item.name)
    }
}

// ✅ NEW WAY
List(items) { item in
    NavigationLink(item.name, value: item)
}
.navigationDestination(for: Item.self) { item in
    DetailView(item: item)
}
```

---

### 14. Tab View API

❌ **AVOID:** Old `tabItem()` modifier
✅ **PREFER:** New `Tab` API

**Why:** Type-safe tab selection, better support for iOS 18+ features like search tabs.

```swift
// ❌ OLD WAY
TabView {
    RoutesView()
        .tabItem {
            Label("Routes", systemImage: "figure.climbing")
        }
}

// ✅ NEW WAY (iOS 18+)
TabView {
    Tab("Routes", systemImage: "figure.climbing") {
        RoutesView()
    }
}
```

---

## Code Quality & Performance

### 15. GeometryReader Overuse

⚠️ **WARNING:** AI tools overuse `GeometryReader` unnecessarily

**Why:** `GeometryReader` affects layout behavior and can cause performance issues.

**Alternatives:**

- `visualEffect()` for reading size/position
- `containerRelativeFrame()` for relative sizing
- Layout protocol for custom layouts

```swift
// ❌ OVERUSED
GeometryReader { geometry in
    Text("Hello")
        .frame(width: geometry.size.width * 0.5)
}

// ✅ BETTER (iOS 17+)
Text("Hello")
    .containerRelativeFrame(.horizontal) { length, axis in
        length * 0.5
    }
```

---

### 16. Fixed Frame Sizes

❌ **AVOID:** Adding fixed frames where not needed
✅ **PREFER:** Let SwiftUI handle layout naturally

**Why:** Fixed frames break adaptive layouts and accessibility.

```swift
// ❌ BAD
Text("Hello").frame(width: 200, height: 50)

// ✅ GOOD
Text("Hello")
    .padding()  // Let it size naturally
```

---

### 17. Multiple Types Per File

❌ **AVOID:** Placing many types in a single file
✅ **PREFER:** One primary type per file

**Why:** Faster build times, better code organization.

---

## Swift Language Patterns

### 18. ForEach with Enumerated

❌ **AVOID:** Unnecessary `Array()` wrapper
✅ **PREFER:** Direct enumeration

```swift
// ❌ BAD
ForEach(Array(items.enumerated()), id: \.element.id) { index, item in
    Text(item.name)
}

// ✅ GOOD
ForEach(items.enumerated(), id: \.element.id) { index, item in
    Text(item.name)
}
```

---

### 19. Documents Directory

❌ **AVOID:** Long code to find documents directory
✅ **PREFER:** `URL.documentsDirectory`

```swift
// ❌ OLD WAY
let documentsPath = FileManager.default.urls(
    for: .documentDirectory,
    in: .userDomainMask
).first!

// ✅ NEW WAY
let documentsPath = URL.documentsDirectory
```

---

### 20. Number Formatting

❌ **AVOID:** C-style format strings
✅ **PREFER:** Type-safe format styles

**Why:** Safer, more localized, harder to get wrong.

```swift
// ❌ OLD WAY
Text(String(format: "%.2f", abs(value)))

// ✅ NEW WAY
Text(abs(value), format: .number.precision(.fractionLength(2)))
```

---

## Concurrency & Main Actor

### 21. DispatchQueue.main Usage

❌ **AVOID:** Excessive `DispatchQueue.main.async`
✅ **PREFER:** Modern Swift concurrency

**Why:** Swift concurrency is safer and more maintainable.

```swift
// ❌ OLD WAY
DispatchQueue.main.async {
    self.updateUI()
}

// ✅ NEW WAY
Task { @MainActor in
    self.updateUI()
}
```

---

### 22. Main Actor Isolation

ℹ️ **INFO:** In new app projects, main actor isolation is ON by default

**What this means:** You DON'T need to mark things with `@MainActor` unless you're explicitly moving off the main actor.

```swift
// ❌ UNNECESSARY in new projects
@MainActor
class ViewModel {
    // Already main-actor isolated by default
}

// ✅ ONLY NEEDED when moving OFF main actor
actor BackgroundProcessor {
    // Explicitly NOT main-actor isolated
}
```

---

## Rendering & Graphics

### 23. Image Rendering

❌ **AVOID:** `UIGraphicsImageRenderer` for SwiftUI views
✅ **PREFER:** `ImageRenderer`

**Why:** Native SwiftUI API, simpler, more efficient.

```swift
// ❌ OLD WAY
let renderer = UIGraphicsImageRenderer(size: size)
let image = renderer.image { context in
    // Complex UIKit rendering
}

// ✅ NEW WAY
let renderer = ImageRenderer(content: MyView())
let image = renderer.uiImage
```

---

## Known AI Hallucinations

### 24. Nonexistent APIs

⚠️ **WARNING:** LLMs frequently hallucinate APIs that don't exist

**What to do:**

1. **Always verify** API existence before using
2. **Check Apple docs** when something looks too good to be true
3. **Use context7 MCP server** to lookup current iOS APIs
4. **Test immediately** to catch hallucinations early

**Common signs of hallucination:**

- API name sounds logical but you've never seen it
- Modifier that does exactly what you want (suspiciously convenient)
- Parameters that don't match Apple's naming conventions
- Combine modifiers in ways that don't compile

---

## [my-app]-Specific Rules

### Apply These Guidelines to [my-app]

**When reviewing or writing code for this project:**

1. **All SwiftUI views** must follow these modern patterns
2. **Accessibility first** - Always use proper buttons, semantic fonts
3. **No deprecated APIs** - Use the modern alternatives listed above
4. **Observable over ObservableObject** - We use `@Observable` macro
5. **Sheet navigation** - Our detail views are sheets, not pushed
6. **No GeometryReader abuse** - Use alternatives
7. **CloudKit compatible** - No `@Attribute(.unique)` in SwiftData models
8. **iOS 17+ target** - We can use all modern APIs
9. **Preview everything** - Every view needs SwiftUI previews

### Before Committing Code

**Checklist:**

- [ ] No `foregroundColor()` - changed to `foregroundStyle()`
- [ ] No `cornerRadius()` - changed to `clipShape(.rect(cornerRadius:))`
- [ ] No deprecated `onChange` - using 2-param or 0-param variant
- [ ] No `NavigationView` - using `NavigationStack`
- [ ] Buttons instead of `onTapGesture()` (unless special case)
- [ ] Button labels include text (not just images)
- [ ] Semantic fonts, not fixed sizes
- [ ] No `@Attribute(.unique)` in SwiftData models
- [ ] Extracted views, not computed properties
- [ ] Modern navigation APIs
- [ ] No excessive `GeometryReader`
- [ ] No hallucinated APIs

---

## Quick Reference Card

| ❌ Don't Use                | ✅ Use Instead                    | Why                       |
| --------------------------- | --------------------------------- | ------------------------- |
| `foregroundColor()`         | `foregroundStyle()`               | Deprecated, less powerful |
| `cornerRadius()`            | `clipShape(.rect(cornerRadius:))` | Deprecated, less flexible |
| `onChange(of:) { }`         | `onChange(of:) { old, new in }`   | Old variant unsafe        |
| `NavigationView`            | `NavigationStack`                 | Deprecated                |
| `Task.sleep(nanoseconds:)`  | `Task.sleep(for: .seconds(1))`    | More readable             |
| `onTapGesture`              | `Button`                          | Better accessibility      |
| `.font(.system(size: 20))`  | `.font(.title)`                   | Respects Dynamic Type     |
| `ObservableObject`          | `@Observable`                     | Better performance        |
| Computed properties         | Extracted views                   | View invalidation         |
| `@Attribute(.unique)`       | App logic                         | CloudKit incompatible     |
| Inline `NavigationLink`     | `navigationDestination(for:)`     | Better performance        |
| `tabItem()`                 | `Tab` API                         | Type-safe, modern         |
| `GeometryReader` everywhere | `visualEffect()`, etc             | Better performance        |
| `DispatchQueue.main.async`  | `Task { @MainActor in }`          | Modern concurrency        |
| `UIGraphicsImageRenderer`   | `ImageRenderer`                   | Native SwiftUI            |

---

## Additional Resources

- **Context7 MCP Server:** Always verify APIs with context7 for latest iOS documentation
- **Apple HIG:** Follow Human Interface Guidelines for patterns
- **SwiftUI Lab:** Check release notes for new APIs
- **Hacking with Swift:** Paul Hudson's tutorials and articles

---

## Summary

**For Claude Code:**

1. **Always prefer modern APIs** over deprecated ones
2. **Accessibility matters** - Use proper buttons, semantic fonts
3. **Verify APIs exist** - Use context7 to check documentation
4. **Extract views** - Don't use computed properties for view composition
5. **No CloudKit conflicts** - Avoid unique attributes in SwiftData
6. **Performance matters** - Avoid GeometryReader abuse, multiple types per file
7. **Modern concurrency** - Use Task/async-await, not DispatchQueue
8. **Check the list** - Review code against the guidelines before finalizing

**The goal:** Write clean, maintainable, accessible Swift code that follows modern best practices and doesn't rely on deprecated APIs or AI hallucinations.
