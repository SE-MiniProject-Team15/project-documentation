# CEMS Team 15 follow-up execution report

| Item | Value |
| --- | --- |
| Test date | 18 November 2026, Asia/Kolkata |
| Application `main` | [`63530693fff487c285b6fcb79f307657c94a02f0`](https://github.com/SE-MiniProject-Team15/cems-codebase/commit/63530693fff487c285b6fcb79f307657c94a02f0) |
| Documentation baseline | [`c0724a1`, including PR #20](https://github.com/SE-MiniProject-Team15/project-documentation/pull/20) |
| Outcome | **75 Pass, 16 Fail, 3 Partial, 1 Pending** of 95 cases |
| Case-level evidence | [Actual Result and Test Result](TestPlan_CollegeEventManagementSystem_Team15.md#3-detailed-test-cases), [follow-up totals](TestPlan_CollegeEventManagementSystem_Team15.md#6-follow-up-verification) |

## Execution environments

| Cases or checks | Environment |
| --- | --- |
| FR-01–FR-08 and NFR_USA_01 | Loopback React/Vite and Express with real MongoDB 8.2.6 replica sets, Node.js 22.23.4, disposable role accounts and a local SMTP receiver. Interactive checks used the Codex in-app Browser. Supplemental live Playwright checks used Google Chrome 153.0.8010.36. The host ran Ubuntu 24.04.5 LTS x86_64, kernel 7.0.0-43-generic. The repeatable workflow and its time fixtures used UTC. |
| FR07_ST_02, NFR_PERF_01 and NFR_PERF_02 | Dedicated loopback CEMS instance, separate MongoDB replica set and SMTP receiver. k6 2.4.0 ran in a separate process on the same 8-logical-CPU, 7.4-GiB Ubuntu host as the server. The browser sampled rendering during the 200-user hold. These measurements describe the isolated local environment. |
| NFR_SEC_01 and NFR_SEC_10 | Deployment inventory and ingress guidance review. SEC-10 also used allowed and alternate loopback origins against the local fixture. An authorized staging URL, access and ingress configuration were unavailable. |
| NFR_SEC_03, NFR_SEC_06, NFR_SEC_07, NFR_SEC_08, NFR_SEC_09 and NFR_SEC_11 | Isolated loopback security checks, matching backend suite or source review, and the live Chrome account, event, ticket or audit fixtures. |
| NFR_SEC_12 | `npm audit` against both current lockfiles and a secret-pattern check over tracked files and the built client. Only documentation example and test fixture patterns matched. Audit reported 5 high and 3 moderate advisories in the client and zero in the server. |
| NFR_REL_01 | Application backup script with MongoDB Database Tools 100.19.1 and two disposable MongoDB 8.2.6 server processes. |
| NFR_PORT_01 | Live CEMS fixture over loopback on Ubuntu 24.04.5 LTS x86_64. Exact browser builds appear below. |

## Supporting suites

| Command | Outcome |
| --- | --- |
| `npm test` in `server` | 131 passed. |
| `npm run test:integration` in `server` | 7 passed with real HTTP, temporary MongoDB, local SMTP and Chromium browser login/discovery. The runner recorded the full application commit. [Runner and scenarios](https://github.com/SE-MiniProject-Team15/cems-codebase/tree/63530693fff487c285b6fcb79f307657c94a02f0/docs/testing) |
| `npm run build` and `npm run lint` in `client` | Both passed on Node.js 22.23.4. The final build and lint used a clean export of the application commit. |
| `npm run test:browsers` in `client` | 308 passed. Each project passed 77 checks in Chromium 156.0.8078.4, Playwright Firefox 157.0, WebKit 27.2 and installed Google Chrome 153.0.8010.36. [Suite configuration](https://github.com/SE-MiniProject-Team15/cems-codebase/blob/63530693fff487c285b6fcb79f307657c94a02f0/docs/NFR-07-browser-compatibility.md) |
| `npm audit` in both packages | Server completed with zero advisories. The client completed with five high and three moderate advisories, which keep NFR_SEC_12 failed. |

The Playwright browser and accessibility suites use controlled API responses. The interactive and supplemental checks exercised the real application. A first WebKit attempt changed the app port while the checked-in test fixtures still supplied a CORS origin on port 4173. A second attempt encountered navigation timeouts while sharing the frontend cache. The verification rerun used the expected port, an isolated Vite cache and the temporary WebKit library launcher.

## Functional and usability coverage

| Area | Live checks |
| --- | --- |
| FR-01 | Signed in as Student, Organizer and Admin. Verified Student and Organizer sign-up, role landing, cookie persistence, restricted Admin route coverage and the lockout boundary. The sign-up redirect, lockout copy and missing recovery flow reproduced. |
| FR-02 and FR-03 | Saved an Organizer draft, submitted a complete event in Playwright, validated required fields, checked an overlapping venue, approved, rejected, requested changes and resubmitted. Students saw published details only after approval and kept the prior published version until reapproval. Cancellation removed the listing and notified registrants. Pending edits remained unavailable and Admin still had no audit view. |
| FR-04 and FR-05 | Searched and filtered the live catalog, inspected event details, registered, joined the waitlist, cancelled a registration and checked ticket display. A last-seat race produced one confirmed registration and one waitlist entry. The integration suite also verified promotion. Confirmation emails omitted the ticket. |
| FR-06 | Used stored time fixtures to enter the check-in window. The fallback rejected a displayed short code and accepted the full QR payload. Five check-ins updated the Organizer count without reload, and Admin saw five of six checked in. Replay and a ticket for a different event were rejected. |
| FR-07 | Verified in-app and SMTP confirmations, review decisions and cancellations. Verified an SMTP refusal left registration successful, and running the reminder twice produced one notice. Checked timestamp ordering and bulk delivery separately. |
| FR-08 | A checked-in Student submitted feedback and saw the read-only result. A Student without attendance was ineligible. Organizer and Admin saw the same submitted rating and comment. Another fixture with three attendee submissions verified matching anonymous comments and ratings in both UIs and APIs. |
| NFR_USA_01 | At 1366×768, 768×1024 and 375×667, used Tab and Enter to search, open details, register and open the QR ticket. Listing, details and tickets fit each viewport. |

The prior nine functional failures in PR #20 reproduced. The original expected outcomes and earlier evidence remain in the [case document](TestPlan_CollegeEventManagementSystem_Team15.md#3-detailed-test-cases). In FR02_ST_03, the full event correctly retained zero seats and offered a waitlist. Its case expects a full-message-only outcome, while FR05_IT_01 explicitly expects the waitlist.

## Load and notification delivery

| Case | Setup and measurement | Result |
| --- | --- | --- |
| NFR_PERF_01 | Seeded 80 events. k6 ramped from 0 to 200 virtual Students in 5 minutes, then held 200 for 30 seconds. Each iteration fetched the event-listing document and the authenticated catalog response. Ten additional Chrome navigations measured time from navigation to visible event cards during the hold. | **Pass.** 35,021 iterations and 70,042 HTTP requests. Combined request p95 **69 ms**, zero errors and zero 5xx. At peak, Chrome time to visible event cards was **492 ms p95**. The ten times in ms were 492, 292, 261, 137, 154, 198, 222, 225, 172 and 234. |
| NFR_PERF_02 | Barrier released 200 virtual Students together. Each posted one registration to the same event with capacity 250. Each Student had a distinct fixture session and there were no client retries. | **Fail.** All-request p95 **2,033 ms**, with 94 HTTP 201 and 106 HTTP 409 responses. Error rate **53.0%**, zero 5xx. The 94 stored registrations and registered-student IDs matched exactly. There were 156 remaining seats in the API, zero waitlisted, 94 in-app confirmations and 94 emails. |
| FR07_ST_02 | Seeded an approved event with 300 distinct confirmed Students. The Organizer submitted a venue edit while ten workers repeatedly requested authenticated event listings. Compared recipient sets and counts in the database and SMTP receiver. | **Pass.** The 511 ms update returned HTTP 200, saved the replacement venue and moved the event to Pending Approval. All 300 Students got exactly one update notice and one email. Listing p95 during dispatch was **68 ms**, with zero errors across 101 probes. |

To reproduce NFR_PERF_02, seed an approved event with at least 200 free seats, use 200 distinct signed-in Students and release one registration request per Student together. The route uses optimistic concurrency with up to five save attempts. At the observed conflict rate, seat balances remained consistent and successful requests still exceeded the latency target at p95 (2.09 seconds). [Registration controller](https://github.com/SE-MiniProject-Team15/cems-codebase/blob/63530693fff487c285b6fcb79f307657c94a02f0/server/controllers/registrationController.js)

## Backup and restore

Ran the application backup command against a quiet test fixture and restored its gzip dump onto a separate, clean MongoDB process. An initial scoped restore needed explicit source and target namespace mapping. The expanded restore stalled during parallel index creation, then completed with `--numParallelCollections 1`. The final run restored 40 documents with zero restore errors. Registrations are embedded in event documents.

| Data | Compared | Result |
| --- | ---: | --- |
| Users | 16 | Canonical BSON content matched. |
| Events | 16 | Canonical BSON content matched. |
| Embedded registrations | 9 | Registration fields, linked Students and event IDs matched. |
| Feedback and notifications | 3 and 5 | Canonical BSON content matched. |
| Audit log | 0 | The empty collection was present on both sides. |
| Collection indexes | 5 collections | Restored index definitions matched. |

## Browser matrix

Each available branded build completed Student sign-in, event discovery, registration and visible QR confirmation. The desktop listing, details and ticket screens had no horizontal overflow. All Linux runs used Ubuntu 24.04.5 LTS x86_64, kernel 7.0.0-43-generic.

| Browser and major | Exact tested version | OS and outcome | Missing plan platforms |
| --- | --- | --- | --- |
| Google Chrome 158 | 158.0.8127.36, Chrome for Testing | Ubuntu 24.04.5 LTS, Pass | Windows, macOS, Android |
| Google Chrome 157 | 157.0.8103.78, Chrome for Testing | Ubuntu 24.04.5 LTS, Pass | Windows, macOS, Android |
| Mozilla Firefox 160 | 160.0.1, Mozilla release via geckodriver 0.37.1 | Ubuntu 24.04.5 LTS, Pass | Windows, macOS |
| Mozilla Firefox 159 | 159.0.1, Mozilla release via geckodriver 0.37.1 | Ubuntu 24.04.5 LTS, Pass | Windows, macOS |
| Microsoft Edge 157 | 157.0.4341.60, Microsoft Stable | Ubuntu 24.04.5 LTS, Pass | Windows |
| Microsoft Edge 156 | 156.0.4314.58, Microsoft Stable | Ubuntu 24.04.5 LTS, Pass | Windows |
| Apple Safari 27 | Unavailable | Apple runtime or connected device lab required | macOS and iOS |
| Apple Safari 26 | Unavailable | Apple runtime or connected device lab required | macOS and iOS |

The stable versions were checked against vendor release listings as of 18 November. [Chrome version history](https://versionhistory.googleapis.com/v1/chrome/platforms/linux/channels/stable/versions?page_size=12), [Mozilla desktop product details](https://product-details.mozilla.org/1.0/firefox_versions.json), [Firefox 159 release notes](https://developer.mozilla.org/en-US/docs/Mozilla/Firefox/Releases/159), [Microsoft Edge Stable release notes](https://learn.microsoft.com/en-us/deployedge/microsoft-edge-relnote-stable-channel), [Safari release records](https://support.apple.com/en-us/100100)

Additional live journeys passed in Chromium 156.0.8078.4 and WebKit 27.2 on the same Ubuntu host. These results are recorded under their own browser engines.

## Remaining prerequisites

| Case | Current state | Remaining check |
| --- | --- | --- |
| NFR_SEC_01 | Pending | An authorized staging URL and network access are needed to confirm the HTTP-to-HTTPS redirect and TLS 1.0/1.1 rejection, and record the offered TLS versions and ciphers. Both code and documentation repositories returned zero GitHub deployments. The code repository returned zero GitHub environments. [Ingress guidance](https://github.com/SE-MiniProject-Team15/cems-codebase/blob/63530693fff487c285b6fcb79f307657c94a02f0/server/DEPLOYMENT.md) |
| NFR_SEC_07 | Partial | Local checks verified type validation and literal rendering with an inert marker. The executable input variants remain unverified. |
| NFR_SEC_10 | Partial | The local API supplied `nosniff`, `DENY` and the configured allowed origin. The alternate-origin preflight returned the configured origin, which does not authorize the alternate origin. The development frontend response omitted the required headers, and the Express configuration omits CSP and uses CORS without an explicit server-side origin guard. Staging ingress configuration and authorized staging access with a disposable session are needed to verify deployed headers and state-changing origin rejection. [Express configuration](https://github.com/SE-MiniProject-Team15/cems-codebase/blob/63530693fff487c285b6fcb79f307657c94a02f0/server/server.js) |
| NFR_PORT_01 | Partial | Safari 27 and 26 need Apple runtimes or a connected browser lab. The required Windows, macOS and Android combinations still need devices or VMs, as listed in the matrix. |

The follow-up confirms a registration-load failure, six other non-functional failures and nine functional failures. Sixty-nine of 78 functional cases passed (88.5%). The 95% functional pass-rate target and the staging and cross-browser exit criteria in STP §5.6 remain unmet.
