# API Versioning Rules

## Purpose & When to Version

* **Goal:** Manage changes to an API over time in a way that allows client applications to continue functioning reliably while the API evolves. Prevent breaking existing clients unexpectedly.
* **When to Introduce a New Version:** A new API version (typically implying a Major version change, e.g., v1 -> v2) is ONLY required when making **breaking changes** (backwards-incompatible changes).
* **Breaking Changes:** Examples include:
    * Removing endpoints, resources, or HTTP methods.
    * Renaming endpoints, resources, or fields.
    * Changing the data type of a field.
    * Adding new *required* request parameters or fields.
    * Removing fields from response payloads.
    * Changing authentication or authorization mechanisms significantly.
    * Changing error response formats or status codes for existing situations.
* **Non-Breaking Changes:** Changes that are generally backwards-compatible and DO NOT require a new major version:
    * Adding new endpoints or resources.
    * Adding new *optional* request parameters or fields.
    * Adding new fields to response payloads.
    * Adding new optional request headers.
    * Reordering fields in JSON responses.
    * Fixing bugs that align behavior with documented specs.

## Versioning Strategies

Choose **one** consistent strategy for versioning APIs across the organization or project portfolio.

1.  **URI Path Versioning (Recommended for most cases):**
    * Include the major version number directly in the URL path (e.g., `/api/v1/users`, `/api/v2/users`).
    * **Pros:** Explicit, highly visible, easy for clients to understand and use, leverages browser/HTTP caching naturally. Standard across many public APIs.
    * **Cons:** Violates the strict REST principle that a URI should identify a unique resource (the resource itself isn't versioned, the API contract is). Can lead to more significant code changes/routing configuration when introducing a new version.
    * **Implementation:** `[Route("api/v1/[controller]")]` (ASP.NET Core), `/v1/users` (Express), etc.

2.  **Query Parameter Versioning:**
    * Include the version as a query parameter (e.g., `/api/users?version=1`, `/api/users?api-version=2.1`).
    * **Pros:** Simple URL structure, potentially easy to default to the latest version if the parameter is omitted.
    * **Cons:** Can clutter URLs, less explicit than path versioning, potentially harder for some caching mechanisms, routing can be trickier.

3.  **Custom Header Versioning:**
    * Include the version in a custom HTTP request header (e.g., `Accepts-version: 1.0`, `X-API-Version: 2`).
    * **Pros:** Keeps URLs clean (adheres better to the unique resource URI principle).
    * **Cons:** Less discoverable for clients (version isn't visible in the URL), requires clients to be more sophisticated to set headers correctly, may bypass standard browser caching, harder to test directly in a browser.

4.  **Media Type Versioning (Content Negotiation):**
    * Include the version in the `Accept` header using custom media types (e.g., `Accept: application/vnd.myapi.v1+json`, `Accept: application/json; version=2.0`).
    * **Pros:** Considered the most "RESTful" approach by some purists, allows versioning individual resource representations.
    * **Cons:** Most complex to implement on both client and server. Can be confusing for developers. Difficult to test in a browser. Can complicate caching. Generally not recommended unless there's a specific need for resource representation versioning.

## Best Practices

* **Plan Early:** Decide on a versioning strategy during the initial API design phase. Don't add versioning as an afterthought.
* **Semantic Versioning (SemVer):** While API versions exposed to clients are often major versions (v1, v2), internally track API changes using SemVer (MAJOR.MINOR.PATCH) principles to communicate the nature of changes clearly.
    * MAJOR version for incompatible API changes.
    * MINOR version for adding functionality in a backward-compatible manner.
    * PATCH version for backward-compatible bug fixes.
* **Backward Compatibility:** Strive to maintain backward compatibility whenever possible to avoid forcing clients to upgrade. Use additive changes (new endpoints, optional fields) instead of breaking changes.
* **Documentation:**
    * Maintain comprehensive documentation for *each* supported API version.
    * Clearly indicate the current version and any deprecated versions.
    * Provide detailed changelogs between versions.
    * Offer clear migration guides for breaking changes.
* **Communication:** Communicate upcoming changes, version releases, and deprecation schedules clearly and proactively to API consumers (e.g., via developer portal announcements, email newsletters, release notes).
* **API Gateway:** Consider using an API Gateway to centralize version management, routing requests to different backend service versions based on the chosen versioning strategy.
* **Testing:** Implement automated tests (including contract tests) for each supported API version to ensure correctness and prevent regressions. Test backward compatibility explicitly.

## Handling Breaking Changes

* **Avoid If Possible:** Exhaust options to introduce changes additively before resorting to a breaking change.
* **New Major Version:** Breaking changes MUST result in a new major version identifier (e.g., increment from `/v1/` to `/v2/`).
* **Parallel Support:** Support both the old and the new version simultaneously for a defined transition period. Do not switch off the old version immediately upon releasing the new one.
* **Clear Migration Path:** Provide clear documentation and potentially tooling to help consumers migrate from the old version to the new version.

## Deprecation Policy

* **Define a Policy:** Establish and publish a clear API deprecation policy.
* **Notice Period:** Provide ample advance notice before retiring an API version (e.g., 6-12 months minimum is common, depending on the API's user base and criticality).
* **Communication:** Communicate the deprecation schedule clearly through multiple channels (documentation, headers, email).
* **Deprecation Headers:** Consider using HTTP headers like `Deprecation` (RFC 8594) or custom headers (e.g., `X-API-Warn-Deprecated`, `Sunset`) in responses from deprecated versions to warn clients programmatically, indicating the sunset date and linking to migration info.
* **Monitoring:** Monitor the usage of deprecated API versions to understand the migration progress before finally retiring the version.
* **Retirement:** After the deprecation period ends, disable the old API version. Ensure it returns clear error responses (e.g., `410 Gone` or `404 Not Found` with an informative message) indicating it's no longer available and pointing to the new version or documentation.
