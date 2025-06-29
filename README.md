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

## 🔗 API Endpoints

### 📂 Lead Management (`/api`)

| Method   | Endpoint                   | Description         | Access      | Request Format                                      | Output Format |
|----------|----------------------------|---------------------|-------------|-----------------------------------------------------|--------------|
| `GET`    | `/leads`                   | Fetch all leads     | `PUBLIC`    | *None* (dont use frequently, very long response)     | 200, 404     | 
| `GET`    | `/leads/{id}`              | Fetch specific lead | `PUBLIC`    | `id` in path                                        | 200, 404     | 
| `GET`    | `/leads/summary`           | Fetch summary view  | `PUBLIC`    | *None*                                              | 200          |
| `GET`    | `/leads/get-id/{email}`    | Get lead ID by email| `PUBLIC`    | `email` in path                                     | 200, 404     |
| `POST`   | `/leads`                   | Create new lead     | `ROLE_ADMIN`| Body: `PartnerLeadRequest`                          | 201, 400     |
| `PUT`    | `/leads/{id}`              | Update lead         | `ROLE_ADMIN`| `id` in path + Body: `PartnerLeadUpdateRequest`     | 200, 400     |
| `PATCH`  | `/leads/{id}/status`       | Update status       | `ROLE_ADMIN`| `id` in path + status as parameter                  | 200, 400     |
| `DELETE` | `/leads/{id}`              | Delete lead         | `ROLE_ADMIN`| `id` in path                                        | 204, 404     |

---

### 🔐 Authentication (`/api`)

| Method   | Endpoint                         | Description             | Access      | Request Format                   | Status Codes |
|----------|----------------------------------|-------------------------|-------------|----------------------------------|--------------|
| `POST`   | `/auth/set-password`             | Set password            | `PUBLIC`    | Body: `PasswordSetupRequest`     | 200, 400     |
| `POST`   | `/auth/resend-invite/{leadId}`   | Resend invite           | `ROLE_ADMIN`| `leadId` in path                 | 200, 404     |
| `GET`    | `/auth/check-token`              | Validate token          | `PUBLIC`    | Param: `token`, `leadId`         | 200, 410     |
| `GET`    | `/auth/request-reset-password`   | Request password reset  | `PUBLIC`    | Body: `ResetPasswordRequest`     | 200, 410     |
| `POST`   | `/auth/login`                    | -see image below-            | `PUBLIC`    |               | 200, 401     |
| `POST`   | `/auth/refresh`                  | -see image below-            | `PUBLIC`    | | 200, 401     |

---

### 👥 Salon Staff Management (`/api`)

#### Staff Listing & Details

| Method   | Endpoint                        | Description       | Access        | Request Format                           | Status Codes |
|----------|---------------------------------|-------------------|---------------|------------------------------------------|--------------|
| `GET`    | `/salon-user/get-all`           | Get all users     | `PUBLIC`      | *None* (dont use frequently, very long response)     | 200          |
| `GET`    | `/salon-user/salon-staff`       | Get salon staff   | `BEARER_JWT`  | Param: `?salonId=ID&requestId=ID`        | 200, 403     |
| `GET`    | `/salon-user/staff/{staffId}`   | Get staff details | `BEARER_JWT`  | `staffId` in path + Param: `?requestId=ID`| 200, 404     |
| `GET`    | `/salon-user/id-email`   | Get id and email of all users  | `PUBLIC`  | *None*| 200, 404     |
| `GET`    | `/salon-user/get-id`   | Get id  of  via email  | `PUBLIC`  | ```?email=<email>```| 200, 404     |

#### Staff Onboarding

| Method   | Endpoint                             | Description             | Access        | Request Format                                         | Status Codes |
|----------|--------------------------------------|-------------------------|---------------|--------------------------------------------------------|--------------|
| `POST`   | `/salon-user/register`               | Manual registration     | `BEARER_JWT`  | Body: `ManualStaffRegistrationRequest`                 | 201, 400     |
| `POST`   | `/salon-user/invite`                 | Send invite             | `BEARER_JWT`  | Body: `StaffInviteRequest` + Param: `?invitedBy=ID`    | 200, 400     |
| `POST`   | `/salon-user/complete-registration`  | Complete registration   | `PUBLIC`      | Body: `CompleteStaffRegistrationRequest`               | 200, 410     |

#### Staff Operations

| Method   | Endpoint                                  | Description      | Access        | Request Format                                                   | Status Codes |
|----------|-------------------------------------------|------------------|---------------|------------------------------------------------------------------|--------------|
| `PUT`    | `/salon-user/staff/update-status`         | Change status    | `BEARER_JWT`  | Body: `StaffStatusUpdateRequest`                                 | 200, 403     |
| `PUT`    | `/salon-user/staff/update-details`        | Update profile   | `BEARER_JWT`  | Body: `StaffDetailUpdateRequest` + Param: `staffId`, `updatedBy` | 200, 400     |
| `PUT`    | `/salon-user/cancel-delete/{staffId}`     | Cancel deletion  | `BEARER_JWT`  | `staffId` in path + Param: `?deletedBy=ID`                       | 200, 404     |
| `DELETE` | `/salon-user/{staffId}`                   | Delete staff     | `BEARER_JWT`  | `staffId` in path + Param: `?deletedBy=ID`                       | 202, 404     |

---

### 👥 Client Management (`/api/client`)

#### Client CRUD & Info

| Method   | Endpoint                               | Description                        | Access       | Request Format                               | Status Codes  |
| -------- | -------------------------------------- | ---------------------------------- | ------------ | -------------------------------------------- | ------------- |
| `POST`   | `/api/client`                          | Create a new client                | `BEARER_JWT` | JSON body: `ClientRequest`                   | 200, 400, 403 |
| `PATCH`  | `/api/client/{clientId}`               | Update an existing client          | `BEARER_JWT` | Path: `clientId`, JSON body: `ClientRequest` | 200, 400, 403 |
| `DELETE` | `/api/client/{clientId}`               | Soft-delete a client               | `BEARER_JWT` | Path: `clientId`                             | 204, 403, 404 |
| `PATCH`  | `/api/client/{clientId}/cancel-delete` | Cancel soft-delete of a client     | `BEARER_JWT` | Path: `clientId`                             | 200, 403, 404 |
| `GET`    | `/api/client/{clientId}`               | Get full details of a client by ID | `BEARER_JWT` | Path: `clientId`                             | 200, 403, 404 |

#### Client Listing & Assignment

| Method | Endpoint                         | Description                                  | Access       | Request Format            | Status Codes  |
| ------ | -------------------------------- | -------------------------------------------- | ------------ | ------------------------- | ------------- |
| `GET`  | `/api/client/all`                | Get all clients of current user's salon      | `BEARER_JWT` | *None*                    | 200, 403      |
| `GET`  | `/api/client/assigned`           | Get clients assigned to current staff        | `BEARER_JWT` | *None*                    | 200, 403      |
| `GET`  | `/api/client/assigned/{staffId}` | Get clients assigned to another staff member | `BEARER_JWT` | Path: `staffId`           | 200, 403, 404 |
| `GET`  | `/api/client`                    | Get paginated list of salon clients          | `BEARER_JWT` | Params: `?page=0&size=10` | 200, 403      |

#### Client Staff Reassignment

| Method  | Endpoint                                     | Description                      | Access       | Request Format                 | Status Codes  |
| ------- | -------------------------------------------- | -------------------------------- | ------------ | ------------------------------ | ------------- |
| `PATCH` | `/api/client/{clientId}/assign/{newStaffId}` | Reassign client to another staff | `BEARER_JWT` | Path: `clientId`, `newStaffId` | 200, 403, 404 |

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


