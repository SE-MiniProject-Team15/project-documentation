# **Software Requirements Specification** 

## **College Event Management System** 

Team 15 

_Tech Stack: JavaScript (MERN Stack) / Python (Django)_ 

|**SRN**|**Name**|
|---|---|
|PES1UG24CS576|Leesha R|
|PES1UG24CS593|Prajwal Manjunath Hegde|
|PES1UG24CS570|G Purvi|
|PES1UG24CS559|Ashith Rao K|



## Document Control 

|**Document Title**|Software Requirements Specification – College Event<br>Management System|
|---|---|
|**Version**|1.0|
|**Team**|Team 15|
|**Date**|10 September 2026|



## Table of Contents 

1. Introduction 

- 1.1 Purpose 

- 1.2 Scope 

- 1.3 Definitions, Acronyms and Abbreviations 

- 1.4 References 

- 1.5 Overview 

2. Overall Description 

- 2.1 Product Perspective 

- 2.2 Product Functions (Summary) 

- 2.3 User Classes and Characteristics 

- 2.4 Operating Environment 

- 2.5 Design and Implementation Constraints 

- 2.6 Assumptions and Dependencies 

3. Specific Requirements 

- 3.1 Functional Requirements (FR-01 to FR-08) 

- 3.2 Non-Functional Requirements 

4. External Interface Requirements 

5. Key Use Cases 

6. Appendix: Team 15 Members 

## 1. Introduction 

### 1.1 Purpose 

This Software Requirements Specification (SRS) document describes the functional and non-functional requirements for the College Event Management System (CEMS). It is intended for use by the development team, project evaluators, and future maintainers to understand the scope, features, and constraints of the system before design and implementation begin. 

### 1.2 Scope 

CEMS is a web-based application that allows a college to manage the full lifecycle of campus events — from creation and approval, through student discovery and registration, to attendance tracking and post-event feedback. The system supports three roles: Student, Event Organizer (faculty/club representative), and Administrator. It will be built using the MERN stack (MongoDB, Express, React, Node.js) and/or Python (Django) for the backend, as decided during the design phase. 

### 1.3 Definitions, Acronyms and Abbreviations 

|**Term**|**Definition**|
|---|---|
|CEMS|College Event Management System|
|SRS|Software Requirements Specification|
|FR|Functional Requirement|
|NFR|Non-Functional Requirement|
|JWT|JSON Web Token|
|RSVP|Répondez s'il vous plaît (event registration response)|
|QR Code|Quick Response Code, used for attendance check-in|
|Admin|College-level Administrator with approval authority|
|Organizer|Faculty member or club representative who creates<br>events|



### 1.4 References 

- IEEE Std 830-1998, Recommended Practice for Software Requirements Specifications 

- Project Test Plan Document – Team 15 

- Team 15 Project Charter / Problem Statement 

### 1.5 Overview 

The remainder of this document is organized as follows: Section 2 gives an overall description of the product; Section 3 lists detailed functional and non-functional requirements; Section 4 describes external interface requirements; Section 5 lists key use cases; and Section 6 lists assumptions and constraints. 

## 2. Overall Description 

### 2.1 Product Perspective 

CEMS is a new, standalone web application. It is not a modification of an existing system. It will integrate with the college's email service (for notifications) and may optionally integrate with an SSO/college ID system in a future release. 

### 2.2 Product Functions (Summary) 

- User Registration and Authentication 

- Event Creation and Management 

- Event Approval Workflow 

- Event Browsing, Search and Filtering 

- Event Registration / RSVP 

- Attendance Tracking (QR Check-in) 

- Notification System 

- Feedback and Rating System 

### 2.3 User Classes and Characteristics 

|**User Class**|**Characteristics**|
|---|---|
|**Student**|Browses events, registers/RSVPs, receives<br>notifications, and submits feedback. No technical<br>expertise assumed.|
|**Organizer**|Faculty or club representative who creates, edits, and<br>manages events, and marks attendance. Comfortable<br>with basic web forms.|
|**Administrator**|Approves/rejects events, manages users, and views<br>system-wide reports. Has the highest privilege level.|



### 2.4 Operating Environment 

Server-side: Node.js/Express (MERN) or Django (Python), with MongoDB or PostgreSQL as the database. Clientside: a responsive web front end (React) accessible via modern desktop and mobile browsers over HTTPS. 

### 2.5 Design and Implementation Constraints 

- Must be delivered using the JavaScript (MERN) stack and/or Python (Django), as assigned to Team 15. 

- Must be completed within the academic semester project timeline. 

- Must run within the college's approved hosting/infrastructure environment. 

### 2.6 Assumptions and Dependencies 

- Users have access to a college-issued or personal email address for registration and notifications. 

- Event venues and their capacities are known and entered manually by Organizers (no live facility-booking integration in v1). 

- Payment gateway integration for paid events is out of scope for the initial release. 

## 3. Specific Requirements 

### 3.1 Functional Requirements 

#### FR-01: User Registration and Authentication 

The system shall allow Students, Event Organizers, and Administrators to register and log in using role-based credentials. 

- Users register with name, college email ID, password, and role (Student / Organizer). 

- Passwords are stored using a secure hashing algorithm (e.g., bcrypt). 

- Admin accounts are pre-provisioned and not self-registered. 

- The system supports login, logout, forgot-password, and session/token-based authentication (JWT). 

- Invalid login attempts display an appropriate error message; accounts lock after 5 consecutive failed attempts. 

#### FR-02: Event Creation and Management 

The system shall allow Organizers to create, edit, and delete college events with all relevant details. 

- Event fields: title, category, description, venue, date/time, capacity, banner image, and registration deadline. 

- Organizers can save an event as a draft before submitting for approval. 

- Organizers can edit or cancel events they own, subject to approval status. 

- The system validates that event date/time is in the future and venue is not double-booked. 

#### FR-03: Event Approval Workflow 

The system shall route every newly created or edited event to an Administrator for approval before it becomes publicly visible. 

- Admin can view a queue of pending events with full details. 

- Admin can Approve, Reject (with a reason), or request changes. 

- Organizer receives a notification of the approval decision. 

- Rejected events remain editable and re-submittable by the Organizer. 

#### FR-04: Event Browsing, Search and Filtering 

The system shall allow Students to browse, search, and filter approved events. 

- Search by keyword (title/description) and filter by category, date range, venue, and department. 

- Results are sortable by date and popularity (registration count). 

- Each event has a detail page showing all event information and remaining seats. 

#### FR-05: Event Registration / RSVP 

The system shall allow Students to register for an approved event before the registration deadline and available capacity. 

- System prevents registration once capacity is full and shows a 'Waitlist' option instead. 

- System prevents duplicate registration by the same student for the same event. 

- A confirmation (with a unique registration/QR code) is generated on successful registration. 

- Students can view and cancel their own upcoming registrations. 

#### FR-06: Attendance Tracking (QR Check-in) 

The system shall allow Organizers to mark attendance at the event venue using the registration QR code. 

- Organizer/Volunteer scans or manually enters the registration code to mark a student present. 

- System rejects an already-used or invalid QR code with a clear error. 

- Attendance data is available in real time to the Organizer and Admin dashboards. 

#### FR-07: Notification System 

The system shall notify users of key events via email and in-app notifications. 

- Notifications are sent for: registration confirmation, event approval/rejection, event updates or cancellation, and reminders 24 hours before the event. 

- Users can view a notification history within the application. 

- Email delivery failures are logged and do not block the underlying action. 

#### FR-08: Feedback and Rating System 

The system shall allow Students who attended an event to submit a rating and feedback after the event concludes. 

- Feedback form becomes available only after the event end time and only to marked-attendee students. 

- Rating is on a 1–5 scale with an optional text comment. 

- Organizers and Admin can view aggregated ratings and comments per event. 

### 3.2 Non-Functional Requirements 

|**Category**|**Requirement**|
|---|---|
|**Performance**|The system shall load the event listing page within 3<br>seconds under normal load (up to 200 concurrent users)<br>and process a registration request within 2 seconds.|
|**Security**|All communication shall use HTTPS/TLS. Passwords<br>shall be hashed (never stored in plain text). Role-based<br>access control shall restrict Admin/Organizer-only<br>functions from Students.|
|**Usability**|The interface shall be responsive (desktop, tablet,<br>mobile) and usable by a first-time user without training,<br>following standard web accessibility guidelines<br>(WCAG 2.1 AA where feasible).|
|**Reliability / Availability**|The system shall be available 99% of the time during<br>active college semesters, with automated daily database<br>backups.|
|**Scalability**|The system architecture shall support horizontal scaling<br>of the application server to accommodate peak<br>registration periods (e.g., fest season).|
|**Maintainability**|The codebase shall follow a modular MVC structure<br>with documented REST APIs to allow independent<br>maintenance of frontend and backend.|
|**Portability**|The web application shall function correctly on the<br>latest two major versions of Chrome, Firefox, Edge,<br>and Safari.|



## 4. External Interface Requirements 

- 4.1 User Interfaces 

   - Responsive web UI with distinct dashboards for Student, Organizer, and Admin roles. 

   - Event listing page, event detail page, registration/QR confirmation screen, and feedback form. 

### 4.2 Hardware Interfaces 

None beyond a standard device with a camera/scanner (optional) for QR-based check-in; manual code entry is provided as a fallback. 

- 4.3 Software Interfaces 

   - Email/SMTP service (e.g., SendGrid or Nodemailer) for notifications. 

   - Database: MongoDB (MERN) or PostgreSQL (Django). 

### 4.4 Communication Interfaces 

All client–server communication occurs over HTTPS using a RESTful JSON API. 

## 5. Key Use Cases 

|**ID**|**Use Case**|**Actor**|**Description**|
|---|---|---|---|
|UC-01|**Register for an Event**|Student|Student browses events,<br>selects one, and registers<br>before the<br>deadline/capacity limit.|
|UC-02|**Create and Submit an**<br>**Event**|Organizer|Organizer fills event<br>details and submits for<br>Admin approval.|
|UC-03|**Approve/Reject an**<br>**Event**|Administrator|Admin reviews a pending<br>event and approves or<br>rejects it with a reason.|
|UC-04|**Check In Attendee**|Organizer|Organizer scans/enters a<br>student's registration code<br>to mark attendance at the<br>venue.|
|UC-05|**Submit Event Feedback**|Student|Attended student rates the<br>event and leaves a<br>comment after it ends.|



## 6. Appendix: Team 15 Members 

||**SRN**|**Name**|
|---|---|---|
|PES1UG24CS576||Leesha R|
|PES1UG24CS593||Prajwal Manjunath Hegde|



||**SRN**||**Name**|
|---|---|---|---|
|PES1UG24CS570||G Purvi||
|PES1UG24CS559||Ashith Rao K||



