# iOS / Swift Rules

## General Swift Principles & Style

* **Style Guide:** Follow the Swift API Design Guidelines and a consistent community style guide (e.g., Kodeco/Ray Wenderlich Style Guide, Airbnb Swift Style Guide). Use tools like SwiftLint and SwiftFormat to enforce style.
* **Clarity over Brevity:** Prioritize clear, readable code. While Swift allows concise syntax, ensure the intent is obvious.
* **Type Safety:** Leverage Swift's strong type system. Avoid using `Any` or `AnyObject` unless necessary. Use specific types.
* **Value Types vs. Reference Types:** Prefer `struct` (value type) over `class` (reference type) for data models or types where identity is not crucial. Use `struct` for immutability benefits and predictable behavior. Use `class` when identity, inheritance, or Objective-C interoperability is needed.
* **Immutability:** Prefer immutable variables (`let`) over mutable variables (`var`). Declare variables as `let` by default and only change to `var` if mutation is required. Use immutable data structures where possible.
* **Optionals:** Handle optionals safely.
    * Avoid force unwrapping (`!`) in production code; it leads to crashes if the optional is `nil`.
    * Prefer optional binding (`if let`, `guard let`) or nil-coalescing (`??`) for safe unwrapping.
    * Use optional chaining (`?.`) for accessing properties or methods on potentially `nil` values.
* **Type Inference:** Use type inference where the type is obvious from the context (e.g., `let count = 0`), but explicitly declare types for clarity in public APIs or complex expressions.
* **Access Control:** Use access control modifiers (`private`, `fileprivate`, `internal` (default), `public`, `open`) deliberately to control visibility and encapsulate implementation details. Prefer the most restrictive access level possible.
* **Keep Functions Small:** Aim for short, focused functions and methods that perform a single, well-defined task.

## Naming & Formatting

* **`SwiftFormat`/`SwiftLint`:** Use automated tools to enforce consistent formatting (indentation, spacing, line breaks) and linting rules.
* **Naming (API Design Guidelines):**
    * **Clarity at Point of Use:** Names should be clear and unambiguous when read in context at the call site. Omit needless words, especially those repeating type information.
    * **Types & Protocols:** `UpperCamelCase` (e.g., `UserProfile`, `DataFetchingService`, `Equatable`).
    * **Functions, Methods, Variables, Constants, Enum Cases:** `lowerCamelCase` (e.g., `fetchUserData()`, `userName`, `maxRetryCount`, `.pending`).
    * **Booleans:** Name properties/methods returning `Bool` like assertions (e.g., `user.isPremium`, `order.isEmpty`).
    * **Protocols:** Protocols describing *what something is* should be nouns (`Collection`). Protocols describing a *capability* should use suffixes like `able`, `ible`, or `ing` (`Equatable`, `ProgressReporting`).
* **Organization:** Organize code within files logically (e.g., using `// MARK: - Section Name` comments). Keep related extensions close to the original type definition if possible. Define one primary type per file.

## Error Handling

* **Swift Error Handling (`throw`, `try`, `catch`):** Use Swift's built-in error handling mechanism for recoverable errors. Functions that can fail should `throw` errors. Callers use `do-catch`, `try?`, or `try!` to handle them.
* **Custom Error Types:** Define custom error types (enums conforming to the `Error` protocol) to represent specific failure conditions within your domain, providing more context than generic errors.
* **Avoid `try!`:** Avoid using `try!` (forced try) except in cases where failure is truly impossible (e.g., loading bundled resources known to exist) and crashing is the desired outcome if the assumption fails. Prefer `try?` or `do-catch`.
* **Result Type:** Consider using the `Result<Success, Failure: Error>` type for asynchronous operations or where explicitly representing success/failure states is clearer than using `throws`.

## Concurrency (Swift Concurrency - `async`/`await`)

* **Prefer `async`/`await`:** Use Swift's modern concurrency features (`async`/`await`) for asynchronous operations (networking, file I/O, long computations) instead of older callback-based or Grand Central Dispatch (GCD) approaches where possible. This improves readability and simplifies error handling.
* **Structured Concurrency:** Use constructs like `async let` for parallel execution of independent tasks and `TaskGroup` for dynamic parallelism where the results need to be collected.
* **Actors:** Use `actor` types to protect shared mutable state from data races in concurrent code. Access actor properties and methods asynchronously using `await`.
* **MainActor:** Use `@MainActor` annotation for classes or functions that interact with the UI (UIKit/AppKit/SwiftUI) to ensure operations run on the main thread.
* **Cancellation:** Support task cancellation using `Task.checkCancellation()` and by passing `Task` contexts appropriately.
* **Avoid Blocking:** Do not block threads unnecessarily, especially the main thread. Use asynchronous APIs.

## UI (UIKit & SwiftUI)

* **Choose Framework:** Select UIKit or SwiftUI based on project requirements, target platforms, team expertise, and desired UI paradigm (imperative vs. declarative). Interoperability is possible but adds complexity.
* **UIKit Best Practices:**
    * **MVC/MVVM/VIPER:** Follow established architectural patterns (Model-View-Controller, Model-View-ViewModel, etc.) to separate concerns. Avoid massive view controllers ("Massive View Controller" problem).
    * **View Lifecycle:** Understand and correctly use `UIViewController` lifecycle methods (`viewDidLoad`, `viewWillAppear`, `viewDidLayoutSubviews`, etc.).
    * **Autolayout:** Use Auto Layout for defining adaptive UIs. Define constraints programmatically or using Interface Builder.
    * **Reusable Views:** Use `UITableViewCell` and `UICollectionViewCell` reuse identifiers correctly for performant scrolling lists.
    * **Main Thread:** Perform all UI updates on the main thread (`DispatchQueue.main.async`).
* **SwiftUI Best Practices:**
    * **Declarative Approach:** Embrace the declarative nature of SwiftUI. Define UI state and how the UI should look based on that state; let the framework handle updates.
    * **State Management:** Choose appropriate state management tools (`@State`, `@Binding`, `@StateObject`, `@ObservedObject`, `@EnvironmentObject`, `Observable` macro) based on the scope and nature of the state.
    * **View Composition:** Break down complex views into smaller, reusable subviews.
    * **Modifiers:** Apply view modifiers correctly. Understand their order matters. Create custom view modifiers for reusable styling/behavior.
    * **Identity:** Use `.id()` modifier or ensure data has stable identifiers (`Identifiable` protocol) in `ForEach` loops for efficient updates.
    * **Performance:** Be mindful of view updates. Avoid unnecessary state changes that trigger re-renders. Use tools like Instruments to profile SwiftUI performance.

## Memory Management (ARC)

* **Automatic Reference Counting (ARC):** Understand that Swift uses ARC to manage memory. References are tracked, and objects are deallocated when their reference count drops to zero.
* **Avoid Retain Cycles:** Be vigilant about strong reference cycles, especially in closures and delegate patterns.
    * Use `[weak self]` or `[unowned self]` in closures that capture `self` when `self` also holds a strong reference to the closure (directly or indirectly). Understand the difference (`weak` becomes `nil`, `unowned` assumes it's never `nil` and crashes if it is). Prefer `weak`.
    * Use `weak var delegate: MyDelegate?` for delegate properties to break cycles between the delegate and the delegating object.

## API Design & SDK Usage

* **Apple API Design Guidelines:** Follow Apple's API Design Guidelines when creating public APIs for libraries or frameworks. Aim for clarity, fluency, and consistency.
* **SDK Usage:** Use iOS SDK frameworks correctly according to Apple's documentation and recommendations. Handle permissions, background execution, and lifecycle events properly.
* **Error Handling:** Check for and handle errors returned by SDK APIs (often via `Error` throwing methods or `NSErrorPointer` in older APIs bridged from Objective-C).
* **Deprecations:** Avoid using deprecated APIs. Plan migrations to newer APIs proactively.

## Security

* **Data Storage:**
    * Store sensitive user data (credentials, tokens) securely in the Keychain.
    * Encrypt sensitive data stored in files or local databases (e.g., using `CryptoKit`).
    * Be mindful of data stored in caches, logs, or temporary files.
* **Networking:**
    * Use HTTPS exclusively for all network communications.
    * Implement certificate pinning for highly sensitive connections if necessary.
    * Configure App Transport Security (ATS) appropriately in `Info.plist`.
* **Input Validation:** Validate and sanitize all data received from external sources (user input, network responses).
* **Authentication/Authorization:** Implement robust authentication and authorization checks, both client-side (for UX) and server-side (for enforcement).
* **Code Obfuscation (Optional):** Consider code obfuscation tools for sensitive applications to make reverse engineering harder, but understand it's not a replacement for secure design.
* **Jailbreak Detection (Optional):** Implement jailbreak/root detection mechanisms for high-security apps if required, but understand these can often be bypassed.
* **Dependencies:** Regularly audit third-party libraries (using Swift Package Manager, CocoaPods, Carthage) for known vulnerabilities. Keep them updated.

## Performance

* **Profiling:** Use Instruments (Time Profiler, Allocations, Leaks, Energy Log, Network) to identify performance bottlenecks (CPU, memory, energy, network). Profile on real devices.
* **Background Threads:** Offload long-running or computationally intensive tasks to background threads (using GCD or Swift Concurrency) to keep the main (UI) thread responsive.
* **Efficient Data Structures:** Choose appropriate data structures for the task (`Array`, `Set`, `Dictionary`).
* **Image Optimization:** Load images efficiently. Use appropriate sizes and formats (e.g., HEIC, WebP). Use asynchronous image loading and caching libraries. Downsample large images before displaying them if possible.
* **Lazy Loading:** Load resources (data, views) lazily only when they are needed.
* **Memory Management:** Avoid memory leaks (see ARC section). Monitor memory usage with Instruments.

## Testing

* **Follow Testing Rules:** Adhere to rules defined in `testing_rules.md`.
* **XCTest:** Use the built-in XCTest framework for unit and integration tests.
* **Unit Tests:** Test individual units (structs, classes, functions) in isolation. Mock dependencies using protocols and dependency injection.
* **UI Tests:** Use XCUITest for end-to-end UI testing. Keep UI tests focused on critical user flows, as they can be slower and more brittle than unit tests. Use accessibility identifiers or stable query predicates for element selection.
* **Test Coverage:** Aim for meaningful test coverage, focusing on logic and edge cases. Use Xcode's code coverage tooling.
* **Testability:** Design code with testability in mind (e.g., use dependency injection, protocols, avoid singletons where possible).
