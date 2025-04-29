# Tailwind CSS Rules

## Core Principles

* **Utility-First:** Embrace the utility-first approach. Style elements by applying existing, single-purpose utility classes directly in your HTML/markup. Avoid writing custom CSS classes for simple styling tasks.
* **Consistency:** Rely on Tailwind's default design system (spacing, colors, typography) or your customized theme to ensure visual consistency across the application. Avoid arbitrary "magic numbers" using bracket notation (`p-[13px]`) unless absolutely necessary for exceptions.
* **Maintainability:** While utility classes can make HTML verbose, focus on component abstraction in your frontend framework (React, Vue, etc.) or templating engine to manage complexity and improve maintainability.

## Configuration (`tailwind.config.js`)

* **Customization:** Use `tailwind.config.js` to customize and extend Tailwind's default theme. Define project-specific colors, fonts, spacing, breakpoints, etc., under the `theme.extend` key to add to defaults rather than replacing them entirely, unless intended.
* **Design Tokens:** Treat the `theme` section of the config file as the source of truth for your project's design tokens. Reference these tokens via utility classes.
* **Content Configuration:** Accurately configure the `content` property in `tailwind.config.js` to list all files containing Tailwind classes (HTML, JS, JSX, TSX, Blade, etc.). This is crucial for PurgeCSS/JIT engine to remove unused styles effectively.
* **Plugins:** Use official Tailwind plugins (e.g., `@tailwindcss/typography`, `@tailwindcss/forms`, `@tailwindcss/aspect-ratio`) or well-vetted community plugins to extend functionality where needed.

## HTML Markup & Class Usage

* **Readability:** While class lists can become long, improve readability by:
    * Using code formatters with Tailwind plugins (like `prettier-plugin-tailwindcss`) to automatically sort classes consistently.
    * Consider breaking long class strings onto multiple lines within the HTML attribute.
    * Using conditional class libraries (like `clsx` or `classnames`) in JavaScript frameworks to manage dynamic classes cleanly.
* **Semantic HTML:** Use semantic HTML elements (`<nav>`, `<article>`, `<button>`, etc.) even when styling heavily with utility classes.
* **Avoid Premature Abstraction:** Don't create custom CSS classes or use `@apply` too early. Stick with utility classes directly in the markup first. Extract components or use `@apply` only when a pattern is clearly repeated and stable.
* **Group Related Utilities:** Consider grouping related utilities together visually in the class list (e.g., layout, spacing, typography, colors) for better scanning, especially if not using an automatic sorter.

## Components & `@apply`

* **Component Abstraction:** For repeated UI patterns (buttons, cards, forms), extract them into reusable components within your frontend framework or templating language. Apply Tailwind utilities *within* these component definitions. This is the preferred way to manage repetition.
* **Use `@apply` Sparingly:** The `@apply` directive allows you to extract utility patterns into custom CSS classes. Use it cautiously:
    * **Good Use Cases:** Small, highly reusable UI elements (e.g., buttons, form inputs, badges) where the utility string becomes genuinely unmanageable *and* framework component abstraction isn't sufficient. Applying base styles to plain HTML elements (like Markdown output via `@tailwindcss/typography`).
    * **Bad Use Cases:** Abstracting away every combination of utilities. Recreating traditional CSS component classes for large page sections. Overusing `@apply` can negate many of Tailwind's benefits (like avoiding naming collisions and easier refactoring).
* **Layer Directive:** When using `@apply`, place custom component classes within the `@layer components` directive in your main CSS file to control rule order and ensure utilities can still override them if needed.
    ```css
    @tailwind base;
    @tailwind components;
    @tailwind utilities;

    @layer components {
      .btn-primary {
        @apply py-2 px-4 bg-blue-500 text-white font-semibold rounded-lg shadow-md hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-blue-400 focus:ring-opacity-75;
      }
    }
    ```

## Responsiveness & States

* **Mobile-First:** Design using a mobile-first approach. Apply base styles for mobile and use responsive prefixes (`sm:`, `md:`, `lg:`, `xl:`, `2xl:`) to add or override styles for larger screens.
* **State Variants:** Use state variants (`hover:`, `focus:`, `active:`, `disabled:`, `group-hover:`, `focus-within:`, etc.) to style elements based on their interaction state. Apply these directly in the markup.
* **Dark Mode:** Use the `dark:` variant to apply styles conditionally when dark mode is enabled (configure `darkMode` strategy in `tailwind.config.js` - usually `class` or `media`).

## Performance Optimization

* **Purging:** Ensure unused styles are removed (purged) in production builds. Tailwind's JIT (Just-In-Time) engine does this automatically based on the `content` paths in `tailwind.config.js`. Verify your configuration is correct.
* **Minification:** Minify the final CSS output in production using tools like `cssnano` (often integrated into build processes or Tailwind CLI's `--minify` flag).
* **Compression:** Ensure the CSS file is served with Gzip or Brotli compression by the web server.
* **Small Bundle Size:** Properly configured Tailwind projects typically result in very small CSS bundles (<10-15kB gzipped) because only the generated CSS for utilities actually used is included. Avoid configuration or `@apply` practices that significantly increase bundle size unnecessarily.

## Maintainability

* **Component-Based Approach:** Heavily rely on framework components (React, Vue, Svelte, Blade, etc.) to encapsulate markup and associated utility classes. Changes are made within the component, not by searching through potentially verbose HTML.
* **Configuration as Source of Truth:** Use `tailwind.config.js` to manage design tokens (colors, spacing, etc.). Avoid hardcoding values directly in utility classes where a theme value exists or should exist.
* **Limit Custom CSS:** Minimize the amount of custom CSS written outside of Tailwind's utility/`@apply` system. If custom CSS is needed, keep it organized and well-documented.

## Common Pitfalls to Avoid

* **Overriding Utility Classes:** Avoid creating CSS that overrides Tailwind utility classes. This creates confusion and can lead to specificity wars.
* **Ignoring Tailwind's Design System:** Resist creating custom utilities or spacing that don't align with Tailwind's spacing scale or design system. Work with the system instead of against it.
* **Mixing Design Systems:** Avoid mixing multiple design systems (e.g., Bootstrap and Tailwind) in the same project. This creates inconsistency and bloat.
* **Using Too Many Arbitrary Values:** While the bracket notation (`text-[#FF00FF]`, `mt-[37px]`) is useful for exceptions, overusing it defeats the purpose of a design system and leads to inconsistency.
* **Neglecting the Config File:** Make configuration changes in the `tailwind.config.js` file rather than using arbitrary values or custom CSS for project-wide standards.

## Integration with JavaScript Frameworks

* **React/JSX:**
    * Use conditional classnames libraries like `clsx` or `classnames` for dynamic classes.
    * Create custom components for repeated UI patterns rather than repeating class strings.
    * Consider tools like Twin.macro for CSS-in-JS with Tailwind syntax.
* **Vue:**
    * Use `:class` binding with object and array syntax for conditional classes.
    * Leverage Vue components to encapsulate repeated UI patterns.
* **Angular:**
    * Use `ngClass` for conditional styling.
    * Create Angular components for reusable UI patterns.
* **Component Libraries:** When using UI component libraries (like Headless UI, Radix UI), apply Tailwind classes for styling rather than creating custom CSS.
