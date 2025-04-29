# Testing Rules

## General Testing Principles

- Follow the FIRST principles for effective tests:
  - Fast: Tests should run quickly to encourage frequent execution
  - Independent/Isolated: Tests should not depend on each other or external state
  - Repeatable: Tests should produce consistent results regardless of environment
  - Self-Validating: Tests should clearly determine pass or fail without manual inspection
  - Timely: Tests should be written alongside or immediately after production code
- Write tests at appropriate levels (unit, integration, end-to-end) based on the testing pyramid.
- Maintain high test coverage for critical business logic and complex code paths.
- Test both the happy path and edge cases/error scenarios.
- Keep tests simple, readable, and maintainable.

## Unit Testing

- Test individual components in isolation.
- Mock or stub external dependencies to focus on the unit being tested.
- Structure unit tests using the Arrange-Act-Assert (AAA) pattern:
  - Arrange: Set up the test preconditions and inputs
  - Act: Execute the code being tested
  - Assert: Verify the results match expectations
- Keep unit tests fast and focused.
- Use descriptive test names that indicate what is being tested, the expected outcome, and the scenario.
- Avoid using actual databases, file systems, or network resources in unit tests.

## Integration Testing

- Test the interaction between components or services.
- Use appropriate test doubles for external services when necessary.
- Implement database integration tests with rollback capabilities or isolated test databases.
- Test API contracts and boundaries between systems.
- Ensure that integration tests cover the complete flow of data through multiple components.

## End-to-End Testing

- Test complete user workflows from start to finish.
- Focus on critical user journeys and business processes.
- Keep the number of E2E tests manageable due to their typically slower execution.
- Use stable selectors (data attributes, IDs) for UI testing rather than relying on CSS classes or XPaths that may change.
- Implement appropriate waiting mechanisms for asynchronous operations.

## Test-Driven Development (TDD)

- When practicing TDD, follow the Red-Green-Refactor cycle:
  - Red: Write a failing test that defines the expected behavior
  - Green: Implement the minimal code necessary to pass the test
  - Refactor: Clean up the code while keeping tests passing
- Focus on writing testable code with clear interfaces and dependencies.
- Use TDD to drive design decisions and clarify requirements.

## Behavior-Driven Development (BDD)

- Express test scenarios in business language using Given-When-Then format:
  - Given: The initial context or setup
  - When: The action or event being tested
  - Then: The expected outcome or result
- Connect BDD scenarios to test implementations that validate the described behavior.
- Use BDD frameworks appropriate for your language/stack (Cucumber, SpecFlow, Jest-Cucumber, etc.).

## Mocking and Test Doubles

- Use appropriate test doubles for different scenarios:
  - Stubs: Provide canned answers for method calls
  - Mocks: Record and verify interactions
  - Fakes: Working implementations with shortcuts unsuitable for production
  - Spies: Record calls without affecting behavior
- Mock external dependencies, not the system under test.
- Keep mocks simple and maintainable to prevent brittle tests.
- Avoid excessive mocking that could hide real issues or make tests less valuable.

## Testing Best Practices

- Treat test code with the same care as production code.
- Refactor tests when they become complex or difficult to maintain.
- Don't test implementation details; focus on behavior and outcomes.
- Avoid non-deterministic tests (flaky tests) that sometimes pass and sometimes fail.
- Use appropriate assertions and matchers for clear test failures.
- Implement test fixtures and factories to reduce test setup duplication.
- Run tests automatically in the CI/CD pipeline.
- Address failing tests immediately - a failing test indicates a real problem.
