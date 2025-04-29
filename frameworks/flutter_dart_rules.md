# Flutter & Dart Rules

## General Principles & Style (Dart/Flutter)

* **Effective Dart:** Follow the guidelines outlined in the "Effective Dart" documentation (Style, Design, Usage, Documentation).
* **Formatting:** Use the standard Dart formatter (`dart format`). Ensure code is formatted before committing. Integrate checks into CI. Use 4 spaces for indentation (formatter default).
* **Linting:** Use the Dart analyzer (`dart analyze`) with a strong set of lint rules (e.g., start with `package:lints/recommended.yaml` or `package:flutter_lints/flutter.yaml` and customize). Address linter warnings and errors. Integrate into CI.
* **Naming Conventions:**
    * **Packages, Directories, Source Files:** `lowercase_with_underscores` (e.g., `my_package`, `user_repository.dart`).
    * **Types (Classes, Enums, Typedefs, Extensions):** `UpperCamelCase` (e.g., `UserProfile`, `AuthStatus`, `JsonMap`).
    * **Variables, Parameters, Methods, Functions:** `lowerCamelCase` (e.g., `userName`, `calculateTotal`).
    * **Constants:** `lowerCamelCase` (preferred in Effective Dart) or `SCREAMING_SNAKE_CASE` if preferred by team convention (e.g., `maxRetries`, `DEFAULT_TIMEOUT`).
    * **Private Identifiers:** Prefix with an underscore (`_`) (e.g., `_privateVariable`, `_calculateInternal`).
* **Readability:** Write clear, concise code. Keep functions and classes small and focused. Use meaningful names. Avoid overly clever or obscure code.
* **Comments & Documentation:**
    * Use `///` for documentation comments readable by `dart doc`. Document all public APIs (libraries, classes, methods, functions, variables). Start doc comments with a single sentence summary.
    * Use `//` for implementation comments explaining non-obvious logic or rationale. Avoid commenting obvious code.
    * Keep comments up-to-date.
* **Null Safety:** Embrace Dart's sound null safety. Avoid nullable types (`?`) unless `null` represents a valid state. Use null-aware operators (`?.`, `??`, `?..`), type promotion, and `late` keyword appropriately. Avoid the null assertion operator (`!`) where possible; prefer explicit checks or safe patterns.
* **Immutability:** Prefer immutable state and data structures where possible. Use `final` for variables that are not reassigned. Use immutable collections (e.g., from `package:fast_immutable_collections` or by convention) or copy collections when passing them if mutation is a risk. Use `const` for compile-time constants.

## Project Structure

* **Standard Flutter Structure:** Adhere to the standard Flutter project structure (`lib/`, `test/`, `assets/`, `android/`, `ios/`, etc.).
* **Feature-First or Layer-First:** Organize code within the `lib/` directory using either a feature-first (e.g., `lib/features/authentication/`, `lib/features/profile/`) or layer-first (e.g., `lib/src/data/`, `lib/src/domain/`, `lib/src/presentation/`) approach. Choose one and be consistent. Feature-first is often preferred for better modularity.
* **Separation:** Separate UI (Widgets), state management/logic (Blocs, Providers, ViewModels), data access (Repositories, Data Sources), and domain logic.
* **`main.dart`:** Keep `lib/main.dart` minimal, primarily for app initialization, setting up dependency injection, and launching the root widget.
* **Barrel Files:** Avoid barrel files (`export '...';`) excessively, as they can sometimes hinder tree-shaking and increase coupling. Import specific files directly where possible.

## Widgets & UI (Flutter)

* **Widget Composition:** Build UI by composing small, reusable widgets. Prefer composition over inheritance for widgets.
* **`const` Constructors:** Use `const` constructors for widgets whenever possible. This allows Flutter to cache widget instances and skip unnecessary rebuilds, significantly improving performance.
* **Split Widgets:** Break down large build methods into smaller, private widget-building methods or, preferably, into separate `StatelessWidget` or `StatefulWidget` classes. This improves readability and helps Flutter optimize rebuilds.
* **`StatelessWidget` vs. `StatefulWidget`:** Prefer `StatelessWidget` by default. Use `StatefulWidget` only when the widget needs to manage internal, mutable state that changes during its lifetime and affects the UI.
* **Build Method Purity:** The `build()` method MUST be pure and free of side effects. It should only depend on the widget's configuration, `BuildContext`, and state (`StatefulWidget`'s `State`). Any asynchronous operations or side effects should be handled elsewhere (e.g., `initState`, state management logic).
* **Keys:** Use `Key`s (e.g., `ValueKey`, `ObjectKey`, `UniqueKey`) on widgets within collections (especially lists modified dynamically) or when needing to preserve state across widget tree changes of the same type. Keys help Flutter identify and update elements efficiently.
* **Layouts:** Understand Flutter's layout system (Constraints go down, sizes go up, parent sets position). Use appropriate layout widgets (`Column`, `Row`, `Stack`, `Expanded`, `Flexible`, `ListView`, `GridView`, etc.). Use `LayoutBuilder` to make layout decisions based on parent constraints.
* **Responsive UI:** Design UIs that adapt to different screen sizes and orientations using widgets like `MediaQuery`, `LayoutBuilder`, `FittedBox`, `FractionallySizedBox`, and responsive layout patterns.
* **Theming:** Use `ThemeData` and `Theme.of(context)` consistently for colors, typography, and styling to ensure a consistent look and feel and support features like dark mode easily.

## State Management

* **Choose Appropriately:** Select a state management approach based on the complexity and scope of the state:
    * **`setState`:** Suitable only for local, ephemeral state within a single `StatefulWidget`.
    * **InheritedWidget / InheritedModel:** Flutter's low-level mechanism for passing data down the widget tree. Often used as the basis for other solutions.
    * **Provider:** A popular, relatively simple solution for dependency injection and state management, often used with `ChangeNotifier` or `ValueNotifier`.
    * **Riverpod:** A compile-safe alternative/improvement to Provider, offering more flexibility and testability without `BuildContext` dependency.
    * **Bloc / Cubit:** Suitable for complex state with well-defined events/states and business logic separation. Offers excellent testability.
    * **GetX:** An opinionated framework providing state management, routing, and dependency injection with minimal boilerplate.
    * **Others:** Redux, MobX implementations exist.
* **Consistency:** Choose one primary state management solution for global/complex state within the project and use it consistently.
* **Minimize `setState` Scope:** When using `setState`, call it on the lowest possible widget in the tree that encompasses the UI needing update to minimize the rebuild scope.
* **Keep State Lean:** Store only the necessary data required for the UI in the state. Avoid storing large, complex objects directly if only parts are needed.
* **Separate UI and Logic:** State management solutions should help separate business/state logic from UI widget code.

## Asynchronicity (Dart)

* **`async`/`await`:** Use `async`/`await` for managing `Future`s and writing asynchronous code that looks synchronous, improving readability over raw `.then()` chains.
* **Error Handling:** Handle errors from `Future`s using `try-catch` blocks within `async` functions or using `.catchError()`.
* **`Future.wait`:** Use `Future.wait()` to run multiple independent futures concurrently and wait for all to complete.
* **Streams:** Use `Stream`s for handling sequences of asynchronous events or data over time. Use `StreamBuilder` or state management integrations to reactively build UI based on stream updates.
* **Isolates:** For CPU-bound tasks that would block the main UI isolate, use `Isolate.run()` or `compute()` to run the computation on a separate isolate (effectively a separate thread with its own memory heap). Avoid running heavy computations directly on the main isolate.

## Platform Channels

* **Use When Necessary:** Use platform channels (`MethodChannel`, `EventChannel`, `BasicMessageChannel`) only when needing to access platform-specific native APIs (iOS/Android) not available through Flutter itself or existing plugins.
* **Async Communication:** Communication over platform channels is asynchronous. Design accordingly using `async`/`await` or `Future`s.
* **Data Types:** Use standard platform channel codecs or define custom codecs for complex data types passed over the channel.
* **Error Handling:** Implement robust error handling on both the Dart and native sides for channel communication failures.
* **Plugin Architecture:** Encapsulate platform channel logic within well-defined plugins/packages for reusability.

## Performance

* **Build Modes:** Understand Flutter's build modes:
    * **Debug:** Enables hot reload, assertions, service extensions. Not optimized, slow performance. Use only for development.
    * **Profile:** Disables most debugging aids but retains service extensions for profiling. Optimized build similar to release. Use for performance analysis.
    * **Release:** Maximum optimization, smallest size, debugging/profiling disabled. Use for deployment.
* **Minimize Widget Rebuilds:** The core of Flutter performance optimization.
    * Use `const` widgets whenever possible.
    * Split large widgets into smaller ones.
    * Use state management solutions that allow fine-grained updates (e.g., `context.select` in Provider, specific state listeners in Bloc/Riverpod).
    * Use `RepaintBoundary` judiciously for isolating parts of the UI that repaint frequently.
* **Optimize Layouts:** Avoid unnecessarily deep or complex widget trees. Use specialized layout widgets where appropriate (e.g., `ListView.builder` for long lists).
* **ListView/GridView Builders:** Use `ListView.builder` and `GridView.builder` constructors for long or infinite lists, as they only build visible items. Provide `itemExtent` or `prototypeItem` if possible for further optimization.
* **Image Optimization:** Load images at appropriate resolutions. Use caching (`cached_network_image` package). Use efficient formats like WebP. Consider `Image.memory` with `cacheWidth`/`cacheHeight` for resizing large images.
* **Avoid Expensive Operations in `build`:** Keep `build` methods fast and free of heavy computations or synchronous I/O (see Widgets section).
* **Profiling:** Use Flutter DevTools (Performance View, CPU Profiler, Memory Profiler) to identify performance bottlenecks (janky frames, high CPU/memory usage).

## Security

* **Follow Security Rules:** Adhere to general rules in `security_rules.md` and platform-specific guidelines below.
* **Data Storage:** Store sensitive data securely using `flutter_secure_storage` (uses Keychain/Keystore). Do NOT use `shared_preferences` for sensitive data. Encrypt local database files (e.g., SQFlite with SQLCipher).
* **Networking:** Use HTTPS exclusively. Implement certificate pinning for high-security applications if required.
* **Input Validation:** Validate all data from external sources (user input, network responses).
* **Platform Channel Security:** Be cautious about data passed over platform channels. Validate data received from native code.
* **Deep Linking:** Validate and sanitize data received from deep links.
* **Secrets Management:** Do not commit secrets (API keys, etc.) to version control. Use techniques like `--dart-define` from environment variables during build time, or retrieve secrets from a secure backend/secrets manager at runtime.
* **Code Obfuscation:** Use `flutter build --obfuscate` along with `--split-debug-info` for release builds to make reverse engineering more difficult.
* **Dependencies:** Regularly audit dependencies (`flutter pub outdated`, use vulnerability scanners) and keep them updated.

## Testing

* **Follow Testing Rules:** Adhere to rules defined in `testing_rules.md`.
* **Testing Pyramid:** Emphasize unit tests, followed by widget tests, and fewer integration/E2E tests.
* **Unit Tests:** Use the `test` package to test Dart logic (functions, classes, state management logic) in isolation without Flutter framework dependencies. Mock external dependencies.
* **Widget Tests:** Use the `flutter_test` package to test individual Flutter widgets in isolation. Verify UI rendering and basic interactions without needing a full device/emulator. Use `tester.pumpWidget()` and finders/matchers. Mock service dependencies.
* **Integration Tests:** Use the `integration_test` package to test critical user flows or interactions between multiple parts of the app, running on a real device or emulator.
* **Testability:** Design code (especially state management logic) for testability (DI, interfaces).
* **CI Integration:** Run tests automatically in CI pipelines.
