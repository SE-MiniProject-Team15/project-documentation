## 7. Test Schedule

The schedule follows the test levels in order (unit, integration, system, acceptance). No execution starts before the test environment and test data are ready. UAT is last because it needs a stable build.

| # | Milestone | Date / Duration | Owner |
| --- | --- | --- | --- |
| 1 | Test plan and test case design (67 functional cases + non-functional cases) | Completed by 28-Sep-2026 | QA Lead |
| 2 | Test environment and test data setup | 05-Oct-2026 | QA Lead / Developer |
| 3 | Unit and integration test execution | 06-Oct-2026 to 14-Oct-2026 | Test Engineers |
| 4 | System test execution (functional and non-functional) | 15-Oct-2026 to 22-Oct-2026 | Test Engineers |
| 5 | Regression testing and defect retest | 23-Oct-2026 to 26-Oct-2026 | Test Engineers / Developer |
| 6 | User Acceptance Testing (UAT) | 27-Oct-2026 to 29-Oct-2026 | Product Owner |
| 7 | Test Summary Report | 30-Oct-2026 | QA Lead |

> Note: Dates after 28-Sep-2026 are planned dates and may be adjusted to match the course calendar for the implementation phase.

## 8. Test Deliverables

The testing effort produces the following deliverables for the College Event Management System (CEMS):

- **Test Plan**: this document.
- **Test Cases**: 67 functional cases (unit, integration and system levels, with IDs such as FR01_UT_01, FR03_ST_03) and non-functional cases covering performance, security, usability, reliability and portability.
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
