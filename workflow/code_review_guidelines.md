# Code Review Guidelines

## Purpose & Goals

* **Primary Goal:** Improve code quality, ensure adherence to standards, catch bugs and potential issues early, share knowledge across the team, foster collaboration, and ensure the proposed changes meet requirements.
* **Scope:** Code reviews apply to all substantive code changes (features, bug fixes, refactoring) intended to be merged into main development or release branches (e.g., `main`, `develop`). Trivial changes (e.g., typo fixes in documentation) might be exempt based on team agreement, but when in doubt, request a review.
* **Culture:** Foster a positive, constructive, and collaborative code review culture. Reviews are about improving the code, not criticizing the author. Assume good intent from both authors and reviewers. Be humble, respectful, and open to learning.

## Author Responsibilities (Before Requesting Review)

* **Self-Review:** Review your own code changes *first*. Read through the entire diff, check for obvious errors, typos, debugging code, commented-out code, and adherence to basic standards.
* **Clear Purpose:** Ensure the change (Pull Request/Merge Request - PR/MR) has a clear, single purpose. Avoid mixing unrelated changes (e.g., a feature implementation and a large unrelated refactoring) in the same PR/MR.
* **Small, Focused Changes:** Keep PRs/MRs small and focused. Large changes (> 400 lines of code is often cited as a limit for effective review) are difficult to review thoroughly and should be broken down into smaller, incremental PRs/MRs if possible.
* **Working Code:** Ensure the code builds successfully, all automated checks (linters, formatters, unit tests, integration tests) pass, and the core functionality works as expected according to requirements. Do not submit broken code for review.
* **Clear Description:** Write a clear and informative PR/MR description:
    * Explain *what* the change does and *why* it's needed (link to relevant issues/tickets).
    * Briefly describe the approach taken, especially for complex changes.
    * Provide context reviewers might need (e.g., decisions made, alternatives considered briefly).
    * Include instructions for testing or specific areas needing reviewer attention.
    * Add screenshots/GIFs for UI changes.
* **Suggest Reviewers:** Suggest appropriate reviewers (consider team members familiar with the code area, platform experts, or follow team rotation policies). Don't request too many reviewers unnecessarily.

## Reviewer Responsibilities

* **Understand Context:** Take time to understand the purpose and context of the change. Read the PR/MR description, linked issues, and potentially check out the branch for complex changes. Ask clarifying questions if the purpose or approach is unclear.
* **Timeliness:** Aim to provide feedback in a timely manner (e.g., within one business day if possible) to avoid blocking the author. If unable to review promptly, communicate this to the author or reassign the review.
* **Thoroughness:** Review the code carefully. Don't just skim. Aim for a review speed under ~500 lines of code per hour for effectiveness. Limit review sessions to ~60-90 minutes to maintain focus.
* **Constructive Feedback:** Provide feedback that is clear, specific, actionable, and respectful. Focus on the code, not the author. Explain *why* a change is suggested, referencing standards, potential issues, or alternative approaches. (See Feedback Style section).
* **Scope Focus:** Primarily focus the review on the changes introduced in the PR/MR. Avoid requesting large, unrelated refactorings unless the existing code directly prevents the current change from being implemented correctly or safely. Log suggestions for unrelated improvements as separate issues/tasks.
* **Prioritize:** Distinguish between essential changes required for approval (e.g., fixing bugs, security flaws, major logic errors) and suggestions/nits (e.g., minor style preferences, optional improvements). Use labels or prefixes (e.g., "[Blocking]", "[Suggestion]", "Nit:") if helpful.
* **Approve When Ready:** Approve the PR/MR when requirements are met, critical issues are resolved, and the code quality is acceptable according to team standards, even if minor nits remain (these can often be addressed in follow-up changes). Don't block merges indefinitely for minor preferences if the code is functional, tested, and safe.

## Process

1.  **Author:** Creates PR/MR, performs self-review, writes description, requests reviewers.
2.  **CI:** Automated checks (build, lint, format, tests) run. These MUST pass before review proceeds.
3.  **Reviewer(s):** Review the code and description, provide feedback via comments.
4.  **Author:** Responds to comments, discusses feedback, pushes updates based on feedback.
5.  **Iteration:** Steps 3 & 4 repeat as needed until reviewers are satisfied.
6.  **Approval:** Reviewer(s) approve the changes.
7.  **Merge:** Author (or designated merger) merges the PR/MR into the target branch (typically using a squash merge or merge commit based on team policy). Delete the feature branch after merging.

## Feedback Style

* **Be Kind & Respectful:** Always assume positive intent. Phrase feedback constructively.
* **Explain Reasoning:** Justify suggestions. Explain *why* a change is needed or preferred (e.g., "This could cause a null pointer if X happens", "This pattern is more performant because...", "Our style guide suggests Y for consistency").
* **Ask Questions:** Instead of making demands ("Change this"), ask questions to understand the author's reasoning or gently guide them ("What do you think about renaming this variable to X for clarity?", "Have you considered approach Y? It might simplify the logic here.").
* **Offer Alternatives:** When pointing out problems, suggest concrete alternatives or improvements where possible.
* **Balance Feedback:** Acknowledge good work and positive aspects alongside constructive criticism.
* **Use "I" Statements:** Frame subjective feedback using "I" statements ("I find this hard to follow", "I suggest...") rather than absolute statements ("This is unreadable", "You must...").
* **Offline Discussion:** If a discussion thread becomes too long or contentious, take it offline (chat, call) to resolve quickly, then summarize the resolution in the PR/MR comments.

## Code Review Checklist Areas (What to Look For)

This is not exhaustive, but reviewers should consider:

* **Functionality:** Does the code correctly implement the requirements/fix the bug? Does it handle edge cases and potential errors gracefully?
* **Design & Architecture:** Does the change fit well within the existing architecture? Does it follow established design patterns and principles (e.g., SOLID, DRY)? Is the solution appropriately simple or overly complex?
* **Readability & Maintainability:** Is the code clear, understandable, and well-organized? Are names meaningful? Are functions/classes focused?
* **Security:** Does the change introduce any potential security vulnerabilities (e.g., injection flaws, insecure handling of credentials, improper access control, XSS)? Are inputs validated? Is output encoded?
* **Performance:** Does the change introduce any obvious performance issues (e.g., inefficient loops, unnecessary database queries, blocking operations)? (Note: Deep performance analysis is usually outside scope, but obvious issues should be flagged).
* **Testing:** Are there sufficient automated tests (unit, integration) for the changes? Do the tests cover important logic paths and edge cases? Do existing tests still pass?
* **Documentation:** Is necessary documentation (code comments, READMEs, API docs) updated or added? Are comments clear and accurate?
* **Standards & Style:** Does the code adhere to project coding standards, style guides, and linting/formatting rules? (Automated tools should catch most of this).
* **Simplicity:** Is there a simpler way to achieve the same result?
