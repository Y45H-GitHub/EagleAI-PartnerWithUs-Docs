

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

## 📅 Updates & Changelog

### ✅ **30th May 2025**

**Entities Created**:

* `BusinessDetails`
* `PartnerLead`
* `PrimaryContact`
* `SalonInfo`

**Form Fields Captured**:

* **Salon Info**: Name, branch number, city, average monthly footfall, client type (walk-in/appointment)
* **Primary Contact**: Name, email, phone, designation (manager/owner)
* **Business Detail**: Business type, GSTIN
* **Meta Info**: Lead source (e.g., website, campaign, app)

> The frontend sends this as a single `PartnerLeadRequest` to the backend via `POST`.

---

### ✅ **31st May 2025**

* Added DTOs
* Integrated with MySQL
* Created REST APIs

> 🔐 `DELETE` and `PATCH` APIs now require **Basic Authentication**

---

## 🔗 API Endpoints

### 📂 **Lead Management** (`/api/leads`)

| Method   | Endpoint                    | Description                     | Access     |
| -------- | --------------------------- | ------------------------------- | ---------- |
| `GET`    | `/api/leads`                | Fetch all leads                 | Public     |
| `GET`    | `/api/leads/{id}`           | Fetch a specific lead by ID     | Public     |
| `GET`    | `/api/leads/summary`        | Fetch summary view of all leads | Public     |
| `GET`    | `/api/leads/get-id/{email}` | Fetch lead ID by email          | Public     |
| `POST`   | `/api/leads`                | Create a new lead               | Admin Only |
| `PUT`    | `/api/leads/{id}`           | Update an existing lead         | Admin Only |
| `PATCH`  | `/api/leads/{id}/status`    | Update lead status              | Admin Only |
| `DELETE` | `/api/leads/{id}`           | Delete a lead                   | Admin Only |

---

### 🔐 **Authentication & Token Setup** (`/api/auth`)

| Method | Endpoint                           | Description                                        | Access     |
| ------ | ---------------------------------- | -------------------------------------------------- | ---------- |
| `POST` | `/api/auth/set-password`           | Set password using token, leadId, and new password | Public     |
| `POST` | `/api/auth/resend-invite/{leadId}` | Resend invite link to lead email                   | Admin Only |
| `GET`  | `/api/auth/check-token`            | Validate password setup token                      | Public     |

---

### 🔐 Auth Info

* **Admin Only** endpoints require `ROLE_ADMIN` and are protected by Spring Security.
* For local testing, Basic Auth credentials are:

  * **Username**: `admin`
  * **Password**: `eagle123`

---

### ✅ **1st June 2025**

* Pushed new summary API: `/api/leads/summary`
* Email service under development

---

### ✅ **2nd June 2025**

* Added **Spring Security** to `DELETE` and `PATCH` endpoints

> For testing use:
> **Username**: `admin`
> **Password**: `eagle123`

---

### ✅ **3rd June 2025**

* Implemented email service to send password setup links
* Updated API list to include auth-related endpoints

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
  "newPassword": "newSecurePassword",
  "leadId": "uuid-of-lead"
}
```

---

## 🔍 Testing

Postman was used to validate all APIs. Public access is currently allowed for testing; auth will be tightened after frontend integration.

---

