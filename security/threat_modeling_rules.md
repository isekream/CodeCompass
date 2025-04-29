# Threat Modeling Rules

## Purpose & Goals

* **Goal:** Proactively identify, communicate, understand, and prioritize potential security threats, vulnerabilities, and attack vectors within the context of a system, application, or feature. The output informs design decisions and mitigation strategies.
* **Why:** To build more secure systems by design ("secure by design"), reduce the likelihood and impact of security breaches, make informed risk decisions, prioritize security efforts, and improve overall security posture.
* **Scope:** Define the scope clearly for each threat modeling exercise (e.g., a specific application, microservice, feature, user story, or infrastructure component).

## When to Threat Model

* **Early and Often:** Integrate threat modeling throughout the Software Development Lifecycle (SDLC), not just as a one-off activity.
    * **Design Phase:** Ideal time to identify architectural flaws and make impactful changes.
    * **Development Phase:** As implementation details emerge or change.
    * **Pre-Release:** Before deploying significant changes or new features.
    * **Post-Release:** Periodically review and update models based on new threats, changes in the system, or security incidents.
* **Triggers:** Initiate or update a threat model when:
    * Designing new systems or major features.
    * Making significant architectural changes.
    * Integrating with new third-party services or dependencies.
    * Responding to new threat intelligence or discovered vulnerabilities.
    * Addressing specific compliance or security requirements.

## Process Steps (Common Framework)

Follow a structured process. A common approach involves these steps (adapt as needed):

1.  **Define Scope & Objectives:**
    * Clearly define what system/feature is being modeled.
    * Identify security objectives and critical assets (what are you trying to protect?).
    * Understand the business context and risk tolerance.
2.  **Decompose the System:**
    * Understand the system architecture.
    * Create diagrams (Data Flow Diagrams - DFDs are common) showing components, data stores, processes, data flows, external dependencies, and trust boundaries (where privilege levels change).
3.  **Identify Threats:**
    * Brainstorm potential threats based on the system diagram and objectives.
    * Use a structured methodology (like STRIDE) to systematically identify types of threats relevant to each component and flow.
    * Consider potential attackers (threat agents), their motives, and capabilities.
    * Leverage threat intelligence and common vulnerability lists (e.g., OWASP Top 10).
4.  **Analyze & Rate Threats:**
    * Evaluate the likelihood and potential impact of each identified threat.
    * Use a risk rating system (e.g., DREAD - Deprecated but concept useful, CVSS, or simpler High/Medium/Low) to prioritize threats. Focus on the most significant risks first.
5.  **Determine & Prioritize Mitigations:**
    * For each significant threat, brainstorm potential countermeasures (mitigations).
    * Mitigations can include design changes, security controls (authentication, authorization, encryption, validation), code fixes, operational procedures, or monitoring/alerting.
    * Decide on the approach for each threat: Mitigate, Accept (document why), Transfer (e.g., insurance), or Eliminate (remove the vulnerable feature/component). Prioritize mitigation implementation based on risk rating.
6.  **Document & Validate:**
    * Document the threat model, identified threats, risk ratings, mitigation strategies, and decisions (see Documentation section).
    * Validate that implemented mitigations effectively address the threats (e.g., through code review, security testing).
    * Periodically review and update the threat model.

## Methodologies (STRIDE Recommended)

* **Use a Methodology:** Employ a structured threat identification methodology to ensure comprehensive coverage.
* **STRIDE:** (Commonly used, especially during design) Focuses on types of threats:
    * **S**poofing: Illegitimately assuming the identity of another user, component, or entity. (Mitigation: Strong Authentication, MFA, Secure Protocols)
    * **T**ampering: Unauthorized modification of data in transit or at rest. (Mitigation: Hashing, Digital Signatures, Access Controls, Input Validation, Encryption)
    * **R**epudiation: Denying having performed an action when others can't prove otherwise. (Mitigation: Secure Logging, Auditing, Digital Signatures, Non-repudiable Timestamping)
    * **I**nformation Disclosure: Exposure of sensitive information to unauthorized parties. (Mitigation: Encryption (at rest, in transit), Access Controls, Data Minimization, Secure Error Handling)
    * **D**enial of Service (DoS): Preventing legitimate users from accessing the system or resources. (Mitigation: Rate Limiting, Resource Quotas, Redundancy, Scalability, Input Validation, Firewalls)
    * **E**levation of Privilege: Gaining capabilities or permissions without proper authorization. (Mitigation: Least Privilege Principle, Input Validation, Secure Defaults, Authorization Checks)
* **Other Methodologies:** Be aware of other methodologies like PASTA (Process for Attack Simulation and Threat Analysis), VAST, OCTAVE, etc., which may be suitable depending on context and goals, but STRIDE provides a good starting point.

## Documentation

* **Store Threat Models:** Store threat models securely and make them accessible to the relevant team members. Options include:
    * Version control (e.g., Git repository alongside code or in a dedicated security repo), often using Markdown files.
    * Wiki pages (e.g., Confluence).
    * Dedicated threat modeling tools.
* **Key Documentation Elements:**
    * **System Overview/Diagrams:** DFDs or architecture diagrams showing scope, components, flows, trust boundaries.
    * **Assumptions:** Any security assumptions made during modeling.
    * **Threat List:** Enumerated threats, categorized (e.g., by STRIDE), with descriptions.
    * **Risk Assessment:** Likelihood and impact ratings for threats.
    * **Mitigation Plan:** Proposed or implemented countermeasures for each threat, including status (e.g., To Do, Implemented, Accepted Risk) and ownership/tracking IDs (e.g., issue tracker links).
    * **Validation:** Notes on how mitigations were/will be validated.
* **Keep it Updated:** Threat models are living documents. Update them when the system architecture, features, or threat landscape changes significantly.

## Integration into SDLC

* **Embed, Don't Bolt On:** Integrate threat modeling activities into existing development workflows (e.g., design reviews, sprint planning, backlog grooming, code reviews) rather than treating it as a separate, isolated phase.
* **Cross-Functional Team:** Involve developers, architects, security specialists, testers, and potentially product owners in the threat modeling process to get diverse perspectives. Empower team members to contribute.
* **Iterative Refinement:** Start with high-level models early and refine them with more detail as the design and implementation progress. Don't aim for perfection in the first pass.
* **Tooling:** Leverage tools where appropriate (diagramming tools, threat modeling specific tools like OWASP Threat Dragon or Microsoft Threat Modeling Tool, security linters) but recognize that threat modeling requires critical thinking, not just tool output.
