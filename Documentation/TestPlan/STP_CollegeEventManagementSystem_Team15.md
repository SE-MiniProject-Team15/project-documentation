# Software Test Plan (STP)

## College Event Management System (CEMS)

Team 15

Tech Stack: JavaScript (MERN Stack)

| **SRN** | **Name** |
| --- | --- |
| PES1UG24CS576 | Leesha R |
| PES1UG24CS593 | Prajwal Manjunath Hegde |
| PES1UG24CS570 | G Purvi |
| PES1UG24CS559 | Ashith Rao K |

## Document Control

| **Document Title** | Software Test Plan - College Event Management System |
| --- | --- |
| **Project** | College Event Management System (CEMS) |
| **Version** | 1.1 |
| **Authors** | Team 15 |
| **Date** | 02-10-2026 |
| **Status** | Draft |
| **Baseline documents** | SRS v1.2 (02 October 2026); SAD v1.0 (2 October 2026) |

## Revision History

| Version | Date | Author | Change Summary |
| --- | --- | --- | --- |
| 1.0 | 01 October 2026 | Team 15 | Initial draft |
| 1.1 | 02 October 2026 | Team 15 | Aligned with SRS v1.2 and SAD v1.0: corrected SAD cross-references (security architecture is SAD §3.8, JWT decision is ADR-005, risks are SAD §3.6); removed items not stated in the SRS/SAD (HSTS check, bcrypt cost factor); replaced the "Completed" event state with the SAD state model (adds Changes Requested); expanded §3 and §4 to cover NFR-01 to NFR-07 and SEC-01 to SEC-08 by ID; added test cases for FR-01.2, FR-01.5, FR-02.1, FR-02.3, FR-06.2 and FR-08.3 gaps (total now 95); added SAD component traceability (§13); fixed section and deliverable cross-references in §12 and §14; schedule milestone 1 aligned to document date |

## Table of Contents

1. Introduction
2. Test Items
3. Features to be Tested
4. Features Not to be Tested
5. Test Approach / Strategy (5.1 Security Validation, 5.2 Test Levels, 5.3 Test Types, 5.4 Design Techniques, 5.5 Entry Criteria, 5.6 Exit Criteria, 5.7 Defect Severity)
6. Test Environment
7. Test Schedule
8. Test Deliverables
9. Roles and Responsibilities
10. Risks and Mitigation
11. Assumptions and Dependencies
12. Suspension and Resumption Criteria
13. Test Case Management and Traceability (RTM, SAD component traceability)
14. Test Metrics and Reporting
15. Approvals

## 1. Introduction

**Purpose:** This document defines the test plan for the College Event Management System (CEMS) v1.0. It outlines objectives, scope, strategy, resources, schedule, and responsibilities for testing the system across all three user roles (Student, Organizer, Administrator).

**Scope:** Testing covers the full event lifecycle supported by CEMS — user registration/authentication, event creation and approval workflow, event browsing/search, event registration (RSVP), QR-based attendance tracking, notifications, and post-event feedback, together with the non-functional requirements (NFR-01 to NFR-07) and security requirements (SEC-01 to SEC-08) of SRS v1.2. Third-party email/SMTP delivery internals and any future SSO/college-ID integration are excluded, as these are out of scope for v1 per the SRS.

**References:** Software Requirements Specification - CEMS (Team 15, v1.2, 02 October 2026); Software Architecture and Design Specification - CEMS (Team 15, v1.0, 2 October 2026); Test Case Document - CEMS (Team 15, companion to this plan); IEEE Std 829-2008 (software test documentation); IEEE Std 830-1998; Team 15 Project Charter / Problem Statement.

**Definitions:** CEMS (College Event Management System), SRS (Software Requirements Specification), FR (Functional Requirement), NFR (Non-Functional Requirement), JWT (JSON Web Token), RSVP (event registration response), QR Code (Quick Response Code used for attendance check-in), RTM (Requirements Traceability Matrix), SAD (Software Architecture and Design Specification), SEC (security requirement ID in SRS §3.3), RBAC (Role-Based Access Control), TLS (Transport Layer Security).

## 2. Test Items

- User Registration and Authentication module (Student / Organizer / Admin)
- Event Creation and Management module (Organizer)
- Event Approval Workflow module (Admin)
- Event Browsing, Search and Filtering module (Student)
- Event Registration / RSVP module (Student)
- Attendance Tracking (QR Check-in) module (Organizer)
- Notification System (email + in-app)
- Feedback and Rating System (Student)

These eight items correspond one-to-one to the components in SAD §3.3 (Authentication and RBAC, Event Management, Event Approval, Event Browsing, Registration / RSVP, Attendance, Notification, Feedback). The React front end, the Express REST API (SAD §4.4) and the MongoDB data layer are exercised through these modules rather than as separate test items.

## 3. Features to be Tested

Features mapped to SRS functional requirement IDs:

- FR-01: User Registration and Authentication (self-registration of Student/Organizer, pre-provisioned Admin, password policy, JWT sessions, forgot-password link valid for 30 minutes, 15-minute lockout after 5 failed attempts, generic login errors)
- FR-02: Event Creation and Management (draft/submit, edit/cancel, re-approval after a material edit to an approved event, future-date validation, venue double-booking check)
- FR-03: Event Approval Workflow (Admin approve/reject/request-changes, resubmission of rejected events, approval audit log)
- FR-04: Event Browsing, Search and Filtering (keyword search, category/date/venue/department filters, sorting)
- FR-05: Event Registration / RSVP (capacity enforcement, waitlist, duplicate-registration prevention, QR confirmation, cancellation)
- FR-06: Attendance Tracking / QR Check-in (scan/manual entry, invalid/reused/out-of-window code rejection, real-time dashboard update)
- FR-07: Notification System (registration confirmation, approval/rejection, updates/cancellations, 24-hour reminders, notification history)
- FR-08: Feedback and Rating System (post-event-only availability, attendee-only access, 1–5 rating with comment, aggregated view for Organizer/Admin)
- NFR-01 Performance: Event listing loads within 3s under 200 concurrent users; registration processed within 2s
- NFR-02 and SEC-01 to SEC-08 Security: HTTPS/TLS, bcrypt password hashing, role-based access control, lockout and rate limiting, JWT cookie handling, input validation / XSS, single-use unguessable registration codes, append-only audit log
- NFR-03 Usability: Responsive layout on desktop, tablet and mobile; keyboard operability
- NFR-04 Reliability: Daily backups and a working restore procedure (the 99% availability figure is monitored during the semester; it cannot be demonstrated within one test cycle)
- NFR-07 Portability: Correct functioning on latest two major versions of Chrome, Firefox, Edge, Safari

## 4. Features Not to be Tested

- Third-party email/SMTP provider internals (e.g., SendGrid/Nodemailer delivery infrastructure) — only the trigger and logging behavior on CEMS's side is tested
- Future SSO / college-ID integration — explicitly out of scope for v1 per SRS §2.1
- Payment gateway integration for paid events — explicitly out of scope for v1 per SRS §2.6
- Physical QR scanner hardware — only the manual code entry fallback and scan-result handling within CEMS are tested
- Underlying database engine reliability (MongoDB internals) — assumed stable per platform vendor
- Live venue/facility-booking integration — not part of v1 per SRS §2.6 (venues and capacities are entered manually)
- A general user-management UI for Administrators — not provided in v1 per SRS §2.3
- Future enhancements listed in SAD §5 (mobile app, calendar integration, push notifications, advanced analytics)
- NFR-05 Scalability (horizontal scaling) — no multi-instance deployment test is planned; only the load results of `NFR_PERF_01` and `NFR_PERF_02` are used as indirect evidence
- NFR-06 Maintainability — verified by code and design review against SAD §3.1 (layered architecture) and §4.4 (documented REST APIs), not by runtime test cases

## 5. Test Approach / Strategy

Testing follows a risk-based, requirement-driven approach. Every SRS requirement (FR-01 to FR-08 and the NFRs listed in Section 3) is covered by at least one test case, and higher-risk areas (concurrent registration, one-time QR check-in, role-based access, notification delivery) receive deeper coverage. The test cases in the Test Case document use the naming convention `<FR_ID>_<UT|IT|ST>_<Seq>`; non-functional cases use `NFR_<AREA>_<Seq>` (performance, security, usability, reliability and portability cases, all defined in Section 4 of the Test Case document; the security checks are specified in 5.1 below).

### 5.1 Security Validation

Security validation verifies the security objectives and requirements in SRS §3.3 (SO-1 to SO-4, SEC-01 to SEC-08), the related requirements FR-01 and NFR-02, and the security controls listed in SAD §3.8 (Security Architecture). Existing functional cases that also cover security behaviour are FR01_IT_03 (lockout), FR01_ST_02 (password reset), FR01_ST_03 (Student blocked from Admin routes), FR01_IT_04 (no self-registration as Admin) and FR03_IT_03 (approval queue restricted to Admin).

| ID | Area | Validation check | Method / tool | Expected result | Traces to |
| --- | --- | --- | --- | --- | --- |
| NFR_SEC_01 | Transport security | All pages and API calls are served over HTTPS; HTTP requests are redirected; TLS 1.2 or higher only; old protocol versions rejected | Browser inspection; SSL Labs or `nmap --script ssl-enum-ciphers`; curl | No content served over plain HTTP; TLS 1.0/1.1 refused | NFR-02, SEC-01 |
| NFR_SEC_02 | Password storage | Passwords are stored only as bcrypt hashes (per SRS FR-01.2 and SEC-02); plain text never appears in the database, API responses or logs | Inspect `users` collection; review logs and network responses | Only hashes stored; no password or hash returned by any endpoint | FR-01.2, SEC-02 |
| NFR_SEC_03 | Authentication and lockout | 5 consecutive failed logins lock the account for 15 minutes (per SRS FR-01.5 and SEC-04); error text is generic and does not reveal whether the email exists; login is rate limited | Manual; Postman/Jest loop | Lockout message on the 6th attempt; identical error for unknown email and wrong password; HTTP 429 on flooding | FR-01.5, SEC-04 (FR01_IT_03, FR01_IT_05) |
| NFR_SEC_04 | Session and token handling | JWT is in an httpOnly, Secure, SameSite=Strict cookie (per SRS SEC-05 and SAD ADR-005); tampered, expired or unsigned tokens are rejected; logout invalidates the session on the client | Postman; browser dev tools; edit token payload | HTTP 401 for altered or expired token; token not readable by JavaScript | SEC-05 |
| NFR_SEC_05 | Role-based access control | Each protected endpoint and page is attempted by an unauthenticated user, a Student, an Organizer and an Admin; an Organizer cannot edit or check in for another Organizer's event; a Student cannot approve events or read attendance data | Role-permission matrix run through Postman and UI | Forbidden actions return HTTP 401/403 with no data exposed; role cannot be changed through a request body | SEC-03 (FR01_ST_03, FR03_IT_03) |
| NFR_SEC_06 | Password reset | Reset link works once, expires after its validity period, and the reset request does not disclose whether the email is registered | Manual; Postman | Second use of the link and an expired link are rejected; same response for known and unknown emails | FR-01.4 (FR01_ST_02) |
| NFR_SEC_07 | Input validation and injection | NoSQL operator injection (e.g. `{"$ne": ""}` in login), stored and reflected XSS in event title, description and feedback comment, oversized inputs, malformed JSON | Manual payloads; OWASP ZAP active scan; fuzzing of form fields | Inputs rejected or safely escaped; no script execution; no server error stack trace | SEC-06 |
| NFR_SEC_08 | Registration code (QR) abuse | Code reuse, code from another event, cancelled-registration code, guessed sequential codes, code used by a non-owner | Manual; Postman script trying 100 random codes | Reused, foreign or invalid codes rejected; codes are unpredictable; excessive attempts are rate limited | SEC-07, FR-06.2 (FR06_UT_02, FR06_UT_03) |
| NFR_SEC_09 | Data exposure | Error responses contain no stack traces or internal details; feedback shown to Organizer does not reveal student identity; a Student can read only their own registrations and notifications | Manual; Postman with another user's IDs | Access to another user's data is denied; generic error messages in production mode | FR-07, FR-08.3, SAD §4.5 (FR08_IT_04) |
| NFR_SEC_10 | Cross-site request forgery and headers | State-changing requests from a foreign origin are rejected; security headers present (Content-Security-Policy, X-Content-Type-Options, X-Frame-Options) | Browser check; OWASP ZAP baseline | Cross-origin POST fails; required headers present | NFR-02 |
| NFR_SEC_11 | Audit trail | Admin approval decisions and check-ins are recorded with actor and timestamp and cannot be edited through the UI or API | Manual review of audit log | Every decision has an entry; no edit or delete endpoint exists | FR-03.5, SEC-08 (FR03_ST_03) |
| NFR_SEC_12 | Dependency and configuration hygiene | Known-vulnerable dependencies and exposed secrets are detected; environment secrets not present in the repository or client bundle | `npm audit`; secret scan; review of built bundle | No Critical or High vulnerabilities unresolved; no secrets in source or bundle | NFR-02 |

**Note on NFR_SEC_11:** SRS FR-03.5 and SEC-08 require approval decisions and attendance check-ins to be recorded in an append-only audit log. The formal test case is `NFR_SEC_11` in the Test Case document (Section 4), alongside `FR03_ST_03`.

Any Critical or High finding from NFR_SEC_01 to NFR_SEC_12 is logged as a defect and must be closed before the exit criteria in 5.6 can be met.

### 5.2 Test Levels

| Level | Code | Scope | Performed by | Environment |
| --- | --- | --- | --- | --- |
| Unit | UT | Single function, validator, service method or UI component in isolation (e.g. password policy validator, capacity check, rating range check) | Developers with QA review | Local / CI with mocked database and email |
| Integration | IT | Interaction between modules and layers: UI to REST API, API to MongoDB, Registration to Notification, Attendance to Feedback eligibility | QA and developers | Test environment with real test database and email sandbox |
| System | ST | End-to-end user journeys across all three roles through the browser, plus non-functional checks on the deployed build | QA | Deployed test environment |
| Acceptance (UAT) | UAT | Business-level walkthrough of the event lifecycle against the SRS acceptance intent (create, approve, register, check in, give feedback) | QA lead with a Student, an Organizer and an Admin representative | Staging-like environment |

### 5.3 Test Types

| Type | What is verified | Requirements | Technique / tool |
| --- | --- | --- | --- |
| Functional | Each FR behaves as specified, including validation messages and role restrictions | FR-01 to FR-08 | Manual test cases; Jest/Supertest for API; Cypress for UI flows |
| Regression | Previously passing cases still pass after fixes or new features | All | Automated API and UI smoke suite run on every build |
| Performance and load | Listing page under 3 s and registration under 2 s at up to 200 concurrent users; behaviour under a 300-recipient notification burst | NFR-01, FR-07 | k6 scripts; browser timings |
| Security | Authentication, RBAC, TLS, password storage, input handling, session and code abuse — see 5.1 | FR-01, NFR-02 | Manual checks, Postman, OWASP ZAP |
| Usability and accessibility | Responsive layout on desktop, tablet and mobile; keyboard use; contrast and labels (WCAG 2.1 AA where feasible); a first-time user can register for an event without help | NFR-03 | Manual walkthrough; Lighthouse and axe |
| Compatibility / portability | Correct behaviour on the latest two major versions of Chrome, Firefox, Edge and Safari | NFR-07 | Cross-browser run of the smoke suite and key journeys |
| Reliability / recovery | Availability checks, graceful behaviour when email service is down, backup restore works | NFR-04, FR-07 | Health-check monitoring, fault injection, restore drill |
| Concurrency | Last-seat race, simultaneous check-in of the same code, duplicate submit | FR-05, FR-06 | Parallel API calls (k6 / Jest with Promise.all) |

**API test scope (SAD §4.4 and §4.5).** API tests cover the authentication, event, approval, registration, attendance and feedback endpoints listed in SAD §4.4, and assert the standard status codes (400, 401, 403, 404, 409, 429, 500) and the JSON error format defined in SAD §4.5. Error responses must not expose passwords, JWTs, credentials or stack traces.

### 5.4 Test Design Techniques

- **Equivalence partitioning and boundary value analysis:** password length (7, 8 characters), rating (0, 1, 5, 6), capacity (0, 1, N, N+1), registration deadline (before, at, after), account lockout (4th, 5th, 6th failed attempt), check-in window (before start, at start, at end, after end).
- **State transition testing:** the event lifecycle Draft, Pending Approval, Approved, Rejected, Changes Requested and Cancelled (SAD §3.3.2; a concluded event is an Approved event whose end time has passed), and the registration states Confirmed, Waitlisted, Cancelled and Checked-in (Present).
- **Role-permission matrix testing:** every protected function is attempted by each of the three roles and by an unauthenticated user.
- **Error guessing:** expired or reused reset links, code from another event, cancelled registration code, past-dated events, event edited after approval.

### 5.5 Entry Criteria

| Level | Entry criteria |
| --- | --- |
| Unit | Code for the unit committed; unit test skeleton reviewed against the SRS |
| Integration | Unit tests for the modules passing; modules merged to the integration branch; test database and email sandbox available |
| System | Stable build deployed to the test environment; smoke suite passing; test data loaded; test cases reviewed and baselined |
| UAT | System testing exit criteria met; no open critical or high defects; UAT accounts and data prepared |

### 5.6 Exit Criteria

- 100% of planned test cases for the cycle executed.
- At least 95% of executed functional test cases passed, and every failed case has a logged defect.
- 0 open Critical and 0 open High defects; Medium defects have an agreed fix or documented workaround.
- 100% of FR-01 to FR-08 covered by at least one passing test (requirement coverage tracked in the RTM).
- Performance targets met: listing page response within 3 s and registration within 2 s at 200 concurrent users.
- All security validation checks in 5.1 executed with no open Critical or High findings.
- Cross-browser smoke run passed on all four browsers.
- UAT sign-off received from the QA lead and the acceptance representatives.

### 5.7 Defect Severity Definitions

| Severity | Definition | Example in CEMS |
| --- | --- | --- |
| Critical | System unusable, data loss, or security breach | Student can access Admin approval queue; seat count goes negative |
| High | Major feature broken with no workaround | Registration fails; QR code cannot be validated |
| Medium | Feature partly broken or workaround exists | Filter returns wrong subset; email not sent but in-app notification works |
| Low | Cosmetic or minor usability issue | Misaligned label; wording inconsistent with the expected message |

## 6. Test Environment

### 6.1 Hardware

| Item | Specification |
| --- | --- |
| Application server (test) | Linux VM or container host, 2 vCPU, 4 GB RAM (runs Node.js API, Nginx and MongoDB for functional testing) |
| Load-test server | Separate machine with equal or greater capacity to generate up to 200 virtual users without becoming the bottleneck |
| Client devices | Desktop or laptop (Windows and macOS), one Android phone, one iPhone or iOS simulator, one tablet or tablet-size browser viewport |
| Camera device | Phone or laptop with a working camera for QR scan tests; one device with camera access denied to test the manual-entry fallback |

### 6.2 Software

| Item | Details |
| --- | --- |
| Application under test | CEMS v1.0 build (React front end, Node.js/Express REST API), version recorded in each test cycle report |
| Database | MongoDB test database seeded with test data; a separate database for load testing so functional data is not affected |
| Web server | Nginx reverse proxy with a test TLS certificate so HTTPS behaviour matches production |
| Email | Email sandbox (e.g. Mailtrap or MailHog) so confirmation, approval, cancellation and reminder emails can be inspected without reaching real users; a switch to simulate SMTP failure (FR07_UT_03) |
| Time control | Ability to move the clock or seed event times so that the deadline, event start, event end, 24-hour reminder and feedback-window cases can be tested without waiting |
| Version control and CI | GitHub with automated unit and API tests on each push |

### 6.3 Browsers and Platforms

| Browser | Versions | Platform |
| --- | --- | --- |
| Google Chrome | Latest two major versions | Windows, macOS, Android |
| Mozilla Firefox | Latest two major versions | Windows, macOS |
| Microsoft Edge | Latest two major versions | Windows |
| Apple Safari | Latest two major versions | macOS, iOS (real device, simulator or a cloud device service) |

Full functional journeys run on Chrome. Smoke suite plus layout and QR-scan checks run on all other browsers. Viewports tested: desktop (1366 x 768 and above), tablet (768 x 1024) and mobile (375 x 667).

### 6.4 Test Tools

| Purpose | Tool |
| --- | --- |
| Unit testing (backend and frontend) | Jest, React Testing Library |
| API testing | Postman (collections and environments), Supertest |
| UI / end-to-end automation | Cypress |
| Performance and load | k6 |
| Security testing | OWASP ZAP, browser dev tools, `npm audit`, SSL Labs or nmap for TLS |
| Accessibility and usability | Lighthouse, axe DevTools |
| Cross-browser | Local browsers plus a cloud device service for Safari and iOS if no Apple device is available |
| Defect tracking and test management | GitHub Issues; test cases maintained as the Test Case document and RTM |
| Monitoring during tests | Health-check endpoint and server logs; response time captured from k6 reports |

### 6.5 Test Data

**User accounts** (dummy data only; no real student information is used):

| Account | Role | Purpose |
| --- | --- | --- |
| student01 to student05 (e.g. student01@college.edu, Password Pass@123) | Student | Registration, waitlist, cancellation, feedback, concurrency cases (two students racing for the last seat) |
| student_locked | Student | Pre-locked account for lockout messages |
| student_absent | Student | Registered but never checked in, for feedback-denied cases |
| organizer01, organizer02 | Organizer | Event creation, ownership checks (organizer02 must not manage organizer01's events), check-in |
| admin01 | Administrator | Pre-provisioned; approval queue, audit log, reports |
| newuser@college.edu | None yet | Fresh registration and duplicate-email cases |

**Event data set** (loaded before each system-test cycle):

| Event | State / condition | Used for |
| --- | --- | --- |
| Tech Fest 2026 | Approved, capacity 100, 40 registered | Search, seat count display, registration |
| Annual Sports Meet | Approved, 3 registered students | Cancellation and notification |
| Cultural Night 2026 | Pending approval | Approve and reject flows |
| Alumni Meet 2026 | Draft | Draft-not-in-queue check |
| Small Workshop | Approved, capacity 2 | Capacity limit and waitlist |
| Last Seat Event | Approved, exactly 1 seat left | Concurrent registration race (FR05_ST_02) |
| Closed Registration Event | Approved, deadline in the past | Registration closed |
| Guest Lecture Series | Concluded, 0 feedback | Empty feedback report |
| Large Event | Approved, 300 registered students | Bulk notification (FR07_ST_02) and load tests |
| Venue Clash Event | Approved, Auditorium 10:00 to 12:00 | Double-booking detection |

**Other data:** events across at least four categories (Technical, Cultural, Sports, Workshop), several departments and venues, dates spread across past, current and future months, and 300 dummy registrations for the load scenarios. Test data is reset by a seed script before each cycle so that results are repeatable.

### 6.6 Environment Set-up Responsibilities

- The test environment is provisioned and the seed script verified before test execution starts (dates are in Section 7).
- A backup environment (cloud VM or container image) is kept available in case the primary test environment is down.
- Configuration (secrets, SMTP sandbox credentials, database URI) is held in environment variables and never committed to the repository.

## 7. Test Schedule

The schedule follows the test levels in order (unit, integration, system, acceptance). No execution starts before the test environment and test data are ready. UAT is last because it needs a stable build.

| # | Milestone | Date / Duration | Owner |
| --- | --- | --- | --- |
| 1 | Test plan and test case design (78 functional cases + 17 non-functional cases = 95 total) | Completed by 02-Oct-2026 | QA Lead |
| 2 | Test environment and test data setup | 05-Oct-2026 | QA Lead / Developer |
| 3 | Unit and integration test execution | 06-Oct-2026 to 14-Oct-2026 | Test Engineers |
| 4 | System test execution (functional and non-functional) | 15-Oct-2026 to 22-Oct-2026 | Test Engineers |
| 5 | Regression testing and defect retest | 23-Oct-2026 to 26-Oct-2026 | Test Engineers / Developer |
| 6 | User Acceptance Testing (UAT) | 27-Oct-2026 to 29-Oct-2026 | Product Owner |
| 7 | Test Summary Report | 30-Oct-2026 | QA Lead |

> Note: Dates after 02-Oct-2026 are planned dates and may be adjusted to match the course calendar for the implementation phase.

## 8. Test Deliverables

The testing effort produces the following deliverables for the College Event Management System (CEMS):

- **Test Plan**: this document.
- **Test Cases**: 78 functional cases (unit, integration and system levels, with IDs such as FR01_UT_01, FR03_ST_03) and 17 non-functional cases covering performance, security, usability, reliability and portability.
- **Test Scripts**: automated API tests (Postman/Jest) and UI tests (Cypress).
- **Test Data**: dummy Student, Organizer and Admin accounts; sample events in each state (pending, approved, rejected, full); sample registrations, waitlist entries and feedback.
- **Requirements Traceability Matrix (RTM)**: maps FR-01 to FR-08 and the non-functional requirements to test cases.
- **Test Execution Logs**: pass/fail record for each test case in each run.
- **Defect Reports**: logged in GitHub Issues with severity, priority and status.
- **Test Summary Report**: pass rate, open defects, requirement coverage and sign-off recommendation.

## 9. Roles and Responsibilities

| Role | Name | Responsibility |
| --- | --- | --- |
| QA Lead | Ashith Rao K | Prepares the test plan, coordinates test execution, tracks metrics, owns the Test Summary Report |
| Test Engineer (Functional) | Leesha R | Designs and executes functional test cases, logs defects, maintains the RTM for FR-01 to FR-08 |
| Test Engineer (Security and API) | G Purvi | Designs and executes security and API tests (RBAC, login lockout, TLS, input validation), logs defects |
| Developer | Prajwal Manjunath Hegde | Fixes defects, supports defect triage, maintains the test build and environment |
| Product Owner | Prajwal Manjunath Hegde (Team Lead) / Course Faculty | Approves test results and confirms release readiness after UAT |

> Note: In a four-member team, one person may hold more than one role.

## 10. Risks and Mitigation

| # | Risk | Likelihood | Impact | Mitigation |
| --- | --- | --- | --- | --- |
| 1 | Delay in stable build delivery blocks test execution | Medium | High | Request early smoke builds from the Developer; keep the fixed test-build schedule in Section 7 |
| 2 | Concurrent-registration race condition causes seat count to go negative or double-books the last seat | Medium | Critical | Covered by `FR05_ST_02`; confirm with the Developer that the seat decrement and capacity check are atomic (SAD §3.6, registration-spike risk) before sign-off; re-run under higher concurrency if the first result is marginal |
| 3 | Waitlist-promotion logic fails silently (student not notified, or two students promoted for one seat) | Medium | High | Covered by `FR05_IT_04`; verify the promotion and capacity check run as a single atomic operation |
| 4 | QR scan reliability: camera access denied, poor lighting, or scanner hardware unavailable at the venue | Medium | High | `FR06_IT_01` covers the manual-entry fallback; test on at least one device with camera access deliberately denied |
| 5 | Email delivery delay or failure (SMTP sandbox down, provider rate limit) | Medium | Medium | Covered by `FR07_UT_03`; in-app notification must still succeed even if email fails |
| 6 | Venue double-booking logic produces false positives/negatives across overlapping time ranges | Low | Medium | Add boundary-value cases for back-to-back bookings (event ends exactly when the next starts) during integration testing, beyond the base case in `FR02_IT_02` |
| 7 | A user bypasses the UI and forces role "Admin" via a direct API call | Low | Critical | Covered by `FR01_IT_04`; confirm server-side validation rejects it independently of the UI's role selector |
| 8 | Load-testing infrastructure (k6) cannot generate 200 concurrent users from the available machines | Low | Medium | Use the separate load-test server (Section 6.1); scale down to a documented lower user count only if infrastructure is confirmed insufficient, and note the deviation in the Test Summary Report |
| 9 | Safari/iOS testing blocked by no available Apple device | Medium | Low | Use a cloud device service (Section 6.4) as a fallback |
| 10 | Audit log tampering or accidental deletion undermines FR-03.5/SEC-08 accountability guarantee | Low | High | `FR03_ST_03` and `NFR_SEC_11` verify no edit/delete path exists; re-verify after any schema change |
| 11 | Test environment downtime during the execution window | Medium | Medium | Maintain a backup environment (cloud VM or container image), per Section 6.6 |
| 12 | Scope creep: features added after Part-1 freeze (e.g. SSO) are untested | Low | Medium | Any post-freeze feature requires new test cases and an RTM update before it is considered test-complete |
| 13 | SRS or SAD is revised after this plan is baselined (as happened between SRS v1.0 and v1.2), leaving test cases out of date | Medium | Medium | Re-review the RTM whenever an SRS or SAD version changes; record the SRS/SAD versions tested in every cycle report |

## 11. Assumptions & Dependencies

- The application build delivered for testing matches SAD v1.0 (component boundaries, API contracts in §4.4) and SRS v1.2.
- The test and load-test databases, the email sandbox, and the TLS test certificate (Section 6.2) are available and stable for the full execution window.
- Test data (dummy accounts, the 10-event data set, 300 dummy registrations) is seeded by the script in Section 6.5 before each test cycle.
- The course does not require testing of SSO/college-ID integration, since the SRS (§2.1) marks it as a future release, out of scope for Part-1.
- Payment integration remains out of scope per SRS §2.6, so no payment test cases are planned.
- Developers resolve Critical and High defects within the cycle so the exit criteria (Section 5.6) can be met before the deadline.
- Team members have access to the devices and browsers listed in Section 6.1 and 6.3 for cross-platform and QR testing.

## 12. Suspension & Resumption Criteria

**Suspend testing if:**
- The test environment is unavailable for more than 4 hours.
- The build is too unstable to proceed — more than 30% of planned test cases are blocked.
- A Critical defect (Section 5.7) is found that prevents further meaningful testing of a module (e.g. registration is completely broken, blocking all FR-05/FR-06/FR-08 cases).
- The seed script fails and test data cannot be reliably reset between cycles.

**Resume testing if:**
- The blocking defect(s) are fixed and verified in a new build.
- The test environment is restored and the seed script runs successfully.
- The QA Lead confirms, with the Developer, that the build is stable enough to continue.

Any suspension and its reason are logged in the Test Execution Log (Section 8, deliverable 6) so the gap is visible in the final Test Summary Report.

## 13. Test Case Management & Traceability (RTM)

The RTM maps each SRS requirement to its test case(s). Every requirement now has at least one corresponding test case; no open gaps remain.

### Functional Requirements

| SRS ID | Requirement (summary) | Test Case IDs |
| --- | --- | --- |
| FR-01.1 | Registration with valid/invalid fields, duplicate email | `FR01_UT_01`, `FR01_UT_02`, `FR01_IT_01` |
| FR-01.2 | Password policy, hashing | `FR01_UT_03`, `FR01_UT_04`, `NFR_SEC_02` |
| FR-01.3 | Admin accounts pre-provisioned, not self-registered | `FR01_IT_04` |
| FR-01.4 | Login, logout, forgot password, JWT | `FR01_IT_02`, `FR01_ST_01`, `FR01_ST_02` |
| FR-01.5 | Lockout after 5 failed attempts, generic login error | `FR01_IT_03`, `FR01_IT_05`, `NFR_SEC_03` |
| FR-02.1–02.2 | Event fields, draft save | `FR02_UT_01`, `FR02_UT_03`, `FR02_UT_04`, `FR02_IT_01`, `FR02_ST_01` |
| FR-02.3 | Edit, cancel, material-edit re-approval | `FR02_IT_03`, `FR02_IT_04`, `FR02_ST_02` |
| FR-02.4 | Future date, venue double-booking | `FR02_UT_02`, `FR02_IT_02` |
| FR-03.1–03.2 | Approval queue, Approve/Reject/Request Changes | `FR03_UT_01`, `FR03_UT_02`, `FR03_UT_03`, `FR03_UT_04`, `FR03_ST_01`, `FR03_ST_02` |
| FR-03.3 | Decision notification | `FR03_IT_01` |
| FR-03.4 | Resubmission after reject/changes requested | `FR03_IT_02` |
| FR-03.5 | Audit log | `FR03_ST_03`, `NFR_SEC_11` |
| FR-04.1 | Keyword search, category filter, date range | `FR04_UT_01`, `FR04_UT_02`, `FR04_UT_03`, `FR04_IT_01`, `FR04_ST_01`, `FR04_ST_02` |
| FR-04.1 | Venue filter, department filter | `FR04_IT_04` |
| FR-04.2 | Sort by date, sort by popularity | `FR04_UT_04`, `FR04_IT_02` |
| FR-04.3 | Seat count on detail page | `FR04_IT_03` |
| FR-05.1 | Registration, capacity enforcement | `FR05_UT_01`, `FR02_ST_03` |
| FR-05 | Registration deadline enforced | `FR05_UT_03` |
| FR-05.1 | Waitlist offered when full | `FR05_IT_01` |
| FR-05.1 | Waitlist auto-promotion on cancellation | `FR05_IT_04` |
| FR-05.2 | Duplicate registration blocked | `FR05_UT_02` |
| FR-05.3 | QR/code generation | `FR05_ST_01` |
| FR-05.4 | View/cancel own registration | `FR05_IT_02`, `FR05_IT_03` |
| — | Concurrency on last seat | `FR05_ST_02` |
| FR-06.1 | Scan / manual entry | `FR06_UT_01`, `FR06_IT_01` |
| FR-06.2 | Reused / invalid / out-of-window code | `FR06_UT_02`, `FR06_UT_03`, `FR06_IT_03`, `FR06_IT_04` |
| FR-06.3 | Real-time attendance dashboard | `FR06_IT_02`, `FR06_ST_02` |
| — | Attendance feeds feedback eligibility | `FR06_ST_01` |
| FR-07.1 | Confirmation, decision, reminder, cancellation notifications | `FR07_UT_01`, `FR07_UT_02`, `FR07_IT_01`, `FR07_IT_02` |
| FR-07.2 | Notification history | `FR07_IT_03` |
| FR-07.3 | Failed email logged, action not blocked | `FR07_UT_03` |
| — | Full lifecycle notification consistency, mass-notification load | `FR07_ST_01`, `FR07_ST_02` |
| FR-08.1 | Feedback available only post-event, only to attendees | `FR08_UT_01`, `FR08_IT_01` |
| FR-08.2 | Rating range, one submission per student | `FR08_UT_02`, `FR08_UT_03`, `FR08_IT_02` |
| FR-08.3 | Aggregated ratings, anonymised comments | `FR08_IT_03`, `FR08_IT_04`, `FR08_ST_01`, `FR08_ST_02` |

### Non-Functional and Security Requirements

| SRS ID | Requirement (summary) | Test Case IDs |
| --- | --- | --- |
| NFR-01 | Listing ≤ 3s, registration ≤ 2s at 200 users | `NFR_PERF_01`, `NFR_PERF_02` |
| NFR-02 / SEC-01 | HTTPS/TLS 1.2+ | `NFR_SEC_01` |
| SEC-02 | Password hashing, never logged | `NFR_SEC_02` |
| SEC-03 | RBAC on every endpoint | `NFR_SEC_05`, `FR01_ST_03`, `FR03_IT_03` |
| SEC-04 | Lockout and rate-limiting | `NFR_SEC_03`, `FR01_IT_03` |
| SEC-05 | JWT in httpOnly/Secure/SameSite cookie | `NFR_SEC_04` |
| SEC-06 | Input validation / injection / XSS | `NFR_SEC_07` |
| SEC-07 | Unique, single-use, unguessable QR codes | `NFR_SEC_08`, `FR06_UT_02`, `FR06_UT_03` |
| SEC-08 | Append-only audit log | `FR03_ST_03`, `NFR_SEC_11` |
| — | Password reset link single-use/expiring | `NFR_SEC_06`, `FR01_ST_02` |
| — | Data exposure (own-data-only access) | `NFR_SEC_09` |
| — | CSRF and security headers | `NFR_SEC_10` |
| — | Dependency/config hygiene | `NFR_SEC_12` |
| NFR-03 | Responsive, accessible, no-training usability | `NFR_USA_01` |
| NFR-04 | 99% availability, backup/restore | `NFR_REL_01` (restore); the 99% figure is monitored in staging, not provable within one test cycle |
| NFR-05 | Horizontal scalability | Not directly tested; inferred from `NFR_PERF_01`/`02` under load |
| NFR-06 | Modular layered architecture, documented REST APIs | Verified by code/design review, not a runtime test case |
| NFR-07 | Latest two versions of Chrome/Firefox/Edge/Safari | `NFR_PORT_01` |

**Coverage status: complete.** All functional and non-functional/security requirements in SRS v1.2 trace to at least one test case or a documented verification method (NFR-05 and NFR-06 are verified by review, see Section 4).

### SAD Component Traceability

| SAD component / design element | Test Case IDs |
| --- | --- |
| §3.3.1 Authentication and RBAC | `FR01_*`, `NFR_SEC_02` to `NFR_SEC_06` |
| §3.3.2 Event Management | `FR02_*` |
| §3.3.3 Event Approval | `FR03_*`, `NFR_SEC_11` |
| §3.3.4 Event Browsing | `FR04_*` |
| §3.3.5 Registration / RSVP | `FR05_*` |
| §3.3.6 Attendance | `FR06_*`, `NFR_SEC_08` |
| §3.3.7 Notification | `FR07_*` |
| §3.3.8 Feedback | `FR08_*` |
| §4.4 API design, §4.5 error handling | `FR01_IT_01`, `FR01_IT_04`, `NFR_SEC_09`, `NFR_SEC_10` |
| §4.6 Logging and monitoring (no passwords/JWTs in logs) | `NFR_SEC_02`, `FR07_UT_03`, `NFR_SEC_11` |
| §3.8 Security architecture | `NFR_SEC_01` to `NFR_SEC_12` |

## 14. Test Metrics & Reporting

**Metrics collected during execution:**

| Metric | Definition | Target |
| --- | --- | --- |
| % test cases executed | Executed ÷ planned (95 total) | 100% by Section 7, milestone 6 (UAT) |
| % passed / failed | Pass ÷ executed, Fail ÷ executed | ≥ 95% functional pass rate (Section 5.6) |
| Defect density | Defects ÷ number of FRs/NFRs | Tracked per FR for hotspot analysis |
| Defect aging | Days a defect stays open, by severity | Critical/High closed within the cycle |
| Requirement coverage | SRS requirements with ≥ 1 passing test ÷ total requirements (via the RTM in Section 13) | 100% — already at full coverage by design; execution must confirm all pass |
| Security findings | Open Critical/High findings from the 12 Section 5.1 checks | 0 open before sign-off |

**Reporting:**
- **Daily execution status** during Section 7 milestones 3–7: cases run, pass/fail count, new defects, by the QA Lead.
- **Final Test Summary Report** (Section 8, deliverable 8): overall pass rate, defect summary by severity, RTM coverage snapshot, exit-criteria checklist, and a recommendation to proceed to or hold release.

## 15. Approvals

| Role | Name | Signature / Date |
| --- | --- | --- |
| QA Lead | Ashith Rao K | |
| Developer | Prajwal Manjunath Hegde | |
| Product Owner | Prajwal Manjunath Hegde (Team Lead) / Course Faculty | |
| Security Test Engineer | G Purvi | |
| Test Engineer | Leesha R | |
