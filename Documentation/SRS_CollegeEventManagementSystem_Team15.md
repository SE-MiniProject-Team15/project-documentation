# Software Requirements Specification

## College Event Management System

## Team 15

Tech Stack: JavaScript (MERN Stack) / Python (Django)

Document Control

Table of Contents

## 1. Introduction

### 1.1 Purpose

### 1.2 Scope

### 1.3 Definitions, Acronyms and Abbreviations

### 1.4 References

### 1.5 Overview

## 2. Overall Description

### 2.1 Product Perspective

### 2.2 Product Functions (Summary)

### 2.3 User Classes and Characteristics

### 2.4 Operating Environment

### 2.5 Design and Implementation Constraints

### 2.6 Assumptions and Dependencies

## 3. Specific Requirements

### 3.1 Functional Requirements (FR-01 to FR-08)

### 3.2 Non-Functional Requirements

## 4. External Interface Requirements

## 5. Key Use Cases

## 6. Appendix

> **Reserved for a future release.**
| SRN | Name |
| --- | --- |
| PES1UG24CS576 | Leesha R |
| PES1UG24CS593 | Prajwal Manjunath Hegde |
| PES1UG24CS570 | G Purvi |
| PES1UG24CS559 | Ashith Rao K |

| Document Title | Software Requirements Specification – College Event Management System |
| --- | --- |
| Version | 1.0 |
| Team | Team 15 |
| Date | 10 September 2026 |

| Term | Definition |
| --- | --- |
| CEMS | College Event Management System |
| SRS | Software Requirements Specification |
| FR | Functional Requirement |
| NFR | Non-Functional Requirement |
| JWT | JSON Web Token |
| RSVP | Répondez s'il vous plaît (event registration response) |
| QR Code | Quick Response Code, used for attendance check-in |
| Admin | College-level Administrator with approval authority |
| Organizer | Faculty member or club representative who creates events |

| User Class | Characteristics |
| --- | --- |
| Student | Browses events, registers/RSVPs, receives notifications, and submits feedback. No technical expertise assumed. |
| Organizer | Faculty or club representative who creates, edits, and manages events, and marks attendance. Comfortable with basic web forms. |
| Administrator | Approves/rejects events, manages users, and views system-wide reports. Has the highest privilege level. |

| Category | Requirement |
| --- | --- |
| Performance | The system shall load the event listing page within 3 seconds under normal load (up to 200 concurrent users) and process a registration request within 2 seconds. |
| Security | All communication shall use HTTPS/TLS. Passwords shall be hashed (never stored in plain text). Role-based access control shall restrict Admin/Organizer-only functions from Students. |
| Usability | The interface shall be responsive (desktop, tablet, mobile) and usable by a first-time user without training, following standard web accessibility guidelines (WCAG 2.1 AA where feasible). |
| Reliability / Availability | The system shall be available 99% of the time during active college semesters, with automated daily database backups. |
| Scalability | The system architecture shall support horizontal scaling of the application server to accommodate peak registration periods (e.g., fest season). |
| Maintainability | The codebase shall follow a modular MVC structure with documented REST APIs to allow independent maintenance of frontend and backend. |
| Portability | The web application shall function correctly on the latest two major versions of Chrome, Firefox, Edge, and Safari. |

| ID | Use Case | Actor | Description |
| --- | --- | --- | --- |
| UC-01 | Register for an Event | Student | Student browses events, selects one, and registers before the deadline/capacity limit. |
| UC-02 | Create and Submit an Event | Organizer | Organizer fills event details and submits for Admin approval. |
| UC-03 | Approve/Reject an Event | Administrator | Admin reviews a pending event and approves or rejects it with a reason. |
| UC-04 | Check In Attendee | Organizer | Organizer scans/enters a student's registration code to mark attendance at the venue. |
| UC-05 | Submit Event Feedback | Student | Attended student rates the event and leaves a comment after it ends. |

| SRN | Name |
| --- | --- |
| PES1UG24CS576 | Leesha R |
| PES1UG24CS593 | Prajwal Manjunath Hegde |
| PES1UG24CS570 | G Purvi |
| PES1UG24CS559 | Ashith Rao K |
