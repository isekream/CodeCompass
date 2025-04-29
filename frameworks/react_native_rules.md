# React Native Rules

## General Principles & Style

* **Follow JS/TS Rules:** Adhere to all rules defined in `javascript_typescript_rules.md`.
* **Follow React Rules:** Adhere to general React best practices defined in `RuleFiles/frameworks/react_rules.md` (e.g., component composition, hooks, immutability).
* **Follow Cross-Platform Rules:** Adhere to rules defined in `cross_platform_mobile_rules.md` (e.g., balancing native look vs. consistency, testing on both platforms).
* **Consistency:** Maintain consistency in naming, file structure, state management patterns, and coding style throughout the project. Use linters (ESLint with React Native plugins) and formatters (Prettier).
* **Use TypeScript:** Prefer TypeScript over plain JavaScript for new projects or significant refactors to leverage static typing for better maintainability, error detection, and developer experience.

## Project Structure

* **Organize by Feature/Module:** Structure the `src` (or equivalent) directory by feature or domain rather than by type (e.g., `src/features/authentication/`, `src/features/user-profile/` containing components, screens, services, etc., related to that feature).
* **Reusable Components:** Place globally reusable UI components in a dedicated directory (e.g., `src/components/`, `src/shared/ui/`).
* **Screens/Pages:** Place top-level components representing application screens in a dedicated directory (e.g., `src/screens/`, `src/pages/`).
* **Services/API:** Centralize API client logic and other services (e.g., `src/services/`, `src/api/`).
* **State Management:** Keep state management logic (stores, reducers, actions) organized, often in a dedicated directory (e.g., `src/store/`, `src/state/`).
* **Navigation:** Centralize navigation configuration (see Navigation section).
* **Assets:** Store static assets like images and fonts in a dedicated directory (e.g., `src/assets/`).
* **Utils/Helpers:** Place generic utility functions in a `src/utils/` or `src/helpers/` directory.
* **Aliases:** Configure path aliases (e.g., using `babel-plugin-module-resolver` or TypeScript paths) for cleaner imports (e.g., `import Button from '@/components/Button'` instead of deep relative paths).

## Components

* **Functional Components & Hooks:** Strongly prefer functional components with Hooks over class components for state management, side effects, and logic reuse.
* **Component Size:** Keep components small and focused on a single responsibility. Decompose complex components into smaller, reusable sub-components.
* **Props:**
    * Use TypeScript interfaces or PropTypes (if using JavaScript) to define expected prop types.
    * Use prop destructuring in functional components for readability.
    * Pass only necessary props down the component tree. Avoid excessive prop drilling (consider state management or Context API).
* **DRY (Don't Repeat Yourself):** Extract reusable UI patterns and logic into separate components or custom hooks.

## Styling

* **`StyleSheet.create`:** Define styles using `StyleSheet.create` for performance optimizations (sending styles over the bridge only once) and better organization compared to inline styles. Keep styles co-located with the component file or in a separate `styles.js`/`.ts` file.
    ```javascript
    import { StyleSheet, Text, View } from 'react-native';

    const MyComponent = () => (
      <View style={styles.container}>
        <Text style={styles.text}>Hello</Text>
      </View>
    );

    const styles = StyleSheet.create({
      container: { padding: 10, backgroundColor: 'white' },
      text: { color: 'blue', fontSize: 16 },
    });
    ```
* **Naming:** Use `camelCase` for style properties (e.g., `backgroundColor`, `fontSize`).
* **Avoid Inline Styles:** Minimize the use of inline style objects (`style={{ padding: 10 }}`) as they can impact performance (new object created on every render) and make consistency harder. Use them only for highly dynamic styles that cannot be predefined.
* **Platform-Specific Styles:** Use `Platform.select()` or platform-specific files (`.ios.js`, `.android.js`) for styles that need to differ significantly between platforms (see Platform-Specific Code section).
* **Theming/Design Systems:** For larger applications, consider using a theming library or establishing a design token system (colors, spacing, typography) to ensure consistency and easier rebranding/theming.

## State Management

* **Local State (`useState`, `useReducer`):** Use component local state for UI state that is not needed elsewhere.
* **Context API:** Use React's Context API for sharing state that needs to be accessed by many components at different nesting levels, without excessive prop drilling. Suitable for low-frequency updates (e.g., theme, user authentication status).
* **Global State Libraries:** For complex, frequently updated global state or state shared across many unrelated components, use dedicated state management libraries like Redux (with Redux Toolkit), Zustand, Jotai, or MobX. Choose one library and use it consistently. Follow best practices for the chosen library (e.g., immutable updates in Redux).

## Navigation

* **Use a Library:** Use a standard navigation library like React Navigation (most common) or React Native Navigation (RNN - for more native performance focus).
* **Centralized Configuration:** Define navigation stacks, tabs, and drawers in a dedicated configuration area (e.g., `src/navigation/`).
* **Screen Structure:** Treat components used directly in navigators as "screens" and keep them focused on orchestrating UI components for that specific screen.
* **Passing Parameters:** Pass necessary parameters between screens via route params. Avoid passing complex objects; pass IDs and fetch data in the target screen if needed.
* **Deep Linking:** Implement deep linking to allow users to navigate directly to specific screens from outside the app.
* **Type Safety (TypeScript):** If using TypeScript, define types for route names and parameters for improved safety and autocompletion with React Navigation.

## Platform-Specific Code

* **Minimize Platform Code:** Write code that works across platforms whenever possible. Abstract platform differences in shared components or hooks.
* **`Platform` Module:** Use the `Platform.OS` check or `Platform.select()` API for small, inline conditional logic or styling based on the platform (iOS/Android).
    ```javascript
    import { Platform, StyleSheet } from 'react-native';
    const styles = StyleSheet.create({
      paddingTop: Platform.OS === 'ios' ? 20 : 10,
      // OR
      buttonColor: Platform.select({ ios: 'blue', android: 'green' }),
    });
    ```
* **Platform-Specific Extensions:** For larger chunks of platform-specific logic or entire components, use platform-specific file extensions:
    * `MyComponent.ios.js` (or `.tsx`)
    * `MyComponent.android.js` (or `.tsx`)
    * Import using `import MyComponent from './MyComponent';` - React Native automatically selects the correct file.
* **Native Modules (if needed):** If requiring platform-specific native functionality not available via core components or community libraries, create custom Native Modules (requires native iOS/Android development knowledge).

## Native Modules & Bridging

* **Use Existing Libraries:** Before building custom native modules, thoroughly check for existing, well-maintained community libraries that provide the needed functionality.
* **New Architecture (TurboModules / Fabric):** For new projects or significant refactors, consider enabling and utilizing React Native's New Architecture (Codegen, TurboModules, Fabric renderer) for improved performance and more direct native integration, though it may have a steeper learning curve.
* **Minimize Bridge Traffic:** Be mindful of data passed over the React Native bridge (between JS and Native). Avoid sending large amounts of data frequently. Optimize native module calls.

## Performance Optimization

* **Rendering:**
    * Use `React.memo`, `useMemo`, `useCallback` to prevent unnecessary re-renders of components and recalculations. Profile to identify bottlenecks first.
    * Avoid anonymous functions directly in props (e.g., `onPress={() => doSomething()}`) as they create new function instances on every render; define functions outside or use `useCallback`.
    * Use `FlatList`, `SectionList`, or `VirtualizedList` for rendering long lists of data efficiently. Implement `keyExtractor` correctly. Optimize list item components (`React.memo`). Consider `getItemLayout` if item height is fixed.
    * Minimize work done on the JS thread during interactions/animations.
* **Images:**
    * Optimize image assets (use appropriate formats like WebP, compress sizes).
    * Use `react-native-fast-image` or similar libraries for better image caching and performance.
    * Specify image dimensions (`width`, `height`) where possible to prevent layout jumps during loading.
* **Startup Time (TTI):** Optimize Time To Interactive (TTI). Techniques include reducing bundle size, deferring non-critical initializations, and using the Hermes JavaScript engine (enabled by default in newer RN versions).
* **Memory Leaks:** Be mindful of memory leaks, especially from uncleared timers/intervals, event listeners, or subscriptions within `useEffect` hooks. Use the cleanup function returned by `useEffect`.
* **Profiling:** Use tools like Flipper, React DevTools Profiler, and platform-native profilers (Xcode Instruments, Android Studio Profiler) to identify JS and Native performance bottlenecks.
* **Hermes Engine:** Ensure the Hermes engine is enabled for improved startup time, reduced memory usage, and smaller app size.

## Security

* **Data Storage:** Store sensitive data (tokens, credentials, PII) securely using platform-specific secure storage (Keychain on iOS, Keystore/EncryptedSharedPreferences on Android). Use libraries like `react-native-keychain` or `react-native-sensitive-info`. Do NOT use `AsyncStorage` (or its community replacements) for sensitive data as it's typically unencrypted.
* **Network Security:** Use HTTPS exclusively. Implement SSL/Certificate Pinning for high-security applications to prevent Man-in-the-Middle (MitM) attacks.
* **Deep Linking Security:** Validate and sanitize data received through deep links.
* **Authentication/Authorization:** Implement secure authentication flows. Store tokens securely. Validate tokens server-side.
* **Code Obfuscation:** Consider using code obfuscation/minification tools (Metro bundler supports this via Terser) in release builds to make reverse engineering harder.
* **Disable Debug Mode:** Ensure debug mode (`__DEV__ = false`) is disabled for production builds.
* **Input Validation:** Validate all input from users or external sources.
* **Dependencies:** Regularly audit dependencies (`npm audit`) and keep them updated.

## Testing

* **Follow Testing Rules:** Adhere to general rules in `testing_rules.md`.
* **Unit Tests:** Use Jest (typically configured by default) for unit testing JavaScript logic (helpers, services, state logic).
* **Component Tests:** Use React Native Testing Library for testing components by interacting with them as a user would, verifying rendered output and behavior without relying on implementation details.
* **E2E Tests:** Use E2E testing frameworks designed for mobile like Detox (preferred for deep native integration) or Appium/WebdriverIO for testing complete user flows on simulators/emulators and real devices.
* **CI Integration:** Run linters, formatters, unit tests, component tests, and potentially E2E tests automatically in CI pipelines.
