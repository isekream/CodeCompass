# Vite Rules

## General Principles & Style

* **Follow JS/TS Rules:** Adhere to all rules defined in `javascript_typescript_rules.md`.
* **Leverage Native ESM:** Understand that Vite's core principle during development is leveraging native ES Module (ESM) imports in the browser for fast startup and Hot Module Replacement (HMR). Write code using ESM syntax (`import`/`export`).
* **Framework Agnostic Core:** While Vite has excellent framework integrations (Vue, React, Svelte, etc.), its core is framework-agnostic. Follow framework-specific best practices in addition to Vite rules.
* **Consistency:** Maintain consistency in configuration, plugin usage, and project structure.
* **Tooling:** Use linters (ESLint) and formatters (Prettier) configured appropriately for your project type (JS/TS, specific framework).

## Configuration (`vite.config.js` / `vite.config.ts`)

* **File Type:** Prefer using `vite.config.ts` with TypeScript for type safety and better intellisense, using the `defineConfig` helper. Plain JavaScript (`vite.config.js`) is also fully supported.
* **Clarity:** Keep the configuration file clean and well-organized. Use comments to explain non-obvious configurations.
* **Conditional Config:** If configuration needs to vary based on the command (`serve` vs. `build`) or mode (`development` vs. `production`), export a function from the config file instead of an object.
    ```typescript
    import { defineConfig } from 'vite';

    export default defineConfig(({ command, mode }) => {
      if (command === 'serve') {
        // Dev-specific config
        return { ... };
      } else {
        // Build-specific config
        return { ... };
      }
    });
    ```
* **Aliases:** Define path aliases (`resolve.alias`) to simplify imports for common directories (e.g., `@` for `src`).
* **Root Directory:** Define the project `root` if it's not the directory containing the config file.

## Development Server (DevX)

* **Fast Startup:** Rely on Vite's native ESM approach for fast development server startup. Avoid patterns or plugins that force unnecessary bundling during development.
* **HMR:** Leverage Hot Module Replacement (HMR). Ensure framework plugins (`@vitejs/plugin-react`, `@vitejs/plugin-vue`) are configured correctly for optimal HMR performance. Write code that is HMR-friendly (e.g., isolating state where possible).
* **Dependency Pre-bundling:** Understand that Vite uses `esbuild` to pre-bundle dependencies on the first run or after dependency changes. This speeds up subsequent loads. Usually requires no configuration, but be aware of it. Clear the cache (`node_modules/.vite`) or run `vite --force` if encountering stale dependency issues.
* **Browser Cache:** Do *not* disable the browser cache during development, as Vite heavily relies on standard HTTP caching headers (like `304 Not Modified`) for optimal performance.
* **Server Options:** Configure `server` options as needed (e.g., `port`, `open` to automatically open the browser, `proxy` for API requests).

## Build Optimization (Production)

* **Rollup Under the Hood:** Understand that Vite uses Rollup for production builds, enabling a rich ecosystem of Rollup plugins and optimizations.
* **Minification:** Production builds automatically minify JavaScript (using esbuild by default, configurable) and CSS (using Lightning CSS if enabled, otherwise esbuild). Ensure `build.minify` is enabled (default for production).
* **Code Splitting:** Vite enables code splitting by default via Rollup, creating separate chunks for dynamic imports and shared vendor code. This improves loading performance.
* **Tree Shaking:** Relies on Rollup's tree shaking capabilities using ESM syntax. Ensure your code and dependencies use ESM properly to allow unused code removal. Check library `package.json` for `sideEffects` configuration.
* **CSS Code Splitting:** CSS is automatically code-split. CSS used by async JavaScript chunks is inlined into the chunk and loaded together. Common CSS is extracted. Configure `build.cssCodeSplit` if needed (default is `true`).
* **Asset Handling:** Configure `build.assetsInlineLimit` to control whether small assets are inlined as base64 URLs or emitted as separate files.
* **Manual Chunks (Advanced):** For fine-grained control over bundle splitting, use `build.rollupOptions.output.manualChunks`. Use this strategically to optimize caching (e.g., separating stable vendors from frequently changing app code).
* **Analyze Bundle:** Use plugins like `rollup-plugin-visualizer` to analyze the production bundle contents and identify opportunities for optimization.

## Plugins

* **Purpose:** Use Vite plugins to extend functionality (framework support, asset handling, custom transformations, integrations).
* **Official Plugins:** Prefer official `@vitejs/` plugins for core functionalities (React, Vue, Legacy browser support).
* **Plugin Order (`enforce`):** Understand and use the `enforce` property (`'pre'`, `'post'`) on plugins when the execution order relative to other plugins or Vite's core transformations matters.
* **Minimal Usage:** Only include plugins necessary for the project. Too many plugins can slow down both dev server startup and build times.
* **Audit Performance:** Be mindful of the performance impact of plugins, especially community plugins. Check if they perform heavy operations in hooks that run frequently or during server startup. Prefer plugins that leverage Vite's efficient hooks and avoid unnecessary work.

## Static Assets & CSS

* **`public/` Directory:** Place assets that need to be served as-is (without processing) and retain their filename (e.g., `robots.txt`, favicons) in the `public` directory. Reference them using root-relative paths (`/my-image.png`).
* **`src/assets/`:** Place assets that are referenced in code (JS/CSS) (e.g., images used in components, fonts referenced in CSS) under `src/assets` (or similar). These assets will be processed, bundled, and potentially hash-named by Vite/Rollup. Reference them using relative paths (`../assets/logo.png`).
* **CSS:** Vite provides first-class CSS support:
    * `@import` inlining and rebasing.
    * CSS Modules (`.module.css`).
    * CSS Preprocessors (Sass, Less, Stylus) - require installing the respective preprocessor.
    * PostCSS integration (automatically applies Autoprefixer). Configure `postcss.config.js` for custom PostCSS plugins.

## Environment Variables

* **`.env` Files:** Use `.env` files (`.env`, `.env.local`, `.env.[mode]`, `.env.[mode].local`) to manage environment variables. Vite uses `dotenv` and `dotenv-expand`.
* **`VITE_` Prefix:** Only environment variables prefixed with `VITE_` are exposed to client-side code via `import.meta.env`. This prevents accidental leakage of server-side secrets.
* **Security:** **NEVER** put sensitive secrets (API keys, database passwords) in variables prefixed with `VITE_`. These belong in server-side only variables (accessed via server-side code or build-time replacement) or secure secret management systems.
* **Type Safety (TypeScript):** Define types for `import.meta.env` in `src/vite-env.d.ts` for better intellisense and type checking.

## Server-Side Rendering (SSR)

* **Built-in Support:** Vite provides built-in, low-level APIs for Server-Side Rendering.
* **Frameworks:** For application development, prefer using higher-level SSR frameworks built on Vite (like SvelteKit, Nuxt 3, Astro, Quasar) which handle much of the complexity.
* **Configuration:** If implementing SSR manually, configure `build.ssr` and structure your code with separate client and server entry points.
* **External Dependencies:** Manage Node.js built-ins and dependencies correctly using `ssr.noExternal` or `ssr.external` options if needed.
