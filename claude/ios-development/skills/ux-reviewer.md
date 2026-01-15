---
name: ux-reviewer
description: Comprehensive UX review and heuristic evaluation for mobile apps and web interfaces. Use this skill when evaluating designs, wireframes, user flows, feature specifications, or interaction patterns against established usability principles (Nielsen's heuristics, iOS HIG, accessibility standards). Particularly useful for reviewing iOS app designs, identifying friction points, assessing information architecture, and providing actionable improvement recommendations. Applies UX best practices from NN/g, Steve Krug's "Don't Make Me Think", and mobile-first design principles.
---

# UX Reviewer

Conduct thorough UX reviews and heuristic evaluations following established usability principles and best practices.

## Core Review Framework

Use Nielsen's 10 Usability Heuristics as the foundation, adapted for modern mobile-first contexts:

1. **Visibility of system status** - Keep users informed about what's happening through appropriate feedback
2. **Match between system and real world** - Use familiar language and concepts rather than system-oriented terms
3. **User control and freedom** - Provide clear exits and undo options
4. **Consistency and standards** - Follow platform conventions and maintain internal consistency
5. **Error prevention** - Design to prevent errors before they occur
6. **Recognition rather than recall** - Minimise memory load through visible options and cues
7. **Flexibility and efficiency of use** - Accommodate both novice and expert users
8. **Aesthetic and minimalist design** - Focus on essential content, remove irrelevant information
9. **Help users recognise, diagnose, and recover from errors** - Clear error messages with solutions
10. **Help and documentation** - Provide contextual help when needed

## iOS-Specific Considerations

When reviewing iOS designs, evaluate against Apple's Human Interface Guidelines:

- **Navigation patterns** - Tab bars, navigation bars, modal sheets, proper hierarchy
- **Gestures** - Swipe actions, pull to refresh, standard iOS gestures
- **Typography and spacing** - Dynamic Type support, appropriate font sizing, touch targets (44pt minimum)
- **Native components** - Use standard iOS controls when appropriate
- **Sheet presentation** - Appropriate use of modal sheets vs. push navigation
- **Dark mode** - Support for system appearance preferences

## Review Process

### 1. Context Gathering

Ask clarifying questions if needed:

- What is the primary user goal or task?
- What screen/flow came before and after this one?
- Are there any technical constraints?
- What user segment is this designed for?

### 2. Heuristic Analysis

Systematically evaluate against relevant heuristics. For each issue found:

- **Severity** - Critical (blocks task), High (major frustration), Medium (minor inconvenience), Low (cosmetic)
- **Heuristic violated** - Which principle is affected
- **Impact** - How it affects the user experience
- **Recommendation** - Specific, actionable improvement

### 3. Friction Analysis

Identify points where users might hesitate, get confused, or make errors:

- Unnecessary steps or taps
- Unclear labels or terminology
- Hidden or hard-to-discover features
- Cognitive load from too many choices
- Missing feedback or confirmation
- Unexpected behaviour

### 4. Mobile-First Considerations

- **One-handed use** - Critical actions within thumb reach (bottom third of screen)
- **Context of use** - Distracted, on-the-go, quick interactions
- **Progressive disclosure** - Show only what's needed, when it's needed
- **Forgiving interactions** - Large touch targets, generous hit areas
- **Efficient task completion** - Minimise taps and form inputs

## Output Format

Structure reviews using this flexible template (adapt as needed):

### Overview

[Brief summary of what was reviewed and key findings]

### Critical Issues

[Severity: Critical - must fix before launch]

- **Issue**: [Clear description]
- **Heuristic**: [Which principle is violated]
- **Impact**: [How it affects users]
- **Recommendation**: [Specific, actionable solution]

### High Priority Issues

[Severity: High - significant friction points]

### Medium Priority Issues

[Severity: Medium - minor improvements]

### Positive Highlights

[What's working well - reinforce good decisions]

### Additional Considerations

[Context-specific observations, edge cases, accessibility notes]

## Domain-Specific Reviews

### Climbing Apps ([my-app] Context)

Consider domain-specific patterns:

- **Terminology** - Use authentic climbing language (send, project, attempt)
- **Grading systems** - Support multiple systems (V-scale, Font, French, etc.)
- **Route types** - Clear distinction between bouldering, sport, trad, etc.
- **Photo workflows** - Quick capture and annotation for beta/holds
- **Progression tracking** - Meaningful metrics for climbers (not just gamification)
- **Privacy considerations** - Personal journal vs. social features

### Information Architecture

Evaluate structure and navigation:

- **Hierarchy** - Logical grouping and nesting
- **Labels** - Clear, descriptive, scannable
- **Search and filters** - Appropriate for content volume
- **Entry points** - Multiple paths to important features
- **Mental models** - Aligns with user expectations

### Forms and Input

Review data entry experiences:

- **Field order** - Logical flow matching user mental model
- **Smart defaults** - Reduce input where possible
- **Validation** - Inline, helpful error messages
- **Input types** - Appropriate keyboard/picker for data type
- **Optional vs. required** - Clear indication, minimal required fields

## Principles to Apply

### Steve Krug - "Don't Make Me Think"

- **Self-evident choices** - Navigation and options should be obvious
- **Remove question marks** - Every element should be self-explanatory
- **Satisficing** - Users don't read, they scan and choose "good enough"
- **Muddle through** - Design for how people actually behave

### Nielsen Norman Group Guidelines

- **Minimalism** - Every element must justify its presence
- **Scent of information** - Clear cues about what users will find
- **Consistency** - Both visual and functional across the product
- **Accessibility** - WCAG standards, colour contrast, screen reader support

### Leah Buley - "Team of One" Pragmatism

- **Quick wins** - Identify easy improvements with high impact
- **Prioritisation** - Focus on issues that matter most to users
- **Practical solutions** - Work within technical and resource constraints
- **Iterative improvement** - Better to ship small improvements than delay for perfection

## When NOT to Flag Issues

Avoid over-critiquing:

- **Style preferences** - Unless it impacts usability or brand
- **Alternative approaches** - If current approach is valid, mention alternatives without demanding change
- **Pedantic details** - Focus on real user impact, not theoretical perfection
- **Out of scope** - Respect project constraints and timeline

## Review Types

### Quick Scan (5-10 min)

- High-level friction points
- Critical blocking issues
- Obvious violations of platform patterns

### Standard Review (20-30 min)

- Systematic heuristic evaluation
- Detailed friction analysis
- Actionable recommendations with priority

### Deep Dive (45-60+ min)

- Comprehensive analysis across multiple flows
- Accessibility audit
- Comparative analysis with similar apps
- Detailed mockups or annotations for recommendations

## Collaboration Approach

Present findings constructively:

- **Start with positives** - Acknowledge what's working well
- **Be specific** - Concrete examples, not vague concerns
- **Explain the 'why'** - Help designers understand the reasoning
- **Offer solutions** - Don't just point out problems
- **Respect constraints** - Acknowledge technical or business limitations
- **Invite discussion** - UX reviews are conversations, not mandates

## Reference Files

For detailed patterns and examples:

- **references/ios-patterns.md** - iOS-specific component patterns and best practices
- **references/heuristics-examples.md** - Real-world examples of heuristic violations and fixes
- **references/climbing-ux.md** - Climbing app specific UX considerations
