# CEMS Implementation Architecture

This implementation map describes the current `cems-codebase` repository. It complements the conceptual architecture in the Software Architecture and Design Specification (SAD).

## Request flow

```text
React page
  -> client/src/services/api.js (Axios, JSON, credentials)
  -> Express router (server/routes)
  -> authentication and role middleware (server/middleware)
  -> controller (server/controllers)
  -> Mongoose model (server/models)
  -> MongoDB
```

The server assembles middleware and routers in `server/server.js`. Controllers apply application rules and return JSON. Mongoose models define persisted shapes and indexes. Notification delivery and scheduled reminders are isolated in `server/services`.

## Module responsibilities

| Module | Implementation location | Responsibility |
|---|---|---|
| Client routing and role-specific navigation | `client/src/App.jsx` | Maps URLs to student, organizer, and admin pages; redirects users who do not have the required role. |
| Session state | `client/src/context/AuthContext.jsx` | Loads the current user and provides login, registration, and logout actions. |
| HTTP boundary | `client/src/services/api.js` | Central Axios client, API base URL, cookie credentials, and endpoint functions. |
| Event discovery and registration | `client/src/pages/Events.jsx` | Searches approved events, shows details, registers a student, and renders the single-use check-in QR code. |
| Event authoring | `client/src/pages/OrganizerEvents.jsx` | Creates, edits, saves, and cancels organizer-owned events. |
| Admin event review | `client/src/pages/AdminEventReview.jsx`, `server/controllers/eventController.js`, `server/models/EventAuditLog.js` | Lists pending submissions and records approvals, rejections, and change requests. The decision, audit record, and organizer notification commit together in a MongoDB transaction. |
| Attendance interface | `client/src/pages/Attendance.jsx` | Scans QR codes where the browser supports `BarcodeDetector`, accepts manual codes, and polls attendance views. |
| Server composition | `server/server.js` | Configures security headers, CORS, JSON and cookie parsing, routes, database connection, error handling, and the reminder job. |
| Authentication and authorization | `server/middleware/authMiddleware.js`, `server/middleware/authRateLimit.js` | Verifies the JWT cookie and restricts endpoints by role. |
| HTTP routing | `server/routes/*.js` | Maps API paths to handlers and applies authentication and role checks. |
| Event and attendance rules | `server/controllers/eventController.js` | Validates event operations, registration deadlines and capacity, registration codes, check-in ownership, schedule, and single-use attendance. |
| Event persistence | `server/models/Event.js` | Stores event details, the last approved snapshot, and embedded registrations with check-in timestamps and organizer IDs. |
| User and notification persistence | `server/models/User.js`, `server/models/Notification.js` | Defines users and in-app notifications. |
| Notification delivery | `server/services/notificationService.js` | Sends optional SMTP email without undoing the application action if delivery fails. |
| Scheduled reminders | `server/services/reminderJob.js` | Creates reminders for registered students before approved events. |

## Authentication and data boundaries

- The API uses JSON under `/api`. The client sends cookies with requests through Axios `withCredentials`.
- `protect` verifies the JWT and loads the user. `restrictTo` limits student, organizer, and admin operations.
- Organizer operations also verify event ownership in the controller; role checks alone do not grant access to another organizer's event.
- Registration codes are generated with cryptographically secure randomness and stored in an embedded field excluded from queries by default. The student ticket endpoints explicitly select the field for the requesting student's registration.
- Rejected submissions and submissions with changes requested can be edited and resubmitted. Material edits to an approved event enter review while students continue to see the last approved snapshot.
- A successful check-in stores its timestamp and the organizer who recorded it on the registration. A conditional database update makes a code single-use under concurrent requests.
- Public event responses are explicit projections. Student, organizer, and admin endpoints return only the fields needed for their respective views.

## Testable boundaries

The server uses CommonJS modules and Node's built-in test runner (`server/package.json`, `npm test`). Route definitions, middleware, controllers, models, and notification services are separate files. Controller functions are exported and can be invoked with request/response stubs; Mongoose model methods can be stubbed for unit-level rule checks. Existing examples are in `server/test/authMiddleware.test.js` and `server/test/eventDraft.test.js`.

Client page behavior is separated from API calls in `client/src/services/api.js`, so UI components can mock endpoint functions. No test result is implied by this architecture description; CI runs the repository's configured checks on pull requests.

## Ashith Rao K’s implementation and verification contributions

| Work | Merged evidence |
| --- | --- |
| FR-01 account lockout after five failed sign-ins, with a 15-minute lock window and a generic authentication error | [Authentication fix](https://github.com/SE-MiniProject-Team15/cems-codebase/commit/ece942aa9449c47e5790415e1bc99dee01bba9b5) |
| FR-03 administrator queue and decisions, resubmission, approved snapshots, and transactional audit records | [Admin event approval PR](https://github.com/SE-MiniProject-Team15/cems-codebase/pull/5) |
| FR-07 in-app and SMTP delivery for event actions, notification history, and scheduled reminders | [Notification system PR](https://github.com/SE-MiniProject-Team15/cems-codebase/pull/8) |
| NFR-03 responsive layouts, keyboard and focus handling, and accessible form validation | [Responsive and accessible UI PR](https://github.com/SE-MiniProject-Team15/cems-codebase/pull/10) |
| NFR-07 Playwright browser journeys and compatibility evidence | [Browser test commit](https://github.com/SE-MiniProject-Team15/cems-codebase/commit/f070d2c) |
| System integration runner and evidence using Express, MongoDB transactions, local SMTP, and Chromium | [System integration PR](https://github.com/SE-MiniProject-Team15/cems-codebase/pull/13), [final UI verification](../TestPlan/TestPlan_CollegeEventManagementSystem_Team15.md#5-final-ui-verification) |
