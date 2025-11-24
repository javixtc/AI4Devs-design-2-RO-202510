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
