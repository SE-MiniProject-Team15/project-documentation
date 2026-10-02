## 10. Risks and Mitigation

| # | Risk | Likelihood | Impact | Mitigation |
| --- | --- | --- | --- | --- |
| 1 | Delay in stable build delivery blocks test execution | Medium | High | Request early smoke builds from the Developer; keep the fixed test-build schedule in Section 7 |
| 2 | Concurrent-registration race condition causes seat count to go negative or double-books the last seat | Medium | Critical | Covered by `FR05_ST_02`; verify the atomic seat-decrement logic from the SAD before sign-off; re-run under higher concurrency if the first result is marginal |
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

## 11. Assumptions & Dependencies

- The application build delivered for testing matches the approved SAD (component boundaries, API contracts) and the current SRS v1.1.
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
| FR-01.2 | Password policy, hashing | `FR01_UT_03` |
| FR-01.3 | Admin accounts pre-provisioned, not self-registered | `FR01_IT_04` |
| FR-01.4 | Login, logout, forgot password, JWT | `FR01_IT_02`, `FR01_ST_01`, `FR01_ST_02` |
| FR-01.5 | Lockout after 5 failed attempts | `FR01_IT_03`, `NFR_SEC_03` |
| FR-02.1–02.2 | Event fields, draft save | `FR02_UT_01`, `FR02_UT_03`, `FR02_IT_01` |
| FR-02.3 | Edit, cancel, material-edit re-approval | `FR02_IT_03`, `FR02_ST_02` |
| FR-02.4 | Future date, venue double-booking | `FR02_UT_02`, `FR02_IT_02` |
| FR-03.1–03.2 | Approval queue, Approve/Reject/Request Changes | `FR03_UT_01`, `FR03_UT_02`, `FR03_UT_03`, `FR03_UT_04` |
| FR-03.3 | Decision notification | `FR03_IT_01` |
| FR-03.4 | Resubmission after reject/changes requested | `FR03_IT_02` |
| FR-03.5 | Audit log | `FR03_ST_03`, `NFR_SEC_11` |
| FR-04.1 | Keyword search, category filter, date range | `FR04_UT_01`, `FR04_UT_02`, `FR04_UT_03`, `FR04_IT_01` |
| FR-04.1 | Venue filter, department filter | `FR04_IT_04` |
| FR-04.2 | Sort by date, sort by popularity | `FR04_UT_04`, `FR04_IT_02` |
| FR-04.3 | Seat count on detail page | `FR04_IT_03` |
| FR-05.1 | Registration, capacity enforcement | `FR05_UT_01`, `FR02_ST_03` |
| FR-05.1 | Waitlist offered when full | `FR05_IT_01` |
| FR-05.1 | Waitlist auto-promotion on cancellation | `FR05_IT_04` |
| FR-05.2 | Duplicate registration blocked | `FR05_UT_02` |
| FR-05.3 | QR/code generation | `FR05_ST_01` |
| FR-05.4 | View/cancel own registration | `FR05_IT_02`, `FR05_IT_03` |
| — | Concurrency on last seat | `FR05_ST_02` |
| FR-06.1 | Scan / manual entry | `FR06_UT_01`, `FR06_IT_01` |
| FR-06.2 | Reused / invalid / out-of-window code | `FR06_UT_02`, `FR06_UT_03`, `FR06_IT_03` |
| FR-06.3 | Real-time attendance dashboard | `FR06_IT_02`, `FR06_ST_02` |
| — | Attendance feeds feedback eligibility | `FR06_ST_01` |
| FR-07.1 | Confirmation, decision, reminder, cancellation notifications | `FR07_UT_01`, `FR07_UT_02`, `FR07_IT_01`, `FR07_IT_02` |
| FR-07.2 | Notification history | `FR07_IT_03` |
| FR-07.3 | Failed email logged, action not blocked | `FR07_UT_03` |
| — | Full lifecycle notification consistency, mass-notification load | `FR07_ST_01`, `FR07_ST_02` |
| FR-08.1 | Feedback available only post-event, only to attendees | `FR08_UT_01`, `FR08_IT_01` |
| FR-08.2 | Rating range, one submission per student | `FR08_UT_02`, `FR08_UT_03`, `FR08_IT_02` |
| FR-08.3 | Aggregated ratings, anonymised comments | `FR08_IT_03`, `FR08_ST_01`, `FR08_ST_02` |

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
| NFR-04 | 99% availability, backup/restore | `NFR_REL_01` |
| NFR-05 | Horizontal scalability | Not directly tested; inferred from `NFR_PERF_01`/`02` under load |
| NFR-06 | Modular MVC, documented REST APIs | Verified by code/design review, not a runtime test case |
| NFR-07 | Latest two versions of Chrome/Firefox/Edge/Safari | `NFR_PORT_01` |

**Coverage status: complete.** All functional and non-functional/security requirements in SRS v1.1 now trace to at least one test case.

## 14. Test Metrics & Reporting

**Metrics collected during execution:**

| Metric | Definition | Target |
| --- | --- | --- |
| % test cases executed | Executed ÷ planned (89 total) | 100% by the end of Section 7, milestone 7 |
| % passed / failed | Pass ÷ executed, Fail ÷ executed | ≥ 95% functional pass rate (Section 5.6) |
| Defect density | Defects ÷ number of FRs/NFRs | Tracked per FR for hotspot analysis |
| Defect aging | Days a defect stays open, by severity | Critical/High closed within the cycle |
| Requirement coverage | SRS requirements with ≥ 1 passing test ÷ total requirements (via the RTM in Section 13) | 100% — already at full coverage by design; execution must confirm all pass |
| Security findings | Open Critical/High findings from the 12 Section 5.1 checks | 0 open before sign-off |

**Reporting:**
- **Daily execution status** during Section 7 milestones 3–8: cases run, pass/fail count, new defects, by the QA Lead.
- **Final Test Summary Report** (Section 8, deliverable 11): overall pass rate, defect summary by severity, RTM coverage snapshot, exit-criteria checklist, and a recommendation to proceed to or hold release.

## 15. Approvals

| Role | Name | Signature / Date |
| --- | --- | --- |
| QA Lead | Ashith Rao K | |
| Developer | Prajwal Manjunath Hegde | |
| Product Owner | Prajwal Manjunath Hegde (Team Lead) / Course Faculty | |
| Security Test Engineer | G Purvi | |
| Test Engineer | Leesha R | |
