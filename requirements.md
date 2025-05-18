# Airbnb Clone Backend Requirements

This document outlines the technical and functional requirements for three core backend features of the Airbnb Clone project: User Authentication, Property Management, and Booking System.

---

## 1. User Authentication

### Description
Handles user registration, login, and session management for guests and hosts.

### API Endpoints
- `POST /api/auth/register`  
  - Input: `{ "email": string, "password": string, "role": "guest" | "host" }`  
  - Output: `{ "userId": string, "token": string }`  
- `POST /api/auth/login`  
  - Input: `{ "email": string, "password": string }`  
  - Output: `{ "userId": string, "token": string }`  
- `GET /api/auth/profile`  
  - Input: Authorization header with JWT token  
  - Output: `{ "userId": string, "email": string, "role": string, "profilePhotoUrl": string, "preferences": object }`

### Validation Rules
- Email must be a valid email format.
- Password must be at least 8 characters with a mix of letters and numbers.
- Role must be either `guest` or `host`.
- Duplicate email registration must return an error.
- JWT token expiry set to 24 hours.

### Performance Criteria
- Authentication endpoints should respond within 300ms under normal load.
- Support concurrent login sessions for multiple devices.

---

## 2. Property Management

### Description
Allows hosts to create, update, delete, and view property listings.

### API Endpoints
- `POST /api/properties`  
  - Input:  
    ```json
    {
      "title": "string",
      "description": "string",
      "location": "string",
      "price": "number",
      "amenities": ["string"],
      "availability": [{ "startDate": "date", "endDate": "date" }]
    }
    ```  
  - Output: `{ "propertyId": string, "status": "created" }`

- `PUT /api/properties/{propertyId}`  
  - Input: Same as POST, fields optional for partial update  
  - Output: `{ "propertyId": string, "status": "updated" }`

- `DELETE /api/properties/{propertyId}`  
  - Output: `{ "propertyId": string, "status": "deleted" }`

- `GET /api/properties/{propertyId}`  
  - Output: Full property details JSON.

### Validation Rules
- Title must be non-empty and max 100 characters.
- Price must be a positive number.
- Availability dates must not overlap for the same property.
- Location must be a valid string (city or address).
- Only authenticated hosts can create/update/delete properties.

### Performance Criteria
- Listing retrieval must support pagination and return results in under 500ms.
- Property updates should be reflected in the search index within 5 seconds.

---

## 3. Booking System

### Description
Enables guests to book properties and manage their bookings.

### API Endpoints
- `POST /api/bookings`  
  - Input:  
    ```json
    {
      "propertyId": "string",
      "guestId": "string",
      "startDate": "date",
      "endDate": "date"
    }
    ```  
  - Output: `{ "bookingId": string, "status": "pending" }`

- `PUT /api/bookings/{bookingId}/cancel`  
  - Output: `{ "bookingId": string, "status": "canceled" }`

- `GET /api/bookings/{bookingId}`  
  - Output: Booking details including status (pending, confirmed, canceled, completed).

### Validation Rules
- Booking dates must fall within property availability.
- Prevent double bookings by checking date overlaps.
- Only the guest who made the booking or the host can cancel.
- Cancellation must respect property-specific cancellation policies.

### Performance Criteria
- Booking creation should complete within 500ms.
- Booking status updates must be propagated immediately to users.

---

# Notes

- All endpoints must return appropriate HTTP status codes (e.g., 200, 201, 400, 401, 404).
- API responses should follow consistent JSON structure with error handling.
- All data inputs must be sanitized to prevent injection attacks.
- Secure all endpoints with JWT-based authentication and role-based authorization.
