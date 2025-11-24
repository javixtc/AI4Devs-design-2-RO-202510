✅ **USER STORY 1 --- Job Publication Workflow**
=============================================

**User Story**
--------------

**As a Recruiter, I want to publish a job across multiple channels so that candidates can view and apply to the open role.**

**Description**
---------------

Recruiters must be able to create and publish job postings that include job details, distribution channels, and automation workflow settings. When published, the system should validate the job information, push it to configured channels, update status, generate a public URL, and notify stakeholders.

**Acceptance Criteria**
-----------------------

-   **Given** a recruiter with appropriate permissions has created a job draft with valid title, description, location, department, stages, and distribution channels\
    **when** the recruiter clicks "Publish"\
    **then** the system must validate all required fields and publish the job.

-   **Given** the job is successfully validated\
    **when** the system pushes the job to all configured channels\
    **then** the job must appear on internal job pages and any integrated job boards.

-   **Given** an external job board rejects the publication\
    **when** the error is returned\
    **then** the system must mark the job status as `PUBLISHED_PARTIAL`, log the error, and notify the recruiter.

-   **Given** the job is published fully or partially\
    **when** the publication completes\
    **then** the system must generate the public job URL, set job status to `PUBLISHED`, and append an audit log entry.

-   **Given** the job is published\
    **when** notifications are triggered\
    **then** subscribed Hiring Managers must receive a publication notification.

**Additional Notes**
--------------------

-   Must enforce RBAC: only Recruiters/Admins can publish jobs.

-   Job-board connectors may be asynchronous; errors must not block internal publishing.

-   Audit logging is mandatory for each publication action.

**Tasks**
---------

### Backend

-   Implement job validation (required fields, workflow configuration, channel configuration).

-   Implement publication handler for internal channel.

-   Integrate job-board connector interface with retry logic.

-   Implement partial failover logic (`PUBLISHED_PARTIAL`).

-   Generate job public URL and persist to DB.

-   Create audit log entry on each publish attempt.

-   Emit `JobPublished` domain event.

### Frontend

-   Implement UI for job publish action.

-   Display validation errors from backend.

-   Display publication result, including status and public URL.

### Infrastructure / Integration

-   Configure async workers for job-board publishing (if applicable).

-   Configure logging for external connector errors.

### QA

-   Test happy path job publication.

-   Test invalid job draft.

-   Test partial publication failures.

-   Test audit log validation.

-   Test RBAC restrictions.

* * * * *

✅ **USER STORY 2 --- Candidate Intake & AI-Assisted Screening**
=============================================================

**User Story**
--------------

**As a Recruiter, I want candidate applications automatically parsed, indexed, and scored so that I can quickly identify the most relevant applicants.**

**Description**
---------------

When a candidate applies, the system must create an Application record, upload attachments, parse the resume, index the candidate into the search/match engine, compute matchScore, and surface the results for recruiters reviewing applications.

**Acceptance Criteria**
-----------------------

-   **Given** a candidate submits an application with required fields (firstName, lastName, jobId)\
    **when** the system receives the application\
    **then** it must create an `Application` record with status `APPLIED`.

-   **Given** the application includes attachments\
    **when** files are uploaded\
    **then** the system must store them in S3 with metadata and link them to the Application or CandidateProfile.

-   **Given** a resume is uploaded\
    **when** it is processed asynchronously\
    **then** the system must extract resumeText, enrich profileJson, and index the candidate.

-   **Given** a candidate profile is indexed\
    **when** the AI Match Engine processes the application\
    **then** it must compute a `matchScore` and persist it to the Application record.

-   **Given** matchScore exists\
    **when** the recruiter views the candidate list\
    **then** applications must be sortable/filterable by matchScore.

-   **Given** required candidate information is missing\
    **when** the system identifies missing fields\
    **then** it must flag the application and request candidate completion via the portal.

**Additional Notes**
--------------------

-   Resume parsing and match scoring must run asynchronously via event-driven pipelines.

-   ApplicationCreated event is critical for triggering indexing and match scoring.

-   Audit logs must capture application creation and state transitions.

**Tasks**
---------

### Backend

-   Implement `POST /applications` handler (multipart form).

-   Persist Application, CandidateProfile (if needed), and attachments.

-   Publish `ApplicationCreated` event.

-   Integrate Resume Parser Adapter to extract and persist resumeText.

-   Integrate Search Index Adapter for indexing candidate.

-   Persist matchScore when AI pipeline updates indexing.

### Frontend

-   Implement candidate application form.

-   Display submission confirmation with timestamp.

-   Display missing-info prompts in candidate portal.

### Infrastructure / ML

-   Configure Resume Parser integration (3rd-party).

-   Configure AI Match Engine batch/event triggers.

-   Ensure Elasticsearch index is up-to-date.

### QA

-   Test application submission with/without resume.

-   Test resume parsing errors.

-   Test matchScore updates.

-   Test missing-info detection flows.

-   Test candidate list sorting/filtering by matchScore.

* * * * *

✅ **USER STORY 3 --- Interview Scheduling With Calendar Sync**
============================================================

**User Story**
--------------

**As a Recruiter, I want to schedule interviews using real-time calendar availability so that interviewers and candidates can coordinate efficiently.**

**Description**
---------------

Recruiters must be able to select interviewers, query availability from Google/Microsoft calendars, propose time slots, send invites, and process confirmations or reschedules. Interview feedback must be stored and linked to the application.

**Acceptance Criteria**
-----------------------

-   **Given** a candidate has the status `SHORTLISTED`\
    **when** the recruiter opens the scheduling UI\
    **then** the system must query connected calendars for free/busy information.

-   **Given** availability is retrieved successfully\
    **when** the recruiter selects slot(s)\
    **then** the candidate must be invited to choose a preferred slot (unless recruiter selects directly).

-   **Given** a slot is selected\
    **when** the recruiter confirms scheduling\
    **then** the system must create calendar events for both interviewers and the candidate, and persist an Interview entity.

-   **Given** the candidate requests a reschedule\
    **when** conflicts are detected\
    **then** the system must surface alternative available slots.

-   **Given** the calendar provider API fails\
    **when** scheduling is attempted\
    **then** the system must provide fallback manual-scheduling with a generated email template.

-   **Given** the interview has occurred\
    **when** feedback is submitted\
    **then** feedback must be stored and linked to the corresponding Application.

**Additional Notes**
--------------------

-   Requires OAuth calendar connectivity.

-   Must handle multi-interviewer availability.

-   Interview events must store `calendarEventId` for sync consistency.

-   Audit logs required for scheduling, updates, and cancellations.

**Tasks**
---------

### Backend

-   Implement availability query to calendar providers (Google/Microsoft).

-   Implement Interview creation and persistence in DB.

-   Generate and send calendar invite events.

-   Implement reschedule conflict-handling logic.

-   Implement fallback manual scheduling template.

-   Append audit logs for each scheduling action.

### Frontend

-   Create scheduling UI for selecting interviewers and viewing proposed time slots.

-   Implement candidate slot selection UI.

-   Implement feedback submission UI for interviewers.

### Integrations

-   Configure OAuth for calendar providers.

-   Implement Calendar Provider Adapter for free/busy lookup and event creation.

### QA

-   Test scheduling flow with single and multiple interviewers.

-   Test rescheduling conflicts.

-   Test calendar API failure fallback.

-   Test feedback persistence.

-   Test authorization and role-based access (Recruiter vs Hiring Manager).





📊 **Backlog Prioritization Table**
======================================

| User Story                                      | Impact on User & Business Value                                                                                                                           | Urgency (Market Trends & User Feedback)                                                                                            | Complexity & Estimated Effort                                                                                                                  | Risks & Dependencies                                                                                                                                                                 |
| ----------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **1. Job Publication Workflow**                 | **High** — Core functionality enabling the hiring pipeline. Critical for user adoption; high business value as job posting is the entry point to the ATS. | **High** — Job publication is expected by all ATS users; competitive markets require multi-channel visibility immediately.         | **Medium** — Requires validation logic, job-board connectors, async handlers, and notifications. Effort moderate due to external API behavior. | - External job-board API reliability issues. <br> - Dependency on Notification Service and Workflow Automation. <br> - Risk of inconsistent statuses if publication partially fails. |
| **2. Candidate Intake & AI-Assisted Screening** | **Very High** — Major differentiator for the product (AI matching). Direct impact on reducing time-to-hire and improving candidate quality.               | **High** — AI-assisted matching is rapidly becoming a competitive requirement; resume parsing is frequently requested by HR teams. | **High** — Requires async pipelines, resume parsing adapter, search indexing, event-driven flows, and matchScore updates.                      | - Heavy reliance on 3rd-party resume parsing tools. <br> - Potential ML pipeline delays. <br> - Search Index uptime and indexing latency are critical dependencies.                  |
| **3. Interview Scheduling with Calendar Sync**  | **Medium–High** — Key value-add for reducing scheduling friction; strong differentiator but not required to publish or intake candidates.                 | **Medium** — Calendar sync is important but can be phased; not always required for early customers.                                | **High** — Complex due to free/busy lookups, multi-interviewer logic, OAuth, event creation, rescheduling flows.                               | - Calendar provider API rate limits/failures. <br> - OAuth dependency and token refresh logic. <br> - Multi-party scheduling complexity (candidate + multiple interviewers).         |



Below is a **Markdown comparison table** showing the priority order of the 3 User Stories using **two different prioritization frameworks**:

-   **RICE** (Reach, Impact, Confidence, Effort) --- numeric, product-led prioritization

-   **MoSCoW** (Must / Should / Could / Won't) --- requirement-classification prioritization

* * * * *

🔽 **Prioritization Comparison Table**
======================================

| User Story | **RICE Priority Order** | **MoSCoW Priority Order** | Notes & Comparison |
| --- | --- | --- | --- |
| **1\. Job Publication Workflow** | **#1** --- Highest RICE score because it impacts all recruiters, required to start the hiring pipeline, and implementation cost is moderate. | **Must Have** --- Core ATS functionality; without it no jobs can be created or published. | Both methods rank this as the **highest priority**. RICE highlights broad reach; MoSCoW marks it essential. |
| **2\. Candidate Intake & AI-Assisted Screening** | **#2** --- Very high impact and reach; effort is high, lowering RICE score slightly compared to Story #1. | **Should Have** --- Significant differentiation but not absolutely required for initial functional ATS operation. | RICE ranks this high but below Story #1 due to complexity. MoSCoW sees this as important but not blocking MVP. |
| **3\. Interview Scheduling with Calendar Sync** | **#3** --- High effort + narrower reach (not all customers need scheduling automation). | **Could Have** --- Enhances experience but is not blocking hiring workflow; can be added post-MVP. | Both methods deprioritize this feature. RICE due to high effort; MoSCoW because it's optional early-stage. |

* * * * *

📌 Summary
==========

-   **Both frameworks agree on overall order:**\
    **1 → Job Publication**\
    **2 → Candidate Intake & AI Screening**\
    **3 → Interview Scheduling**

-   **RICE is more quantitative:** It ranks based on weighted value vs. effort.

-   **MoSCoW is more categorical:** It separates foundational features from enhancements.




✅ **ENGINEERING WORK TICKETS --- USER STORY 1 (Job Publication Workflow)**
========================================================================

* * * * *

🏗️ **BACKEND TICKETS**
=======================

* * * * *

**1\. Implement PublishJobCommand Handler**
-------------------------------------------

**Category:** Backend\
**Description:**\
Create the command handler responsible for executing the job publication workflow, including RBAC enforcement, validation, status changes, and orchestration of publication steps.

### Requirements & Components

-   **Domain Models:**

    -   `Job` (id, title, description, location, department, workflowStages, distributionChannels, createdByUserId, status)

-   **Validations:**

    -   Required: title, description, location, department, workflowStages

    -   distributionChannels must not be empty

    -   createdByUserId must belong to RBAC roles (Recruiter/Admin)

-   **DB Operations:**

    -   Read job by id

    -   Update job status

    -   Persist public job URL

-   **Architecture:**

    -   Use DDD Application Layer → `PublishJobCommand`

    -   Use Domain Service → `PublicationValidator`

-   **Event Emission:**

    -   Emit `PublicationEvent` on both success & partial success

    -   Emit `JobPublished` event after successful internal publish

-   **Error Handling:**

    -   Capture all domain validation errors

    -   Capture exceptions from connectors but continue internal publishing

    -   Return structured error object for partial failures

### Technical Acceptance Criteria

-   **Given** a recruiter triggers "publish job" for a valid job\
    **When** validation succeeds\
    **Then** the command handler updates job status, initiates publication, emits events, and logs audit entry.

-   **Given** validation fails\
    **When** command executes\
    **Then** status remains `DRAFT` and an error is returned.

-   **Given** external connectors fail\
    **When** publication proceeds\
    **Then** job status becomes `PUBLISHED_PARTIAL`.

### Dependencies

-   Ticket #2 (Publication Validator)

-   Ticket #3 (Public URL Generator)

-   Ticket #4 (Audit Logging)

-   Integration Tickets for job-board publishing

### Deliverables

-   `PublishJobCommand`

-   `PublishJobCommandHandler`

-   Unit tests

### Non-Functional Constraints

-   Must complete within < 300ms excluding external calls

-   Use structured logging with correlationIds

-   Fully deterministic behavior

* * * * *

**2\. Implement PublicationValidator Service**
----------------------------------------------

**Category:** Backend\
**Description:**\
Service to validate all required fields for job publication.

### Requirements & Components

-   Validate presence of mandatory fields

-   Validate distributionChannels (non-empty)

-   Validate workflowStages (>=1)

-   Validate RBAC via injected AuthorizationService

### Technical Acceptance Criteria

-   Missing fields must return structured error with field list

-   RBAC violations must return forbidden error

-   Validator must be reusable across handlers

### Dependencies

-   None

### Deliverables

-   `PublicationValidator`

-   Unit tests (field-level + RBAC tests)

### Non-Functional Constraints

-   Must run in < 10ms

* * * * *

**3\. Implement Public Job URL Generator**
------------------------------------------

**Category:** Backend\
**Description:**\
Generate non-guessable, deterministic public URLs for published jobs.

### Requirements & Components

-   Inputs: job.id

-   Output: formatted URL `/jobs/{hash}`

-   Hash must be deterministic but non-reversible

-   Store generated URL in Job table

### Technical Acceptance Criteria

-   URL must be unique per job

-   URL must remain stable across re-publication

-   Cannot expose incremental IDs

### Dependencies

-   Command handler (Ticket #1)

### Deliverables

-   `PublicJobUrlGenerator`

-   Hash utility

### Non-Functional Constraints

-   Must resist enumeration

-   99.9% collision-free guarantees

* * * * *

**4\. Implement Audit Log Entry for Publication Attempts**
----------------------------------------------------------

**Category:** Backend\
**Description:**\
Append an audit entry for every job publication attempt.

### Requirements & Components

-   Log fields: jobId, userId, timestamp, newStatus, previousStatus, errors(if any)

-   Use AuditLogService

-   Create entry both for success and partial success

### Technical Acceptance Criteria

-   Audit entry must be written on every publish attempt

-   Audit log must persist even if connectors fail

### Dependencies

-   Command Handler

### Deliverables

-   Method inside `PublicationAuditService`

-   DB schema update if necessary

### Non-Functional Constraints

-   Write latency < 25ms

* * * * *

**5\. Implement JobPublished Internal Event**
---------------------------------------------

**Category:** Backend\
**Description:**\
Emit an internal domain event after internal publication completes.

### Requirements & Components

-   Event fields: jobId, publicUrl, distributionChannels, publicationStatus

-   Publish through Spring ApplicationEventPublisher

-   Event should be consumed by NotificationService handler

### Acceptance Criteria

-   Event must fire exactly once per successful internal publication

-   Must not fire on validation failures

### Dependencies

-   URL generator

-   Command handler

### Deliverables

-   `JobPublishedEvent`

-   Domain event publisher method

### Non-Functional Constraints

-   Event processing must be async safe

* * * * *

🎨 **FRONTEND TICKETS**
=======================

* * * * *

**6\. Implement "Publish Job" UI Action**
-----------------------------------------

**Category:** Frontend\
**Description:**\
Add UI button + modal confirmation for publishing jobs.

### Requirements & Components

-   Add "Publish Job" button in job details page

-   Disable if job already has `PUBLISHED` status

-   RBAC: only Recruiter/Admin can see it

-   Display validation or connector errors

### Acceptance Criteria

-   Button visible only for valid roles

-   Errors displayed in toast or inline

-   Success displays public URL

### Dependencies

-   Backend API endpoint for publish

### Deliverables

-   UI components

-   Error/success toasts

-   RBAC guard

* * * * *

**7\. Display Publication Result UI**
-------------------------------------

**Category:** Frontend

### Requirements & Components

-   Show publication status: `PUBLISHED`, `PUBLISHED_PARTIAL`

-   Show generated public URL

-   Show list of channel errors if partial

### Acceptance Criteria

-   Must show correct status after backend response

-   Must hyperlink public job URL

### Dependencies

-   Publish job handler response

### Deliverables

-   React component: `<PublicationStatusPanel />`

* * * * *

🔌 **INTEGRATION TICKETS**
==========================

* * * * *

**8\. Job-Board Connector Adapter Implementation**
--------------------------------------------------

**Category:** Integration\
**Description:**\
Provide adapter interface and concrete implementations for each job board.

### Requirements & Components

-   Interface: `JobBoardPublisher`

    -   method: `publish(Job job): PublicationResult`

-   Retry logic with exponential backoff

-   Timeout < 2 seconds

-   Error-handling returns structured fail result, not exceptions

### Acceptance Criteria

-   Must publish job data in correct JSON format

-   Must return failure gracefully

-   Must not block internal publish path

### Dependencies

-   Command handler

### Deliverables

-   Adapter implementations

-   HTTP clients

-   Integration tests

* * * * *

**9\. PublicationResult Aggregator**
------------------------------------

**Category:** Integration\
**Description:**\
Collect, aggregate, and classify job-board responses.

### Requirements & Components

-   Success/failed channel list

-   Flag partial failures

-   Provide structured summary to PublishJobCommandHandler

### Acceptance Criteria

-   Partial failures must be detected reliably

-   Aggregator must not throw exceptions

### Dependencies

-   Adapter responses

### Deliverables

-   Aggregator class + unit tests

* * * * *

☁️ **INFRASTRUCTURE TICKETS**
=============================

* * * * *

**10\. Configure Async Worker for Job Publication**
---------------------------------------------------

**Category:** Infra\
**Description:**\
Set up async worker queue for processing job publication events.

### Requirements & Components

-   Queue: `job_publication`

-   Worker concurrency config

-   Failure retry policy

-   Dead-letter queue for repeated connector failures

### Acceptance Criteria

-   Connector calls must run in async worker

-   Main HTTP request must not block on external connectors

### Dependencies

-   Adapter implementation

-   Event publisher

### Deliverables

-   Queue configuration in AWS

-   Worker deployment config

* * * * *

**11\. Logging & Monitoring for Publication Pipeline**
------------------------------------------------------

**Category:** Infra\
**Description:**\
Add observability tooling for job publication workflow.

### Requirements

-   Structured logs for all publication tasks

-   Metrics:

    -   publication_latency

    -   connector_failure_rate

    -   partial_publication_count

-   Alerts when connector failures exceed threshold

### Acceptance Criteria

-   Metrics visible in Grafana

-   Alerts fire correctly

### Deliverables

-   Log formats

-   Prometheus metrics

-   Alert rules

* * * * *

🧪 **QA TICKETS**
=================

* * * * *

**12\. End-to-End Job Publication Test Plan**
---------------------------------------------

**Category:** QA\
**Description:**\
Define and execute full E2E test suite for job publishing.

### Requirements

-   Test internal publish success

-   Test external connector failure → partial publish

-   Test public URL generation

-   Test audit log creation

-   Test RBAC restrictions

### Acceptance Criteria

-   All E2E scenarios must pass

-   Reproducible results in CI

### Dependencies

-   All backend + integration tickets

### Deliverables

-   Test cases

-   Automation scripts

* * * * *

**13\. API Contract Tests for Publish Endpoint**
------------------------------------------------

**Category:** QA\
**Description:**\
Verify API request/response structure for publishing jobs.

### Requirements

-   Validate request shape

-   Validate success + error objects

-   Validate partial publication response format

### Acceptance Criteria

-   Contract tests verify schemas strictly

-   Tests run automatically in CI

### Deliverables

-   JSON schema tests

-   Postman/Newman/testcontainers setup


Below is a **complete estimation table** comparing **three estimation methodologies** (Fibonacci Story Points, T-Shirt Sizes, and Hour-Based Estimation) for **all work tickets** generated from User Story 1 (Job Publication Workflow).\
After the table, you'll find **recommendations** on which method to choose depending on your team and context.

* * * * *

📊 **Effort Estimation Comparison Table (All Tickets)**
=======================================================

### **Legend**

-   **SP** = Story Points using Fibonacci sequence (1, 2, 3, 5, 8, 13...)

-   **T-Shirt** = XS / S / M / L / XL

-   **Hours** = Ideal engineering hours (not calendar time)

* * * * *

🧩 **Estimation Table**
-----------------------

| Ticket # | Work Item | Fibonacci SP | T-Shirt Size | Estimated Hours |
| --- | --- | --- | --- | --- |
| 1 | PublishJobCommand Handler | **8 SP** | L | 16--20 hrs |
| 2 | PublicationValidator Service | **3 SP** | S | 4--6 hrs |
| 3 | Public Job URL Generator | **2 SP** | XS | 2--3 hrs |
| 4 | Audit Log Entry for Publication Attempts | **3 SP** | S | 4--5 hrs |
| 5 | JobPublished Internal Event | **2 SP** | XS | 2--3 hrs |
| 6 | Publish Job UI Action | **5 SP** | M | 10--12 hrs |
| 7 | Publication Result UI | **3 SP** | S | 5--7 hrs |
| 8 | Job-Board Connector Adapter | **8 SP** | L | 18--24 hrs |
| 9 | PublicationResult Aggregator | **5 SP** | M | 8--10 hrs |
| 10 | Async Worker for Job Publication | **8 SP** | L | 12--18 hrs |
| 11 | Logging & Monitoring Pipeline | **5 SP** | M | 8--12 hrs |
| 12 | End-to-End Job Publication Test Plan | **5 SP** | M | 10--14 hrs |
| 13 | API Contract Tests for Publish Endpoint | **3 SP** | S | 5--6 hrs |

* * * * *

📌 **Effort Summary**
=====================

| Method | Total Effort |
| --- | --- |
| **Fibonacci Story Points** | **60 SP** |
| **T-Shirt Sizes Distribution** | XS: 2, S: 3, M: 4, L: 3 |
| **Estimated Hours** | **~110 -- 150 hours** |

* * * * *

🧠 **Analysis & Recommendations**
=================================

### ⭐ **1\. Fibonacci Story Points --- Best for Agile Predictability**

**Recommended if:**

-   Your team works in Scrum with Velocity tracking

-   You want to plan sprints probabilistically

-   Multiple roles collaborate (backend, frontend, integrations)

**Pros:**

-   Works well with uncertainty

-   Helps balance capacity across sprints

-   Encourages relative sizing rather than false precision

**Cons:**

-   Requires team maturity with story-pointing

-   Harder to correlate directly with delivery dates

**Recommendation:**\
**Use Fibonacci SP as your primary estimation model for sprint planning.**

* * * * *

### ⭐ **2\. T-Shirt Sizes --- Best for Early Stage or Cross-Team Alignment**

**Recommended if:**

-   The work is still in discovery or grooming

-   You need a high-level estimate across multiple teams

-   You want simplicity and fast estimation

**Pros:**

-   Very fast to use

-   Good for leadership or roadmap-level conversations

-   Easy for non-technical stakeholders to understand

**Cons:**

-   Too coarse for sprint planning

-   Cannot produce clear forecasts

**Recommendation:**\
Use T-Shirt sizes **during early scoping**, then convert to SP later.

* * * * *

### ⭐ **3\. Hour-Based Estimation --- Best for Deadline-Driven Work**

**Recommended if:**

-   A project manager needs calendar timelines

-   Work is highly deterministic and technical

-   There are contractual deliverables or SLAs

**Pros:**

-   Clear translation into deadlines

-   Works well for solo engineers or predictable domains

**Cons:**

-   Illusion of false precision

-   Slows down grooming

-   Not suitable for high uncertainty tasks (like integrations)

**Recommendation:**\
Use hour-based estimates **only for roadmap forecasting**, not sprint planning.

* * * * *

✅ **Final Recommendation (Most Effective Mix)**
===============================================

| Context | Recommended Estimation Method |
| --- | --- |
| **Sprint Planning** | **Fibonacci Story Points** |
| **Early Product/Tech Alignment** | **T-Shirt Sizes** |
| **Delivery Timelines / Budgeting** | **Hour Estimates** |

**In an ATS with job-board integrations, async workers, and event pipelines, Story Points (Fibonacci) provide the most sustainable accuracy and velocity tracking.**
