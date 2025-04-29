# Android / Kotlin Rules

## General Principles & Style

* **Kotlin First:** Prefer Kotlin over Java for new Android development. Leverage its features for concise, safe, and expressive code.
* **Kotlin Style Guide:** Follow the official Kotlin Coding Conventions and the Android Kotlin Style Guide for consistent formatting, naming, and language usage.
* **Formatting:** Use `ktlint` or the IDE's built-in formatter (configured according to the style guide - typically 4 spaces for indentation) to enforce consistent formatting. Integrate formatting checks into CI.
* **Readability:** Write clear, understandable code. Keep functions and classes focused. Use meaningful names.
* **Project Structure:** Follow standard Android project structure (e.g., organize by feature or layer within modules). Use appropriate Gradle module separation for larger apps.

## Kotlin Language Usage

* **Null Safety:** Embrace Kotlin's null safety features. Avoid platform types from Java interop where possible. Use non-nullable types by default. Use nullable types (`?`) only when `null` represents a valid state. Prefer safe calls (`?.`), Elvis operator (`?:`), and scope functions (`let`, `run`, `apply`, `also`) over force unwrapping (`!!`).
* **Immutability:** Prefer immutable variables (`val`) over mutable ones (`var`). Use immutable collections (`List`, `Set`, `Map`) over mutable ones (`MutableList`, etc.) where possible, especially for function parameters and return types. Use `data class` for immutable data holders.
* **Data Classes:** Use `data class` for classes primarily holding data. They automatically generate `equals()`, `hashCode()`, `toString()`, `copy()`, and component functions.
* **Scope Functions:** Use scope functions (`let`, `run`, `with`, `apply`, `also`) appropriately to make code more concise and readable, but avoid excessive nesting.
* **Extension Functions:** Use extension functions to add utility methods to existing classes without inheriting from them, especially for helper or conversion logic. Keep extensions focused and discoverable.
* **Sealed Classes/Interfaces:** Use `sealed class` or `sealed interface` for representing restricted class hierarchies (e.g., different states of a UI or result types), enabling exhaustive `when` expressions.
* **Top-Level Functions/Properties:** Use top-level functions and properties for true utility functions or constants not tied to a specific class instance. Organize them in meaningful files (e.g., `StringUtils.kt`).
* **Type Aliases:** Use `typealias` to create descriptive aliases for complex types (e.g., function types, nested generics) to improve readability.
* **Named Arguments:** Use named arguments when calling functions with multiple parameters, especially booleans or those with default values, to improve clarity at the call site.

## Android App Architecture

* **Official Guide:** Follow the recommended Android App Architecture guidelines (UI Layer, Domain Layer (optional), Data Layer).
* **MVVM / MVI:** Prefer architectural patterns like MVVM (Model-View-ViewModel) or MVI (Model-View-Intent) for separating concerns, especially in the UI layer. Use Android Architecture Components (ViewModel, LiveData/StateFlow, Room, DataStore).
* **Dependency Injection:** Use Dependency Injection (DI) frameworks like Hilt (recommended) or Koin to manage dependencies, improve testability, and reduce boilerplate. Avoid manual dependency instantiation in classes.
* **Repository Pattern:** Abstract data sources (network, database, cache) behind a Repository interface in the data layer. The repository coordinates data operations and provides a clean API to the domain or view model layers.
* **Single Source of Truth:** Establish a single source of truth for application data, typically the database (e.g., Room) or a cached layer managed by the Repository. Network data should be fetched and stored in the source of truth, which then emits updates to the UI layer.

## UI (Jetpack Compose & Views)

* **Jetpack Compose First (for new UI):** Prefer Jetpack Compose for building new UI over the traditional Android View system for its declarative nature, Kotlin integration, and improved developer experience.
* **Compose Best Practices:**
    * **Composable Functions:** Keep composable functions small, focused, stateless (where possible), and reusable. They should describe the UI based on inputs (state) and emit events.
    * **Unidirectional Data Flow (UDF):** Follow UDF principles. State flows down (from ViewModel/State Holder to Composables via parameters), and events flow up (via lambda callbacks passed as parameters).
    * **State Hoisting:** Hoist state to the lowest common ancestor component that needs it. Pass state down and event callbacks up.
    * **`remember`:** Use `remember` to store state within a composable across recompositions. Use `rememberSaveable` for state that needs to survive configuration changes or process death.
    * **Side Effects:** Handle side effects (e.g., launching coroutines, fetching data, showing toasts) correctly using Compose's effect handlers (`LaunchedEffect`, `DisposableEffect`, `SideEffect`, `produceState`, `derivedStateOf`).
    * **Performance:**
        * Minimize unnecessary recompositions. Pass stable types (primitives, immutable objects/collections) or lambdas as parameters where possible.
        * Use `derivedStateOf` to limit recompositions based on derived state changes.
        * Use lazy layouts (`LazyColumn`, `LazyRow`) for long lists. Provide stable keys using the `key` parameter.
        * Profile UI performance using Layout Inspector and System Tracing in Android Studio.
        * Avoid complex calculations directly within composable functions; move them to `remember` blocks or `derivedStateOf`.
    * **Theming:** Use Material Design 3 (`androidx.compose.material3`) components and theming (`MaterialTheme`) for consistent styling. Define custom themes appropriately.
* **Interoperability (if using Views & Compose):** Use `ComposeView` (to host Compose in Views) and `AndroidViewBinding` (to host Views in Compose) carefully when migrating or mixing UI toolkits.

## Android SDK & Lifecycle

* **Component Lifecycles:** Understand and respect the lifecycles of Activities, Fragments, Services, and ViewModels. Acquire and release resources (e.g., listeners, database connections, coroutine scopes) at appropriate lifecycle events (e.g., register in `onStart`/`onResume`, unregister in `onStop`/`onPause`; launch coroutines in `viewModelScope` or `lifecycleScope`).
* **Permissions:** Request permissions only when necessary for a feature. Explain clearly why the permission is needed. Handle permission denial gracefully. Follow platform guidelines for runtime permissions.
* **Background Work:** Use appropriate solutions for background tasks:
    * **Kotlin Coroutines:** For asynchronous operations tied to a specific scope (ViewModel, Lifecycle).
    * **WorkManager:** For deferrable, guaranteed background work, even if the app exits or the device restarts (e.g., syncing data, background uploads).
    * **Foreground Services:** For tasks requiring immediate user awareness that need to continue running (e.g., music playback, navigation). Use appropriately and provide clear notifications.
* **Resource Management:** Use resource qualifiers (e.g., `layout-sw600dp`, `values-night`, `drawable-xxhdpi`) to provide alternative resources for different screen sizes, densities, orientations, and modes (dark/light). Use vector drawables (`VectorDrawable`) where possible for scalable graphics.

## Concurrency (Kotlin Coroutines)

* **Use Coroutines:** Prefer Kotlin Coroutines for managing asynchronous operations over traditional callbacks, AsyncTasks (deprecated), or RxJava (unless existing project standard).
* **Structured Concurrency:** Launch coroutines within specific `CoroutineScope`s (e.g., `viewModelScope`, `lifecycleScope`) that are tied to component lifecycles. This ensures coroutines are automatically cancelled when the scope is cancelled, preventing leaks. Avoid using `GlobalScope`.
* **Dispatchers:** Use appropriate dispatchers (`Dispatchers.Main` for UI updates, `Dispatchers.IO` for network/disk I/O, `Dispatchers.Default` for CPU-intensive work) using `withContext` to switch contexts safely. Define dependencies on dispatchers for testability.
* **Error Handling:** Handle exceptions within coroutines using `try-catch` blocks. Understand exception propagation differences between `launch` (propagates to scope/handler) and `async` (deferred until `await`). Use `CoroutineExceptionHandler` for global uncaught exception handling within a scope.
* **Flow:** Use Kotlin Flow for handling streams of asynchronous data. Collect flows in a lifecycle-aware manner (e.g., using `lifecycleScope.launch` with `repeatOnLifecycle` or `flowWithLifecycle`).

## Performance

* **Profiling:** Use Android Studio Profilers (CPU, Memory, Network, Energy) and Systrace/Perfetto to identify performance bottlenecks. Profile on real devices.
* **Strict Mode:** Enable `StrictMode` during development to detect accidental disk or network access on the main thread, memory leaks, etc.
* **Memory Management:**
    * Avoid memory leaks (e.g., holding references to Activities/Views in ViewModels or long-lived objects, unclosed resources, static references to contexts). Use LeakCanary in debug builds.
    * Use memory-efficient data structures (e.g., `ArrayMap`, `SparseArray` over `HashMap` for integer keys where appropriate).
    * Analyze memory usage with the Memory Profiler. Optimize bitmap loading and caching.
* **App Startup:** Optimize app startup time (Cold, Warm, Hot starts). Defer non-essential initialization. Consider using the App Startup library.
* **Baseline Profiles / Startup Profiles:** Generate and include Baseline Profiles (or Startup Profiles for libraries) with release builds to improve code execution speed for critical user journeys, especially app startup.
* **Build Performance:** Optimize Gradle build times (use latest Gradle and Android Gradle Plugin, configure caching, avoid unnecessary computations during configuration phase).

## Security

* **Follow Security Rules:** Adhere to rules defined in `security_rules.md` and platform-specific guidelines below.
* **Data Storage:**
    * Store sensitive data (tokens, user credentials) securely using the Android Keystore system via libraries like Jetpack Security (EncryptedSharedPreferences, EncryptedFile). Do NOT store sensitive data in plain SharedPreferences or easily accessible files.
    * Use appropriate file storage locations (internal vs. external) based on data sensitivity and sharing needs. Use Scoped Storage correctly on newer Android versions.
* **Networking:**
    * Use HTTPS exclusively. Configure Network Security Configuration to enforce this and potentially apply certificate pinning for high-security scenarios.
    * Validate server certificates properly.
* **Permissions:** Request only necessary permissions. Use runtime permissions correctly.
* **Inter-Process Communication (IPC):** Secure IPC mechanisms (Intents, Services, Broadcast Receivers, Content Providers).
    * Use explicit Intents over implicit Intents where possible.
    * Protect components with permissions (`android:permission`).
    * Validate data received from other apps via Content Providers or Intents.
    * Do not export Content Providers or Services unnecessarily (`android:exported="false"`).
* **WebView Security:** Use `WebView` cautiously. Disable JavaScript (`setJavaScriptEnabled(false)`) if not needed. Limit URL loading to trusted domains. Be careful when using `addJavascriptInterface`.
* **Input Validation:** Validate all data received from external sources (user input, network, IPC).
* **Code Obfuscation/Shrinking:** Use R8 (or ProGuard) in release builds to shrink, obfuscate, and optimize code, making reverse engineering more difficult.

## Testing

* **Follow Testing Rules:** Adhere to rules defined in `testing_rules.md`.
* **Testing Pyramid:** Focus on a strong base of unit tests, followed by integration tests, and fewer end-to-end (UI) tests.
* **Unit Tests:** Use JUnit4/5 and mocking frameworks (like Mockito/Mockito-Kotlin) or test doubles to test classes (ViewModels, Repositories, UseCases, Utils) in isolation. Test Coroutines using `kotlinx-coroutines-test`.
* **Integration Tests:** Test interactions between layers (e.g., ViewModel with Repository, Repository with Dao/API). Often require instrumented tests or specific test setups (e.g., Hilt testing support, in-memory Room database).
* **UI Tests (Compose/Espresso):**
    * **Compose:** Use `androidx.compose.ui.test` APIs for testing composables. Use semantic nodes and test tags for reliable element finding.
    * **Views (Espresso):** Use Espresso for testing UI interactions within the traditional View system.
* **Testability:** Design code for testability (DI, interfaces, avoiding static dependencies).
* **CI Integration:** Run tests automatically in CI pipelines.

## Build & Dependencies (Gradle)

* **Gradle Wrapper:** Always commit the Gradle Wrapper (`gradlew`, `gradlew.bat`, `gradle/wrapper/`) to version control to ensure consistent Gradle versions across environments.
* **Dependency Management:** Define dependencies clearly in `build.gradle` (or `build.gradle.kts`). Use version catalogs (e.g., `libs.versions.toml`) for centralized dependency version management.
* **Keep Updated:** Regularly update Android Gradle Plugin, Kotlin version, and library dependencies.
* **Build Types & Flavors:** Utilize build types (`debug`, `release`) and product flavors to manage different build configurations (e.g., different API endpoints, feature flags). Store keys and secrets securely, not directly in build files.
