# CEMS REST API Reference

This reference describes the routes currently mounted by `server/server.js` and `server/routes`. The base path is `/api`; all request and response bodies use JSON unless stated otherwise.

## Authentication

The login and registration endpoints issue an HttpOnly JWT cookie. The React client uses credentialed requests. Protected routes require that cookie; the server also accepts a bearer token for API clients. Roles are `Student`, `Organizer`, and `Admin`.

| Method | Path | Access | Purpose |
|---|---|---|---|
| POST | `/auth/register` | Public | Create a student or organizer account. Admin self-registration is rejected. |
| POST | `/auth/login` | Public | Authenticate and issue the session cookie. |
| POST | `/auth/logout` | Public/session-clearing | Expire the session cookie. |
| GET | `/auth/me` | Authenticated | Return the current user's `id`, `name`, `email`, and `role`. |

Registration request:

```json
{
  "name": "Student Name",
  "email": "student@college.edu",
  "password": "example-password",
  "role": "Student"
}
```

`role` may be omitted; the server defaults it to `Student`.

## Events

| Method | Path | Access | Purpose |
|---|---|---|---|
| GET | `/events/public` | Public | List public approved-event details. |
| GET | `/events` | Student | Search and paginate approved events. |
| GET | `/events/:eventId` | Student | Return one approved event, including `isRegistered` for the current student. |
| POST | `/events` | Organizer | Create an event or draft. |
| GET | `/events/mine` | Organizer | List events owned by the organizer. |
| PATCH | `/events/:id` | Owning Organizer | Edit an event. Material changes to approved events return them to approval. |
| PATCH | `/events/:id/cancel` | Owning Organizer | Cancel an event. |
| PATCH | `/events/:id/approve` | Admin | Approve a pending event. |

`GET /events` accepts optional query parameters: `q`, `category`, `venue`, `department`, `dateFrom`, `dateTo`, `page`, `limit`, and `sort` (`date` or `popular`). It returns `data.events` and `data.pagination` (`page`, `limit`, `total`, `totalPages`).

Create-event requests use fields `title`, `category`, `description`, `venue`, `department`, `startDateTime`, `endDateTime`, `capacity`, `bannerUrl`, `registrationDeadline`, and `status` (`Draft` or `Pending Approval`). Complete event details and date/capacity rules are validated before submission.

Example submission:

```json
{
  "title": "Engineering Open Day",
  "category": "Workshop",
  "description": "Student project demonstrations",
  "venue": "Main Hall",
  "department": "Engineering",
  "startDateTime": "2026-11-20T09:00:00.000Z",
  "endDateTime": "2026-11-20T12:00:00.000Z",
  "capacity": 120,
  "bannerUrl": "https://example.edu/events/open-day.png",
  "registrationDeadline": "2026-11-19T17:00:00.000Z",
  "status": "Pending Approval"
}
```

## Registration and attendance

| Method | Path | Access | Purpose |
|---|---|---|---|
| POST | `/events/:eventId/registrations` | Student | Register for an approved event and receive a check-in code. Repeating this before check-in rotates the code and invalidates the old one. |
| POST | `/events/:eventId/attendance/check-in` | Organizer | Record a student's attendance using a scanned or manually entered code. The organizer must own the event. |
| GET | `/events/:eventId/attendance` | Owning Organizer or Admin | Return event attendance totals and registered students with check-in times. |
| GET | `/events/attendance/overview` | Admin | List events with registrations and attendance totals. |

Registration needs no request body. Example response:

```json
{
  "status": "success",
  "data": {
    "eventId": "507f1f77bcf86cd799439011",
    "code": "CEMS-0123456789ABCDEF0123456789ABCDEF",
    "registeredAt": "2026-11-02T10:00:00.000Z"
  }
}
```

The code is returned to the registered student so the client can display it and render a QR. Only its SHA-256 digest is stored. A new registration is accepted only while the event is approved, before its registration deadline, and while capacity remains. An existing student can rotate and recover an unused code after the deadline; the old code becomes invalid.

Check-in request:

```json
{
  "code": "CEMS-0123456789ABCDEF0123456789ABCDEF"
}
```

The code must belong to a registration for the selected event. Check-in is accepted from event start through event end, inclusive. A code cannot be used twice. Successful attendance records `attendedAt` and `checkedInBy` on the registration.

Successful check-in response:

```json
{
  "status": "success",
  "data": {
    "eventId": "507f1f77bcf86cd799439011",
    "studentId": "507f191e810c19729de860ea",
    "attendedAt": "2026-11-20T09:10:00.000Z",
    "checkedInBy": "507f191e810c19729de860eb"
  }
}
```

The attendance response includes event `id`, `title`, `status`, `startDateTime`, `endDateTime`, `capacity`, `registeredCount`, and `attendedCount`, plus a `registrations` array. Each entry contains `studentId`, `name`, `email`, `registeredAt`, `attendedAt` (null until checked in), and `checkedInBy` (null until checked in). Organizer and admin pages refresh this view every eight seconds.

## Notifications and role dashboards

| Method | Path | Access | Purpose |
|---|---|---|---|
| GET | `/notifications` | Authenticated | List the current user's notifications, newest first. |
| PATCH | `/notifications/:id/read` | Authenticated | Mark one of the current user's notifications as read. |
| GET | `/dashboard/student` | Student | Confirm access to the student dashboard. |
| GET | `/dashboard/organizer` | Organizer | Confirm access to the organizer dashboard. |
| GET | `/dashboard/admin` | Admin | Confirm access to the admin dashboard. |

## Response and error behavior

Successful resource responses use a JSON `status: "success"` and place result data in `data`. Validation, authorization, missing-resource, schedule-window, capacity, duplicate-registration, and reused-code errors use an HTTP error status and a body shaped like:

```json
{
  "status": "fail",
  "message": "Check-in is available only during the event"
}
```

Common status codes are `400` for malformed input, `401` for missing/invalid authentication, `403` for insufficient role or event ownership, `404` for an unknown resource or code, `409` for an invalid state transition or a registration/check-in conflict, and `500` for an unexpected server error. The centralized handler returns a generic message for `500` responses and logs the underlying error on the server.
