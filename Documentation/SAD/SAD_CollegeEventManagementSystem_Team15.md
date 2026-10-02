# Software Architecture and Design Specification
## College Event Management System (CEMS)
### Team 15

**Version:** 1.0  
**Date:** 2 October 2026  
**Technology Stack:** MERN (MongoDB, Express.js, React.js, Node.js)

---

# Document Control

| Item | Details |
|---|---|
| Document Title | Software Architecture and Design Specification – College Event Management System |
| Version | 1.0 |
| Team | Team 15 |
| Date | 2 October 2026 |
| Architecture Style | Layered Architecture with RESTful Client-Server Interface |
| Technology Stack | MERN |

---

# Table of Contents

1. Introduction  
   1.1 Purpose  
   1.2 Scope  
2. Architecture Overview  
   2.1 Architecture Goals  
   2.2 Constraints  
   2.3 Stakeholders and Concerns  
3. Architecture  
   3.1 Architecture Style  
   3.2 Component Diagram  
   3.3 Component Descriptions  
   3.4 Technology Stack and Data Stores  
   3.5 Architecture Decisions  
   3.6 Risks and Mitigations  
   3.7 Traceability to Requirements  
   3.8 Security Architecture  
4. Design  
   4.1 Design Overview  
   4.2 Sequence Diagram – Event Registration  
   4.3 Sequence Diagram – Event Approval  
   4.4 API Design  
   4.5 Error Handling  
   4.6 Logging and Monitoring  
   4.7 User Interface Design  
5. Open Issues and Future Enhancements  
6. Appendices  
   Appendix A – Diagram Files  
   Appendix B – Requirement Traceability Summary  

---

# 1. Introduction

## 1.1 Purpose

This Software Architecture and Design Specification (SAD) describes the architecture and detailed design of the College Event Management System (CEMS). It translates the requirements defined in the Software Requirements Specification (SRS) into an implementation-oriented architecture.

The document provides guidance for development, integration, testing, deployment, maintenance, and future enhancement of CEMS.

## 1.2 Scope

CEMS is a web-based college event management application supporting the complete event lifecycle:

- user registration and authentication;
- event creation and management;
- event approval and rejection;
- event browsing, search, and filtering;
- student registration/RSVP;
- attendance tracking using QR codes;
- notifications;
- feedback and ratings.

The system has three primary roles:

- Student
- Event Organizer
- Administrator

The implementation uses the MERN stack:

- MongoDB
- Express.js
- React.js
- Node.js

The architecture and design described here address the functional requirements FR-01 to FR-08 and the non-functional and security requirements defined in the SRS.

---

# 2. Architecture Overview

## 2.1 Architecture Goals

The architecture is designed to achieve the following goals:

1. **Modularity** – individual system responsibilities should be separated into manageable components.
2. **Maintainability** – changes to one module should have minimal impact on unrelated modules.
3. **Security** – authentication, authorization, input validation, secure communication, and auditability must be enforced.
4. **Performance** – the architecture should support the response-time requirements specified in the SRS.
5. **Scalability** – the application server should be capable of horizontal scaling during peak registration periods.
6. **Reliability** – failures should be handled without corrupting application state.
7. **Testability** – components should be testable independently and through integration/system testing.

The SRS specifies a target of loading the event listing page within 3 seconds under normal load of up to 200 concurrent users and processing a registration request within 2 seconds.

## 2.2 Constraints

The following constraints apply:

- The application is web-based.
- The implementation uses the MERN stack.
- Client-server communication uses HTTPS.
- REST/JSON is used for application APIs.
- Role-based access control is required.
- Administrator functions must not be available to Students.
- QR-based attendance must support manual registration-code entry as a fallback.
- College email is used for registration and notifications.
- Email delivery failure must not prevent the underlying application action.
- Payment gateway integration is outside the initial release.
- The project must operate within the college's approved hosting/infrastructure environment.

## 2.3 Stakeholders and Concerns

| Stakeholder | Architectural Concern |
|---|---|
| Student | Fast event discovery, registration, QR access, notifications, feedback |
| Event Organizer | Event management, approval status, registration information, attendance |
| Administrator | Approval workflow, role-restricted operations, auditability |
| Development Team | Modularity, maintainability, API clarity |
| QA Team | Testability, predictable interfaces, error handling |
| Project Evaluators | Requirement coverage, security, architecture quality |

---

# 3. Architecture

## 3.1 Architecture Style

CEMS uses a **layered architecture with a RESTful client-server interface**.

### Presentation Layer

Implemented using React.js.

Responsibilities:

- display pages and forms;
- collect user input;
- display events;
- provide role-specific navigation;
- display registration and approval status;
- display notifications;
- provide QR/manual attendance interfaces;
- display feedback forms and results.

### API / Controller Layer

Implemented using Node.js and Express.js.

Responsibilities:

- receive HTTP requests;
- authenticate requests;
- validate request structure;
- authorize operations using user roles;
- invoke the appropriate business service;
- return standardized JSON responses.

### Business Logic Layer

Contains the application's core rules.

Responsibilities include:

- authentication and account controls;
- event creation and approval;
- event search/filtering;
- capacity and registration rules;
- waitlist handling;
- QR attendance validation;
- notification triggering;
- feedback eligibility and submission;
- audit actions.

### Data Access Layer

Responsible for communication with MongoDB.

Responsibilities:

- create, retrieve, update, and delete application data;
- enforce data-level consistency through application validation;
- provide reusable database access functions.

### External Services Layer

Includes external services required by the application, primarily college email/SMTP for notifications.

---

## 3.2 Component Diagram

The component architecture is represented by:

**`CEMS_Component_Diagram.png`**

The major flow is:

```text
Student / Organizer / Administrator
                |
                v
        React Web Frontend
                |
          HTTPS / REST
                |
                v
       Node.js + Express API
                |
     +----------+-----------+
     |          |           |
     v          v           v
 Authentication Event     Registration
 & RBAC       Management   & RSVP
     |          |           |
     +----------+-----------+
                |
        +-------+--------+
        |                |
        v                v
 Attendance          Feedback
        |
        v
 Notification Service -----> Email / SMTP
        |
        v
     MongoDB
```

The diagram is conceptual and shows the principal system components and their dependencies.

---

## 3.3 Component Descriptions

### 3.3.1 Authentication and RBAC Component

Responsibilities:

- Student/Organizer registration;
- login and logout;
- password reset;
- password hashing;
- JWT-based authentication;
- account lockout/rate limiting;
- role-based authorization.

Roles:

- Student
- Organizer
- Administrator

Administrator accounts are pre-provisioned and are not self-registered.

### 3.3.2 Event Management Component

Responsibilities:

- create event drafts;
- edit events;
- cancel events;
- validate event information;
- validate future date/time;
- detect venue conflicts;
- submit events for approval;
- maintain event status.

Typical event states include:

```text
Draft -> Pending Approval -> Approved
                         -> Rejected
                         -> Changes Requested
Approved -> Cancelled
```

Material edits to an approved event cause the event to return to the approval workflow according to the SRS.

### 3.3.3 Event Approval Component

Responsibilities:

- display pending events to administrators;
- approve events;
- reject events with a reason;
- request changes;
- record approval decisions;
- trigger organizer notifications.

### 3.3.4 Event Browsing Component

Responsibilities:

- list approved events;
- search by keyword;
- filter by category, date range, venue, and department;
- sort by date or popularity;
- display event details;
- display remaining seats.

### 3.3.5 Registration / RSVP Component

Responsibilities:

- register students for approved events;
- prevent duplicate registration;
- check registration deadline;
- check capacity;
- add students to the waitlist when capacity is full;
- promote the first eligible waitlisted student when a seat becomes available;
- allow students to view and cancel their own upcoming registrations;
- generate a unique registration/QR code.

### 3.3.6 Attendance Component

Responsibilities:

- scan registration QR codes;
- support manual registration-code entry;
- validate registration and event;
- enforce the event check-in window;
- prevent duplicate check-ins;
- record attendance;
- reject invalid or already-used codes.

### 3.3.7 Notification Component

Responsibilities:

- create in-app notifications;
- send relevant email notifications;
- notify organizers about approval decisions;
- send registration confirmations;
- send event update/cancellation notifications;
- handle waitlist notifications;
- send event reminders;
- record notification delivery failures.

Email failure must not prevent the underlying system action. The failure is logged and the in-app notification remains available.

### 3.3.8 Feedback Component

Responsibilities:

- verify student attendance/eligibility;
- accept one feedback submission per student per event;
- accept rating and comments;
- prevent feedback before the event or for ineligible students;
- calculate/display aggregate ratings where permitted.

---

## 3.4 Technology Stack and Data Stores

| Layer | Technology | Purpose |
|---|---|---|
| Frontend | React.js | User interface |
| Backend Runtime | Node.js | Server-side JavaScript runtime |
| API Framework | Express.js | REST API and middleware |
| Database | MongoDB | Persistent application data |
| Authentication | JWT | Authenticated session/token mechanism |
| Communication | REST + JSON | Frontend/backend communication |
| Secure Transport | HTTPS/TLS | Confidential communication |
| Notifications | Email/SMTP + in-app notifications | User notifications |

MongoDB stores application entities such as users, events, registrations, attendance records, notifications, feedback, and audit records.

---

## 3.5 Architecture Decisions

### ADR-001: MERN Stack

**Decision:** Use MongoDB, Express.js, React.js, and Node.js.

**Reason:** The project requires a web application with a JavaScript-based frontend and backend. A common language across the application simplifies development and integration.

### ADR-002: Layered Architecture

**Decision:** Separate presentation, API/controller, business logic, data access, and external service responsibilities.

**Reason:** Separation of concerns improves maintainability, testing, and future modification.

### ADR-003: RESTful API

**Decision:** Use REST APIs with JSON payloads.

**Reason:** REST provides a clear interface between the React frontend and Node/Express backend.

### ADR-004: MongoDB

**Decision:** Use MongoDB as the primary database.

**Reason:** MongoDB provides document-oriented persistence suitable for the event-management data model and integrates naturally with the Node.js ecosystem.

### ADR-005: JWT-Based Authentication

**Decision:** Use JWT-based authentication with secure cookie handling.

**Reason:** The SRS requires token-based authentication and security controls for authenticated sessions.

---

## 3.6 Risks and Mitigations

| Risk | Mitigation |
|---|---|
| Database failure | Automated daily backups |
| Unauthorized access | Authentication + RBAC |
| Malicious input | Server-side validation and sanitization |
| QR code misuse | Validate registration, event, time window, and check-in state |
| Email failure | Log failure and retain in-app notification |
| Duplicate registration | Server-side uniqueness/business-rule validation |
| Registration spikes | Efficient queries and horizontally scalable application server |
| Application errors | Centralized error handling and logging |
| Data loss | Regular database backups |

---

## 3.7 Traceability to Requirements

| Requirement | Architectural Component / Design |
|---|---|
| FR-01 Authentication | Authentication and RBAC |
| FR-02 Event Management | Event Management |
| FR-03 Event Approval | Event Approval |
| FR-04 Browse/Search/Filter | Event Browsing + REST API |
| FR-05 Registration/RSVP | Registration / RSVP |
| FR-06 Attendance | Attendance Component |
| FR-07 Notifications | Notification Component + Email |
| FR-08 Feedback | Feedback Component |
| NFR-01 Performance | Node/Express API + optimized MongoDB access |
| NFR-02 Security | HTTPS/TLS + JWT + RBAC + validation |
| NFR-03 Usability | Responsive React UI |
| NFR-04 Reliability | Error handling + backups |
| NFR-05 Scalability | Stateless application-server design and scalable deployment |
| NFR-06 Maintainability | Layered modular architecture |
| NFR-07 Portability | Browser-based React application |
| Security Requirements | HTTPS, password hashing, RBAC, account controls, JWT security, input validation, QR validation, audit logging |

---

## 3.8 Security Architecture

The security flow is:

```text
User
  |
  v
HTTPS / TLS
  |
  v
React Frontend
  |
  v
Express API
  |
  +--> Authentication / JWT
  |
  +--> Input Validation
  |
  +--> RBAC Authorization
  |
  v
Business Logic
  |
  v
MongoDB
```

### Security Controls

1. **HTTPS/TLS** protects communication between client and server.
2. **Password hashing** prevents plaintext password storage.
3. **Authentication** verifies user identity.
4. **RBAC** restricts operations according to Student, Organizer, and Administrator roles.
5. **Account controls** limit repeated failed authentication attempts.
6. **JWT security** protects authenticated sessions.
7. **Input validation and sanitization** reduce malformed or malicious input.
8. **QR validation** prevents unauthorized or reused attendance codes.
9. **Audit logging** records security-sensitive actions.
10. Sensitive information such as passwords and authentication tokens must not be written to ordinary application logs.

---

# 4. Design

## 4.1 Design Overview

The design follows a request-response model.

1. A user interacts with the React frontend.
2. React sends an HTTPS request to the Express API.
3. Authentication middleware verifies the JWT where required.
4. Authorization middleware verifies the user's role.
5. The relevant business service validates the operation.
6. The data-access layer reads or updates MongoDB.
7. If required, the notification service creates an in-app notification and attempts email delivery.
8. The API returns a JSON response.
9. React updates the user interface.

Business rules are enforced on the server rather than relying only on client-side validation.

---

# 4.2 Sequence Diagram – Event Registration

The sequence is represented by:

**`CEMS_Sequence_Event_Registration.png`**

Main interaction:

```text
Student
   |
   | Open approved event
   v
React Frontend
   |
   | POST registration request
   v
Express API
   |
   | Authenticate JWT
   v
Auth/RBAC Middleware
   |
   | Authorized Student
   v
Registration Service
   |
   | Check event status/deadline/capacity
   v
MongoDB
   |
   | Check duplicate registration
   v
Registration Service
   |
   | Create registration / waitlist entry
   v
MongoDB
   |
   | Generate registration/QR information
   v
Notification Service
   |
   | Create confirmation
   v
Student
```

### Important design rules

- Only approved events can receive registrations.
- Registration after the deadline is rejected.
- Duplicate registration is rejected.
- If capacity is available, the student is registered.
- If capacity is full, the student is offered the waitlist.
- A successful registration receives a unique registration/QR code.
- Registration cancellation can free a seat and trigger waitlist promotion according to the defined business rule.

---

# 4.3 Sequence Diagram – Event Approval

The sequence is represented by:

**`CEMS_Sequence_Event_Approval.png`**

Main interaction:

```text
Organizer
   |
   | Create / edit event
   v
React Frontend
   |
   | Submit event
   v
Express API
   |
   | Authenticate + authorize
   v
Event Management Service
   |
   | Validate event
   v
MongoDB
   |
   | Save Pending Approval
   v
Administrator
   |
   | View pending event
   v
React Frontend
   |
   | Approve / Reject / Request Changes
   v
Express API
   |
   v
Approval Service
   |
   | Record decision
   v
MongoDB
   |
   | Trigger notification
   v
Notification Service
   |
   v
Organizer
```

### Approval rules

- Newly created events require administrator approval before becoming publicly visible.
- An administrator can approve, reject with a reason, or request changes.
- Rejected events remain editable and can be resubmitted.
- Material changes to approved events return the event to the approval workflow.
- Approval decisions are auditable.

---

# 4.4 API Design

The following API design is proposed to implement the requirements. Endpoint names should be kept consistent with the final implementation.

## Authentication APIs

| Method | Endpoint | Purpose | Access |
|---|---|---|---|
| POST | `/api/auth/register` | Register Student/Organizer | Public |
| POST | `/api/auth/login` | Authenticate user | Public |
| POST | `/api/auth/logout` | End authenticated session | Authenticated |
| POST | `/api/auth/forgot-password` | Initiate password reset | Public |

### Registration Request

```json
{
  "name": "Student Name",
  "email": "student@college.edu",
  "password": "password",
  "role": "Student"
}
```

## Event APIs

| Method | Endpoint | Purpose | Access |
|---|---|---|---|
| GET | `/api/events` | Browse/search/filter approved events | Public/Student |
| GET | `/api/events/:id` | View event details | Public/Student |
| POST | `/api/events` | Create event | Organizer |
| PUT | `/api/events/:id` | Edit event | Owner Organizer |
| DELETE | `/api/events/:id` | Cancel/delete event | Owner Organizer |

## Approval APIs

| Method | Endpoint | Purpose | Access |
|---|---|---|---|
| GET | `/api/admin/events/pending` | View pending events | Administrator |
| POST | `/api/admin/events/:id/approve` | Approve event | Administrator |
| POST | `/api/admin/events/:id/reject` | Reject event | Administrator |
| POST | `/api/admin/events/:id/request-changes` | Request changes | Administrator |

## Registration APIs

| Method | Endpoint | Purpose | Access |
|---|---|---|---|
| POST | `/api/events/:id/register` | Register student | Student |
| DELETE | `/api/events/:id/register` | Cancel registration | Student |
| GET | `/api/registrations/me` | View own registrations | Student |

## Attendance APIs

| Method | Endpoint | Purpose | Access |
|---|---|---|---|
| POST | `/api/events/:id/check-in` | Check in using QR/code | Organizer |

## Feedback APIs

| Method | Endpoint | Purpose | Access |
|---|---|---|---|
| POST | `/api/events/:id/feedback` | Submit feedback | Eligible Student |
| GET | `/api/events/:id/feedback` | View permitted feedback/ratings | Authorized |

### API Response Convention

Successful responses should return JSON with the relevant resource or operation status.

Example:

```json
{
  "status": "success",
  "message": "Registration successful",
  "data": {
    "registrationId": "REG-12345"
  }
}
```

---

# 4.5 Error Handling

The backend uses centralized error handling so that API responses remain consistent.

| HTTP Status | Meaning | Example |
|---|---|---|
| 400 | Bad Request | Invalid input |
| 401 | Unauthorized | Missing/invalid authentication |
| 403 | Forbidden | Insufficient role |
| 404 | Not Found | Event/registration not found |
| 409 | Conflict | Duplicate registration or conflicting state |
| 429 | Too Many Requests | Authentication/request rate limit |
| 500 | Internal Server Error | Unexpected server failure |

Example error response:

```json
{
  "status": "error",
  "code": "EVENT_NOT_FOUND",
  "message": "Event not found"
}
```

Error messages must not expose passwords, JWTs, database credentials, stack traces, or other sensitive implementation details.

Client-side validation provides immediate feedback, but server-side validation remains authoritative.

---

# 4.6 Logging and Monitoring

The application should record operational and security-relevant events such as:

- failed authentication attempts;
- authorization failures;
- event creation and approval decisions;
- registrations and cancellations;
- attendance check-ins;
- notification delivery failures;
- API errors;
- database errors;
- significant performance/response-time information;
- audit-sensitive administrator actions.

The following must not be logged in ordinary application logs:

- passwords;
- JWT values;
- database credentials;
- unnecessary sensitive personal information.

Logs should contain timestamps and sufficient context to diagnose failures without exposing sensitive information.

---

# 4.7 User Interface Design

The React frontend should provide a responsive interface for desktop, tablet, and mobile browsers.

## Student Interface

Primary functions:

- Register/Login
- Browse events
- Search and filter
- View event details
- Register/RSVP
- View registration/QR code
- View notifications
- Cancel upcoming registration
- Submit eligible feedback

## Organizer Interface

Primary functions:

- Create event
- Save draft
- Edit event
- Submit for approval
- View approval status
- View registrations
- Mark attendance
- Manage event cancellation/updates

## Administrator Interface

Primary functions:

- View pending events
- Approve events
- Reject events with reasons
- Request changes
- Manage authorized administrative operations
- View audit information and system-level information permitted by the SRS

### UI Design Principles

- Role-specific navigation
- Clear event status indicators
- Clear validation and error messages
- Confirmation for destructive actions
- Responsive forms and tables
- Accessible controls
- Consistent feedback after successful actions
- QR check-in with manual-code fallback

---

# 5. Open Issues and Future Enhancements

The following are possible future enhancements and are not required for the initial release:

1. Mobile application for Android/iOS.
2. Calendar integration.
3. Push notifications.
4. Advanced event analytics and reporting.
5. Integration with college Single Sign-On.
6. Integration with live venue/facility booking.
7. Payment gateway support for paid events.

These enhancements should not be considered part of the current implementation unless explicitly added to the SRS.

---

# 6. Appendices

## Appendix A – Diagram Files

The SAD package contains the following UML/architecture diagrams:

1. `CEMS_Component_Diagram.png`
2. `CEMS_Sequence_Event_Registration.png`
3. `CEMS_Sequence_Event_Approval.png`

## Appendix B – Requirement Traceability Summary

The architecture provides coverage for:

- FR-01 – User Registration and Authentication
- FR-02 – Event Creation and Management
- FR-03 – Event Approval Workflow
- FR-04 – Event Browsing, Search and Filtering
- FR-05 – Event Registration / RSVP
- FR-06 – Attendance Tracking
- FR-07 – Notification System
- FR-08 – Feedback and Rating System

and the SRS-defined non-functional and security requirements.

---

# End of Document
