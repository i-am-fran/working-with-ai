# Apple Design & Development Guidelines for [my-app]

[my-app] should feel like an Apple-built app. This means following Human Interface Guidelines (HIG) for UX/UI and Apple's Swift/SwiftUI conventions for code.

## Core Principle

**"If Apple made a climbing app, what would it look like?"**

---

## iOS Design Patterns (HIG)

### Navigation Patterns

#### ✅ What [my-app] Should Use

- **Tab Bar** for top-level sections (if you have 2-5 main sections)
- **Navigation Stack** for hierarchical content (List → Detail → Edit)
- **Sidebar** for iPad (you're already using this - good!)
- **Sheets** for temporary tasks (creating/editing routes)
- **Alerts** for destructive actions (delete route confirmation)

#### ❌ What to Avoid

- Custom navigation that doesn't follow iOS conventions
- Hamburger menus (Apple discourages these)
- Bottom sheets that aren't native iOS sheets
- Back buttons that don't look like iOS back buttons

**Current check:** Your sidebar navigation is correct for iPad. For iPhone, consider tab bar instead.

### Layout & Spacing

#### System Spacing (Use These)

```swift
.padding()                    // Standard padding (16pt)
.padding(.standard)           // Same as above
.padding(.small)              // 8pt
.padding(.large)              // 20pt

// List row spacing - iOS handles this automatically
// Don't add custom spacing between list items
```

#### Safe Areas

```swift
// ✅ GOOD - Respects safe areas
VStack {
	// Content
}
// iOS automatically handles safe area insets

// ❌ BAD - Ignoring safe areas
.edgesIgnoringSafeArea(.all)  // Only use when absolutely necessary (photos, maps)
```

#### Dynamic Type Support

```swift
// ✅ GOOD - Scales with user's text size preference
Text("Route Name")
	.font(.headline)  // System font, scales automatically

// ❌ BAD - Fixed size text
Text("Route Name")
	.font(.system(size: 17))  // Won't scale for accessibility
```

### Typography

#### System Font Styles (Use These)

```swift
.font(.largeTitle)    // Page titles
.font(.title)         // Section headers
.font(.title2)        // Sub-section headers
.font(.headline)      // Emphasised text, list item titles
.font(.body)          // Default text
.font(.callout)       // Secondary text
.font(.subheadline)   // Tertiary text
.font(.footnote)      // Metadata, timestamps
.font(.caption)       // Labels, supplementary info
```

**Rule:** Never use custom font sizes. Always use semantic styles.

#### Font Weights

```swift
.fontWeight(.regular)   // Default
.fontWeight(.medium)    // Slight emphasis
.fontWeight(.semibold)  // Strong emphasis
.fontWeight(.bold)      // Very strong emphasis
```

**Use sparingly:** Don't bold everything. Let hierarchy do the work.

### Colours

#### System Colours (Always Use These)

```swift
// Dynamic - automatically adapt to light/dark mode
Color.primary           // Main text
Color.secondary         // Secondary text
Color.accentColor       // Your app's brand colour (set in Assets)

// Semantic colours
Color.red               // Destructive actions
Color.blue              // Default interactive colour
Color.green             // Success, positive actions
Color.gray              // Disabled, placeholder
```

#### Background Colours

```swift
Color(.systemBackground)           // Main background
Color(.secondarySystemBackground)  // Cards, grouped lists
Color(.tertiarySystemBackground)   // Nested cards
```

**Critical:** These automatically handle light/dark mode. Never use `Color.white` or `Color.black` for backgrounds.

### SF Symbols (Icons)

#### Always Use SF Symbols

```swift
// ✅ GOOD - Native iOS icons
Image(systemName: "plus.circle.fill")
Image(systemName: "mappin.circle")
Image(systemName: "photo")

// ❌ BAD - Custom icons (unless absolutely necessary)
Image("custom-icon")  // Only use for climbing-specific glyphs you'll design
```

#### Icon Sizing

```swift
Image(systemName: "plus")
	.imageScale(.small)    // Small
	.imageScale(.medium)   // Default
	.imageScale(.large)    // Large

// Or specific size
	.font(.system(size: 24))
```

**Your custom climbing icons** (holds, grades) should visually match SF Symbols style.

### Lists & Forms

#### Native List Styles

```swift
// ✅ GOOD - iOS list styles
List {
	// content
}
.listStyle(.insetGrouped)  // Modern grouped look (most common)
.listStyle(.plain)         // Simple list
.listStyle(.sidebar)       // For navigation sidebars
```

#### List Rows

```swift
// ✅ GOOD - Simple, clean rows
NavigationLink {
	RouteDetailView(route: route)
} label: {
	VStack(alignment: .leading) {
		Text(route.name)
			.font(.headline)
		Text(route.grade)
			.font(.subheadline)
			.foregroundColor(.secondary)
	}
}

// ❌ BAD - Over-designed custom rows
// Unless you have a very good UX reason, stick to simple patterns
```

#### Forms (For Create/Edit)

```swift
// ✅ GOOD - Native form style
Form {
	Section("Route Details") {
		TextField("Name", text: $name)
		Picker("Grade", selection: $grade) {
			// options
		}
	}

	Section("Location") {
		// location picker
	}
}
.navigationTitle("New Route")
.navigationBarTitleDisplayMode(.inline)
```

**Apple convention:** Forms use grouped sections with headers.

### Buttons & Controls

#### Button Styles

```swift
// Primary action
Button("Save") { }
	.buttonStyle(.borderedProminent)  // Filled, coloured

// Secondary action
Button("Cancel") { }
	.buttonStyle(.bordered)           // Outlined

// Tertiary action
Button("Delete") { }
	.buttonStyle(.borderless)         // Text only
```

#### Toolbar Buttons

```swift
.toolbar {
	ToolbarItem(placement: .primaryAction) {
		Button("Save") { }
	}
	ToolbarItem(placement: .cancellationAction) {
		Button("Cancel") { }
	}
}
```

**Convention:** Save/Done = primary action (right), Cancel = left

### Sheets & Modals

#### Presentation Styles

```swift
// ✅ GOOD - Native sheet presentation
.sheet(isPresented: $isShowingForm) {
	NavigationStack {
		RouteFormView()
			.navigationTitle("New Route")
			.toolbar {
				ToolbarItem(placement: .cancellationAction) {
					Button("Cancel") { isShowingForm = false }
				}
				ToolbarItem(placement: .primaryAction) {
					Button("Save") { saveRoute() }
				}
			}
	}
}

// Full screen modals - only for camera, immersive experiences
.fullScreenCover(isPresented: $isShowingCamera) {
	CameraView()
}
```

#### Confirmation Dialogs

```swift
// ✅ GOOD - For destructive actions
.confirmationDialog("Delete this route?", isPresented: $showingDeleteConfirm) {
	Button("Delete", role: .destructive) {
		deleteRoute()
	}
	Button("Cancel", role: .cancel) { }
}
```

### Animations

#### Use Built-in Animations

```swift
// ✅ GOOD - Simple, native animations
withAnimation {
	isExpanded.toggle()
}

// Or on specific views
Text("New Route Added")
	.transition(.move(edge: .top))
```

#### Animation Durations

Apple's standard:

- **Quick interactions:** 0.2-0.3 seconds
- **State changes:** 0.3-0.4 seconds
- **Major transitions:** 0.4-0.5 seconds

**Rule:** When in doubt, use `.animation(.default)` - Apple's already tuned it perfectly.

---

## SwiftUI Code Conventions

### File Organisation

```
Views/
├── Navigation/
│   └── SidebarView.swift        // One view per file
├── Routes/
│   ├── RouteListView.swift
│   ├── RouteDetailView.swift
│   ├── RouteFormView.swift
│   └── Components/
│       └── RouteRowView.swift   // Reusable row component
```

**Convention:** One view per file, named clearly, grouped by feature.

### View Structure

```swift
struct RouteListView: View {
	// MARK: - Properties
	@Environment(\.modelContext) private var modelContext
	@Query private var routes: [Route]
	@State private var isShowingForm = false

	// MARK: - Body
	var body: some View {
		NavigationStack {
			List {
				ForEach(routes) { route in
					RouteRowView(route: route)
				}
			}
			.navigationTitle("Routes")
			.toolbar {
				toolbarContent
			}
		}
	}

	// MARK: - Toolbar
	@ToolbarContentBuilder
	private var toolbarContent: some ToolbarContent {
		ToolbarItem(placement: .primaryAction) {
			Button("Add", systemImage: "plus") {
				isShowingForm = true
			}
		}
	}
}
```

**Apple conventions:**

- `MARK` comments for sections
- Properties before body
- Private computed properties for complex UI
- Extract toolbar/sections for readability

### Naming Conventions

#### Views

```swift
RouteListView       // ✅ Feature + Type
RouteDetailView     // ✅ Feature + Type
RouteFormView       // ✅ Feature + Type

RoutesView          // ❌ Ambiguous
RouteScreen         // ❌ Not iOS terminology
```

#### Variables

```swift
@State private var isShowingForm = false        // ✅ Boolean: is/has/should
@State private var selectedRoute: Route?        // ✅ Noun for data
@State private var searchText = ""              // ✅ Clear purpose

@State private var flag = false                 // ❌ Unclear
@State private var temp: Route?                 // ❌ Temporary names
```

#### Functions

```swift
func saveRoute() { }              // ✅ Verb, clear action
func deleteRoute(_ route: Route) { }  // ✅ Verb + noun
func filterRoutesByGrade() { }    // ✅ Descriptive

func doStuff() { }                // ❌ Vague
func handle() { }                 // ❌ Handle what?
```

### Property Wrappers (When to Use What)

```swift
// View's internal UI state
@State private var isExpanded = false

// Shared with child views (two-way binding)
@Binding var route: Route

// Database queries
@Query private var routes: [Route]

// SwiftData context
@Environment(\.modelContext) private var modelContext

// System environment values
@Environment(\.dismiss) private var dismiss
@Environment(\.colorScheme) private var colorScheme
```

### Error Handling

```swift
// ✅ GOOD - Graceful error handling
do {
	try modelContext.save()
} catch {
	// Show user-friendly alert
	print("Error saving route: \(error)")
}

// ❌ BAD - Force try (crashes app)
try! modelContext.save()  // Never use try! in production
```

### Optional Handling

```swift
// ✅ GOOD - Safe unwrapping
guard let location = route.location else { return }

if let locationName = route.location?.name {
	Text(locationName)
}

// ❌ BAD - Force unwrapping
Text(route.location!.name)  // App crashes if location is nil
```

---

## Accessibility (Non-Negotiable)

Apple strongly emphasises accessibility. These are minimum requirements:

### VoiceOver Support

```swift
// All interactive elements need labels
Button("", systemImage: "plus") {
	// action
}
.accessibilityLabel("Add route")

Image("climb-icon")
	.accessibilityLabel("Climbing route")
```

### Dynamic Type

```swift
// ✅ Always use system fonts
Text("Route Name").font(.headline)

// ❌ Never use fixed sizes
Text("Route Name").font(.system(size: 17))
```

### Colour Contrast

- Text must have 4.5:1 contrast ratio minimum
- Use system colours - they're already optimised
- Test in both light and dark mode

### Tap Targets

- Minimum 44x44 points for all tappable elements
- SwiftUI buttons already handle this
- Custom tap areas: use `.frame(minWidth: 44, minHeight: 44)`

---

## iOS Patterns for Climbing Features

### Photo Annotation (Your Route Drawing)

```swift
// Apple's approach: Use native gestures
.gesture(
	DragGesture()
		.onChanged { value in
			// Track drawing
		}
		.onEnded { value in
			// Finalise mark
		}
)

// Alternative: Use PencilKit for Apple Pencil support
import PencilKit
```

**Apple convention:** Gestures should feel like built-in iOS gestures (Photos app markup, Notes sketching).

### Location/Map Integration

```swift
import MapKit

// ✅ Use native Map view
Map(position: $position) {
	ForEach(locations) { location in
		Marker(location.name, coordinate: location.coordinate)
	}
}

// Not custom map implementations
```

### Photo Picker

```swift
import PhotosUI

// ✅ Use native photo picker
PhotosPicker(selection: $selectedPhoto, matching: .images) {
	Label("Add Photo", systemImage: "photo")
}
```

### Sharing

```swift
// ✅ Native share sheet
.toolbar {
	ToolbarItem {
		ShareLink(item: route.shareURL) {
			Label("Share", systemImage: "square.and.arrow.up")
		}
	}
}
```

---

## Dark Mode Support

**Non-negotiable:** Your app must support dark mode.

### Automatic Support

```swift
// ✅ System colours automatically adapt
Color.primary
Color.secondary
Color(.systemBackground)

// ❌ Fixed colours break dark mode
Color.black  // Will be black in dark mode (invisible on black background)
Color.white  // Will be white in light mode (invisible on white background)
```

### Testing Dark Mode

- Xcode: Environment Overrides (sun/moon icon in debug toolbar)
- Always test every view in both modes

### Images/Assets

- Asset catalogs support "Any Appearance" / "Dark" variants
- Your climbing icons may need dark mode variants

---

## Performance Guidelines

### SwiftData Best Practices

```swift
// ✅ GOOD - Fetch only what you need
@Query(filter: #Predicate<Route> { route in
	route.state == .working
}) private var workingRoutes: [Route]

// ❌ BAD - Fetch everything then filter in code
@Query private var allRoutes: [Route]
// then: let working = allRoutes.filter { $0.state == .working }
```

### Image Handling

```swift
// For thumbnails/lists
Image(uiImage: photo.thumbnail)
	.resizable()
	.aspectRatio(contentMode: .fill)
	.frame(width: 60, height: 60)
	.clipped()

// Cache thumbnails, don't load full images in lists
```

### List Performance

```swift
// ✅ GOOD - Use List with ForEach
List {
	ForEach(routes) { route in
		RouteRowView(route: route)
	}
}

// ❌ BAD - ScrollView with manual rows (less efficient)
```

---

## What "Native Feel" Actually Means

### It Means:

- ✅ Uses system fonts, colours, spacing
- ✅ Animations feel like iOS (not too fast, not too slow)
- ✅ Navigation patterns match Photos, Reminders, Notes apps
- ✅ Gestures work like users expect (swipe to delete, pull to refresh)
- ✅ Dark mode support
- ✅ Dynamic type support
- ✅ Works smoothly on all iPhone sizes

### It Doesn't Mean:

- ❌ Boring or generic design
- ❌ Can't have personality
- ❌ Must look identical to Apple apps
- ❌ Can't use custom colours/branding

**Your climbing-specific features** (route drawing, hold marking) can be unique. The **framework around them** should be native iOS.

---

## Testing Checklist

Before any refactor is "done", verify:

- [ ] Works in light mode
- [ ] Works in dark mode
- [ ] Text scales with Dynamic Type (Settings → Accessibility → Display → Larger Text)
- [ ] VoiceOver can access all interactive elements
- [ ] Works on iPhone SE (smallest screen)
- [ ] Works on iPhone Pro Max (largest screen)
- [ ] Navigation feels natural (back buttons, gestures)
- [ ] No force unwrapping (`!`) that could crash
- [ ] Follows HIG patterns for navigation/layout

---

## Reference Resources

### Essential Reading

- **Human Interface Guidelines**: https://developer.apple.com/design/human-interface-guidelines/
  - Start with "iOS and iPadOS" section
  - Focus on "Components" (buttons, lists, navigation)

### Inspiration Apps (Study These)

- **Apple Apps:**
  - Reminders (list management, simple forms)
  - Photos (image handling, albums)
  - Maps (location, pins)
  - Notes (text + media)

- **Third-party "Native Feel":**
  - Things 3 (project management)
  - Fantastical (calendar)
  - Bear (notes)

### Quick Reference

- **SF Symbols App** (Mac App Store) - Browse all available icons
- **Xcode Previews** - Test different devices/modes instantly

---

## Implementation Priority

When refactoring Routes → Locations → Home:

1. **First pass:** Make it work
2. **Second pass:** Make it follow Apple patterns (this doc)
3. **Third pass:** Make it simple (SIMPLIFIED-SWIFT.md)

Don't try to do all three at once. Native patterns should come AFTER core functionality works.

---

## Red Flags (Not Native iOS)

Watch for these signs your app is drifting from Apple guidelines:

- Custom navigation that doesn't use NavigationStack
- Non-system fonts (unless brand-specific)
- Fixed colour values instead of semantic colours
- Custom buttons that don't look like iOS buttons
- Animations that feel too fast or janky
- No dark mode support
- Text that doesn't scale
- Non-standard gestures
- Custom icons that clash with SF Symbols style

---

**Remember:** "Native" doesn't mean "boring". Apps like Things 3, Fantastical, and Bear all feel completely native while having strong personalities. They achieve this by following Apple's framework but expressing personality through:

- Thoughtful colour choices (still using system colours)
- Custom illustrations/icons (that match SF Symbols style)
- Delightful micro-interactions (using Apple's animation timings)
- Excellent UX decisions (that align with iOS patterns)

[my-app] can be distinctly "climbing-focused" while feeling native. The route drawing can be unique. The hold marking can be innovative. But the list views, navigation, forms, and buttons should feel like iOS.
