# React Rules

## Core Principles

- Components and Hooks must be pure functions regarding their props and state.
- Call Hooks only at the top level of functional components or other custom Hooks.
- Follow the Airbnb React/JSX Style Guide for component structure, naming, and JSX formatting.
- Use functional components with Hooks rather than class components for new code.
- Create small, focused components responsible for one part of the UI or one functionality.

## State Management

- Avoid prop drilling; use Context API or state management libraries (Redux, Zustand) for global/shared state.
- Do not modify state directly; always use the `setState` function (or the updater function from `useState`).
- Keep state as local as possible; lift state up only when necessary.
- Consider using reducers (`useReducer`) for complex state logic involving multiple sub-values or when the next state depends on the previous state.
- Extract reusable stateful logic into custom Hooks (named `useSomething`).

## Performance Optimization

- Use stable identifiers (not array indices) as `key` props for lists.
- Use `React.memo`, `useMemo`, and `useCallback` judiciously to optimize performance by preventing unnecessary re-renders, but avoid premature optimization.
- Use lazy loading (`React.lazy` and `Suspense`) for code splitting and optimizing initial load times.
- Memoize expensive calculations with `useMemo`.
- Use the React DevTools Profiler to identify and fix performance bottlenecks.

## Security

- Avoid `dangerouslySetInnerHTML`. If absolutely necessary, ensure the HTML content is rigorously sanitized first (e.g., using DOMPurify).
- Never pass user-supplied data directly to inline event handlers or style attributes.
- Sanitize all props that might contain HTML or user-generated content.

## Component Structure

- Use named exports for components.
- Co-locate related files (component, styles, tests) in the same directory.
- Organize props using destructuring in the function parameters.
- Define default prop values using default parameters.
- Use a consistent pattern for conditional rendering.

## Styling

- Prefer CSS-in-JS solutions or component-specific CSS modules to avoid global CSS conflicts.
- Use a consistent styling approach throughout the application.
- Follow responsive design principles, with mobile-first development.
- Use CSS variables for consistent theming and dark mode support.

## Testing

- Write tests for components using React Testing Library, focusing on user behavior rather than implementation details.
- Test both success and error states.
- Test common user interactions and edge cases.
- Mock external dependencies appropriately.
- Ensure accessibility in your tests by using screen queries from React Testing Library.
