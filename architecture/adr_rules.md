# Architecture Decision Record (ADR) Rules

## Purpose & Scope

* **Goal:** Document *architecturally significant* decisions made during a project's lifecycle. An architecturally significant decision affects non-functional requirements (e.g., performance, security, scalability, maintainability), structure, dependencies, interfaces, or key technology choices.
* **Why:** Provide context ("why"), justification, and consequences for decisions to aid current team understanding, onboard new members, align stakeholders, avoid repeating debates, and preserve historical knowledge.
* **Scope:** Focus on *one* significant decision per ADR. Break down large decisions into multiple ADRs if necessary.

## When to Create an ADR

* Create an ADR when a decision:
    * Has a significant impact on the system's architecture (structure, non-functional characteristics, major dependencies).
    * Involves trade-offs between different options.
    * Requires choosing a specific technology, pattern, or standard (e.g., database choice, messaging queue, API design style, primary framework).
    * Addresses a key architectural requirement (ASR) or constraint.
    * Introduces a new convention or standard for the team/project.
    * Is likely to be questioned or revisited later.
* Start the ADR process *before or during* the decision-making process, not just as after-the-fact documentation.

## ADR Template (Based on common patterns like Michael Nygard's / MADR)

Use a consistent template for all ADRs. A recommended structure includes:

1.  **Title:** Short, descriptive title capturing the essence of the decision (e.g., "ADR-001: Use PostgreSQL for primary data storage"). Use a sequential numbering system (e.g., `NNN-short-title.md`).
2.  **Status:** The current state of the decision (e.g., `Proposed`, `Accepted`, `Rejected`, `Deprecated`, `Superseded by ADR-XXX`).
3.  **Date:** Date the decision reached its current status.
4.  **Context:** Describe the problem, the forces at play, requirements (functional and non-functional), constraints, and assumptions driving the need for this decision. Explain the "why" clearly.
5.  **Decision Drivers (Optional but Recommended):** List key factors influencing the decision (e.g., performance needs, cost constraints, team skills, maintainability goals).
6.  **Considered Options:** List the alternative solutions that were evaluated. Briefly describe each option.
7.  **Decision Outcome:** Clearly state the chosen option and the *rationale* behind choosing it over the alternatives. Explain how it addresses the context and drivers. Justify trade-offs. Include pros and cons of the chosen option relative to the project's context.
8.  **Consequences:** Describe the expected results and impacts (positive and negative) of the decision on the system, team, and future options. Include impacts on quality attributes, required follow-up actions, potential risks, and how they might be mitigated.

## Content & Style

* **Clarity & Conciseness:** Write clearly and directly. Avoid jargon where possible. Focus on the decision and its justification, not deep implementation details. Link to external design docs or prototypes for more detail if needed.
* **Objectivity:** Present rationale factually. Acknowledge trade-offs and downsides of the chosen option. Avoid overly positive "sales pitch" language.
* **Assertiveness:** State the decision clearly.
* **Format:** Use Markdown (`.md`) for easy versioning and readability.

## Lifecycle

* **Proposed:** An ADR is drafted and proposed for discussion/review.
* **Accepted:** The team agrees on the decision.
* **Rejected:** The proposed decision is not adopted.
* **Deprecated:** The decision was once accepted but is no longer relevant (perhaps the feature was removed).
* **Superseded:** A later ADR makes a new decision that replaces this one. The superseded ADR should link to the new ADR.
* **Immutability:** Treat *accepted* ADRs as immutable records of past decisions. Do not modify the original context, decision, or consequences after acceptance. If a decision needs changing, create a *new* ADR that supersedes the old one. (Note: Some teams prefer a "living document" approach, updating existing ADRs with timestamps; decide on a consistent team approach).

## Storage & Tooling

* **Version Control:** Store ADRs in the project's version control repository (e.g., Git), typically in a dedicated directory like `docs/adr/` or `docs/architecture/decisions/`. This ensures decisions are versioned alongside the code they affect.
* **Accessibility:** Ensure ADRs are easily discoverable and accessible to the entire team and relevant stakeholders. Link to the ADR directory from the main project README.
* **Tooling (Optional):** Consider using simple command-line tools (like `adr-tools`) or IDE extensions to help create, manage, and link ADRs consistently.

## Best Practices for Implementation

* **Start Small:** Begin with a few critical decisions rather than trying to document everything all at once.
* **Be Consistent:** Use the same template and level of detail across all ADRs in a project.
* **Involve the Team:** Make ADR writing a collaborative process, not just a task for architects or tech leads.
* **Review Process:** Establish a lightweight review process for proposed ADRs to gather feedback.
* **Link to Requirements:** Where applicable, reference specific requirements, user stories, or architectural drivers that influenced the decision.
* **Diagrams:** Include simple diagrams (e.g., using PlantUML, Mermaid, or C4 model notation) when they help clarify the decision context or consequences.
* **Cross-Reference:** Link between related ADRs when one decision influences or builds upon another.

## Sample ADR Structure

```markdown
# ADR-001: Use PostgreSQL for Primary Data Storage

## Status
Accepted

## Date
2023-07-15

## Context
Our application needs a relational database to store structured transaction data with ACID properties. We anticipate complex queries with joins across multiple entities and require strong data integrity guarantees.

## Decision Drivers
* Need for complex queries with joins
* ACID compliance requirement
* Team's existing skill set
* Long-term support and community
* Performance under expected load
* Cost considerations

## Considered Options
1. PostgreSQL
2. MySQL/MariaDB
3. Microsoft SQL Server
4. Oracle Database

## Decision Outcome
Chosen option: PostgreSQL

### Rationale
PostgreSQL offers the best combination of features for our needs:
* Strong ACID compliance and data integrity features
* Excellent support for complex queries and advanced SQL features
* Open-source with no licensing costs
* Most team members have prior experience
* Good performance characteristics for our read/write patterns
* Strong community support and active development

## Consequences
### Positive
* Simplified data model due to PostgreSQL's support for JSON/JSONB for semi-structured data
* No licensing costs
* Can leverage team's existing knowledge

### Negative
* Will need to set up and maintain our own database infrastructure (vs. a managed service)
* Some team members will need training on PostgreSQL-specific features

### Follow-up Actions
* Develop database migration strategy
* Set up automated backup solution
* Establish connection pooling configuration
```
