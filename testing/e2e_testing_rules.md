# End-to-End (E2E) Testing Rules

## Purpose & Scope

* **Goal:** Verify complete user workflows from start to finish, simulating real user interactions across the entire application stack (frontend, backend, APIs, databases, integrations). Ensure all integrated components work together correctly.
* **Focus:** Test user journeys and critical business flows, not individual unit logic (that's for unit tests) or isolated component interactions (that's for integration/component tests).
* **Testing Pyramid:** E2E tests sit at the top of the testing pyramid. Have fewer E2E tests compared to unit and integration tests, as they are typically slower, more brittle, and more expensive to run and maintain. Focus E2E tests on the most critical paths.
* **Scope Definition:** Clearly define the scope of E2E tests: which user scenarios, browsers, devices, and environments are covered.

## Strategy

* **Identify Key Scenarios:** Prioritize E2E tests for critical user journeys and core business functionalities (e.g., user registration, login, main feature usage, checkout process).
* **User-Centric Approach:** Design tests from the end-user's perspective. Test what the user *does*, not how the system implements it internally.
* **Realistic Simulation:** Aim to simulate realistic user behavior and environments, but balance realism with test stability and speed.
* **Complement, Don't Replace:** E2E tests should complement unit and integration tests, not replace them. Push testing down the pyramid whenever possible. Test logic thoroughly with unit tests; test service interactions with integration or contract tests; use E2E tests to verify the integrated whole.

## Test Design

* **Test Independence:** Each E2E test case MUST be independent and self-contained. Tests should not rely on the state or outcome of previous tests. Each test should perform its own setup and teardown or start from a known clean state.
* **Avoid UI for Setup:** Do not use the UI to set up application state required for a test whenever possible. Use backend APIs, database seeding, or application state manipulation commands (like `cy.request()` in Cypress or APIRequestContext in Playwright) to set up preconditions faster and more reliably. Log in programmatically instead of via the UI.
* **Keep Tests Focused:** While covering a workflow, keep individual test cases focused on verifying a specific outcome or part of that workflow. Avoid overly long tests trying to assert too many things at once.
* **Page Object Model (POM) or Equivalent:** Use design patterns like POM to abstract page structure and interactions away from test logic.
    * Create classes representing pages or significant UI components.
    * Encapsulate element selectors and methods for interacting with those elements within the page object.
    * Test scripts interact with the page object's methods, not directly with selectors or WebDriver/Playwright/Cypress commands.
    * This improves maintainability (UI changes only require updating the page object) and readability.
* **Descriptive Names:** Use clear, descriptive names for test suites (`describe`/`context`) and test cases (`it`/`test`) that explain the scenario being tested.

## Selectors

* **Prefer Stable Selectors:** Use selectors that are resilient to UI changes. Prioritize:
    1.  Dedicated test attributes (e.g., `data-testid`, `data-cy`). These are explicitly for testing and unlikely to change with styling or structure updates.
    2.  User-facing attributes with semantic meaning (e.g., ARIA roles/labels, input labels, button text - use framework-provided locators like Playwright's `getByRole`, `getByLabel`, `getByText`).
    3.  Unique IDs (`#id`) - Use with caution if IDs are stable and meaningful.
    4.  Avoid relying on brittle selectors like CSS classes used for styling or complex XPath/CSS paths tied to DOM structure.
* **Centralize Selectors:** Store selectors within Page Objects or a dedicated constants file rather than scattering them throughout test scripts.

## Handling Asynchronicity & Waits

* **Avoid Static Waits:** Do NOT use fixed sleep/wait times (`cy.wait(5000)`, `await page.waitForTimeout(5000)`). They slow down tests and lead to flakiness (either waiting too long or not long enough).
* **Use Built-in Waits:** Rely on the testing framework's built-in automatic waiting capabilities. Cypress commands and Playwright auto-waiting actions typically wait for elements to be visible, enabled, and actionable before interacting.
* **Explicit Waits (When Necessary):** Use explicit waits for specific conditions when built-in waits are insufficient:
    * Waiting for specific network requests to complete (`cy.intercept().wait()`, `page.waitForResponse()`).
    * Waiting for specific text to appear/disappear or element state changes (`cy.contains(...)`, `expect(locator).toBeVisible()`).
    * Waiting for application-specific events or conditions.
* **Configure Timeouts:** Configure reasonable default command/action timeouts in the framework settings, balancing stability against test execution speed. Override timeouts per command only when necessary for specific slow operations.

## Test Data Management

* **Isolation:** Ensure tests use isolated data that doesn't conflict with parallel test runs.
* **Creation:** Create necessary test data programmatically via APIs or database seeding as part of the test setup (`beforeEach`, `beforeAll`, or fixture setup).
* **Cleanup:** Clean up test data created during the test execution in teardown hooks (`afterEach`, `afterAll`) or rely on resetting the environment.
* **Avoid Dependencies:** Minimize dependencies on pre-existing data in the test environment, making tests less brittle.
* **Sensitive Data:** Do not hardcode sensitive data (credentials, PII) in test scripts. Use environment variables or secure vaults managed by the test runner or CI/CD system.

## Execution & CI/CD

* **Automated Execution:** Integrate E2E tests into the CI/CD pipeline to run automatically (e.g., on merge to main, before deployment to staging/production).
* **Parallelization:** Run E2E tests in parallel (using framework features like Cypress Parallelization or Playwright sharding/workers) to significantly reduce overall execution time. Ensure tests are independent to allow this.
* **Headless Mode:** Run tests in headless browser mode in CI/CD for faster execution and lower resource usage.
* **Environment Strategy:** Run E2E tests against stable, production-like environments (e.g., staging). Running against ephemeral preview/review environments can also be valuable but might require more robust setup/teardown. Avoid running extensive E2E suites against local development setups in CI unless absolutely necessary.
* **Retry Strategy:** Implement a limited retry mechanism (e.g., retry failed tests once or twice) in CI to handle occasional infrastructure-related flakiness, but don't rely on retries to mask consistently unstable tests. Investigate and fix flaky tests.

## Reporting & Debugging

* **Clear Reports:** Configure test runners to generate clear, detailed reports including test status, duration, error messages, and stack traces.
* **Artifacts on Failure:** Capture diagnostic artifacts on test failure, such as:
    * Screenshots
    * Videos of the test execution
    * Browser console logs
    * Application/network logs (if possible)
* **Debugging Tools:** Utilize framework features for debugging (e.g., Cypress Time-Traveling Debugger, Playwright Trace Viewer).

## Framework Specifics (Cypress/Playwright Examples)

* **Cypress:**
    * Leverage command chaining and built-in assertions (`should`).
    * Use `cy.intercept()` to stub or wait for network requests.
    * Use `data-cy` attributes for preferred selectors.
    * Understand the Cypress command queue and retry-ability.
* **Playwright:**
    * Utilize auto-waiting actions.
    * Leverage built-in locators (`page.getByRole`, `page.getByTestId`, etc.).
    * Use APIRequestContext for API interactions within tests.
    * Utilize Tracing for detailed debugging.
    * Leverage features like parallel execution and sharding.
