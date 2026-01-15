# Simplified Swift for [my-app]

A plain-English guide to Swift patterns you'll encounter in [my-app]. For when Claude Code does something and you want to understand what actually happened.

## SwiftUI Basics

### Views are Structs (Not Classes)
```swift
// This is what you'll see everywhere
struct RouteListView: View {
	var body: some View {
		// UI goes here
	}
}
```
**What this means:** Views are lightweight blueprints. SwiftUI creates and destroys them constantly. Don't overthink it.

### @State - Internal View Data
```swift
@State private var isShowingForm = false
```
**What this means:** This variable belongs to THIS view. When it changes, the view updates. That's it.

**When to use:** Temporary UI state (is sheet showing? which tab selected?)

### @Binding - Shared Data Between Views
```swift
@Binding var route: Route
```
**What this means:** This view can read AND change data that lives elsewhere. Two-way connection.

**When to use:** When a child view needs to modify parent's data (form editing a route).

### @Environment - App-Wide Stuff
```swift
@Environment(\.modelContext) private var modelContext
```
**What this means:** Access to stuff provided by your app (database, navigation, colour scheme).

**When to use:** SwiftData operations, navigation, dismissing sheets.

### @Query - Database Queries
```swift
@Query private var routes: [Route]
```
**What this means:** Automatically fetches and keeps updated your SwiftData models.

**When to use:** Any time you need to display a list of your models.

## Common Patterns You'll See

### Optional Chaining (`?`)
```swift
route.location?.name
```
**What this means:** "If route has a location, get its name. If not, just return nil instead of crashing."

**Why it matters:** Climbing routes don't always have locations assigned yet.

### Nil Coalescing (`??`)
```swift
route.notes ?? "No notes"
```
**What this means:** "Use route.notes if it exists, otherwise use 'No notes' instead."

**Why it matters:** Better than showing blank spaces in your UI.

### Guard Statements
```swift
guard let location = route.location else { return }
```
**What this means:** "If route doesn't have a location, stop right here and return."

**Why it matters:** Cleaner than nested if-statements. Fails fast.

### Computed Properties
```swift
var displayName: String {
	return "\(name) - \(grade)"
}
```
**What this means:** Not stored data, calculated on-demand from other properties.

**When you'll use it:** Display formatting, derived values (like "3 routes sent this month").

## SwiftData Specifics

### @Model Macro
```swift
@Model
class Route {
	var name: String
	var grade: String
	// ...
}
```
**What this means:** SwiftData will automatically save this to the database.

**Critical:** Changing properties here changes your database schema. Be careful.

### Relationships
```swift
// In Route model
var location: Location?

// In Location model
var routes: [Route] = []
```
**What this means:** Routes can belong to locations. Location can have many routes. SwiftData handles the connections.

**Don't break:** These relationships are fragile. Changing them requires migration.

### Model Context
```swift
modelContext.insert(newRoute)
modelContext.delete(route)
try? modelContext.save()
```
**What this means:** Your database operations. Insert = create, delete = remove, save = commit changes.

**When things break:** Usually because save() failed or relationship constraints violated.

## Code Smells (Bad Signs)

### Over-Complicated
```swift
// BAD - Too nested, hard to follow
if route != nil {
	if route.location != nil {
		if route.location.name != nil {
			// do something
		}
	}
}

// GOOD - Simple and clear
guard let locationName = route.location?.name else { return }
// do something with locationName
```

### Repetitive Code
```swift
// BAD - Same code in multiple places
Text(route.name).font(.headline).foregroundColor(.primary)
Text(location.name).font(.headline).foregroundColor(.primary)
Text(event.name).font(.headline).foregroundColor(.primary)

// GOOD - Reusable component
struct HeadlineText: View {
	let text: String
	var body: some View {
		Text(text).font(.headline).foregroundColor(.primary)
	}
}
```

### Magic Numbers
```swift
// BAD - What does 20 mean?
.padding(20)

// GOOD - Clear intent
.padding(.standard) // where .standard is defined somewhere obvious
```

## SwiftUI Layout Patterns

### Stacks
```swift
VStack { }  // Vertical - items stack top to bottom
HStack { }  // Horizontal - items stack left to right
ZStack { }  // Depth - items stack front to back (overlapping)
```

### Common Modifiers
```swift
.padding()           // Space around the view
.frame(width: 200)   // Set size
.background(Color.blue) // Colour behind view
.foregroundColor(.white) // Text/icon colour
.cornerRadius(10)    // Rounded corners
.shadow(radius: 5)   // Drop shadow
```

### Conditional Views
```swift
// Show different things based on state
if isEditing {
	EditView()
} else {
	DisplayView()
}
```

## What "Best Practices" Actually Means for [my-app]

### Extract Views When They're Reused
Not: Create a component for everything
But: If you copy-paste a view 2-3 times, extract it

### Keep Views Small
Not: Every view must be under 100 lines
But: If you can't see a view on one screen, consider breaking it up

### Name Things Clearly
Not: Follow strict naming conventions
But: `RouteListView` is better than `RLV` or `ListView`

### Comments for Context
Not: Comment every line
But: Explain WHY you made UX decisions ("Showing preview of first 3 holds only to avoid clutter")

### Handle Errors Gracefully
Not: Perfect error handling everywhere
But: Don't crash if user cancels a photo picker or loses internet

## Red Flags to Watch For

These patterns might indicate Claude Code is over-complicating:

- **Too many nested ifs** - Consider guard statements or early returns
- **Massive switch statements** - Might need better model design
- **Lots of force unwrapping (`!`)** - Crashes waiting to happen
- **Callback hell** - Modern Swift uses async/await instead
- **Manual state synchronisation** - SwiftUI should handle this
- **Repeated database calls** - @Query should handle this

## When to Ask "Is This Too Complicated?"

If you read code and think:
- "I have no idea what this does"
- "Why are there 5 steps to do something simple?"
- "This seems like magic"
- "I could never modify this myself"

Then it probably IS too complicated for a solo project.

## Learning Resources

When you want to understand something deeper:
- **Hacking with Swift** (Paul Hudson) - Best free SwiftUI tutorials
- **Apple's SwiftUI Tutorials** - Official and comprehensive
- **Stack Overflow** - For specific problems (but check dates, Swift changes fast)

---

**Remember:** You don't need to be a Swift expert. You need to understand YOUR code well enough to maintain it and make UX decisions. That's a much smaller goal.
