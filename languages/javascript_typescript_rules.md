# JavaScript/TypeScript Rules

## JavaScript Style

- Follow the Airbnb JavaScript Style Guide for general coding conventions.
- Use `const` by default; use `let` only for variables that need reassignment. Avoid `var`.
- Use single quotes for strings.
- Always use strict equality (`===`, `!==`) instead of abstract equality (`==`, `!=`).
- Use modern ES6+ features like arrow functions, destructuring, template literals, and modules where appropriate.
- Avoid common pitfalls: understand `this` binding (or use arrow functions), handle truthy/falsy values explicitly, avoid `for...in` for arrays.

## TypeScript Style and Type Safety

- Enable `strict` mode in `tsconfig.json` and adhere to all strict checks (`noImplicitAny`, `strictNullChecks`, etc.).
- Avoid using the `any` type. Use `unknown` for values with uncertain types and perform type checks/guards before use.
- Use `interface` for defining object shapes and contracts; use `type` for unions, intersections, or aliasing primitives/complex types.
- Use PascalCase for type names, interfaces, enums. Use camelCase for variables, functions, properties.
- Use `readonly` modifiers for properties that should not be reassigned after initialization.
- Annotate array types using `Type[]` syntax, not `Array<Type>`.
- Do not use the `I` prefix for interface names (e.g., use `User` not `IUser`).

## Performance (JS/TS)

- Maintain stable object shapes; avoid adding/deleting properties after object creation to aid V8's hidden classes and inline caching.
- Keep functions small and focused to potentially enable V8 inlining.
- Prefer numeric enums or constants over string comparisons in performance-critical code.
- Utilize efficient built-in data structures like `Map` and `Set` over plain objects or arrays for specific use cases (e.g., frequent lookups/additions/deletions).
- For TypeScript compilation, ensure `incremental: true` is set in `tsconfig.json` for faster rebuilds.
- Use `skipLibCheck: true` in `tsconfig.json` if not modifying library declaration files.
- Avoid overly complex or deeply nested generic types that can slow down the compiler.

## Security (JS/TS)

- Prevent XSS: Sanitize user input before rendering it into the DOM (use libraries like DOMPurify). Escape output appropriately. Implement a strict Content Security Policy (CSP).
- Prevent CSRF: Use the synchronizer token pattern or double submit cookie pattern (preferably signed/HMAC-based). Utilize the `SameSite` cookie attribute (`Lax` or `Strict`).
- Validate and sanitize all input from external sources (user input, API responses) on the server-side.
- Use secure methods for authentication (e.g., OAuth, JWT with proper handling) and authorization.
- Keep all dependencies (npm packages) updated and scan for vulnerabilities regularly.
