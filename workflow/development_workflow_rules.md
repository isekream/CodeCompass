# Development Workflow Rules

## Version Control

- Follow the agreed-upon branching strategy (e.g., Git Flow, GitHub Flow, Trunk-Based Development).
- Write meaningful commit messages that follow the Conventional Commits specification:
  - Format: `<type>(<scope>): <description>`
  - Types: feat, fix, docs, style, refactor, test, chore
  - Keep subject line under 50 characters
  - Add detailed explanation in commit body when necessary
- Create focused commits that represent logical changes.
- Regularly pull and rebase from the main branch to minimize merge conflicts.
- Use Pull Requests/Merge Requests for code review before merging to main branches.
- Write descriptive PR titles and descriptions that explain the purpose of the change.
- Link PRs to relevant issues in the issue tracker.

## Code Review

- Review code for functionality, readability, maintainability, and adherence to project standards.
- Use automated tools (linters, formatters) before submitting code for review.
- Be specific, constructive, and respectful in code review comments.
- Address all review comments before merging.
- Look for security vulnerabilities, performance issues, and edge cases during reviews.
- Verify test coverage for new code and bug fixes.

## Dependency Management

- Select dependencies based on these criteria:
  - Active maintenance and community support
  - Compatibility with project's license requirements
  - No known critical security vulnerabilities
  - Appropriate size and scope for the required functionality
- Pin dependencies to specific versions to ensure reproducible builds.
- Document the purpose and usage of key dependencies.
- Regularly update dependencies to incorporate security fixes and improvements.
- Run vulnerability scans (e.g., npm audit, pip-audit) regularly to identify security issues.
- Review major dependency updates carefully for breaking changes.

## CI/CD

- All code changes should pass CI checks before merging.
- Automate tests, linting, formatting, and security scanning in the CI pipeline.
- Maintain a fast CI pipeline that provides quick feedback.
- Use automated deployments for predictable and repeatable releases.
- Implement appropriate deployment environments (dev, staging, production).
- Include appropriate manual approval gates for critical environments.
- Monitor deployments and be prepared to roll back if issues are detected.

## Task Management

- Keep the issue tracker up to date with current status.
- Write clear, detailed issue descriptions with acceptance criteria.
- Break down large tasks into smaller, manageable issues.
- Use appropriate labels, milestones, and assignees to organize work.
- Follow the project's definition of "done" for completing tasks (e.g., code review, testing, documentation).
- Regularly triage and prioritize the backlog.

## Documentation

- Maintain up-to-date documentation for APIs, configurations, and important processes.
- Document architectural decisions using Architecture Decision Records (ADRs).
- Keep README files current with setup instructions, prerequisites, and basic usage.
- Document known issues and limitations.
- Update documentation alongside code changes.

## Communication

- Use the agreed-upon communication channels for different types of discussions.
- Document important decisions and discussions.
- Be clear, concise, and respectful in all communications.
- Provide context when asking questions or discussing issues.
- Share knowledge and insights with the team regularly.
