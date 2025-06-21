

# EagleVerse AI – Partner With Us (Backend)

This repository contains the backend service for **EagleVerse AI's Partner With Us** module, built with Spring Boot. It enables salon managers and owners to submit partnership requests and helps the internal team manage and track these leads.

---

## 🛠 Tech Stack

* Java 17
* Spring Boot
* Spring Security
* MySQL
* Redis (for token storage)
* JavaMailSender (for email communication)
* Postman (for API testing)

---

## 📌 Key Features

* Submit detailed partnership requests
* Store and retrieve leads via REST APIs
* Send password setup links via email
* Protect sensitive APIs using Basic Auth
* Track request source (website, app, campaigns, etc.)

---

## 🔗 API Endpoints

### 📂 Lead Management (`/api/leads`)
| Method | Endpoint | Description | Access | Request Format | Status Codes |
|--------|----------|-------------|--------|----------------|--------------|
| `GET` | `/leads` | Fetch all leads | `PUBLIC` | *None* | 200, 404 |
| `GET` | `/leads/{id}` | Fetch specific lead | `PUBLIC` | `id` in path | 200, 404 |
| `GET` | `/leads/summary` | Fetch summary view | `PUBLIC` | *None* | 200 |
| `GET` | `/leads/get-id/{email}` | Get lead ID by email | `PUBLIC` | `email` in path | 200, 404 |
| `POST` | `/leads` | Create new lead | `ROLE_ADMIN` | Body: `PartnerLeadRequest` | 201, 400 |
| `PUT` | `/leads/{id}` | Update lead | `ROLE_ADMIN` | `id` in path + Body: `PartnerLeadUpdateRequest` | 200, 400 |
| `PATCH` | `/leads/{id}/status` | Update status | `ROLE_ADMIN` | `id` in path + status as parameter | 200, 400 |
| `DELETE` | `/leads/{id}` | Delete lead | `ROLE_ADMIN` | `id` in path | 204, 404 |

### 🔐 Authentication (`/api/auth`)
| Method | Endpoint | Description | Access | Request Format | Status Codes |
|--------|----------|-------------|--------|----------------|--------------|
| `POST` | `/auth/set-password` | Set password | `PUBLIC` | Body: `PasswordSetupRequest` | 200, 400 |
| `POST` | `/auth/resend-invite/{leadId}` | Resend invite | `ROLE_ADMIN` | `leadId` in path | 200, 404 |
| `GET` | `/auth/check-token` | Validate token | `PUBLIC` | in param `token` and `leadId` | 200, 410 |
| `GET` | `/auth/request-reset-password` | To reequest to reset password | `PUBLIC` | Body `ResetPasswordRequest` | 200, 410 |
| `POST` | `/auth/login` | Generate JWT | `PUBLIC` | Body: `AuthRequest` | 200, 401 |

### 👥 Salon Staff Management (`/api`)
#### Staff Listing & Details
| Method | Endpoint | Description | Access | Request Format | Status Codes |
|--------|----------|-------------|--------|----------------|--------------|
| `GET` | `/salon-user/get-all` | Get all users | `PUBLIC` | *None* | 200 |
| `GET` | `/salon-user/salon-staff` | Get salon staff | `BEARER_JWT` | param: `?salonId=ID&requestId=ID` | 200, 403 |
| `GET` | `/salon-user/staff/{staffId}` | Get staff details | `BEARER_JWT` | `staffId` in path + param: `?requestId=ID` | 200, 404 |

#### Staff Onboarding
| Method | Endpoint | Description | Access | Request Format | Status Codes |
|--------|----------|-------------|--------|----------------|--------------|
| `POST` | `/salon-user/register` | Manual registration | `BEARER_JWT` | Body: `ManualStaffRegistrationRequest` | 201, 400 |
| `POST` | `/salon-user/invite` | Send invite | `BEARER_JWT` | Body: `StaffInviteRequest` + param: `?invitedBy=ID` | 200, 400 |
| `POST` | `/salon-user/complete-registration` | Complete registration | `PUBLIC` | Body: `CompleteStaffRegistrationRequest` | 200, 410 |

#### Staff Operations
| Method | Endpoint | Description | Access | Request Format | Status Codes |
|--------|----------|-------------|--------|----------------|--------------|
| `PUT` | `/salon-user/staff/update-status` | Change status | `BEARER_JWT` | Body: `StaffStatusUpdateRequest` | 200, 403 |
| `PUT` | `/salon-user/staff/update-details` | Update profile | `BEARER_JWT` | Body: `StaffDetailUpdateRequest` + params: `staffId` and `updatedBy` | 200, 400 |
| `PUT` | `/salon-user/cancel-delete/{staffId}` | Cancel deletion | `BEARER_JWT` | `staffId` in path + param: `?deletedBy=ID` | 200, 404 |
| `DELETE` | `/salon-user/{staffId}` | Delete staff | `BEARER_JWT` | `staffId` in path + param: `?deletedBy=ID` | 202, 404 |

### 🔐 Auth Info

* **Admin Only** endpoints require `ROLE_ADMIN` and are protected by Spring Security.
* For local testing, Basic Auth credentials are:

  * **Username**: `admin`
  * **Password**: `eagle123`

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

### `PasswordSetupRequest`

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

---
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
---
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
---
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
---
### StaffStatusUpdateRequest
```json
{
  "staffId": "c0a80123-4567-8901-2345-67890abcdef5",
  "isActive": true,
  "updatedBy": "c0a80123-4567-8901-2345-67890abcdef6"
}

```
---
### AuthRequest
```json
{
  "email": "demo@demo.com",
  "password": ""
}

```
---

## 🔍 Testing

Postman was used to validate all APIs. Public access is currently allowed for testing; auth will be tightened after frontend integration.

---

