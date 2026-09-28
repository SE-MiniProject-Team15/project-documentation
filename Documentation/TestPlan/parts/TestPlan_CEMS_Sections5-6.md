## 5. Test Approach / Strategy

Testing follows a risk-based, requirement-driven approach. Every SRS requirement (FR-01 to FR-08 and the NFRs listed in Section 3) is covered by at least one test case, and higher-risk areas (concurrent registration, one-time QR check-in, role-based access, notification delivery) receive deeper coverage. The test cases in the Test Case document use the naming convention `<FR_ID>_<UT|IT|ST>_<Seq>`; non-functional cases use `NFR_<AREA>_<Seq>`.

### 5.1 Test Levels

| Level | Code | Scope | Performed by | Environment |
| --- | --- | --- | --- | --- |
| Unit | UT | Single function, validator, service method or UI component in isolation (e.g. password policy validator, capacity check, rating range check) | Developers with QA review | Local / CI with mocked database and email |
| Integration | IT | Interaction between modules and layers: UI to REST API, API to MongoDB, Registration to Notification, Attendance to Feedback eligibility | QA and developers | Test environment with real test database and email sandbox |
| System | ST | End-to-end user journeys across all three roles through the browser, plus non-functional checks on the deployed build | QA | Deployed test environment |
| Acceptance (UAT) | UAT | Business-level walkthrough of the event lifecycle against the SRS acceptance intent (create, approve, register, check in, give feedback) | QA lead with a Student, an Organizer and an Admin representative | Staging-like environment |

### 5.2 Test Types

| Type | What is verified | Requirements | Technique / tool |
| --- | --- | --- | --- |
| Functional | Each FR behaves as specified, including validation messages and role restrictions | FR-01 to FR-08 | Manual test cases; Jest/Supertest for API; Cypress for UI flows |
| Regression | Previously passing cases still pass after fixes or new features | All | Automated API and UI smoke suite run on every build |
| Performance and load | Listing page under 3 s and registration under 2 s at up to 200 concurrent users; behaviour under a 300-recipient notification burst | NFR-Performance, FR-07 | k6 or JMeter scripts; browser timings |
| Security | Authentication, RBAC, TLS, password storage, input handling, session and code abuse (see 5.3) | FR-01, NFR-Security | Manual checks, Postman, OWASP ZAP |
| Usability and accessibility | Responsive layout on desktop, tablet and mobile; keyboard use; contrast and labels (WCAG 2.1 AA where feasible); a first-time user can register for an event without help | NFR-Usability | Manual walkthrough; Lighthouse and axe |
| Compatibility / portability | Correct behaviour on the latest two major versions of Chrome, Firefox, Edge and Safari | NFR-Portability | Cross-browser run of the smoke suite and key journeys |
| Reliability / recovery | Availability checks, graceful behaviour when email service is down, backup restore works | NFR-Reliability, FR-07 | Health-check monitoring, fault injection, restore drill |
| Concurrency | Last-seat race, simultaneous check-in of the same code, duplicate submit | FR-05, FR-06 | Parallel API calls (k6 / Jest with Promise.all) |

### 5.3 Test Design Techniques

- **Equivalence partitioning and boundary value analysis:** password length (7, 8 characters), rating (0, 1, 5, 6), capacity (0, 1, N, N+1), registration deadline (before, at, after), account lockout (4th, 5th, 6th failed attempt).
- **State transition testing:** the event lifecycle Draft, Pending Approval, Approved, Rejected, Cancelled, Completed, and the registration states Confirmed, Waitlisted, Cancelled, Checked-in.
- **Role-permission matrix testing:** every protected function is attempted by each of the three roles and by an unauthenticated user.
- **Error guessing:** expired or reused reset links, code from another event, cancelled registration code, past-dated events, event edited after approval.

### 5.4 Entry Criteria

| Level | Entry criteria |
| --- | --- |
| Unit | Code for the unit committed; unit test skeleton reviewed against the SRS |
| Integration | Unit tests for the modules passing; modules merged to the integration branch; test database and email sandbox available |
| System | Stable build deployed to the test environment; smoke suite passing; test data loaded; test cases reviewed and baselined |
| UAT | System testing exit criteria met; no open critical or high defects; UAT accounts and data prepared |

### 5.5 Exit Criteria

- 100% of planned test cases for the cycle executed.
- At least 95% of executed functional test cases passed, and every failed case has a logged defect.
- 0 open Critical and 0 open High defects; Medium defects have an agreed fix or documented workaround.
- 100% of FR-01 to FR-08 covered by at least one passing test (requirement coverage tracked in the RTM).
- Performance targets met: listing page response within 3 s and registration within 2 s at 200 concurrent users.
- All security validation checks in 5.6 executed with no open Critical or High findings.
- Cross-browser smoke run passed on all four browsers.
- UAT sign-off received from the QA lead and the acceptance representatives.

### 5.6 Defect Severity Definitions

| Severity | Definition | Example in CEMS |
| --- | --- | --- |
| Critical | System unusable, data loss, or security breach | Student can access Admin approval queue; seat count goes negative |
| High | Major feature broken with no workaround | Registration fails; QR code cannot be validated |
| Medium | Feature partly broken or workaround exists | Filter returns wrong subset; email not sent but in-app notification works |
| Low | Cosmetic or minor usability issue | Misaligned label; wording inconsistent with the expected message |

## 5.7 Security Validation

Security validation verifies the security-related requirements of FR-01 and NFR-Security (HTTPS/TLS, password hashing, role-based access control), and the security design controls in the Software Architecture and Design Specification (Section 3.9, STRIDE threat model). Existing functional cases that also cover security behaviour are FR01_IT_03 (lockout), FR01_ST_02 (password reset), FR01_ST_03 (Student blocked from Admin routes) and FR03_IT_03 (approval queue restricted to Admin).

| ID | Area | Validation check | Method / tool | Expected result | Traces to |
| --- | --- | --- | --- | --- | --- |
| NFR_SEC_01 | Transport security | All pages and API calls are served over HTTPS; HTTP requests are redirected; TLS 1.2 or higher only; HSTS header present; old protocol versions rejected | Browser inspection; SSL Labs or `nmap --script ssl-enum-ciphers`; curl | No content served over plain HTTP; TLS 1.0/1.1 refused | NFR-Security |
| NFR_SEC_02 | Password storage | Passwords are stored only as bcrypt hashes; plain text never appears in the database, API responses or logs | Inspect `users` collection; review logs and network responses | Only hashes stored; no password or hash returned by any endpoint | FR-01, NFR-Security |
| NFR_SEC_03 | Authentication and lockout | 5 consecutive failed logins lock the account; error text is generic and does not reveal whether the email exists; login is rate limited | Manual; Postman/Jest loop | Lockout message on the 6th attempt; identical error for unknown email and wrong password; HTTP 429 on flooding | FR-01 (FR01_IT_03) |
| NFR_SEC_04 | Session and token handling | JWT is in an httpOnly, Secure, SameSite cookie; tampered, expired or unsigned tokens are rejected; logout invalidates the session on the client | Postman; browser dev tools; edit token payload | HTTP 401 for altered or expired token; token not readable by JavaScript | FR-01 |
| NFR_SEC_05 | Role-based access control | Each protected endpoint and page is attempted by an unauthenticated user, a Student, an Organizer and an Admin; an Organizer cannot edit or check in for another Organizer's event; a Student cannot approve events or read attendance data | Role-permission matrix run through Postman and UI | Forbidden actions return HTTP 401/403 with no data exposed; role cannot be changed through a request body | FR-01, FR-03 (FR01_ST_03, FR03_IT_03) |
| NFR_SEC_06 | Password reset | Reset link works once, expires after its validity period, and the reset request does not disclose whether the email is registered | Manual; Postman | Second use of the link and an expired link are rejected; same response for known and unknown emails | FR-01 (FR01_ST_02) |
| NFR_SEC_07 | Input validation and injection | SQL/NoSQL operator injection (e.g. `{"$ne": ""}` in login), stored and reflected XSS in event title, description and feedback comment, oversized inputs, malformed JSON | Manual payloads; OWASP ZAP active scan; fuzzing of form fields | Inputs rejected or safely escaped; no script execution; no server error stack trace | NFR-Security |
| NFR_SEC_08 | Registration code (QR) abuse | Code reuse, code from another event, cancelled-registration code, guessed sequential codes, code used by a non-owner | Manual; Postman script trying 100 random codes | Reused, foreign or invalid codes rejected; codes are unpredictable; excessive attempts are rate limited | FR-06 (FR06_UT_02, FR06_UT_03) |
| NFR_SEC_09 | Data exposure | Error responses contain no stack traces or internal details; feedback shown to Organizer does not reveal student identity; a Student can read only their own registrations and notifications | Manual; Postman with another user's IDs | Access to another user's data is denied; generic error messages in production mode | FR-07, FR-08 |
| NFR_SEC_10 | Cross-site request forgery and headers | State-changing requests from a foreign origin are rejected; security headers present (Content-Security-Policy, X-Content-Type-Options, X-Frame-Options) | Browser check; OWASP ZAP baseline | Cross-origin POST fails; required headers present | NFR-Security |
| NFR_SEC_11 | Audit trail | Admin approval decisions and check-ins are recorded with actor and timestamp and cannot be edited through the UI or API | Manual review of audit log | Every decision has an entry; no edit or delete endpoint exists | FR-03 (FR03_ST_03) |
| NFR_SEC_12 | Dependency and configuration hygiene | Known-vulnerable dependencies and exposed secrets are detected; environment secrets not present in the repository or client bundle | `npm audit`; secret scan; review of built bundle | No Critical or High vulnerabilities unresolved; no secrets in source or bundle | NFR-Security |

Any Critical or High finding from these checks is logged as a defect and must be closed before the exit criteria in 5.5 can be met.

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
| UI / end-to-end automation | Cypress (Selenium is an acceptable alternative) |
| Performance and load | k6 or Apache JMeter |
| Security testing | OWASP ZAP, browser dev tools, `npm audit`, SSL Labs or nmap for TLS |
| Accessibility and usability | Lighthouse, axe DevTools |
| Cross-browser | Local browsers plus a cloud device service for Safari and iOS if no Apple device is available |
| Defect tracking and test management | Jira or GitHub Issues; test cases maintained as the Test Case document and RTM |
| Monitoring during tests | Health-check endpoint and server logs; response time captured from k6/JMeter reports |

### 6.5 Test Data

**User accounts** (dummy data only; no real student information is used):

| Account | Role | Purpose |
| --- | --- | --- |
| student01 to student05 (e.g. ashith.rao@college.edu, Password Pass@123) | Student | Registration, waitlist, cancellation, feedback, concurrency cases (two students racing for the last seat) |
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
