# EagleVerse AI – Partner With Us (Backend)

This repository contains the backend service for **EagleVerse AI's Partner With Us** module, built with Spring Boot. It enables salon managers and owners to submit partnership requests and helps the internal team manage and track these leads.

---

## 🛠 Tech Stack

- Java 17  
- Spring Boot  
- Spring Security  
- MySQL  
- Redis (for token storage)  
- JavaMailSender (for email communication)  
- Postman (for API testing)

---

## 📌 Key Features

- Submit detailed partnership requests  
- Store and retrieve leads via REST APIs  
- Send password setup links via email  
- Protect sensitive APIs using Basic Auth  
- Track request source (website, app, campaigns, etc.)

---

# EagleVerse  API Documentation

This document provides a comprehensive guide to the PartnerWithUs API endpoints for frontend developers. The API is organized into several modules that handle different aspects of the application.

## Table of Contents

1. [Authentication](#authentication)
2. [Partner Lead Management](#partner-lead-management)
3. [Salon User Management](#salon-user-management)
4. [Client Management](#client-management)
5. [Scan Management](#scan-management)
6. [Anonymous Scan Flow](#anonymous-scan-flow)
7. [OTP Verification](#otp-verification)
8. [Status Codes](#status-codes)
9. [Error Handling](#error-handling)

---

## Authentication

### Login and Token Management

| Method | Endpoint | Description | Access | Request Format | Status Codes |
|--------|----------|-------------|--------|----------------|-------------|
| POST | `/api/auth/login` | Login with email and password | Public | ```{ "email": "user@example.com", "password": "password123" }``` | 200 OK, 401 Unauthorized |
| POST | `/api/auth/refresh` | Refresh access token | JWT | ```{ "refreshToken": "token" }``` (for mobile) or Cookie (for web) | 200 OK, 400 Bad Request, 401 Unauthorized |
| POST | `/api/auth/otp-login` | Login with OTP | Public | ```{ "phone": "1234567890", "otp": "123456" }``` | 200 OK, 401 Unauthorized |
| POST | `/api/auth/set-password` | Set password using token | Public | ```{ "token": "token", "password": "newPassword", "id": "uuid" }``` | 200 OK, 400 Bad Request |
| POST | `/api/auth/request-reset-password` | Request password reset | Public | ```{ "email": "user@example.com" }``` | 200 OK |
| GET | `/api/auth/check-token` | Validate token | Public | Query params: `token`, `leadId` | 200 OK, 401 Unauthorized |
| POST | `/api/auth/resend-invite/{leadId}` | Resend invitation | Admin JWT | Path variable: `leadId` | 200 OK, 403 Forbidden, 404 Not Found |

---

## Partner Lead Management

### Lead Operations

| Method | Endpoint | Description | Access | Request Format | Status Codes |
|--------|----------|-------------|--------|----------------|-------------|
| GET | `/api/leads/{id}` | Get lead by ID | Basic Auth | Path variable: `id` | 200 OK, 404 Not Found |
| GET | `/api/leads/summary` | Get summary of all leads | Basic Auth | None | 200 OK |
| GET | `/api/leads/get-id` | Get lead ID by email | Basic Auth | Query param: `email` | 200 OK, 404 Not Found |
| GET | `/api/leads/metrics` | Get dashboard metrics | Basic Auth | None | 200 OK |
| POST | `/api/leads` | Create new lead | Public | ```{ "source": "website", "preferredStartDate": "2025-12-31", "salonName": "Beauty Salon", "branchNumber": "B001", "city": "Mumbai", "avgMonthlyFootfall": 500, "clientType": "Premium", "contactName": "John Doe", "contactEmail": "john@example.com", "contactPhone": "1234567890", "contactDesignation": "Manager", "businessType": "Salon", "gstin": "22AAAAA0000A1Z5" }``` | 200 OK, 400 Bad Request |
| PUT | `/api/leads/{id}` | Update lead | Basic Auth | Path variable: `id`, Body: Lead update data | 200 OK, 400 Bad Request, 404 Not Found |
| PATCH | `/api/leads/{id}/status` | Update lead status | Basic Auth | Path variable: `id`, Query param: `status` (NEW, IN_PROGRESS, CONTACTED, REJECTED, APPROVED) | 200 OK, 400 Bad Request, 404 Not Found |
| DELETE | `/api/leads/{id}` | Delete lead | Basic Auth | Path variable: `id` | 204 No Content, 404 Not Found |

---

## Salon User Management

### Staff Operations

| Method | Endpoint | Description | Access | Request Format | Status Codes |
|--------|----------|-------------|--------|----------------|-------------|
| GET | `/api/salon-user/id-email` | Get all salon IDs and emails | JWT | None | 200 OK |
| GET | `/api/salon-user/get-id` | Get user ID by email | JWT | Query param: `email` | 200 OK, 404 Not Found |
| GET | `/api/salon-user/get-all` | Get all salon users | JWT | None | 200 OK |
| GET | `/api/salon-user/salon-staff` | Get all staff for a salon | JWT | Query params: `salonId` | 200 OK, 403 Forbidden |
| GET | `/api/salon-user/staff/{staffId}` | Get staff by ID | JWT | Path variable: `staffId` | 200 OK, 403 Forbidden, 404 Not Found |
| PUT | `/api/salon-user/staff/update-status` | Update staff status | JWT | ```{ "staffId": "uuid", "active": true }``` | 200 OK, 400 Bad Request, 403 Forbidden |
| PUT | `/api/salon-user/staff/update-details` | Update staff details | JWT | Query param: `staffId`, Body: Staff details | 200 OK, 400 Bad Request, 403 Forbidden |
| DELETE | `/api/salon-user/{staffId}` | Mark staff for deletion | JWT | Path variable: `staffId` | 200 OK, 403 Forbidden, 404 Not Found |
| DELETE | `/api/salon-user/delete/{staffId}` | Permanently delete staff | JWT | Path variable: `staffId` | 204 No Content, 403 Forbidden, 404 Not Found |
| POST | `/api/salon-user/register` | Register staff manually | JWT | ```{ "name": "Staff Name", "email": "staff@example.com", "phone": "1234567890", "role": "STYLIST", "salonId": "uuid" }``` | 200 OK, 400 Bad Request, 403 Forbidden |
| POST | `/api/salon-user/invite` | Invite staff | JWT | ```{ "email": "staff@example.com", "role": "STYLIST", "salonId": "uuid" }``` | 200 OK, 400 Bad Request, 403 Forbidden |
| POST | `/api/salon-user/complete-registration` | Complete staff registration | Public | ```{ "token": "token", "staffId": "uuid", "password": "password", "name": "Staff Name", "phone": "1234567890" }``` | 200 OK, 400 Bad Request |

---

## Client Management

### Client Operations

| Method | Endpoint | Description | Access | Request Format | Status Codes |
|--------|----------|-------------|--------|----------------|-------------|
| POST | `/api/client` | Create client | JWT + MANAGE_CLIENTS | ```{ "firstName": "John", "lastName": "Doe", "email": "client@example.com", "phone": "1234567890", "dateOfBirth": "1990-01-01", "gender": "MALE", "notes": "Client notes" }``` | 200 OK, 400 Bad Request, 403 Forbidden |
| PATCH | `/api/client/{clientId}` | Update client | JWT + MANAGE_CLIENTS | Path variable: `clientId`, Body: Client update data | 200 OK, 400 Bad Request, 403 Forbidden, 404 Not Found |
| DELETE | `/api/client/{clientId}` | Delete client (soft) | JWT + REMOVE_CLIENT | Path variable: `clientId` | 204 No Content, 403 Forbidden, 404 Not Found |
| GET | `/api/client/{clientId}` | Get client by ID | JWT + VIEW_CLIENT_HISTORY | Path variable: `clientId` | 200 OK, 403 Forbidden, 404 Not Found |
| GET | `/api/client/all` | Get all clients of salon | JWT + MANAGE_CLIENTS | None | 200 OK, 403 Forbidden |
| GET | `/api/client/assigned` | Get clients assigned to staff | JWT | None | 200 OK |
| GET | `/api/client/assigned/{staffId}` | Get clients assigned to specific staff | JWT + MANAGE_CLIENTS | Path variable: `staffId` | 200 OK, 403 Forbidden, 404 Not Found |
| GET | `/api/client` | Get paginated clients | JWT + MANAGE_CLIENTS | Query params: `page` (default: 0), `size` (default: 10) | 200 OK, 403 Forbidden |
| PATCH | `/api/client/{clientId}/assign/{newStaffId}` | Reassign client to new staff | JWT + MANAGE_CLIENTS | Path variables: `clientId`, `newStaffId` | 200 OK, 403 Forbidden, 404 Not Found |

---

## Scan Management

### Scan Operations

| Method | Endpoint | Description | Access | Request Format | Status Codes |
|--------|----------|-------------|--------|----------------|-------------|
| POST | `/api/scans/create-session` | Create scan session | JWT + CREATE_SCAN | Query param: `clientId`, Body: ```{ "imageUrl": "https://example.com/image.jpg", "sessionPurpose": "Initial Assessment", "notes": "Scan notes" }``` | 201 Created, 400 Bad Request, 403 Forbidden |
| POST | `/api/scans/complete` | Complete scan (ML callback) | System | ```{ "scanSessionId": "uuid", "status": "success/failed", "error": "Error message if failed", ... }``` | 200 OK, 400 Bad Request |
| GET | `/api/scans/{scanSessionId}` | Get scan details | JWT + VIEW_SCAN | Path variable: `scanSessionId` | 200 OK, 202 Accepted, 403 Forbidden, 404 Not Found |
| GET | `/api/scans/{clientId}/all` | Get all scans for client | JWT + VIEW_SCAN | Path variable: `clientId` | 200 OK, 403 Forbidden, 404 Not Found |
| GET | `/api/scans/{clientId}/dashboard-scans` | Get paginated scans for client | JWT + VIEW_SCAN | Path variable: `clientId`, Pageable params | 200 OK, 403 Forbidden, 404 Not Found |
| GET | `/api/scans/{scanSessionId}/status` | Check scan status | JWT + VIEW_SCAN | Path variable: `scanSessionId` | 200 OK, 403 Forbidden, 404 Not Found |
| DELETE | `/api/scans/{scanSessionId}` | Delete scan | JWT + DELETE_SCAN | Path variable: `scanSessionId` | 200 OK, 403 Forbidden, 404 Not Found |

---

## Anonymous Scan Flow

### Anonymous Scan Operations

| Method | Endpoint | Description | Access | Request Format | Status Codes |
|--------|----------|-------------|--------|----------------|-------------|
| POST | `/api/anonscans/{anonId}` | Create anonymous scan | Public | Path variable: `anonId`, Body: ```{ "imageUrl": "https://example.com/image.jpg", "sessionPurpose": "Initial Assessment" }``` | 201 Created, 400 Bad Request, 429 Too Many Requests |
| GET | `/api/anonscans/{anonId}/usage` | Check usage limits | Public | Path variable: `anonId` | 200 OK |
| GET | `/api/anonscans/result/{sessionId}` | Get scan result | Public | Path variable: `sessionId` | 200 OK, 202 Accepted, 404 Not Found |
| GET | `/api/anonscans/results/{anonId}` | Get all scan results for user | Public | Path variable: `anonId` | 200 OK |
| POST | `/api/anonscans/{anonId}/link` | Link anonymous scans to client | Public | Path variable: `anonId`, Query param: `clientId` | 204 No Content, 400 Bad Request, 404 Not Found |
| POST | `/api/anonscans/analysis` | Receive ML analysis result | System | Analysis result data | 204 No Content, 400 Bad Request |

---

## OTP Verification

### OTP Operations

| Method | Endpoint | Description | Access | Request Format | Status Codes |
|--------|----------|-------------|--------|----------------|-------------|
| POST | `/api/otp/send` | Send OTP | Public | ```{ "phone": "1234567890" }``` | 200 OK, 400 Bad Request |
| POST | `/api/otp/verify` | Verify OTP | Public | ```{ "phone": "1234567890", "otp": "123456" }``` | 200 OK, 400 Bad Request |

---

## Status Codes

The API uses standard HTTP status codes to indicate the success or failure of requests:

| Status Code | Description |
|-------------|-------------|
| 200 OK | The request was successful and the response contains the requested data |
| 201 Created | The request was successful and a new resource was created |
| 202 Accepted | The request has been accepted for processing but is not yet complete (used for scan polling) |
| 204 No Content | The request was successful but there is no content to return (often used for DELETE operations) |
| 400 Bad Request | The request was malformed or contained invalid parameters |
| 401 Unauthorized | Authentication is required or the provided credentials are invalid |
| 403 Forbidden | The authenticated user does not have permission to access the requested resource |
| 404 Not Found | The requested resource could not be found |
| 409 Conflict | The request could not be completed due to a conflict with the current state of the resource |
| 429 Too Many Requests | The user has sent too many requests in a given amount of time (rate limiting) |
| 500 Internal Server Error | An unexpected error occurred on the server |

## Error Handling

### Common Error Response Structures

The API returns consistent error responses across all endpoints:

1. **Validation Errors** (400 Bad Request):
   ```json
   {
     "fieldName1": "Error message for field 1",
     "fieldName2": "Error message for field 2"
   }
   ```

2. **Authentication Errors** (401 Unauthorized):
   ```json
   {
     "error": "Unauthorized - Login required"
   }
   ```

3. **Permission Errors** (403 Forbidden):
   ```json
   {
     "error": "You do not have permission to perform this action."
   }
   ```

4. **Resource Not Found** (404 Not Found):
   ```json
   {
     "error": "Resource not found"
   }
   ```

5. **Conflict Errors** (409 Conflict):
   ```json
   {
     "error": "Resource already exists"
   }
   ```

6. **Rate Limiting** (429 Too Many Requests):
   ```json
   {
     "error": "Scan limit exceeded"
   }
   ```

7. **Server Errors** (500 Internal Server Error):
   ```json
   {
     "error": "An unexpected server error occurred. Please try again later."
   }
   ```

8. **Scan Not Ready** (202 Accepted):
   ```json
   {
     "status": "PENDING",
     "message": "Scan analysis not available yet for session: [sessionId]"
   }
   ```

## Response Formats

### Common Response Structures

1. **Authentication Responses**:
   - Web: `{ "accessToken": "jwt_token" }`
   - Mobile: `{ "accessToken": "jwt_token", "refreshToken": "refresh_token", "id": "user_id" }`

2. **Partner Lead Responses**:
   - `{ "id": "uuid", "status": "NEW", "createdAt": "2023-01-01T12:00:00", "contactEmail": "email@example.com", "contactDesignation": "Manager" }`

3. **Client Responses**:
   - Summary: `{ "id": "uuid", "firstName": "John", "lastName": "Doe", "phone": "1234567890", ... }`
   - Details: `{ "id": "uuid", "firstName": "John", "lastName": "Doe", "phone": "1234567890", "email": "client@example.com", "dateOfBirth": "1990-01-01", "gender": "MALE", "notes": "Client notes", "scanSessions": [...] }`

4. **Scan Responses**:
   - Session: `{ "id": "uuid", "imageUrl": "https://example.com/image.jpg", "scannedAt": "2023-01-01T12:00:00", "sessionPurpose": "Initial Assessment", "status": "PENDING" }`
   - Details: `{ "id": "uuid", "imageUrl": "https://example.com/image.jpg", "scannedAt": "2023-01-01T12:00:00", "sessionPurpose": "Initial Assessment", "overallGlowIndex": 85.5, "regionMetrics": [...], "visualOutputs": [...] }`

## Authentication Headers

- **JWT Authentication**: Include the JWT token in the Authorization header:
  ```
  Authorization: Bearer <access_token>
  ```

- **Basic Authentication** (for Partner Lead endpoints):
  ```
  Authorization: Basic <base64(username:password)>
  ```
  Default credentials: username=admin, password=eagle123

- **Client Type Header**: For mobile clients, include the client type header:
  ```
  X-Client-Type: mobile
  ```

## Pagination

For endpoints that support pagination, the following query parameters are available:
- `page`: Page number (zero-based, default: 0)
- `size`: Page size (default: 10)

Paginated responses include:
```json
{
  "content": [...],
  "pageable": {
    "pageNumber": 0,
    "pageSize": 10,
    "sort": {...}
  },
  "totalElements": 100,
  "totalPages": 10,
  "last": false,
  "first": true,
  "empty": false
}
```
---


### 🔐 Auth Info

- **Admin Only** endpoints require `ROLE_ADMIN` and are protected by Spring Security.
- For local testing, Basic Auth credentials are:

  - **Username**: `admin`  
  - **Password**: `eagle123`
### JWT TOKEN FLOW : 
![image](https://github.com/user-attachments/assets/fcbbe8fa-dcef-4d67-9d74-17fe8956cd7b)

---

## 📦 Request DTO Formats

### `PartnerLeadRequest`

```json
{
  "source": "website",
  "salonName": "Glamour Studio",
  "branchNumber": "001",
  "city": "Mumbai",
  "avgMonthlyFootfall": 300,
  "clientType": "walk-in",
  "contactName": "Rohit Sharma",
  "contactEmail": "rohit@example.com",
  "contactPhone": "9876543210",
  "contactDesignation": "Manager",
  "businessType": "Franchise",
  "gstin": "29ABCDE1234F2Z5"
}
```

### **PasswordSetupRequest**
```json
{
  "token": "abc123tokenvalue",
  "password": "newSecurePassword",
  "id": "uuid-of-lead"
}
```

### ManualStaffRegistrationRequest
```json
{
  "name": "Ananya Sharma",
  "email": "ananya@example.com",
  "password": "SecurePass123",
  "phone": "9876543210",
  "role": "STAFF",
  "profilePhoto": "https://example.com/images/ananya.jpg",
  "isActive": "true",
  "salonId": "c0a80123-4567-8901-2345-67890abcdef1",
  "registeredBy": "c0a80123-4567-8901-2345-67890abcdef2"
}

```
### StaffInviteRequest
```json
{
  "name": "Ravi Kumar",
  "email": "ravi.kumar@example.com",
  "phone": "9123456789",
  "role": "MANAGER",
  "salonId": "c0a80123-4567-8901-2345-67890abcdef3",
  "expiresAt": "2025-06-20T18:30:00"
}
```
### CompleteStaffRegistrationRequest
```json
{
  "staffId": "c0a80123-4567-8901-2345-67890abcdef4",
  "token": "a1b2c3d4e5f6g7h8",
  "phone": "9988776655",
  "password": "NewPass@456",
  "role": "STYLIST",
  "profilePhoto": "https://example.com/images/stylist.png"
}

```
### StaffDetailUpdateRequest
```json
{
  "name": "Priya Mehta",
  "email": "priya.mehta@example.com",
  "phone": "9012345678",
  "password": "UpdatedPass789",
  "role": "RECEPTIONIST",
  "profilePhoto": "https://example.com/images/priya.jpg"
}
```
### StaffStatusUpdateRequest
```json
{
  "staffId": "c0a80123-4567-8901-2345-67890abcdef5",
  "isActive": true,
  "updatedBy": "c0a80123-4567-8901-2345-67890abcdef6"
}
```
### AuthRequest
```json
{
  "email": "demo@demo.com",
  "password": ""
}

```
####  POST  /api/client ClientRequest
```json 
{
  "firstName": "John",
  "lastName": "Doe",
  "email": "john.doe@example.com",
  "phone": "9876543210",
  "dateOfBirth": "1995-08-15",
  "gender": "MALE",
  "notes": "Client prefers morning appointments.",
  "photoUrl": "https://example.com/photos/john.jpg",
  "assignedStaffId": "d3fcaec2-4f5a-43f2-8c97-1e3e9c672ed2"
}

```

---

## Output Format 
### Lead Management (```/api/leads```)
#### GET
```json
{
  "id": "c0a80123-4567-8901-2345-67890abcdef1",
  "preferredStartDate": "2025-07-01",
  "status": "PENDING",
  "createdAt": "2025-06-22T18:45:00",

  "salonName": "Glamour Studio",
  "branchNumber": "001",
  "city": "Mumbai",
  "avgMonthlyFootfall": 300,
  "clientType": "walk-in",

  "contactName": "Rohit Sharma",
  "contactEmail": "rohit.sharma@example.com",
  "contactPhone": "9876543210",
  "contactDesignation": "Manager",

  "businessType": "Franchise",
  "gstin": "29ABCDE1234F2Z5"
}
```
#### GET /leads/{id}
``` 
same as GET /api/leads
```
#### GET /leads/summary
```json
{
  "id": "c0a80123-4567-8901-2345-67890abcdef2",
  "status": "PENDING"
}
```
####  GET /leads/get-id/{email}
```json 
{
 "id":"c0a80123-4567-8901-2345-67890abcdef2"
}
```
####  GET /leads/get-id/{email}
```json 
{
 "id":"c0a80123-4567-8901-2345-67890abcdef2"
}
```
####  POST  /leads
```json 
{
  "id": "c0a80123-4567-8901-2345-67890abcdeff",
  "status": "APPROVED",
  "createdAt": "2025-06-22T15:30:00",
  "contactEmail": "neha.kapoor@example.com",
  "contactDesignation": "Franchise Owner"
}
```
####  PUT  /leads/{id}
```json 
{
  "id": "c0a80123-4567-8901-2345-67890abcdeff",
  "status": "APPROVED",
  "createdAt": "2025-06-22T15:30:00",
  "contactEmail": "neha.kapoor@example.com",
  "contactDesignation": "Franchise Owner"
}
```

####  PATCH   /leads/{id}/status?status=<status>
```json 
{
  "id": "c0a80123-4567-8901-2345-67890abcdeff",
  "status": "APPROVED"
}
```
### Authentication 
####  POST   /api/auth/login
```json 
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```
### Salon Staff Management (```/api/salon-user```)
####  GET /get-all
```json 
{
  "id": "c0a80123-4567-8901-2345-67890abcdef9",
  "name": "Ananya Sharma",
  "email": "ananya.sharma@example.com",
  "phone": "9876543210",
  "role": "STYLIST",
  "salonId": "c0a80123-4567-8901-2345-67890abcdea1",
  "salonName": "Glamour Studio - Andheri",
  "active": true,
  "deleted": false
}

```
####  GET /id-email
```json 
{
  "id": "c0a80123-4567-8901-2345-67890abcde77",
  "email": "manager@glamourstudio.com"
}
```
####  GET /get-id
```json 
{
  "id": "c0a80123-4567-8901-2345-67890abcde77",
  "email": "manager@glamourstudio.com"
}
```
####  GET /salon-staff
```json 
{
  "id": "c0a80123-4567-8901-2345-67890abcdef9",
  "name": "Ananya Sharma",
  "email": "ananya.sharma@example.com",
  "phone": "9876543210",
  "role": "STYLIST",
  "salonId": "c0a80123-4567-8901-2345-67890abcdea1",
  "salonName": "Glamour Studio - Andheri",
  "active": true
}
```
####  GET /staff/{staffId}
``` 
Same as GET /get-all
```

####  POST  /register
```json 
{
  "id": "c0a80123-4567-8901-2345-67890abcdef9",
  "name": "Ananya Sharma",
  "email": "ananya.sharma@example.com",
  "phone": "9876543210",
  "role": "STYLIST",
  "salonId": "c0a80123-4567-8901-2345-67890abcdea1",
  "salonName": "Glamour Studio - Andheri",
  "active": true
}
```


