# Software Test Plan (STP) - College Event Management System (CEMS)

**Project:** College Event Management System (CEMS)
**Version:** 1.0
**Authors:** Team 15
**Date:** 01-10-2026
**Status:** Draft

## 1. Introduction

**Purpose:** This document defines the test plan for the College Event Management System (CEMS) v1.0. It outlines objectives, scope, strategy, resources, schedule, and responsibilities for testing the system across all three user roles (Student, Organizer, Administrator).

**Scope:** Testing covers the full event lifecycle supported by CEMS — user registration/authentication, event creation and approval workflow, event browsing/search, event registration (RSVP), QR-based attendance tracking, notifications, and post-event feedback. Third-party email/SMTP delivery internals and any future SSO/college-ID integration are excluded, as these are out of scope for v1 per the SRS.

**References:** SRS_CollegeEventManagementSystem.md (Team 15, v1.1), IEEE Std 830-1998, Team 15 Project Charter / Problem Statement.

**Definitions:** CEMS (College Event Management System), SRS (Software Requirements Specification), FR (Functional Requirement), NFR (Non-Functional Requirement), JWT (JSON Web Token), RSVP (event registration response), QR Code (Quick Response Code used for attendance check-in), RTM (Requirements Traceability Matrix).

## 2. Test Items

- User Registration and Authentication module (Student / Organizer / Admin)
- Event Creation and Management module (Organizer)
- Event Approval Workflow module (Admin)
- Event Browsing, Search and Filtering module (Student)
- Event Registration / RSVP module (Student)
- Attendance Tracking (QR Check-in) module (Organizer)
- Notification System (email + in-app)
- Feedback and Rating System (Student)

## 3. Features to be Tested

Features mapped to SRS functional requirement IDs:

- FR-01: User Registration and Authentication (role-based login, JWT sessions, forgot-password, account lockout after 5 failed attempts)
- FR-02: Event Creation and Management (draft/submit, edit/cancel, future-date validation, venue double-booking check)
- FR-03: Event Approval Workflow (Admin approve/reject/request-changes, resubmission of rejected events)
- FR-04: Event Browsing, Search and Filtering (keyword search, category/date/venue/department filters, sorting)
- FR-05: Event Registration / RSVP (capacity enforcement, waitlist, duplicate-registration prevention, QR confirmation, cancellation)
- FR-06: Attendance Tracking / QR Check-in (scan/manual entry, invalid/reused code rejection, real-time dashboard update)
- FR-07: Notification System (registration confirmation, approval/rejection, updates/cancellations, 24-hour reminders, notification history)
- FR-08: Feedback and Rating System (post-event-only availability, attendee-only access, 1–5 rating with comment, aggregated view for Organizer/Admin)
- NFR-Performance: Event listing loads within 3s under 200 concurrent users; registration processed within 2s
- NFR-Security: HTTPS/TLS, password hashing, role-based access control
- NFR-Reliability: 99% availability during active semesters with daily backups
- NFR-Portability: Correct functioning on latest two major versions of Chrome, Firefox, Edge, Safari

## 4. Features Not to be Tested

- Third-party email/SMTP provider internals (e.g., SendGrid/Nodemailer delivery infrastructure) — only the trigger and logging behavior on CEMS's side is tested
- Future SSO / college-ID integration — explicitly out of scope for v1 per SRS §2.1 and §2.6
- Payment gateway integration for paid events — explicitly out of scope for v1 per SRS §2.6
- Physical QR scanner hardware — only the manual code entry fallback and scan-result handling within CEMS are tested
- Underlying database engine reliability (MongoDB internals) — assumed stable per platform vendor
