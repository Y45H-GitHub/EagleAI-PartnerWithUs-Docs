# Eagle Verse Backend DOCS cum UPDATES

# Updates
## 30th May, 2025 : Made entities Business Details, PartnerLead, PrimaryContact, SalonInfo
When a salon owner or manager opens the “Partner With Us” form, they’ll fill out fields like:
- Salon Info:
name, branch number, city, average monthly footfall, client type (walk-in, appointment, etc.)
- Primary Contact:
name, email, phone, designation (manager, owner, etc.)
- Business Detail:
business type, GSTIN
The form may also internally capture the source (e.g., "website", "campaign", "app" etc.)
- Frontend Sends:
All this data will be bundled into one PartnerLeadRequest and sent to the backend via a POST API.

## 31st May: Made DTOS, REST controlled, Linked with MySQL (updated 3rd June)
- POST /api/leads: Create a new lead. <br />
  @RequestBody PartnerLeadRequest request <br /> 
- GET /api/leads: Get all leads. 
- GET /api/leads/summary: Get a summary of all leads.
- GET /api/leads/get-id/{email}: Get lead ID by email. <br />
  @PathVariable String email <br />
- GET /api/leads/{id}: Get a specific lead by its ID. <br />
  @PathVariable UUID id <br />
- PUT /api/leads/{id}: Update a specific lead by its ID. <br />
  @PathVariable UUID id, @RequestBody PartnerLeadUpdateRequest request <br />
- DELETE /api/leads/{id}: Delete a specific lead by its ID. <br />
  @PathVariable UUID id <br />
- PATCH /api/leads/{id}/status: Update the status of a specific lead. <br />
  @PathVariable UUID id, @RequestParam LeadStatus status <br />
- POST /api/auth/resend-invite/{leadId}: Resend an invitation to a lead. <br />
  @PathVariable UUID leadId <br />
- GET /api/auth/check-token: Check if a token is valid. <br />
  @RequestParam String token <br />
- POST /api/auth/set-password: Set a password using a token/request. <br />
  @RequestBody PasswordSetupRequest request <br />

  
  
 ## 31st May update 2: Testing of APIs done with postman. API public access left. After testing with frontend will host it.


## 1st June: 
Pushed new api <url>/api/leads/summary. 
Working on email service

## 2nd June
Added Spring Secuity to DELETE and PATCH apis. These two now requre Basic Auth . <br />
401 - AUnauthorized  and 403- Forbidden    <br />
For testing use Basic Auth , username: admin, password: eagle123

## 3rd June
Added new Email Service to set password for Leads that have been approved by our team.
Added new API endpoints. [updated above] 
### format for request DTO
PartnerLeadRequest
```
{
source
salonName
branchNumber
city
avgMonthlyFootfall
clientType
contactName
contactEmail
contactPhone
contactDesignation
businessType
gstin
}
```
PasswordSetupRequest
```
{
token
newPassword
leadId
}
```
