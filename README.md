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

---

## Authentication

### Login and Token Management

| Method | Endpoint | Description | Access | Request Format |
|--------|----------|-------------|--------|----------------|
| POST | `/api/auth/login` | Login with email and password | Public | ```{ "email": "user@example.com", "password": "password123" }``` |
| POST | `/api/auth/refresh` | Refresh access token | JWT | ```{ "refreshToken": "token" }``` (for mobile) or Cookie (for web) |
| POST | `/api/auth/otp-login` | Login with OTP | Public | ```{ "phone": "1234567890", "otp": "123456" }``` |
| POST | `/api/auth/set-password` | Set password using token | Public | ```{ "token": "token", "password": "newPassword", "id": "uuid" }``` |
| POST | `/api/auth/request-reset-password` | Request password reset | Public | ```{ "email": "user@example.com" }``` |
| GET | `/api/auth/check-token` | Validate token | Public | Query params: `token`, `leadId` |
| POST | `/api/auth/resend-invite/{leadId}` | Resend invitation | Admin JWT | Path variable: `leadId` |

---

## Partner Lead Management

### Lead Operations

| Method | Endpoint | Description | Access | Request Format |
|--------|----------|-------------|--------|----------------|
| GET | `/api/leads/{id}` | Get lead by ID | Public | Path variable: `id` |
| GET | `/api/leads/summary` | Get summary of all leads | Public | None |
| GET | `/api/leads/get-id/{email}` | Get lead ID by email | Public | Path variable: `email` |
| GET | `/api/leads/metrics` | Get dashboard metrics | Public | None |
| POST | `/api/leads` | Create new lead | Public | ```{ "source": "website", "preferredStartDate": "2025-12-31", "salonName": "Beauty Salon", "branchNumber": "B001", "city": "Mumbai", "avgMonthlyFootfall": 500, "clientType": "Premium", "contactName": "John Doe", "contactEmail": "john@example.com", "contactPhone": "1234567890", "contactDesignation": "Manager", "businessType": "Salon", "gstin": "22AAAAA0000A1Z5" }``` |
| PUT | `/api/leads/{id}` | Update lead | Admin JWT | Path variable: `id`, Body: Lead update data |
| PATCH | `/api/leads/{id}/status` | Update lead status | Admin JWT | Path variable: `id`, Query param: `status` (NEW, IN_PROGRESS, CONTACTED, REJECTED, APPROVED) |
| DELETE | `/api/leads/{id}` | Delete lead | Admin JWT | Path variable: `id` |

---

## Salon User Management

### Staff Operations

| Method | Endpoint | Description | Access | Request Format |
|--------|----------|-------------|--------|----------------|
| GET | `/api/salon-user/id-email` | Get all salon IDs and emails | JWT | None |
| GET | `/api/salon-user/get-id` | Get user ID by email | JWT | Query param: `email` |
| GET | `/api/salon-user/get-all` | Get all salon users | JWT | None |
| GET | `/api/salon-user/salon-staff` | Get all staff for a salon | JWT | Query params: `salonId`, `requestedBy` |
| GET | `/api/salon-user/staff/{staffId}` | Get staff by ID | JWT | Path variable: `staffId`, Query param: `requestedBy` |
| PUT | `/api/salon-user/staff/update-status` | Update staff status | JWT | ```{ "staffId": "uuid", "active": true, "updatedBy": "uuid" }``` |
| PUT | `/api/salon-user/staff/update-details` | Update staff details | JWT | Query params: `staffId`, `updatedBy`, Body: Staff details |
| PUT | `/api/salon-user/cancel-delete/{staffId}` | Cancel staff deletion | JWT | Path variable: `staffId`, Query param: `deletedBy` |
| DELETE | `/api/salon-user/{staffId}` | Mark staff for deletion | JWT | Path variable: `staffId`, Query param: `deletedBy` |
| DELETE | `/api/salon-user/delete/{staffId}` | Permanently delete staff | JWT | Path variable: `staffId`, Query param: `deletedBy` |
| POST | `/api/salon-user/register` | Register staff manually | JWT | ```{ "name": "Staff Name", "email": "staff@example.com", "phone": "1234567890", "role": "STYLIST", "salonId": "uuid", "registeredBy": "uuid" }``` |
| POST | `/api/salon-user/invite` | Invite staff | JWT | ```{ "email": "staff@example.com", "role": "STYLIST", "salonId": "uuid" }```, Query param: `invitedBy` |
| POST | `/api/salon-user/complete-staff-registration` | Complete staff registration | Public | ```{ "token": "token", "staffId": "uuid", "password": "password", "name": "Staff Name", "phone": "1234567890" }``` |

---

## Client Management

### Client Operations

| Method | Endpoint | Description | Access | Request Format |
|--------|----------|-------------|--------|----------------|
| POST | `/api/client` | Create client | JWT | ```{ "firstName": "John", "lastName": "Doe", "email": "client@example.com", "phone": "1234567890", "dateOfBirth": "1990-01-01", "gender": "MALE", "notes": "Client notes" }``` |
| PATCH | `/api/client/{clientId}` | Update client | JWT | Path variable: `clientId`, Body: Client update data |
| DELETE | `/api/client/{clientId}` | Delete client (soft) | JWT | Path variable: `clientId` |
| PATCH | `/api/client/{clientId}/cancel-delete` | Cancel client deletion | JWT | Path variable: `clientId` |
| GET | `/api/client/{clientId}` | Get client by ID | JWT | Path variable: `clientId` |
| GET | `/api/client/all` | Get all clients of salon | JWT | None |
| GET | `/api/client/assigned` | Get clients assigned to staff | JWT | None |
| GET | `/api/client/assigned/{staffId}` | Get clients assigned to specific staff | JWT | Path variable: `staffId` |
| GET | `/api/client` | Get paginated clients | JWT | Query params: `page` (default: 0), `size` (default: 10) |
| PATCH | `/api/client/{clientId}/assign/{newStaffId}` | Reassign client to new staff | JWT | Path variables: `clientId`, `newStaffId` |

---

## Scan Management

### Scan Operations

| Method | Endpoint | Description | Access | Request Format |
|--------|----------|-------------|--------|----------------|
| POST | `/api/scans/create-session` | Create scan session | JWT | Query params: `clientId`, `salonUserId`, Body: ```{ "imageUrl": "https://example.com/image.jpg", "sessionPurpose": "Initial Assessment", "notes": "Scan notes" }``` |
| POST | `/api/scans/complete` | Complete scan (ML callback) | System | ```{ "scanSessionId": "uuid", "status": "success/failed", "error": "Error message if failed", ... }``` |
| GET | `/api/scans/{scanSessionId}` | Get scan details | JWT | Path variable: `scanSessionId` |
| GET | `/api/scans/{clientId}/all` | Get all scans for client | JWT | Path variable: `clientId` |
| GET | `/api/scans/{clientId}/dashboard-scans` | Get paginated scans for client | JWT | Path variable: `clientId`, Pageable params |
| GET | `/api/scans/{scanSessionId}/status` | Check scan status | JWT | Path variable: `scanSessionId` |
| DELETE | `/api/scans/scans/{scanSessionId}` | Delete scan | JWT | Path variable: `scanSessionId` |

---

## Anonymous Scan Flow

### Anonymous Scan Operations

| Method | Endpoint | Description | Access | Request Format |
|--------|----------|-------------|--------|----------------|
| POST | `/api/anonscans/{anonId}` | Create anonymous scan | Public | Path variable: `anonId`, Body: ```{ "imageUrl": "https://example.com/image.jpg", "sessionPurpose": "Initial Assessment" }``` |
| GET | `/api/anonscans/{anonId}/usage` | Check usage limits | Public | Path variable: `anonId` |
| GET | `/api/anonscans/result/{sessionId}` | Get scan result | Public | Path variable: `sessionId` |
| POST | `/api/anonscans/{anonId}/link` | Link anonymous scans to client | Public | Path variable: `anonId`, Query param: `clientId` |
| POST | `/api/anonscans/analysis` | Receive ML analysis result | System | Analysis result data |

---

## OTP Verification

### OTP Operations

| Method | Endpoint | Description | Access | Request Format |
|--------|----------|-------------|--------|----------------|
| POST | `/api/otp/send` | Send OTP | Public | ```{ "phone": "1234567890" }``` |
| POST | `/api/otp/verify` | Verify OTP | Public | ```{ "phone": "1234567890", "otp": "123456" }``` |

---

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
   - Details: `{ "id": "uuid", "imageUrl": "https://example.com/image.jpg", "scannedAt": "2023-01-01T12:00:00", "sessionPurpose": "Initial Assessment", "status": "COMPLETED", "analysis": { ... }, "regionMetrics": [...], "visualOutputs": [...] }`

5. **Error Responses**:
   - `{ "status": 400, "message": "Error message", "timestamp": "2023-01-01T12:00:00" }`

## Authentication Headers

- **JWT Authentication**: Include the JWT token in the Authorization header:
  ```
  Authorization: Bearer <access_token>
  ```

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


