# General Cross-Platform Mobile Rules

## Framework Choice & Architecture

* **Evaluate Frameworks:** Choose a cross-platform framework (e.g., React Native, Flutter, Kotlin Multiplatform, Xamarin) based on project requirements, performance needs, target platforms, available native integrations, ecosystem maturity, community support, and team skillset. Understand the trade-offs of each.
* **Modular Design:** Structure the application into reusable modules or components. Follow principles like Separation of Concerns.
* **Shared vs. Platform-Specific Layers:** Clearly define which parts of the codebase will be shared (e.g., business logic, state management, data access) and which parts might require platform-specific implementations (e.g., certain UI components, native module integrations).

## User Interface (UI) & User Experience (UX)

* **Platform Conventions vs. Brand Consistency:**
    * **Decision:** Decide early whether the app should strictly follow native platform UI conventions (Material Design for Android, Human Interface Guidelines for iOS) or maintain a consistent brand look and feel across platforms.
    * **Balance:** Often, a balance is best. Use standard platform navigation patterns (tabs, navigation bars), gestures, and interactions where users expect them, but apply consistent branding (colors, typography, core component styles).
    * **Avoid "Uncanny Valley":** Don't try to perfectly mimic one platform's native controls on the other; this often feels slightly "off". Aim for a clean design that feels appropriate on both, potentially using UI component libraries designed for cross-platform consistency (but still respecting platform navigation/interaction norms).
* **Responsive Design:** Design layouts that adapt gracefully to various screen sizes, densities, and orientations (phones, tablets, foldables). Use framework-specific tools for responsive layouts (e.g., Flexbox, relative units).
* **Performance:** Ensure UI interactions, animations, and transitions are smooth on both platforms. Profile UI performance, especially for complex screens or lists.

## Codebase Management

* **Single Codebase (Maximize):** Strive to maximize code sharing across platforms. Abstract platform differences where possible.
* **Platform-Specific Code:** When platform-specific code is necessary:
    * **Isolate:** Keep platform-specific logic clearly separated from shared code.
    * **Conditional Logic:** Use framework mechanisms for conditional execution based on the platform (`Platform.OS` in React Native, conditional imports/compilation in Flutter/KMP). Avoid littering shared code with excessive platform checks.
    * **File Naming Conventions:** Use platform-specific file extensions if supported by the framework (e.g., `.ios.js`, `.android.js` in React Native) for components or modules with significant platform differences.
    * **Abstraction:** Create common interfaces or abstractions in shared code and provide platform-specific implementations.
* **Code Style & Linting:** Use linters and formatters configured for the chosen framework/language (e.g., ESLint/Prettier for React Native, Dart analyzer/formatter for Flutter) to maintain consistency.

## Native Features & Modules

* **Minimize Native Dependencies:** Rely on the framework's core components and APIs first. Use native modules (custom native code bridged to the cross-platform layer) only when necessary for:
    * Accessing platform-specific APIs not exposed by the framework.
    * Performance-critical tasks requiring native execution (e.g., heavy computation, custom graphics rendering).
    * Integrating with existing native SDKs.
* **Evaluate Third-Party Plugins:** Carefully vet third-party plugins/libraries that interact with native features. Check for maintenance status, platform compatibility, performance impact, and security. Prefer well-maintained community or official packages.

## Performance Optimization

* **Platform Differences:** Be aware that performance characteristics can differ between iOS and Android, even with a shared codebase. Profile and optimize on both platforms.
* **Startup Time:** Optimize application startup time, as it's a critical UX factor. Techniques include code splitting, lazy loading, and optimizing initial data loading.
* **UI Thread:** Keep the main UI thread free from long-running or blocking operations. Offload work using background threads, workers, or asynchronous patterns specific to the framework (e.g., Coroutines/Isolates in Flutter, separate JS thread in React Native).
* **Resource Optimization:** Optimize assets (images, fonts) for mobile formats and sizes. Use efficient data serialization formats for network requests.
* **Memory Management:** Monitor memory usage on both platforms. Be mindful of potential leaks, especially related to native module interactions or event listeners.

## Testing

* **Follow Testing Rules:** Adhere to general rules in `testing_rules.md`.
* **Testing Pyramid:** Apply the testing pyramid:
    * **Unit Tests:** Write extensive unit tests for shared business logic (ideally in pure language - Dart, JS/TS) independent of the framework UI.
    * **Widget/Component Tests:** Test individual UI components in isolation using framework-specific testing utilities (e.g., `flutter_test`, React Native Testing Library). Mock dependencies.
    * **Integration Tests:** Test interactions between components or modules within the cross-platform framework.
    * **End-to-End (E2E) Tests:** Write fewer E2E tests covering critical user flows on both platforms. Use cross-platform E2E frameworks (like Appium, Maestro) or run platform-specific tests (XCUITest, Espresso) via framework drivers if needed.
* **Platform Coverage:** Run automated tests (especially Component and E2E tests) on simulators/emulators and *real devices* for both target platforms (iOS and Android) as part of the CI/CD process. Platform differences often surface only on real hardware or specific OS versions.
* **Device Farms:** Utilize cloud-based device farms (e.g., Sauce Labs, BrowserStack, Firebase Test Lab) for broader E2E test coverage across various devices and OS versions.

## Security

* **Follow Security Rules:** Adhere to general rules in `security_rules.md`.
* **Platform-Specific Security:** Implement security best practices specific to both iOS and Android platforms regarding data storage (Keychain, Keystore), permissions, IPC, and network security configuration.
* **Secrets Management:** Do not embed API keys or other secrets directly in the cross-platform codebase. Use secure storage mechanisms and retrieve them at runtime, potentially using platform-specific secure storage if necessary.
* **Dependency Vulnerabilities:** Regularly scan cross-platform *and* native dependencies for vulnerabilities.

## Build & Deployment

* **CI/CD:** Set up separate build and deployment pipelines for iOS and Android within your CI/CD system.
* **Automated Builds:** Automate the build process for both platforms.
* **App Store Guidelines:** Ensure the application adheres to the submission guidelines for both the Apple App Store and Google Play Store.
