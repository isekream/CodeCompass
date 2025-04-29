# General Architecture Rules

## Architectural Patterns

- Clearly document and adhere to the chosen architectural pattern (e.g., Layered, MVC, Microservices, Hexagonal) for the project.
- Enforce the constraints inherent in the chosen pattern (e.g., in a Layered Architecture, layers should only interact with adjacent layers).
- Keep cross-cutting concerns (logging, authentication, error handling) consistent across the application.

## SOLID Principles

- Single Responsibility Principle (SRP): Classes and functions should have a single, well-defined purpose. Avoid "God Objects/Classes" that manage unrelated concerns.
- Open/Closed Principle (OCP): Software entities should be open for extension but closed for modification. Favor composition over inheritance.
- Liskov Substitution Principle (LSP): Subtypes must be substitutable for their base types without altering the correctness of the program.
- Interface Segregation Principle (ISP): Many client-specific interfaces are better than one general-purpose interface. Avoid interfaces with methods unused by implementing classes.
- Dependency Inversion Principle (DIP): High-level modules should not depend on low-level modules. Both should depend on abstractions.

## Project Structure

- Maintain a consistent and logical project structure that reflects the architectural decisions.
- Group related functionality together (feature modules or vertically-sliced components).
- Use consistent directory naming conventions throughout the project.
- Place configuration, utilities, and shared components in appropriate common directories.
- Clearly separate business logic from infrastructure concerns.

## Coupling and Cohesion

- Aim for high cohesion (related functionality grouped together) and low coupling (minimal dependencies between modules).
- Use dependency injection or service locator patterns to manage dependencies.
- Implement interfaces/abstractions at boundaries between modules or layers.
- Avoid tight coupling to external libraries or frameworks; use adapter patterns or abstraction layers.

## Scalability Considerations

- Design for horizontal scalability when appropriate (stateless services, distributed caching).
- Handle concurrency and race conditions appropriately.
- Consider load balancing and service discovery for distributed systems.
- Implement appropriate caching strategies for performance-critical components.
- Design for failure: implement circuit breakers, retries, and fallback mechanisms for external service calls.

## Design Patterns

- Use established design patterns appropriately to solve common problems, but avoid overengineering.
- Document the design patterns used and the rationale behind their selection.
- Common useful patterns include:
  - Factory Method/Abstract Factory for object creation
  - Strategy for encapsulating algorithms
  - Observer for event handling
  - Repository for data access abstraction
  - Command for operation encapsulation
- Avoid anti-patterns such as:
  - Singleton (except for very specific use cases)
  - Global state
  - "Magic" strings or numbers
  - Deep inheritance hierarchies
