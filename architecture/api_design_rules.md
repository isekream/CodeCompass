# API Design Rules

## REST API Guidelines

- APIs in this project follow REST principles.
- Use plural nouns for resource collections (e.g., `/users`, `/products`).
- Use standard HTTP methods appropriately:
  - GET for retrieving resources (never modify data)
  - POST for creating resources or complex operations
  - PUT for full updates of existing resources
  - PATCH for partial updates of existing resources
  - DELETE for removing resources
- Use proper HTTP status codes:
  - 200 OK for successful requests
  - 201 Created when a resource is successfully created
  - 400 Bad Request for invalid input
  - 401 Unauthorized for authentication issues
  - 403 Forbidden for authorization issues
  - 404 Not Found for non-existent resources
  - 409 Conflict for resource conflicts
  - 500 Internal Server Error for server errors
- Implement consistent error handling with standardized error response format.
- Use nesting for child resources (e.g., `/users/{id}/posts`), but limit nesting depth to avoid complexity.
- Include pagination for collections with potentially large result sets.
- Support filtering, sorting, and searching through query parameters.
- Implement proper HATEOAS links where appropriate.

## GraphQL Guidelines

- GraphQL schema types must use PascalCase.
- GraphQL schema fields must use camelCase.
- Keep resolver functions focused and consistent.
- Implement proper error handling and validation.
- Use fragments for reusable parts of queries.
- Consider implementing query complexity limits to prevent abuse.
- Design schemas with a focus on the client's data requirements.
- Use input types for mutations with complex input requirements.
- Separate mutation inputs from response types.
- Consider implementing DataLoader for efficient data fetching and avoiding N+1 problems.

## General API Best Practices

- Version your APIs (URI path, query parameter, or header).
- Document all endpoints with examples, parameter descriptions, and response formats.
- Implement rate limiting to prevent abuse.
- Support CORS configurations appropriately for web clients.
- Validate all input data on the server side.
- Return descriptive error messages that help clients understand the issue.
- Use consistent naming conventions across all endpoints.
- Minimize the number of requests needed to accomplish common tasks.
- Design idempotent operations where possible.
- Implement proper authentication and authorization.

## API Security

- Use HTTPS for all API communications.
- Implement proper authentication (OAuth2, JWT, API keys).
- Apply the principle of least privilege for API authorizations.
- Validate and sanitize all input data to prevent injection attacks.
- Implement proper CORS policies.
- Use security headers (Content-Security-Policy, X-Content-Type-Options, etc.).
- Protect against common API vulnerabilities (OWASP API Security Top 10).
- Do not expose sensitive data in error messages.
- Implement rate limiting and throttling to prevent DoS attacks.
- Use parameterized queries for database operations to prevent SQL injection.
