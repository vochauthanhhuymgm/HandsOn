## Actors
- **Provider** – Business owner or manager registering their massage business.
- **Platform Admin** – You or your team, responsible for verifying providers.

---

## Current Workflow (No Platform)
- Provider operates via walk-ins, phone calls, or social media.
- No structured service catalog, technician management, or online presence.
- No commission collection or digital booking.

---

## MVP Workflow Steps

### 1. Provider Registration

| Step | Action | System Behavior |
|------|--------|-----------------|
| 1.1 | Provider visits platform and clicks **"Register as Provider"** | System displays registration form. |
| 1.2 | Provider enters business details: business name, address (city/street), contact number, email, password. | System validates format; checks for duplicate email. |
| 1.3 | Provider uploads business logo / photo (optional for MVP). | System stores image. |
| 1.4 | Provider submits registration. | System creates account with status **Pending**; sends notification to platform admin. |

---

### 2. Admin Verification

| Step | Action | System Behavior |
|------|--------|-----------------|
| 2.1 | Admin receives notification of new provider registration. | Admin dashboard shows list of pending providers. |
| 2.2 | Admin reviews business details. Verification includes: business license upload, phone call, possible in-person visit (as determined by your policy). | Admin can approve, reject, or request more information. |
| 2.3 | Admin approves provider. | System sends approval email to provider with login link; provider account status becomes **Active**. |
| 2.4 | (If rejected) Admin provides reason. | System sends rejection email; provider can re-apply with corrections. |

---

### 3. Service Definition (Custom Services)

| Step | Action | System Behavior |
|------|--------|-----------------|
| 3.1 | Provider logs in and navigates to **"Services"** section. | System displays list of existing services (empty initially). |
| 3.2 | Provider clicks **"Add Service"**. | System displays service creation form. |
| 3.3 | Provider enters: **service name**, **description**, **duration** (minutes), **price**. | System validates fields (positive numbers, required fields). |
| 3.4 | Provider saves service. | System adds service to provider’s catalog; displays in list. |
| 3.5 | Provider can edit or delete services as needed. | System updates catalog accordingly (deleting a service does not affect past bookings). |

---

### 4. Technician Management

| Step | Action | System Behavior |
|------|--------|-----------------|
| 4.1 | Provider navigates to **"Technicians"** section. | System displays list of existing technicians. |
| 4.2 | Provider clicks **"Add Technician"**. | System displays technician creation form. |
| 4.3 | Provider enters: **name**, **short introduction**, **image** (photo), **age**, **gender** (male/female/other). | System stores technician profile. |
| 4.4 | Provider saves technician. | System adds technician to list. |
| 4.5 | Provider can edit, deactivate, or delete technicians. | Deactivated technicians are hidden from new bookings; past bookings retain technician info. |

---

### 5. Service–Technician Assignment

| Step | Action | System Behavior |
|------|--------|-----------------|
| 5.1 | Provider selects a service from their catalog. | System shows service details with an **"Assign Technicians"** option. |
| 5.2 | Provider clicks **"Assign Technicians"**. | System displays a list of all active technicians with checkboxes. |
| 5.3 | Provider selects which technicians can perform this service. | System saves assignments. When a customer books this service, they will see only the assigned technicians. |
| 5.4 | Provider repeats for each service. | System maintains many-to-many relationships. |

---

## Edge Cases & Handling

| Scenario | Handling |
|----------|----------|
| Registration missing required fields | System highlights errors; does not submit until complete. |
| Duplicate email | System shows error: “Email already registered. Log in or use another email.” |
| Admin rejects provider | System sends rejection email with reason; provider can edit and resubmit. |
| Provider edits a service after bookings exist | Existing pending/confirmed bookings retain original price/duration; new bookings use updated values. |
| Technician leaves provider | Provider deactivates technician. Technician no longer appears in new bookings, but past bookings remain. |
| Provider has multiple locations | Each location registers as a separate provider account (with unique email). |
| Provider forgets password | Standard password reset via email. |

---

## Key Decisions Incorporated

| Decision | Implementation |
|----------|----------------|
| **Services are custom** | Providers create their own service names, descriptions, durations, and prices. |
| **Technician fields** | Name, short introduction, image, age, gender. All are required for MVP except possibly introduction? *(Confirm if optional)* |
| **Multi‑location providers** | Separate provider accounts per location. |
| **Verification rigor** | Admin will perform thorough verification (business license, phone call, possibly in-person). Provider accounts remain inactive until approved. |

---

## Open Questions (for refinement)

1. **Technician introduction:** Is this a required field for MVP? If not, we can make it optional.
2. **Service images:** Should providers be able to upload an image per service? This is currently not in MVP but may be added later.
3. **Technician age/gender usage:** Will customers be able to filter by these? If yes, we must include them in the booking workflow.
---
*Workflow documented: 2026-03-27*
