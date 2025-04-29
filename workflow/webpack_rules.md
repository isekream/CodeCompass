# Webpack Rules

## Configuration (`webpack.config.js`)

* **Mode:** Explicitly set the `mode` configuration option to either `'development'` or `'production'` to enable appropriate built-in optimizations for each environment. Avoid relying on the default (which is 'production') or `'none'`.
* **Entry Points:** Define clear entry points (`entry` option). For single-page applications (SPAs), this is typically one main entry file (e.g., `./src/index.js`). For multi-page applications (MPAs), define multiple entry points.
* **Output:** Configure the `output` section clearly:
    * `filename`: Use placeholders like `[name].bundle.js` for single entry points or multiple entry points. Use `[name].[contenthash].js` for production builds to enable long-term caching.
    * `path`: Specify the absolute output directory (e.g., `path.resolve(__dirname, 'dist')`).
    * `publicPath`: Define the public URL path of the output directory when referenced in a browser (e.g., `/`, `/assets/`, or CDN path). Important for assets and code splitting.
    * `clean: true`: Use `output.clean: true` (Webpack 5+) to automatically clean the output directory before each build, replacing `clean-webpack-plugin`.
* **Separate Configs:** Maintain separate Webpack configuration files for development (`webpack.dev.js`) and production (`webpack.prod.js`). Use `webpack-merge` to share common configuration (`webpack.common.js`).
* **Environment Variables:** Use environment variables (passed via CLI or system) to control build variations within configuration files (e.g., checking `process.env.NODE_ENV`).
* **TypeScript Config (Optional):** If using TypeScript heavily, consider writing the Webpack config in TypeScript (`webpack.config.ts`) using `ts-node`.

## Loaders

* **Purpose:** Use loaders (`module.rules`) to preprocess non-JavaScript files (e.g., CSS, SCSS, images, fonts, TypeScript, Babel) before they are added to the dependency graph.
* **Specificity:** Apply loaders only to the necessary files using the `test` property (RegExp). Be as specific as possible.
* **Exclusion:** Use the `exclude` property (e.g., `exclude: /node_modules/`) to prevent loaders (especially transpilers like Babel) from processing files in `node_modules`, significantly speeding up builds. Use `include` to explicitly specify directories if preferred.
* **Chaining:** Loaders are applied in reverse order (bottom-to-top or right-to-left). Ensure the order is correct for chained operations (e.g., `sass-loader` -> `css-loader` -> `style-loader`).
* **Minimal Loaders:** Use only the loaders essential for the project.

## Plugins

* **Purpose:** Plugins hook into the Webpack build lifecycle to perform a wide range of tasks that loaders cannot (e.g., bundle optimization, asset management, environment variable injection).
* **Essential Plugins (Examples):**
    * `HtmlWebpackPlugin`: Simplifies creation of HTML files to serve your bundles. Useful for injecting bundles with hashed filenames.
    * `MiniCssExtractPlugin`: Extracts CSS into separate files for production builds instead of injecting them via `<style>` tags (which `style-loader` does). Essential for CSS caching.
    * `DefinePlugin`: Allows creating global constants configurable at compile time (e.g., setting `process.env.NODE_ENV`).
    * `CopyWebpackPlugin`: Copies individual files or entire directories to the build directory.
* **Instantiation:** Remember to instantiate plugins using `new PluginName(...)` in the `plugins` array.

## Performance Optimization

* **Stay Updated:** Keep Webpack, loaders, and plugins updated to their latest stable versions, as performance improvements are frequently made.
* **Measure:** Use tools like `webpack-bundle-analyzer` to visualize bundle content and identify large dependencies or duplication. Use `--profile` and `--json` flags with the Webpack build command and tools like `analyse` or `statoscope` to inspect build performance details.
* **Persistent Caching:** Enable Webpack 5's persistent filesystem cache (`cache: { type: 'filesystem' }`) to significantly speed up subsequent builds. Clear cache on dependency updates (e.g., via `postinstall` script).
* **Parallelism:** Use tools like `thread-loader` (use with caution, overhead can sometimes negate benefits) or parallel execution features in plugins (like `TerserWebpackPlugin`) to leverage multiple CPU cores.
* **Loader Optimization:**
    * Apply loaders only to necessary files (see Loaders section).
    * Use `babel-loader`'s `cacheDirectory: true` option.
    * For TypeScript, use `ts-loader` with `transpileOnly: true` and perform type checking separately using `fork-ts-checker-webpack-plugin`.
* **Resolver Configuration:** Minimize items in `resolve.extensions` and `resolve.modules`. Consider setting `resolve.symlinks: false` if not using symlinks.
* **Minimize Entry Chunk:** Keep the entry chunk (containing the webpack runtime) small, especially if using hashes in filenames, by using `optimization.runtimeChunk: 'single'`.

## Code Splitting & Tree Shaking

* **Code Splitting:** Break down large bundles into smaller chunks that can be loaded on demand or in parallel. Strategies:
    * **Multiple Entry Points:** Simple but can lead to duplication.
    * **`optimization.splitChunks`:** Automatically split chunks based on shared modules between entry points or dynamic imports. Configure `cacheGroups` for fine-grained control (e.g., extracting vendor libraries).
    * **Dynamic `import()`:** Use the dynamic `import()` syntax within your application code to load modules lazily (e.g., for route-based splitting). Webpack automatically creates separate chunks.
* **Tree Shaking:** Eliminate unused code (dead code elimination).
    * Relies on ES Modules (`import`/`export` syntax). Ensure code is written using ESM.
    * Enabled by default in `production` mode.
    * Requires JavaScript minifier (like `TerserWebpackPlugin`, included by default in production mode) to remove the dead code.
    * Mark modules/packages as side-effect-free using the `sideEffects: false` property in `package.json` where applicable, to help Webpack prune more aggressively. Be careful with CSS imports or polyfills which often have side effects. List specific files with side effects if needed (e.g., `sideEffects: ["./src/styles.css"]`).

## Development vs. Production Builds

* **Separate Configurations:** Maintain distinct configurations for development and production (see Configuration section).
* **Development (`mode: 'development'`):**
    * **Focus:** Fast build/rebuild times, good debugging experience.
    * **Devtool:** Use fast source maps suitable for development (e.g., `eval-source-map`, `eval-cheap-module-source-map`).
    * **Dev Server:** Use `webpack-dev-server` for fast in-memory builds, live reloading, and Hot Module Replacement (HMR).
    * **Optimizations:** Disable production-only optimizations (like minification, aggressive code splitting).
* **Production (`mode: 'production'`):**
    * **Focus:** Small bundle sizes, optimized assets, performance.
    * **Devtool:** Use source maps suitable for production (e.g., `source-map` for high quality, or `hidden-source-map` if serving only to error tracking services), or disable them (`devtool: false`).
    * **Minification:** Ensure JavaScript (`TerserWebpackPlugin`) and CSS (`CssMinimizerWebpackPlugin`) minification is enabled (default in production mode).
    * **Optimization:** Leverage `optimization` options like `splitChunks`, `runtimeChunk`, `concatenateModules` (Module Concatenation/Scope Hoisting - enabled by default in production).
    * **Output Hashing:** Use `[contenthash]` in output filenames (`output.filename`, `output.chunkFilename`, `MiniCssExtractPlugin` filename) for long-term caching.

## General Principles

* **Understand the Basics:** Have a solid understanding of Webpack's core concepts: Entry, Output, Loaders, Plugins, Mode.
* **Keep It Simple:** Start with a simple configuration and add complexity only as needed. Avoid premature optimization.
* **Document:** Comment non-obvious parts of your Webpack configuration, especially complex loader chains, plugin configurations, or custom logic.
