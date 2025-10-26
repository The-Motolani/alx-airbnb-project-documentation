# Requirements Specification – Airbnb Clone Backend

**Project:** Airbnb Clone Backend API  
**Document Version:** 1.0  
**Date:** 26/10/2025  
**Prepared by:** Motolani Alebiosu  

---

## 1. User Authentication & Authorization

### 1.1 Feature Overview

Allow users (guests, hosts, admins) to register, authenticate (login), and manage sessions securely. Apply role-based access control (RBAC) so that different roles can perform different operations.

### 1.2 API Endpoints

#### 1.2.1 Register User

- **Endpoint:** `POST /api/v1/users/register`  
- **Request Body:**

  ```json
  {
    "first_name": "string",
    "last_name": "string",
    "email": "string",
    "password": "string",
    "role": "guest" | "host"
  }
  ```

- **Response (201 Created):**

```json
{
  "user_id": "uuid",
  "first_name": "string",
  "last_name": "string",
  "email": "string",
  "role": "string",
  "created_at": "timestamp"
}
```

- Response Errors: 400 Bad Request (validation fails), 409 Conflict (email already exists)

- **Validation Rules:**

- `email` must be valid format and unique.
- `password` minimum length 8, must include at least one number & one special character.
- `role` must be either “guest” or “host”.

- **Performance Criteria:**

- Registration latency ≤ 200ms under 1000 concurrent requests.
- Unique-email check index used for O(log N) lookup performance.

#### 1.2.2 Login User

- Endpoint: `POST /api/v1/users/login`
- Request Body:

```json
{
  "email": "string",
  "password": "string"
}
```

- **Response (200 OK):**

```json
{
  "access_token": "jwt-token",
  "token_type": "Bearer",
  "expires_in": 3600
}
```

- Response Errors: 400 Bad Request, 401 Unauthorized (wrong credentials)

- **Validation Rules:**

- `email` must exist.

- `password` must match hashed password.

- **Performance Criteria:**

- Token issuance latency ≤ 150ms.

- JWT generation must use secure signing algorithm (e.g., HS256 or RS256).

#### 1.2.3 Get Current User Profile

- Endpoint: `GET /api/v1/users/me`

- Headers: `Authorization: Bearer <token>`

- **Response (200 OK):**

```json
{
  "user_id": "uuid",
  "first_name": "string",
  "last_name": "string",
  "email": "string",
  "role": "string",
  "created_at": "timestamp",
  "last_login": "timestamp"
}
```

- **Response Errors:** 401 Unauthorized (invalid token)

- **Validation Rules:**

- Token must be valid and not expired.

- User must exist and be active.

- **Performance Criteria:**

- Profile retrieval latency ≤ 100ms.

### 1.3 Authorization Rules

- Hosts and admins can perform listing creation/editing; guests cannot.

- Admin role can manage all users and data (see Admin Dashboard feature).

## 2. Property Management

### 2.1 Feature Overview

Hosts can create, update, delete property listings. Guests can search and view listings. The backend must support full CRUD operations, search/filter, and listing availability.

## 2.2 API Endpoints

### 2.2.1 Create Property

- **Endpoint:** `POST /api/v1/properties`

- **Headers:** `Authorization: Bearer <token>` (host role required)

- **Request Body:**

```json
{
  "name": "string",
  "description": "string",
  "location": "string",
  "price_per_night": "decimal",
  "property_type": "apartment" | "house" | "villa" | "room" | "cabin",
  "bedrooms": integer,
  "bathrooms": integer,
  "amenities": ["string", ...],
  "latitude": decimal,
  "longitude": decimal
}
```

- **Response (201 Created):**

```json
{
  "property_id": "uuid",
  "host_id": "uuid",
  "name": "string",
  ...
  "created_at": "timestamp"
}
```

- **Validation Rules:**

- `price_per_night` > 0.

- `bedrooms`, `bathrooms` ≥ 0.

- `amenities` array contains valid amenity identifiers.

- **Performance Criteria:**

- Listing creation latency ≤ 250ms.

- `Host_id` foreign key check must use indexed `user_id`.

### 2.2.2 Get Property (Single)

- **Endpoint:** `GET /api/v1/properties/{property_id}`

- **Response (200 OK):**

```json
{
  "property_id": "uuid",
  "host_id": "uuid",
  "name": "string",
  "description": "string",
  "location": "string",
  "price_per_night": "decimal",
  ...
}
```

- **Response Errors:** 404 Not Found

- **Performance Criteria:**

- Retrieval latency ≤ 150ms.

- Indexed property_id used.

### 2.2.3 Search/Filter Properties

- **Endpoint:** `GET /api/v1/properties?location=string&min_price=decimal&max_price=decimal&page=integer&page_size=integer&amenities=string, …`

- **Response (200 OK):**

```json
{
  "page": integer,
  "page_size": integer,
  "total": integer,
  "properties": [
    { property object },
    …
  ]
}
```

- **Validation Rules:**

- `page` and `page_size` must be > 0 and, say, `page_size` ≤ 100.

- `min_price` ≤ `max_price`.

- **Performance Criteria:**

- Query latency ≤ 500ms under moderate load (e.g., 1000 concurrent users).

- Use indexes on location, price_per_night, property_type, amenities (JSONB field or join table) to enable filtering.

### 2.2.4 Update Property

- **Endpoint:** `PUT /api/v1/properties/{property_id}`

- **Headers:** `Authorization: Bearer <token>` (host role, and owns the listing)

- **Request Body:** (only fields to update)

- **Response (200 OK):** Updated object

- **Validation & Performance:** Similar to Create.

### 2.2.5 Delete Property

- **Endpoint:** `DELETE /api/v1/properties/{property_id}`

- **Header:** `Authorization: Bearer <token>` (host or admin)

- **Response (204 No Content)**

- **Performance Criteria:** Deletion latency ≤ 200ms.

## 3. Booking System

## 3.1 Feature Overview

Guests book properties for specific dates, hosts see bookings, and the system processes payments and updates booking status.

## 3.2 API Endpoints

### 3.2.1 Create Booking

- **Endpoint:** `POST /api/v1/bookings`

- **Request Body:**

```json
{
  "property_id": "uuid",
  "user_id": "uuid",
  "start_date": "YYYY-MM-DD",
  "end_date": "YYYY-MM-DD"
}
```

- **Response (201 Created):**

```json
{
  "booking_id": "uuid",
  "property_id": "uuid",
  "user_id": "uuid",
  "start_date": "date",
  "end_date": "date",
  "total_price": "decimal",
  "status": "pending",
  "created_at": "timestamp"
}
```

- **Validation Rules:**

- `start_date` ≥ `current date`.

- `end_date` > `start_date`.

- Property must be available (no overlapping bookings with status confirmed or pending).

- **Performance Criteria:**

- Booking creation latency ≤ 300ms including availability check.

- Ensure availability query uses indexed bookings table on property_id and date range.

### 3.2.2 Confirm Booking & Payment

- **Endpoint:** `POST /api/v1/bookings/{booking_id}/payment`

- **Request Body:**

```json
{
  "payment_method": "credit_card" | "paypal" | "stripe",
  "amount": "decimal"
}
```

- **Response (200 OK):**

```json
{
  "payment_id": "uuid",
  "booking_id": "uuid",
  "amount": "decimal",
  "payment_date": "timestamp",
  "payment_method": "string",
  "payment_status": "success",
  "booking_status": "confirmed"
}
```

- **Validation Rules:**

- Payment amount must equal calculated total_price.

- Payment method must be valid.

- **Performance Criteria:**

- Payment completion latency ≤ 400ms.

- Transaction logged atomically; booking status updated atomically.

### 3.2.3 Cancel Booking

- **Endpoint:** `PATCH /api/v1/bookings/{booking_id}/cancel`

- **Response (200 OK):**

```json
{
  "booking_id": "uuid",
  "status": "canceled",
  "canceled_at": "timestamp"
}
```

- **Validation Rules:**

- Only user who booked or host can cancel (depending on policy).

- Status must change only if still pending or confirmed.

- **Performance Criteria:**

- Cancellation latency ≤ 200ms.

- Booking status index updated promptly.

## 3.3 Data Validation & Integrity

- Ensure referential integrity: `property_id` exists, `user_id` exists.

- Ensure atomic transaction: booking+payment or booking status update are consistent.

- Use transactions (ACID) for booking process.

## 4. General API Design & Non-Functional Requirements

## 4.1 API Design Best Practices

- Use plural nouns for endpoints, e.g., `/users`, `/properties`.
- Accept and produce JSON.
- Use correct HTTP methods (GET, POST, PUT, PATCH, DELETE) according to resource operations.
- Version your API endpoints (`/api/v1/`).
- Use standard HTTP status codes.

## 4.2 Performance & Scalability

- Use indexes on high-traffic columns (e.g., user email, property location, booking property_id).

- Use pagination for list retrieval endpoints.

- Design system statelessly, so horizontal scaling is feasible.

## 4.3 Security & Auth

- Use HTTPS for all endpoints.

- Use JWT tokens for authentication; include expiry.

- Use role-based access control (RBAC).

- Hash passwords using bcrypt or similar with salt.

- Validate input to avoid injection attacks.

- Rate limit sensitive endpoints.

## 4.4 Logging & Error Handling

- Return consistent error response structure; include code, message, details.

- Example: {"error": {"code": "USER_EMAIL_EXISTS", "message": "Email already registered"}}

- Log all failed attempts and critical operations.

## 4.5 Testing & Reliability

- Unit tests for each controller/service.

- Integration tests for API endpoints using mock data.

- Performance load testing to meet criteria above.

## 5. Glossary

- **Guest:** A user role who searches listings, books properties.

- **Host:** A user role who creates listings and hosts guests.

- **Admin:** A user role who can manage users, listings, bookings and payments globally.

- **JWT:** JSON Web Token used for authentication.

- **RBAC:** Role-Based Access Control, restricts functionality based on user role.

## 6. Revision History

|Version|Date|Signed Off By|Description of Changes|
|-------|----|-------------|----------------------|
|1.0|26/10/25|Motolani Alebiosu|Initial requirements specification|
