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
| Attendance interface | `client/src/pages/Attendance.jsx` | Scans QR codes where the browser supports `BarcodeDetector`, accepts manual codes, and polls attendance views. |
| Server composition | `server/server.js` | Configures security headers, CORS, JSON and cookie parsing, routes, database connection, error handling, and the reminder job. |
| Authentication and authorization | `server/middleware/authMiddleware.js`, `server/middleware/authRateLimit.js` | Verifies the JWT cookie and restricts endpoints by role. |
| HTTP routing | `server/routes/*.js` | Maps API paths to handlers and applies authentication and role checks. |
| Event and attendance rules | `server/controllers/eventController.js` | Validates event operations, registration deadlines and capacity, registration codes, check-in ownership, schedule, and single-use attendance. |
| Event persistence | `server/models/Event.js` | Stores event details, registered students, hashed registration-code digests, and attendance audit fields. Registration records are embedded in the event document. |
| User and notification persistence | `server/models/User.js`, `server/models/Notification.js` | Defines users and in-app notifications. |
| Notification delivery | `server/services/notificationService.js` | Sends optional SMTP email without undoing the application action if delivery fails. |
| Scheduled reminders | `server/services/reminderJob.js` | Creates reminders for registered students before approved events. |

## Authentication and data boundaries

- The API uses JSON under `/api`. The client sends cookies with requests through Axios `withCredentials`.
- `protect` verifies the JWT and loads the user. `restrictTo` limits student, organizer, and admin operations.
- Organizer operations also verify event ownership in the controller; role checks alone do not grant access to another organizer's event.
- Registration codes are generated with cryptographically secure randomness. The database stores a SHA-256 digest, not the code shown to the student.
- A successful check-in stores its timestamp and the organizer who recorded it on the registration. A conditional database update makes a code single-use under concurrent requests.
- Public event responses are explicit projections. Student, organizer, and admin endpoints return only the fields needed for their respective views.

## Testable boundaries

The server uses CommonJS modules and Node's built-in test runner (`server/package.json`, `npm test`). Route definitions, middleware, controllers, models, and notification services are separate files. Controller functions are exported and can be invoked with request/response stubs; Mongoose model methods can be stubbed for unit-level rule checks. Existing examples are in `server/test/authMiddleware.test.js` and `server/test/eventDraft.test.js`.

Client page behavior is separated from API calls in `client/src/services/api.js`, so UI components can mock endpoint functions. No test result is implied by this architecture description; CI runs the repository's configured checks on pull requests.
