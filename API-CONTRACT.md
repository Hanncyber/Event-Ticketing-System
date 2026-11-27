# Event Ticketing System — Shared API Contract

**Project:** Event Ticketing System  
**Version:** 1.0 — Initial proposal  
**Backend:** Node.js + Express  
**Database:** MongoDB + Mongoose  
**Frontends:** Organizer React + Attendee React

## 1. Purpose

This document defines how the Organizer frontend and Attendee frontend communicate with the shared backend.

Both frontend teams must follow the same:
- API endpoints
- HTTP methods
- Request body formats
- Response formats
- Field names
- Authentication rules
- Error responses

The backend team is responsible for implementing these endpoints.

## 2. Backend connection

Local development base URL:

`http://localhost:5000/api`

Both frontends use this same backend.

- Organizer frontend: `http://localhost:5173`
- Attendee frontend: `http://localhost:5174`
- Backend: `http://localhost:5000`

## 3. General API rules

- All request and response bodies use JSON.
- Use the correct HTTP method for each operation.
- Protected endpoints require a valid authentication token.
- Use `Authorization: Bearer <token>` for authenticated requests.
- Do not change agreed field names without informing both teams.

## 4. Authentication APIs

| Method  | Endpoint          | Purpose                 | Access |
|---      |---                |---                      |---     |
| POST    | `/auth/register`  | Create an account       | Public |
| POST    | `/auth/login`     | Log in                  | Public |
| GET     | `/auth/me`        | Get logged-in user      | Authenticated |

### Register — POST `/auth/register`

Request:
```json
{
  "name": "Sara Ahmed",
  "email": "sara@example.com",
  "password": "examplePassword"
}
```

Response:
```json
{
  "success": true,
  "message": "Account created successfully",
  "data": {
    "id": "USER_ID",
    "name": "Sara Ahmed",
    "email": "sara@example.com"
  }
}
```

### Login — POST `/auth/login`

Request:
```json
{
  "email": "sara@example.com",
  "password": "examplePassword"
}
```

Response:
```json
{
  "success": true,
  "message": "Login successful",
  "data": {
    "token": "AUTH_TOKEN",
    "user": {
      "id": "USER_ID",
      "name": "Sara Ahmed",
      "role": "attendee"
    }
  }
}
```

## 5. Events APIs

These APIs are shared between the Organizer and Attendee frontends.

| Method | Endpoint | Purpose | Access |
|--     -|---|---|---|
| POST | `/events` | Create an event | Organizer |
| GET | `/events` | Get all available events | Public |
| GET | `/events/:id` | Get one event | Public |
| PUT | `/events/:id` | Update an event | Event owner / Organizer |
| DELETE | `/events/:id` | Delete an event | Event owner / Organizer |

### Create event — POST `/events`

Request:
```json
{
  "title": "Music Festival",
  "description": "A live music event",
  "location": "Cairo",
  "price": 500,
  "availableTickets": 100,
  "startDate": "2027-01-15T18:00:00.000Z",
  "endDate": "2027-01-15T23:00:00.000Z"
}
```

Response:
```json
{
  "success": true,
  "message": "Event created successfully",
  "data": {
    "id": "EVENT_ID",
    "title": "Music Festival",
    "location": "Cairo",
    "price": 500,
    "availableTickets": 100
  }
}
```

### Get all events — GET `/events`

Response:
```json
{
  "success": true,
  "data": [
    {
      "id": "EVENT_ID",
      "title": "Music Festival",
      "location": "Cairo",
      "price": 500,
      "availableTickets": 100
    }
  ]
}
```

### Get one event — GET `/events/:id`

Returns the details of one event.

### Update event — PUT `/events/:id`

Uses the same event fields as Create Event. The backend validates the updated information.

### Delete event — DELETE `/events/:id`

Deletes an event if the requesting organizer is authorized to do so.

## 6. Booking APIs

| Method | Endpoint | Purpose | Access |
|---|---|---|---|
| POST | `/bookings` | Book tickets | Attendee |
| GET | `/bookings/my-bookings` | View my bookings | Attendee |
| GET | `/bookings/:id` | View one booking | Authorized user |
| PUT | `/bookings/:id/cancel` | Cancel a booking | Authorized user |
| GET | `/events/:eventId/bookings` | View bookings for an event | Event owner / Organizer |

### Create booking — POST `/bookings`

Request:
```json
{
  "eventId": "EVENT_ID",
  "quantity": 2
}
```

Response:
```json
{
  "success": true,
  "message": "Booking created successfully",
  "data": {
    "id": "BOOKING_ID",
    "eventId": "EVENT_ID",
    "quantity": 2,
    "totalPrice": 1000,
    "status": "confirmed"
  }
}
```

The backend must check ticket availability before confirming a booking and updating the remaining ticket count.

## 7. User APIs

| Method | Endpoint | Purpose | Access |
|---|---|---|---|
| GET | `/users/me` | View my profile | Authenticated |
| PUT | `/users/me` | Update my profile | Authenticated |

## 8. Standard response format

Successful response:

```json
{
  "success": true,
  "message": "Operation successful",
  "data": {}
}
```

Error response:

```json
{
  "success": false,
  "message": "Something went wrong",
  "errors": []
}
```

## 9. HTTP status codes

| Status | Meaning |
|---|---|
| 200 | Request successful |
| 201 | Resource created |
| 400 | Invalid request data |
| 401 | Not authenticated |
| 403 | Not authorized |
| 404 | Resource not found |
| 409 | Conflict, such as insufficient tickets |
| 500 | Internal server error |

## 10. Responsibilities

**Backend team**
- Implement the endpoints.
- Validate incoming data.
- Apply authentication and authorization.
- Read and write data in MongoDB.
- Return responses according to this contract.

**Organizer frontend team**
- Use the agreed event management endpoints.
- Send requests using the agreed field names.
- Display the responses and errors.

**Attendee frontend team**
- Use the agreed event browsing and booking endpoints.
- Send requests using the agreed field names.
- Display event and booking information.

## 11. Important team agreement

Before implementation, both frontend teams and the backend team must review this document.

Any change to an endpoint, field name, request format, or response format must be agreed upon and communicated to all teams.

**Note:** This is the initial proposed contract. Authentication roles, organizer account creation, booking/payment behavior, event deletion rules, and cancellation/refund rules still need team approval. Payment processing is not included in this version.