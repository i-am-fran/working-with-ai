# Pre-Commit Checklist

Use this checklist before committing ANY Swift/SwiftUI code to prevent deprecated APIs and common issues.

---

## 🔴 CRITICAL - CloudKit Compatibility

**SwiftData Models:**
- [ ] No `@Attribute(.unique)` on ANY model property
- [ ] UUIDs use plain `var id: UUID` (no attributes)
- [ ] All model relationships properly configured
- [ ] No CloudKit-incompatible types

**Why:** CloudKit sync will fail with unique attributes. This is non-negotiable.

---

## 🟠 HIGH PRIORITY - Deprecated APIs

**Search your changed files for these patterns:**

### 1. Foreground Style
- [ ] No `.foregroundColor()` (use `.foregroundStyle()`)
```bash
# Quick check:
grep "foregroundColor(" YourFile.swift
```

### 2. Corner Radius
- [ ] No `.cornerRadius()` (use `.clipShape(.rect(cornerRadius:))`)
```bash
# Quick check:
grep "\.cornerRadius(" YourFile.swift
```

### 3. Task Sleep
- [ ] No `Task.sleep(nanoseconds:)` (use `Task.sleep(for: .milliseconds(X))`)
```bash
# Quick check:
grep "Task.sleep(nanoseconds:" YourFile.swift
```

### 4. Navigation
- [ ] No `NavigationView` (use `NavigationStack` or `NavigationSplitView`)
```bash
# Quick check:
grep "NavigationView" YourFile.swift
```

### 5. OnChange Modifier
- [ ] No single-parameter `.onChange(of:) { }` (use 2-param or 0-param variant)
```bash
# Quick check (manual review needed):
grep "\.onChange(of:" YourFile.swift
```

---

## 🟡 MEDIUM PRIORITY - Accessibility & Quality

### Tap Gestures
- [ ] No `onTapGesture` unless you NEED tap location or tap count
- [ ] Use `Button` for simple tap actions
```swift
// ❌ BAD (unless you need location/count)
.onTapGesture { doSomething() }

// ✅ GOOD
Button { doSomething() } label: { YourView() }
    .buttonStyle(.plain)
```

### Font Sizes
- [ ] No `.font(.system(size: X))` (use semantic fonts)
- [ ] Use `.title`, `.body`, `.caption`, etc. for Dynamic Type
```swift
// ❌ BAD
Text("Title").font(.system(size: 24))

// ✅ GOOD
Text("Title").font(.title)
```

### Button Labels
- [ ] All icon buttons include text label (accessibility)
```swift
// ❌ BAD
Button(action: add) {
    Image(systemName: "plus")
}

// ✅ GOOD
Button("Add", systemImage: "plus", action: add)
```

---

## 🔍 AUTOMATED CHECKS

Run these grep commands on your staged files:

```bash
# Check for CloudKit-breaking patterns
grep -r "@Attribute(.unique)" Models/

# Check for deprecated APIs
grep -r "foregroundColor(" Views/
grep -r "\.cornerRadius(" Views/
grep -r "Task.sleep(nanoseconds:" .
grep -r "NavigationView" Views/

# Check for accessibility issues
grep -r "onTapGesture" Views/
grep -r "\.system(size:" Views/
```

**Expected result:** No matches (or justified exceptions)

---

## ✅ CODE QUALITY

### SwiftUI Views
- [ ] Every new view has SwiftUI preview
- [ ] Preview uses `PreviewData.createPreviewContainer()`
- [ ] View is in correct feature folder
- [ ] Follows component extraction patterns (not computed properties)

### Styles & Design
- [ ] Uses centralized styles from `Views/Shared/Styles/`
- [ ] No hardcoded styling (padding, colors, fonts)
- [ ] Follows button style hierarchy ([my-app]Primary, [my-app]Secondary, etc.)
- [ ] Uses design tokens (Spacing.md, CornerRadius.md, etc.)

### Testing
- [ ] Code builds without errors
- [ ] Code builds without warnings (new warnings)
- [ ] SwiftUI previews work
- [ ] Manual testing completed for changed functionality

---

## 📱 iOS & Accessibility

### Dynamic Type
- [ ] Test with largest accessibility text size
- [ ] UI doesn't break with large text
- [ ] Uses semantic fonts that scale

### VoiceOver
- [ ] Test changed UI with VoiceOver enabled
- [ ] All interactive elements are accessible
- [ ] Buttons have meaningful labels

### Dark Mode
- [ ] Test in both light and dark mode
- [ ] Uses semantic colors (not hardcoded)

---

## 🧪 TESTING CHECKLIST

### Before Committing:
1. **Build:** `xcodebuild -project [my-app].xcodeproj -scheme [my-app] build`
2. **Previews:** Verify all changed views preview correctly
3. **Manual Test:** Run on simulator and test changed functionality
4. **CloudKit:** If models changed, verify CloudKit configuration

### Critical Flows to Test (if affected):
- [ ] Route creation/editing
- [ ] Location creation/editing
- [ ] Photo upload/viewing
- [ ] Navigation between tabs
- [ ] Sheet presentation/dismissal

---

## 📝 COMMIT MESSAGE

### Format:
```
<type>: <description>

🤖 Generated with Claude Code
Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>
```

### Types:
- `fix:` Bug fix
- `feat:` New feature
- `refactor:` Code refactoring
- `style:` UI/styling changes
- `docs:` Documentation
- `test:` Testing
- `chore:` Maintenance

---

## 🚨 RED FLAGS - DO NOT COMMIT IF:

- ❌ `@Attribute(.unique)` present in any model
- ❌ Build fails or has new errors
- ❌ CloudKit sync is broken
- ❌ Critical user flows are broken
- ❌ Changed code doesn't have previews
- ❌ Multiple deprecated APIs introduced
- ❌ Accessibility significantly degraded

---

## 📚 QUICK REFERENCE

### Key Documentation:
- **Full Guidelines:** `.claude/docs/SWFIT-AI-ISSUES.md`
- **Project Rules:** `CLAUDE.md` → "Deprecated API Prevention"
- **Tech Debt:** `.claude/TECHNICAL_DEBT.md`
- **Audit Report:** `.claude/DEPRECATED_API_AUDIT_2025-12-17.md`

### Quick Pattern Reference:

| ❌ Don't Use | ✅ Use Instead |
|-------------|---------------|
| `@Attribute(.unique)` | `var` (plain) |
| `.foregroundColor()` | `.foregroundStyle()` |
| `.cornerRadius()` | `.clipShape(.rect(cornerRadius:))` |
| `Task.sleep(nanoseconds:)` | `Task.sleep(for:)` |
| `NavigationView` | `NavigationStack` |
| `.onChange(of:) { }` | `.onChange(of:) { old, new in }` |
| `onTapGesture` | `Button` (usually) |
| `.font(.system(size:))` | `.font(.title)` etc. |

---

## 💡 TIPS

1. **Run grep checks** before staging files
2. **Fix issues immediately** - don't defer
3. **Update documentation** if you find new patterns
4. **Test accessibility** with real settings, not just visually
5. **Use context7** to verify current APIs

---

## ✨ DONE!

Once all checks pass:
```bash
git add .
git commit -m "your message"
```

**Remember:** Prevention is easier than fixing. Taking 5 minutes now saves hours later.

---

**Last Updated:** 2025-12-17
**Version:** 1.0
