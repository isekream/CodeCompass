# CSS/SCSS Rules

## Formatting & Readability

* **Consistency:** Adhere to a consistent code style throughout the project (indentation, spacing, quotes, capitalization). If a project-specific style guide exists, follow it; otherwise, establish one.
* **Indentation:** Use 2 spaces for indentation. Do not use tabs.
* **Property Declaration:** Place each property declaration on its own line for readability.
* **Whitespace:** Use a single blank line between rule sets. Use spaces after colons (`:`) in declarations and around combinators (`>`, `+`, `~`).
* **Quotes:** Use double quotes (`"`) consistently, for example, around font names with spaces or attribute selectors.
* **Comments:** Use CSS comments (`/* ... */`) to explain non-obvious code, complex selectors, magic numbers, or workarounds. Organize large stylesheets with block comments for major sections. Keep comments up-to-date.

## Selectors & Specificity

* **Prefer Classes:** Prefer using classes (`.my-class`) over IDs (`#my-id`) or tag selectors (`div`) for styling components. Classes are reusable and have lower specificity than IDs.
* **Low Specificity:** Keep selectors as short and specific as necessary. Avoid overly long selector chains (e.g., `.nav ul li a`). High specificity makes overriding styles difficult.
* **Avoid `!important`:** Only use `!important` as a last resort to override external styles or inline styles you cannot control. Overuse of `!important` makes debugging and maintenance significantly harder.
* **Avoid Qualified Selectors:** Avoid qualifying class or ID selectors with tag names (e.g., use `.nav-link` instead of `a.nav-link`), unless necessary for specific styling contexts.
* **Attribute Selectors:** Use attribute selectors (`[type="text"]`) where appropriate, but be mindful of performance if used excessively.

## Naming Conventions (BEM Recommended)

* **Use a Convention:** Adopt a consistent naming convention like BEM (Block, Element, Modifier) or a similar methodology (e.g., SUIT CSS, SMACSS principles) to create meaningful, structured, and collision-resistant class names.
* **BEM (Block, Element, Modifier):**
    * **Block:** Represents a standalone component (e.g., `.card`, `.menu`, `.button`). Use kebab-case (e.g., `.main-nav`).
    * **Element:** Represents a part of a block, semantically tied to it (e.g., `.card__title`, `.menu__item`, `.button__icon`). Separated from the block by double underscores (`__`). Use kebab-case. Elements should not be used outside their block.
    * **Modifier:** Represents a different state or version of a block or element (e.g., `.card--dark`, `.button--primary`, `.menu__item--active`). Separated from the block/element by double hyphens (`--`). Use kebab-case. Modifiers should always be used *in addition* to the base block or element class (e.g., `<button class="button button--primary">`).
* **Readability:** Choose names that are descriptive and communicate the purpose or structure of the element.

## Organization & Structure

* **Modular CSS:** Design CSS with modularity in mind. Create self-contained components that can be reused across the project without unintended side effects.
* **File Structure (SCSS):** Organize SCSS files into logical folders and use a main file (e.g., `main.scss`) to `@import` or `@use` partials (`_*.scss`). Common structures include:
    * `abstracts/` or `utils/`: Variables, functions, mixins.
    * `base/`: Reset/normalize, base element styles (body, typography).
    * `components/`: Styles for individual UI components (buttons, cards, modals).
    * `layout/`: Styles for major layout sections (header, footer, grid).
    * `pages/`: Page-specific styles (use sparingly).
    * `vendors/`: Third-party CSS.
* **Avoid Deep Nesting (SCSS):** Limit nesting depth (e.g., to 2-3 levels) in SCSS. Deep nesting increases specificity unnecessarily and makes the compiled CSS harder to read and potentially less performant. Use nesting primarily for pseudo-classes/elements and modifiers related to the parent selector.
* **Separate Styling from Layout:** Distinguish between rules that define internal styling (padding, color, font) and rules that define layout (position, margin, width). Components should ideally define their own styling, while parent containers manage their layout.

## SCSS Specifics (if using Sass/SCSS)

* **Use `@use` over `@import`:** Prefer `@use` for importing partials, as it provides namespacing and better dependency management compared to the older `@import` rule (which will eventually be deprecated).
* **Variables:** Use SCSS variables (`$variable-name`) for reusable values like colors, font sizes, spacing units, etc. Define variables in dedicated files (e.g., `_variables.scss`). Use descriptive names.
* **Mixins:** Use mixins (`@mixin`) for reusable blocks of styles, especially those requiring arguments (e.g., generating vendor prefixes, complex background patterns). Avoid using mixins for simple property-value pairs where a variable would suffice.
* **Functions:** Use functions (`@function`) for computing values (e.g., converting pixels to rems, calculating shades of a color).
* **Placeholders (`%`):** Use placeholder selectors (`%placeholder-name`) with `@extend` for sharing common styles *without* outputting the placeholder ruleset itself unless extended. Use `@extend` judiciously, primarily with placeholders, to avoid generating overly complex selectors. Be aware of potential side effects of extending nested selectors.
* **Nesting:** Use nesting logically (see Organization & Structure section). Use the parent selector (`&`) for pseudo-classes, pseudo-elements, and modifiers (e.g., `&:hover`, `&::before`, `&--modifier`).

## Performance

* **Minimize File Size:**
    * Remove unused CSS (use tools like PurgeCSS, UnCSS).
    * Minify CSS files in production builds (remove whitespace, comments).
    * Use CSS shorthand properties (`margin: 10px 20px;` instead of separate `margin-top`, etc.).
* **Reduce Complexity:** Avoid overly complex selectors that require significant browser computation (e.g., deep descendant selectors, complex universal selectors).
* **Optimize Animations/Transitions:**
    * Prefer animating `transform` and `opacity` as they are typically hardware-accelerated and less likely to cause layout recalculations (reflow).
    * Avoid animating properties like `width`, `height`, `margin`, `padding`, `top`, `left` which often trigger reflow/repaint.
    * Use `will-change` property sparingly and only when needed to hint to the browser about upcoming transformations.
* **Critical CSS:** For optimal perceived performance, consider identifying and inlining critical CSS (styles needed for above-the-fold content) in the HTML `<head>` and loading the rest asynchronously.
* **Font Loading:** Use efficient font loading strategies (`font-display: swap;`). Limit the number of custom web fonts used.
* **Compression:** Ensure CSS files are served with Gzip or Brotli compression from the web server.

## Accessibility

* **Color Contrast:** Ensure sufficient color contrast between text and background according to WCAG guidelines.
* **Focus Styles:** Provide clear visual `:focus` styles for interactive elements (links, buttons, form controls) for keyboard navigation users. Do not disable outlines (`outline: none;`) without providing an alternative focus indicator.
* **Relative Units:** Consider using relative units (`rem`, `em`) for font sizes and spacing to allow users to scale text according to their preferences.
* **Hide Content Appropriately:** Use appropriate techniques to visually hide content while keeping it accessible to screen readers if necessary (e.g., using a dedicated `.sr-only` class) instead of `display: none;` or `visibility: hidden;`.

## Responsive Design

* **Mobile-First Approach:** Start with styles for smaller viewports as the base, then use media queries to progressively enhance the design for larger screens. This typically results in more maintainable and efficient code.
* **Breakpoints:** Define standard breakpoints based on common device sizes or, preferably, on natural points where the design needs adjustment. Use variables for breakpoints in SCSS.
* **Fluid Layouts:** Use percentage-based widths and flexible grid systems rather than fixed pixel widths for major layout containers. Consider CSS Grid and Flexbox for complex layouts.
* **Media Queries:** Group media queries logically. Either place them within each component file (if using a component-based architecture) or collect them in dedicated files for major breakpoints.
* **Viewport Meta Tag:** Ensure the HTML includes the viewport meta tag: `<meta name="viewport" content="width=device-width, initial-scale=1.0">`.

## CSS Custom Properties (Variables)

* **Define at Root:** Define global custom properties at the `:root` level for project-wide access.
* **Naming Convention:** Follow a consistent naming convention for custom properties, typically using kebab-case with meaningful prefixes (e.g., `--color-primary`, `--spacing-unit`).
* **Fallbacks:** Always provide fallbacks for custom properties for browser compatibility: `color: #1a2b3c; color: var(--color-primary, #1a2b3c);`.
* **Scope:** Take advantage of the cascading nature of custom properties by redefining them in more specific contexts where values need to change.
* **Dynamic Values:** Use custom properties for values that might change dynamically (via JavaScript or media queries).

## Modern CSS Best Practices

* **Flexbox & Grid:** Use CSS Flexbox for one-dimensional layouts (rows or columns) and CSS Grid for two-dimensional layouts. Avoid older techniques like floats for layout.
* **Feature Queries:** Use `@supports` to provide progressive enhancement for newer CSS features with appropriate fallbacks.
* **Logical Properties:** Consider using logical properties (`margin-block-start`, `padding-inline-end`) for better internationalization support in multilingual applications.
* **Clamp & Calc:** Use `clamp()`, `min()`, `max()`, and `calc()` functions for responsive typography and fluid sizing rather than multiple media queries.
* **Container Queries:** Use container queries when available for component-level responsive design based on the container's size rather than just the viewport.
