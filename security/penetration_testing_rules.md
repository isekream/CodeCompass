# Penetration Testing Guidelines & Scope

## Purpose & Ethics

* **Goal:** Simulate real-world attacks in a controlled manner to identify and validate security vulnerabilities in systems, applications, or networks before malicious actors exploit them. Provide actionable recommendations for remediation.
* **Ethical Hacking:** All penetration testing activities MUST be conducted ethically, legally, and with explicit, documented authorization from the system owner(s). The primary goal is to improve security, not cause harm or disruption.
* **Confidentiality:** Testers MUST handle all discovered information (vulnerabilities, data accessed) with strict confidentiality, according to the terms agreed upon in the scope and Rules of Engagement (ROE).

## Planning & Scoping

* **Define Objectives:** Clearly articulate the goals of the penetration test. What are the primary concerns? (e.g., compliance validation, assessing specific application security, evaluating network defenses, simulating a specific threat actor).
* **Identify Assets:** Clearly identify all assets within the scope of the test (e.g., specific IP ranges, applications/URLs, APIs, cloud environments, physical locations, source code repositories).
* **Define Scope Boundaries:** Explicitly define what is *in scope* and what is *out of scope*. This includes:
    * Target systems, networks, applications.
    * Allowed testing techniques (e.g., are DoS tests permitted? Is social engineering allowed?).
    * Excluded systems (e.g., third-party services, production systems during business hours unless specifically agreed).
    * Testing timeframe (start date, end date, allowed testing hours).
* **Obtain Authorization:** Secure explicit, written authorization from the system owner and relevant stakeholders *before* any testing begins. This authorization should reference the agreed-upon scope and ROE. For cloud environments, ensure compliance with the cloud provider's penetration testing policies.
* **Choose Test Type:** Select the appropriate type of penetration test based on objectives and available information:
    * **Black Box:** Tester has minimal to no prior knowledge of the target system. Simulates an external attacker.
    * **White Box:** Tester has full knowledge, including source code, architecture diagrams, and potentially credentials. Simulates an insider threat or allows for deeper code-level analysis.
    * **Grey Box:** Tester has partial knowledge (e.g., user-level credentials, basic architecture information). A common balance between black and white box.

## Rules of Engagement (ROE)

* **Formal Document:** The ROE is a critical document agreed upon by the testing team and the client/system owner before testing starts.
* **Key Elements:** The ROE MUST clearly define:
    * **Scope:** Detailed list of in-scope and out-of-scope targets and activities.
    * **Timeline:** Start date, end date, allowed testing windows (e.g., business hours, off-hours).
    * **Contact Information:** Primary points of contact for both the testing team and the client, including emergency contacts available 24/7 during the testing window.
    * **Testing Methods:** Allowed tools and techniques. Any explicitly forbidden actions (e.g., destructive tests, specific social engineering tactics, DoS).
    * **Handling Sensitive Data:** Procedures for handling any sensitive data discovered during the test (e.g., PII, credentials). How will it be stored, reported, and destroyed?
    * **Incident Handling:** Process for reporting critical vulnerabilities found during the test. Procedure if the test inadvertently causes disruption or impacts production systems. Procedure if evidence of a prior, real compromise is discovered.
    * **Tester IPs:** Source IP addresses used by the testing team (for whitelisting or monitoring purposes, if agreed upon).
    * **Third-Party Approvals:** Confirmation that any necessary approvals from third-party providers (e.g., hosting providers) have been obtained.
    * **Reporting Requirements:** Expected format, content, and delivery timeline for the final report.
* **Approval:** The ROE must be formally reviewed and signed off by both parties before testing commences.

## Methodologies

* **Structured Approach:** Follow a recognized penetration testing methodology or standard to ensure systematic and comprehensive testing. Examples include:
    * PTES (Penetration Testing Execution Standard)
    * OWASP Testing Guide (for web applications)
    * OSSTMM (Open Source Security Testing Methodology Manual)
    * NIST SP 800-115
* **Phases:** Methodologies typically include phases like:
    * Planning & Reconnaissance (Information Gathering)
    * Scanning & Vulnerability Analysis
    * Exploitation (Gaining Access)
    * Post-Exploitation (Maintaining Access, Privilege Escalation, Pivoting)
    * Analysis & Reporting
    * Cleanup

## Execution

* **Minimize Disruption:** Conduct testing within the agreed-upon timeframe and scope, taking precautions to minimize disruption to business operations, especially for production systems.
* **Evidence Collection:** Carefully document all steps taken, tools used, commands executed, vulnerabilities identified, and evidence of exploitation (e.g., screenshots, logs). Maintain a clear audit trail.
* **Communication:** Maintain communication with the client point of contact as defined in the ROE, especially if critical vulnerabilities are found or if testing might become disruptive.
* **Clean Up:** Thoroughly remove any tools, scripts, accounts, or system modifications created during the test upon completion, unless otherwise specified in the ROE (e.g., leaving a harmless indicator file).

## Reporting & Remediation

* **Comprehensive Report:** Provide a detailed report at the conclusion of the test, including:
    * **Executive Summary:** High-level overview for management, summarizing key findings, risk posture, and strategic recommendations (non-technical language).
    * **Scope & Objectives:** Recap of the agreed-upon scope and goals.
    * **Methodology:** Description of the methodology and tools used.
    * **Findings:** Detailed description of each vulnerability identified, including:
        * Location (URL, IP address, code snippet)
        * Technical description of the vulnerability
        * Steps to reproduce the vulnerability
        * Evidence of exploitation (screenshots, logs)
        * Risk Rating (e.g., based on CVSS - Critical, High, Medium, Low, Info) considering likelihood and business impact.
    * **Recommendations:** Clear, actionable recommendations for remediating each vulnerability, prioritized by risk.
    * **Positive Findings (Optional but Recommended):** Mention security controls that were tested and found to be effective.
    * **Appendices:** Raw tool output, detailed logs, etc. (optional).
* **Clarity:** Write the report clearly for different audiences (technical and non-technical).
* **Timeliness:** Deliver the report within the timeframe specified in the ROE.
* **Debriefing:** Conduct a debriefing session with the client/stakeholders to discuss findings and recommendations.
* **Remediation & Retesting:** The client should prioritize and implement remediation actions. Consider including a retesting phase (within the initial scope or as a follow-up engagement) to verify that identified vulnerabilities have been successfully fixed.
