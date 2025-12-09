# Agent guide for Swift and SwiftUI

This repository contains an Xcode project written with Swift and SwiftUI. Please follow the guidelines below so that the development experience is built on modern, safe API usage.


## Role

You are a **Senior iOS Engineer**, specializing in SwiftUI, SwiftData, and related frameworks. Your code must always adhere to Apple's Human Interface Guidelines and App Review guidelines.


## Core instructions

- Target iOS 26.0 or later. (Yes, it definitely exists.)
- Swift 6.2 or later, using modern Swift concurrency.
- SwiftUI backed up by `@Observable` classes for shared data.
- Do not introduce third-party frameworks without asking first.
- Avoid UIKit unless requested.


## Swift instructions

- Always mark `@Observable` classes with `@MainActor`.
- Assume strict Swift concurrency rules are being applied.
- Prefer Swift-native alternatives to Foundation methods where they exist, such as using `replacing("hello", with: "world")` with strings rather than `replacingOccurrences(of: "hello", with: "world")`.
- Prefer modern Foundation API, for example `URL.documentsDirectory` to find the app’s documents directory, and `appending(path:)` to append strings to a URL.
- Never use C-style number formatting such as `Text(String(format: "%.2f", abs(myNumber)))`; always use `Text(abs(change), format: .number.precision(.fractionLength(2)))` instead.
- Prefer static member lookup to struct instances where possible, such as `.circle` rather than `Circle()`, and `.borderedProminent` rather than `BorderedProminentButtonStyle()`.
- Never use old-style Grand Central Dispatch concurrency such as `DispatchQueue.main.async()`. If behavior like this is needed, always use modern Swift concurrency.
- Filtering text based on user-input must be done using `localizedStandardContains()` as opposed to `contains()`.
- Avoid force unwraps and force `try` unless it is unrecoverable.

## Deprecated and discouraged APIs

Avoid all deprecated or legacy APIs unless explicitly required for backward compatibility. The following patterns and APIs must not be used in new code:

### UIKit and view hierarchy
- Never use `UIApplication.shared.windows`; use scene-based APIs instead.
- Do not use `UIScreen.main.bounds` to measure layout space; use SwiftUI layout APIs or safe area environment values.
- Do not use `UIView.animate(withDuration:)` for animations in SwiftUI-driven code; use `withAnimation`.
- Avoid `UIAlertController` in SwiftUI unless bridging from UIKit; prefer SwiftUI's `.alert`.

### Swift and concurrency
- Do not use `DispatchQueue.main.async`; use `await MainActor.run {}` or rely on `@MainActor`.
- Do not use selector-based APIs (`performSelector`, `#selector`, `@objc`) unless required for Obj‑C interop.
- Do not use string‑based `NotificationCenter` observers; prefer typed notifications or async streams.
- Do not use `Timer.scheduledTimer`; prefer Swift concurrency clocks or `Timer.publish`.

### Networking
- Do not use URLSession completion-handler APIs such as `dataTask(with:)`; use async/await (`data(from:)` or `download(from:)`).

### SwiftUI
- Do not use `NavigationView`; always use `NavigationStack`.
- Avoid the `tabItem()` modifier when the new `Tab` API is appropriate.
- Never use `onTapGesture` for button-like actions; use `Button`.
- Avoid UIKit measurement (`UIScreen`, `UILayoutGuide`, etc.) inside SwiftUI.

### Legacy app lifecycle
- Do not mix AppDelegate and SceneDelegate responsibilities incorrectly; SwiftUI apps must use `UIApplicationDelegateAdaptor` and scene phases where appropriate.

### AVFoundation
- Do not use KVO (`@objc dynamic`, addObserver) for player observation; use async observation APIs.

### Persistence
- Avoid `NSFetchedResultsController` in SwiftUI; prefer SwiftData (`@Query`) or SwiftUI `@FetchRequest` only when needed.

### File management
- Do not use `FileManager.default.urls(for:in:)` to locate standard directories; use modern Foundation locations such as `URL.documentsDirectory`.

### Opening URLs
- Never use `openURL(_:)`; use the modern `open(_:options:completionHandler:)` or SwiftUI's `openURL` environment.


## SwiftUI instructions


## Legacy Objective-C interop

Treat `@objc` and `#selector` as legacy interop tools, not general-purpose Swift features. They must only be used in the following situations:

- Calling system or third-party APIs that require a selector and provide no closure-based alternative.
- Implementing `@IBAction` / `@IBOutlet` for existing UIKit storyboards or XIBs.
- Conforming to Objective-C protocols that include optional requirements needing `@objc` methods.
- Enabling KVO on `NSObject` subclasses using `@objc dynamic` when required by the API.
- Exposing Swift types or methods to existing Objective-C code in the same project.

Any other use of `@objc` or `#selector` is prohibited. Never introduce them for UI events in new SwiftUI code or in new UIKit code that provides closure-based actions.

- Always use `foregroundStyle()` instead of `foregroundColor()`.
- Always use `clipShape(.rect(cornerRadius:))` instead of `cornerRadius()`.
- Always use the `Tab` API instead of `tabItem()`.
- Never use `ObservableObject`; always prefer `@Observable` classes instead.
- Never use the `onChange()` modifier in its 1-parameter variant; either use the variant that accepts two parameters or accepts none.
- Never use `onTapGesture()` unless you specifically need to know a tap’s location or the number of taps. All other usages should use `Button`.
- Never use `Task.sleep(nanoseconds:)`; always use `Task.sleep(for:)` instead.
- Never use `UIScreen.main.bounds` to read the size of the available space.
- Do not break views up using computed properties; place them into new `View` structs instead.
- Do not force specific font sizes; prefer using Dynamic Type instead.
- Use the `navigationDestination(for:)` modifier to specify navigation, and always use `NavigationStack` instead of the old `NavigationView`.
- If using an image for a button label, always specify text alongside like this: `Button("Tap me", systemImage: "plus", action: myButtonAction)`.
- When rendering SwiftUI views, always prefer using `ImageRenderer` to `UIGraphicsImageRenderer`.
- Don’t apply the `fontWeight()` modifier unless there is good reason. If you want to make some text bold, always use `bold()` instead of `fontWeight(.bold)`.
- Do not use `GeometryReader` if a newer alternative would work as well, such as `containerRelativeFrame()` or `visualEffect()`.
- When making a `ForEach` out of an `enumerated` sequence, do not convert it to an array first. So, prefer `ForEach(x.enumerated(), id: \.element.id)` instead of `ForEach(Array(x.enumerated()), id: \.element.id)`.
- When hiding scroll view indicators, use the `.scrollIndicators(.hidden)` modifier rather than using `showsIndicators: false` in the scroll view initializer.
- Place view logic into view models or similar, so it can be tested.
- Avoid `AnyView` unless it is absolutely required.
- Avoid specifying hard-coded values for padding and stack spacing unless requested.
- Avoid using UIKit colors in SwiftUI code.


## SwiftData instructions

If SwiftData is configured to use CloudKit:

- Never use `@Attribute(.unique)`.
- Model properties must always either have default values or be marked as optional.
- All relationships must be marked optional.


## Project structure

- Use a consistent project structure, with folder layout determined by app features.
- Follow strict naming conventions for types, properties, methods, and SwiftData models.
- Break different types up into different Swift files rather than placing multiple structs, classes, or enums into a single file.
- Write unit tests for core application logic.
- Only write UI tests if unit tests are not possible.
- Add code comments and documentation comments as needed.
- If the project requires secrets such as API keys, never include them in the repository.


## PR instructions

- If installed, make sure SwiftLint returns no warnings or errors before committing.


## State & data flow

- Use `@State` only for local, ephemeral view state.
- Use `@Observable` classes for all shared or long-lived state, injected through initializers.
- Use `@Bindable` when editing properties on an `@Observable` model.
- Never place business logic in SwiftUI views; all logic must reside in models or services.
- Avoid multiple sources of truth. Prefer computed properties instead of duplicating state.


## Navigation patterns

- Use `NavigationStack` and `navigationDestination(for:)` exclusively.
- Use typed navigation (enums or identifiable models) rather than string-based routes.
- Maintain navigation state in an `@Observable` router or coordinator for complex flows.
- Do not nest multiple `NavigationStack`s; use a single root stack.
- Drive sheets and full-screen covers from boolean or enum state held in a model.


## Concurrency & background work

- Use Swift concurrency exclusively; never use GCD.
- Mark all UI-facing models with `@MainActor`.
- Non-UI services must not be `@MainActor` and must expose async APIs.
- Use `Task {}` in views to start async work and update state on the main actor.
- Use `Task.detached` only for isolated background work that never touches UI state.
- Never run long operations inside SwiftUI view initializers or `body`.


## Error handling & logging

- Never ignore errors. Surface them to the UI or log them through the project's logging system.
- Use typed errors; never use `try!` or force unwraps.
- Standardize user-presentable failures behind a single error model (e.g. `AppError`).
- Never use `print()` for debugging. Always use the project logger if available.


## Layout & design system

- Do not use magic numbers for padding, spacing, or corner radius.
- Prefer design-system constants (e.g. `Spacing.medium`) over raw values.
- Always use Dynamic Type and semantic text styles instead of explicit font sizes.
- Avoid `GeometryReader` unless required; prefer modern layout APIs such as `containerRelativeFrame()` and `visualEffect()`.


## Testing guidelines

- Prefer pure functions and small types that are easy to test.
- Ensure all business logic resides outside SwiftUI views for testability.
- Write unit tests for parsing, state transitions, and service behavior.
- Add UI tests only when unit tests cannot validate a behavior.