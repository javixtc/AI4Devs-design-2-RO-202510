✅ **PROMPT 1**
==========

🧪 **TOPIC**
------------

Generate 3 User Stories based on the info of the attached PRD. Follow good practices with the following structure:

-   **Standard Format:**\
    *"As a [user type], I want to [perform an action] to [gain a benefit]."*

-   **Description:**\
    A concise, natural-language description of the functionality the user desires.

-   **Acceptance Criteria:**\
    Must follow Gherkin-style syntax:\
    *Given* [initial context], *when* [action performed], *then* [expected result].

-   **Additional Notes:**\
    Any clarifying detail that aids development.

-   **Tasks:**\
    A list of engineering tasks and subtasks needed to complete the story.

**User Story Examples:**

-   Product Development:\
    *"As a product manager, I want a way for team members to understand how individual tasks contribute to the goals, so they can better prioritize their work."*

-   Customer Experience:\
    *"As a returning customer, I expect my information to be saved to create a smoother checkout experience, so I can complete my purchases quickly and easily."*

-   Mobile App:\
    *"As a frequent app user, I want a way to quickly and easily access relevant information so I can find what I need efficiently."*

* * * * *

1️⃣ **ROLE & CONTEXT**
----------------------

You are acting as a **Senior Product Requirements Architect**, with expertise in:

-   Enterprise-grade PRD decomposition

-   Writing high-quality User Stories aligned with Agile/Scrum and INVEST

-   Translating complex or ambiguous product requirements into atomic, testable stories

-   Working within engineering, UX, QA, and architecture teams to ensure feasibility and clarity

Context:

-   You are the final reviewer of User Stories generated from the provided PRD.

-   You must infer missing details only when logically necessary, but never invent features that are not present in the PRD.

-   Your output must meet enterprise-quality standards for precision, consistency, and testability.

* * * * *

2️⃣ **TASK**
------------

Produce **exactly 3 complete User Stories**, using **only** the information contained in the attached PRD.

Each story must follow the full structure described above.

* * * * *

3️⃣ **SPECIFICATIONS**
----------------------

When generating the 3 User Stories:

### **Use only the PRD as the functional source of truth.**

Extract from it:

-   User types (Recruiter, Hiring Manager, Candidate, Admin, Automation System, etc.)

-   Actions grounded in actual features described in the PRD

-   Benefits aligned with the value proposition and use-cases

### **Each story must include:**

1.  **Title** (clear, descriptive)

2.  **User Story sentence** (As a..., I want..., so that...)

3.  **Description**

4.  **Acceptance Criteria** (Gherkin: Given / When / Then)

5.  **Additional Notes**

6.  **Tasks & Subtasks**

### **Acceptance Criteria rules:**

-   Must be testable, measurable, unambiguous

-   Must not overlap with other stories

-   Must reflect the minimum definition of done for the feature

### **Tasks must:**

-   Be written as action verbs (Implement, Create, Validate, Integrate...)

-   Cover backend, frontend, UX, indexing, validations, events, error cases, and QA whenever applicable

-   Include edge-case validations required by the PRD

* * * * *

4️⃣ **TECHNICAL REQUIREMENTS**
------------------------------

Your output must follow:

-   **Language:** English, concise, technical, neutral

-   **Format:** Markdown headings + bullet lists

-   **Corporate Standards:**

    -   INVEST criteria

    -   Gherkin-style acceptance criteria

    -   No architectural decisions not explicitly in the PRD

-   **Naming conventions:**

    -   Titles: descriptive and functional

    -   Tasks: action verbs only

-   **Constraints:**

    -   No assumptions beyond reasonable inferences

    -   No features invented outside the PRD

    -   No filler content

    -   Zero duplication across stories

* * * * *

🧩 **METAPROMPT REQUIREMENTS**
------------------------------

You must:

-   Produce a **highly detailed**, **prescriptive**, and **consistent** output

-   Maintain strict formatting with headings and bullet lists

-   Avoid ambiguity; use objective technical language

-   Ensure reproducibility

-   Provide stories that are immediately usable by engineering teams

* * * * *

📌 **EXPECTED OUTPUT**
----------------------

A final deliverable containing **exactly 3 complete User Stories**, each with:

1.  Title

2.  Standard User Story sentence

3.  Description

4.  Acceptance Criteria (Given / When / Then)

5.  Additional Notes

6.  Tasks & Subtasks


✅ **PROMPT 2**
==========
Consider the 3 user stories you've generated as a backlog, generate a Markdown table estimating the following for each item: Impact on the user and business value. Urgency based on market trends and user feedback. Complexity and estimated implementation effort. Risks and dependencies between tasks.

✅ **PROMPT 3**
==========
Order the user stories by priority using two typical different approaches and compare them in a markdown table

✅ **PROMPT 4**
==========
1️⃣ **ROLE & CONTEXT**
----------------------

You will act as a **Senior Technical Architect & Lead Engineer** with expertise in:

-   Large-scale backend architectures for Applicant Tracking Systems (ATS)

-   Domain-Driven Design (DDD), Clean Architecture, and distributed systems

-   Event-driven design, asynchronous pipelines, messaging, and integrations

-   Microservice API architecture (REST, event contracts, adapters)

-   Full-stack coordination across backend, frontend, DevOps, QA, and integrations

-   Deep domain knowledge of job publication flows, job-board integrations, RBAC, and audit/event logging

### Professional Context

-   You operate inside a multi-team engineering organization delivering features from formal PRDs and User Stories.

-   You are responsible for decomposing a User Story into **implementation-ready work tickets**.

-   The tickets you produce must be unambiguous, technically detailed, and actionable by engineers.

-   Your output must strictly adhere to the User Story's functional scope, ensuring domain correctness and production feasibility.

* * * * *

2️⃣ **TASK**
------------

**Produce a complete and detailed set of engineering work tickets derived from User Story 1 (Job Publication Workflow).**

The tickets must be divided into:

-   Backend tickets

-   Frontend tickets

-   Integration tickets

-   Infrastructure tickets

-   QA tickets

Each ticket must be **implementation-ready**, containing:

-   Clear goals

-   Domain objects involved

-   Technical requirements

-   Validations

-   Architecture interactions

-   Acceptance criteria

-   Dependencies

-   Deliverables

* * * * *

3️⃣ **SPECIFICATIONS**
----------------------

When generating the work tickets, follow **these strict requirements**:

### Use Only the Content from User Story 1

Include the following domain elements:

-   **Job entity** (fields: `id`, `title`, `description`, `location`, `department`, `workflowStages`, `distributionChannels`, `createdByUserId`, `status`)

-   **Job publication workflow**

-   **Distribution channels**

-   **Job-board connectors**

-   **Publication validation**

-   **Notification triggers**

-   **Audit logging framework**

-   **Status transitions** (`DRAFT → PUBLISHED` or `PUBLISHED_PARTIAL`)

-   **Public Job URL generator**

-   **RBAC rules (Recruiter, Admin)**

-   **Internal event: `JobPublished`**

### Each Work Ticket Must Include:

-   **Title**

-   **Category (Backend / Frontend / Integration / Infra / QA)**

-   **Description**

-   **Requirements & Components**, including:

    -   Domain models, DTOs, entities, and exact fields

    -   Validation rules

    -   DB operations

    -   External system interactions

    -   Event emission rules

    -   Error-handling logic

-   **Technical Acceptance Criteria**

-   **Dependencies**

-   **Deliverables**

-   **Non-functional constraints** (security, performance, observability)

### Required Domain Components

Tickets must correctly use:

-   **Job entity model**

-   **PublicationEvent**

-   **Public Job URL Generator**

-   **Job-Board Connector Adapter(s)**

-   **Notification Service**

-   **Audit Log Service**

-   **RBAC roles (Recruiter, Admin)**

### Rules & Validation Requirements

-   Validate required job fields before publish.

-   Partial publication must update status to `PUBLISHED_PARTIAL`.

-   External failures must not block internal publishing.

-   Generate and store public job URL.

-   Emit internal event `JobPublished`.

-   Enforce RBAC restrictions.

-   Create audit logs on every publish attempt.

* * * * *

4️⃣ **TECHNICAL REQUIREMENTS**
------------------------------

The work tickets must assume the following technical environment:

### Languages & Frameworks

-   **Backend:** Java, Spring Boot, Spring Data JPA, Spring Events

-   **Frontend:** React or Angular

-   **Database:** PostgreSQL

-   **Infrastructure:** AWS S3, async queues/workers

-   **Integrations:** REST adapters to external job boards

### Corporate Standards

-   Use **DTOs** for API boundaries

-   Use **domain models** internally

-   Follow **hexagonal architecture** (Ports & Adapters)

-   DB fields use `snake_case`

-   Java fields use `camelCase`

-   Apply naming conventions such as:

    -   `PublishJobCommand`

    -   `JobBoardPublisherService`

    -   `PublicationStatusUpdater`

### Security & Logging

-   Enforce RBAC (Recruiter, Admin)

-   Apply audit logs on all status changes

-   Secure URL generation (non-guessable)

-   Validate input constraints (length, forbidden chars)

-   Store external errors safely (no sensitive data)

### Output Requirements

-   Output must be in **Markdown**

-   Include **headings**, **sections**, **tables**, **bullets** where appropriate

-   The result must be **fully deterministic**, **unambiguous**, and **production-ready**

-   No vague phrasing; no missing pieces

-   No invented features beyond User Story 1

* * * * *

🧩 METAPROMPT REQUIREMENTS
==========================

Your output **must**:

-   Be highly prescriptive and technically precise

-   Use professional tone, tactical formatting, and strict structure

-   Produce consistent and reproducible engineering work tickets

-   Contain zero ambiguity

-   Be aligned with enterprise engineering standards

-   Provide actionable implementation direction across backend, frontend, integration, infra, and QA

-   Remain strictly within the functional scope of User Story 1

* * * * *

📌 EXPECTED OUTPUT OF THIS METAPROMPT
=====================================

Your final output must be a **complete, copy/paste-ready set of work tickets**, including:

-   Backend tickets

-   Frontend tickets

-   Integration tickets

-   Infrastructure tickets

-   QA tickets

Each with:

-   Title

-   Category

-   Description

-   Requirements & Components

-   Detailed acceptance criteria

-   Dependencies

-   Deliverables

-   Non-functional constraints

✅ **PROMPT 5**
==========
Estimate the effort of work tickets using the methodology (fibonacci, poker, t-shirt sizes) and units (hours, story points) that you prefer. Compare in a markdown table all of them and make recommendations about what I can use.
