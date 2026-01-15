---
name: ios-dev-standards
description: Enforce iOS development best practices for SwiftUI apps targeting iOS 17+. Use this skill when writing, reviewing, or refactoring Swift/SwiftUI code to ensure simplicity, consistency, maintainability, and adherence to Apple's guidelines. Validates code architecture, SwiftUI patterns, data flow, performance, and iOS platform conventions. Integrates Context7 MCP for codebase analysis and pattern detection. Ideal for reviewing pull requests, architectural decisions, new features, and ensuring code quality standards.
---

# iOS Development Standards

Ensure [my-app]'s codebase remains simple, consistent, and follows iOS best practices for iOS 17+.

## Core Principles

### 1. Simplicity Over Cleverness

- Write obvious code that any developer can understand
- Avoid over-engineering and premature optimisation
- Prefer standard patterns over custom solutions
- If it needs extensive comments to explain, simplify it

### 2. Consistency is King

- Follow established patterns in the codebase
- Match naming conventions throughout
- Use the same approach for similar problems
- Maintain visual and functional consistency

### 3. iOS Native First

- Leverage native SwiftUI components
- Follow Apple's Human Interface Guidelines
- Use system APIs and frameworks
- Stay current with iOS 17+ features

### 4. Maintainability Matters

- Future developers (including you) should understand it easily
- Keep files focused and reasonably sized
- Clear separation of concerns
- Comprehensive but not excessive testing

## When to Use This Skill

**Automatic activation for**:

- Code reviews and pull request feedback
- Architectural decision validation
- New feature implementation guidance
- Refactoring recommendations
- SwiftUI pattern verification
- Performance issue diagnosis

**Explicit usage**:

- "Review this code for iOS best practices"
- "Is this SwiftUI pattern appropriate?"
- "How can I simplify this implementation?"
- "Check this for consistency with existing code"

## Context7 MCP Integration

Use Context7 for comprehensive codebase analysis:

### Query Patterns

```swift
// Find similar implementations
"Find all view models in the codebase"
"Show me how we handle CloudKit sync elsewhere"
"Find examples of sheet presentation"

// Check consistency
"How do we typically structure route models?"
"What's our pattern for error handling?"
"Show me other uses of this view component"

// Identify patterns
"Find all data models with CloudKit integration"
"Show me view hierarchies for detail screens"
"List all uses of @Published properties"
```

### Before Writing New Code

1. Query Context7 for similar existing implementations
2. Match established patterns
3. Reuse existing components where possible
4. Maintain architectural consistency

### During Code Review

1. Compare against existing patterns using Context7
2. Verify consistency across similar features
3. Check for duplicated logic
4. Ensure naming conventions match

## SwiftUI Best Practices (iOS 17+)

### View Structure

**DO**: Keep views small and focused

```swift
struct RouteCard: View {
	let route: Route

	var body: some View {
		VStack(alignment: .leading, spacing: 12) {
			RouteHeader(route: route)
			RouteGrade(grade: route.grade)
			if let photo = route.photo {
				RoutePhoto(photo: photo)
			}
		}
		.padding()
		.background(.background.secondary)
		.clipShape(RoundedRectangle(cornerRadius: 12))
	}
}
```

**DON'T**: Create massive views with complex logic

```swift
struct RouteCard: View {
	let route: Route
	@State private var showDetail = false
	@State private var isEditing = false
	@State private var tempGrade: Grade?
	// ... 20 more @State properties

	var body: some View {
		// 200+ lines of view code with complex nested conditions
	}
}
```

### Data Flow

**DO**: Clear, unidirectional data flow

```swift
struct RouteDetailView: View {
	@Bindable var route: Route
	@Environment(\.modelContext) private var modelContext

	var body: some View {
		Form {
			TextField("Name", text: $route.name)
			GradePicker(grade: $route.grade)
		}
	}
}
```

**DON'T**: Passing data through multiple layers unnecessarily

```swift
// Avoid prop drilling through 4+ view layers
ContentView -> TabView -> ListView -> RowView -> DetailButton
// Instead, use @Environment or direct navigation
```

### State Management

**DO**: Use appropriate property wrappers

- `@State` - Private view state
- `@Bindable` - Two-way binding to SwiftData models
- `@Environment` - Dependency injection (modelContext, dismiss, etc.)
- `@Observable` - View models and shared state (Swift 5.9+)

**DON'T**: Mix old and new patterns

```swift
// Don't use @ObservedObject with @Observable classes
// Don't use @StateObject when @State works fine
// Don't create @Published properties in @Observable classes
```

### ViewModifiers

**DO**: Create reusable modifiers for common styling

```swift
struct CardStyle: ViewModifier {
	func body(content: Content) -> some View {
		content
			.padding()
			.background(.background.secondary)
			.clipShape(RoundedRectangle(cornerRadius: 12))
	}
}

extension View {
	func cardStyle() -> some View {
		modifier(CardStyle())
	}
}
```

**DON'T**: Repeat styling inline everywhere

```swift
// Avoid this pattern repeated across 20 views
.padding()
.background(.background.secondary)
.clipShape(RoundedRectangle(cornerRadius: 12))
```

## Swift Data Best Practices

### Model Definition

**DO**: Clean, well-structured models

```swift
@Model
final class Route {
	var name: String
	var grade: Grade
	var type: RouteType
	var createdAt: Date

	@Relationship(deleteRule: .cascade)
	var attempts: [Attempt]

	@Relationship(inverse: \Location.routes)
	var location: Location?

	init(name: String, grade: Grade, type: RouteType) {
		self.name = name
		self.grade = grade
		self.type = type
		self.createdAt = Date()
	}
}
```

**DON'T**: Overuse relationships or create circular dependencies

```swift
@Model
final class Route {
	// Don't create bidirectional relationships without inverse
	var location: Location?
	var attempts: [Attempt]
	var photos: [Photo]
	var tags: [Tag]
	var comments: [Comment]
	// ... 10 more relationships
}
```

### Queries and Predicates

**DO**: Use type-safe predicates with #Predicate

```swift
@Query(
	filter: #Predicate<Route> { route in
		route.status == .project && route.createdAt > startDate
	},
	sort: \.createdAt,
	order: .reverse
)
private var projects: [Route]
```

**DON'T**: Over-fetch or filter in memory

```swift
// Don't fetch everything then filter
@Query private var allRoutes: [Route]
var projects: [Route] {
	allRoutes.filter { $0.status == .project }
}
```

## CloudKit Integration Patterns

### Sync Architecture

**DO**: Use CloudKit Shared Zones correctly

```swift
// For shared trips
let sharedDatabase = CKContainer.default().sharedCloudDatabase
let zone = CKRecordZone(zoneName: "SharedTrip-\(tripID)")

// Clear ownership model
struct SharedTrip {
	let owner: CKRecord.Reference
	let participants: [CKRecord.Reference]
	let routes: [Route]
}
```

**DON'T**: Mix private and shared data incorrectly

```swift
// Don't store personal route notes in shared zones
// Don't expose private training data
// Don't create complex sync resolution logic
```

### Error Handling

**DO**: Handle CloudKit errors gracefully

```swift
do {
	try await container.save(record)
} catch let error as CKError {
	switch error.code {
	case .networkUnavailable:
		// Queue for later sync
		await syncQueue.add(record)
	case .quotaExceeded:
		// Show user-friendly message
		showAlert("Storage limit reached")
	default:
		logger.error("CloudKit error: \(error)")
	}
}
```

**DON'T**: Ignore CloudKit errors or crash

```swift
// Don't do this
try! await container.save(record)

// Or this
try? await container.save(record) // Silently fails
```

## Architecture Guidelines

### MVVM-Light Pattern

**DO**: Keep view models focused and thin

```swift
@Observable
final class RouteFormViewModel {
	var name: String = ""
	var grade: Grade = .v0
	var type: RouteType = .boulder

	func validate() -> Bool {
		!name.isEmpty && name.count <= 100
	}

	func createRoute(in context: ModelContext) throws -> Route {
		guard validate() else {
			throw ValidationError.invalidName
		}

		let route = Route(name: name, grade: grade, type: type)
		context.insert(route)
		return route
	}
}
```

**DON'T**: Create overly complex view models

```swift
// Avoid view models that do everything
@Observable
final class RouteViewModel {
	// Managing state, networking, persistence, navigation, analytics...
	// This should be broken into focused components
}
```

### File Organization

**DO**: Group by feature, not by type

```
[my-app]/
├── Features/
│   ├── Routes/
│   │   ├── Models/
│   │   │   ├── Route.swift
│   │   │   └── RouteType.swift
│   │   ├── Views/
│   │   │   ├── RouteListView.swift
│   │   │   ├── RouteDetailView.swift
│   │   │   └── RouteCard.swift
│   │   └── ViewModels/
│   │       └── RouteFormViewModel.swift
│   ├── Locations/
│   └── Dashboard/
├── Shared/
│   ├── Components/
│   └── Extensions/
└── Core/
	├── Persistence/
	└── Networking/
```

**DON'T**: Group by type (all models together, all views together)

```
[my-app]/
├── Models/  // 50+ files mixed together
├── Views/   // 100+ files hard to navigate
├── ViewModels/
└── Services/
```

## Code Quality Standards

### Naming Conventions

**DO**: Clear, descriptive names

```swift
// Views
struct RouteDetailView: View
struct LocationPickerSheet: View

// View Models
class RouteFormViewModel
class LocationSearchViewModel

// Properties
var selectedGrade: Grade
var isShowingDeleteConfirmation: Bool

// Functions
func updateRouteStatus()
func validateClimbingDate()
```

**DON'T**: Abbreviations or unclear names

```swift
// Avoid
struct RtDtlVw: View
var selGrd: Grade
var showing: Bool  // Showing what?
func upd()
func validate()  // Validate what?
```

### Comments and Documentation

**DO**: Document complex logic and business rules

```swift
/// Calculates the user's pyramid distribution across grades.
/// Groups sends by grade and counts attempts, helping identify strengths.
/// - Returns: Dictionary mapping grade to send count
func calculateGradePyramid() -> [Grade: Int] {
	// Grade pyramids show climbing proficiency distribution
	// More sends at lower grades indicate solid base
	// ...
}
```

**DON'T**: State the obvious

```swift
// Bad comments
var name: String // The name
func save() {} // Saves the route
```

### Error Handling

**DO**: Use typed errors and handle specifically

```swift
enum RouteError: LocalizedError {
	case invalidGrade
	case missingLocation
	case syncFailed(underlying: Error)

	var errorDescription: String? {
		switch self {
		case .invalidGrade:
			return "Please select a valid climbing grade"
		case .missingLocation:
			return "Add a location before creating this route"
		case .syncFailed(let error):
			return "Failed to sync: \(error.localizedDescription)"
		}
	}
}
```

**DON'T**: Use generic errors or force-unwrap

```swift
// Don't do this
func loadRoute() -> Route {
	guard let route = try? fetchRoute() else {
		fatalError() // Crashes the app
	}
	return route
}
```

## Performance Guidelines

### SwiftUI Performance

**DO**: Optimize view updates

```swift
// Use Equatable to control view updates
struct RouteCard: View, Equatable {
	let route: Route

	static func == (lhs: RouteCard, rhs: RouteCard) -> Bool {
		lhs.route.id == rhs.route.id &&
		lhs.route.name == rhs.route.name &&
		lhs.route.grade == rhs.route.grade
	}

	var body: some View {
		// View implementation
	}
}

// Use in parent
RouteCard(route: route)
	.equatable()
```

**DON'T**: Cause unnecessary re-renders

```swift
// Avoid creating new objects in body
var body: some View {
	let formatter = DateFormatter() // DON'T create here
	Text(formatter.string(from: date))
}

// Instead, use @State or computed at struct level
private static let formatter = DateFormatter()
```

### LazyStacks and Grids

**DO**: Use lazy loading for long lists

```swift
ScrollView {
	LazyVStack(spacing: 12) {
		ForEach(routes) { route in
			RouteCard(route: route)
		}
	}
}
```

**DON'T**: Load everything upfront

```swift
ScrollView {
	VStack { // Don't use VStack for 100+ items
		ForEach(routes) { route in
			ComplexRouteCard(route: route) // Loads all immediately
		}
	}
}
```

### Image Handling

**DO**: Optimize image loading and caching

```swift
struct RoutePhoto: View {
	let photo: Photo

	var body: some View {
		AsyncImage(url: photo.url) { phase in
			switch phase {
			case .empty:
				ProgressView()
			case .success(let image):
				image
					.resizable()
					.aspectRatio(contentMode: .fill)
			case .failure:
				Image(systemName: "photo")
			@unknown default:
				EmptyView()
			}
		}
		.frame(height: 200)
		.clipped()
	}
}
```

**DON'T**: Load full-resolution images everywhere

```swift
// Don't load 4K photos in list thumbnails
// Don't block main thread for image processing
// Don't load all trip photos at once
```

## Testing Standards

### What to Test

**DO**: Test business logic and data transformations

```swift
@Test("Route grade pyramid calculation")
func testGradePyramidCalculation() async throws {
	let routes = [
		Route(grade: .v4),
		Route(grade: .v4),
		Route(grade: .v5),
	]

	let pyramid = await calculatePyramid(routes)

	#expect(pyramid[.v4] == 2)
	#expect(pyramid[.v5] == 1)
}

@Test("Route status transitions")
func testRouteStatusTransitions() {
	let route = Route(status: .project)

	route.markAsSent(on: Date())

	#expect(route.status == .sent)
	#expect(route.sentDate != nil)
}
```

**DON'T**: Test SwiftUI views directly (brittle, slow)

```swift
// Avoid testing view rendering
// Focus on the logic, not the UI
```

### Test Organization

**DO**: Group tests logically

```swift
@Suite("Route Management")
struct RouteTests {
	@Test("Creating routes")
	func testCreateRoute() { }

	@Test("Updating route grades")
	func testUpdateGrade() { }
}

@Suite("CloudKit Sync")
struct SyncTests {
	@Test("Syncing new routes")
	func testSyncNewRoute() { }
}
```

## iOS 17+ Features to Leverage

### SwiftData

- Use for local persistence
- Integrate with CloudKit for sync
- Leverage @Query for reactive updates

### Observable Macro

- Replace @ObservedObject and @StateObject
- Simpler, more performant
- Better integration with SwiftUI

### #Preview Macro

- Use for all views
- Include multiple variants (light/dark, sizes)
- Provide sample data

### Swift Testing

- Modern testing framework
- Better error messages
- Async support built-in

## Code Review Checklist

When reviewing code, check:

### Simplicity

- [ ] Could this be simpler?
- [ ] Is the logic clear and obvious?
- [ ] Are we using standard patterns?
- [ ] Is there unnecessary complexity?

### Consistency

- [ ] Matches existing code style?
- [ ] Follows naming conventions?
- [ ] Uses established patterns?
- [ ] Consistent error handling?

### iOS Best Practices

- [ ] Follows SwiftUI patterns?
- [ ] Uses appropriate property wrappers?
- [ ] Handles iOS lifecycle correctly?
- [ ] Respects user preferences (dark mode, text size)?

### Performance

- [ ] Efficient view updates?
- [ ] Appropriate use of lazy loading?
- [ ] No obvious performance issues?
- [ ] Images optimized?

### Maintainability

- [ ] Easy to understand?
- [ ] Well-organized?
- [ ] Adequately tested?
- [ ] Documented where needed?

### Context7 Verification

- [ ] Checked against existing patterns?
- [ ] No duplicate implementations?
- [ ] Reuses existing components?
- [ ] Maintains architectural consistency?

## Common Anti-Patterns to Avoid

### Over-Engineering

❌ Creating abstractions before they're needed
❌ Complex inheritance hierarchies
❌ Generic solutions for specific problems
❌ Premature optimization

### Inconsistency

❌ Different approaches for similar problems
❌ Mixed naming styles
❌ Inconsistent error handling
❌ Varying code organization

### Poor SwiftUI Usage

❌ Massive views with complex logic
❌ Incorrect property wrapper usage
❌ Fighting the framework
❌ Overusing @State

### Maintenance Issues

❌ God classes (do everything)
❌ Tight coupling
❌ No separation of concerns
❌ Inadequate testing

## Reference Files

- **references/swiftui-patterns.md** - Common SwiftUI patterns and examples
- **references/swiftdata-guide.md** - SwiftData best practices and migration patterns
- **references/cloudkit-integration.md** - CloudKit sync architecture and error handling
- **references/testing-guide.md** - Testing strategies and examples
- **references/context7-queries.md** - Context7 MCP query patterns for [my-app]

## Context7 MCP Usage Examples

### Finding Similar Code

```
"Show me how we handle photo uploads elsewhere"
"Find all view models that use @Observable"
"List files that use CloudKit"
```

### Consistency Checks

```
"How do we typically name sheet presentation properties?"
"Show me our error handling pattern"
"Find examples of location pickers"
```

### Architecture Analysis

```
"Show me the data flow for route creation"
"List all SwiftData models"
"Find navigation patterns in detail views"
```

### Before Implementing

Always query Context7 first:

1. Check if similar functionality exists
2. Find the established pattern
3. Reuse or adapt existing code
4. Maintain consistency
