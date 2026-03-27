*Last updated: 2026-03-27*

---

## Table of Contents

1. [Business Overview](#1-business-overview)
2. [Core Hypotheses](#2-core-hypotheses)
3. [MVP Scope (MoSCoW)](#3-mvp-scope-moscow)
4. [User Stories](#4-user-stories)
5. [Data Model](#5-data-model)
6. [Stakeholder Map](#6-stakeholder-map)
7. [Gap Analysis & Mitigations](#7-gap-analysis--mitigations)
8. [Workflows](#8-workflows)
   - 8.1 [Provider Onboarding & Service Catalog](#81-provider-onboarding--service-catalog)
   - 8.2 [Customer Registration & Service Discovery](#82-customer-registration--service-discovery)
   - 8.3 [Booking Flow](#83-booking-flow)
   - 8.4 [Payment & Payout](#84-payment--payout)
   - 8.5 [Feedback & Ratings](#85-feedback--ratings)
   - 8.6 [Notifications](#86-notifications)
9. [Recommended Next Steps](#9-recommended-next-steps)
10. [Pending Decisions](#10-pending-decisions)

---

## 1. Business Overview

A two-sided platform connecting customers with Massage Providers (spas, wellness centers) and their individual Technicians (masseurs), organized by the **services** offered.

- **Customers:** Browse providers, select a **service type**, choose a specific technician (filtered by gender/age), book a time, pay online **or pay cash to provider**, and leave feedback on the technician and service.
- **Providers:** List their business, define **custom services**, assign technicians to services, manually confirm bookings, receive payouts for online bookings (commission deducted), and pay commission separately for cash bookings.
- **Platform:** Collects online payments, holds funds, manages refunds, processes payouts (daily batch, manual bank transfer), invoices cash bookings, and handles customer support.

---

## 2. Core Hypotheses

1. **Customer-side:** Will customers use the application to discover and book massage providers, specifically choosing by service type and individual technician based on feedback?
2. **Provider-side:** Will providers perceive sufficient incremental revenue to justify the commission and the operational effort of managing services and manual confirmation?

---

## 3. MVP Scope (MoSCoW)

### Must Have
| Area | Description |
|------|-------------|
| **Customer app/web** | Registration/login (email/phone). Browse providers, search by service or provider name. View service details (description, duration, price). View technician profiles with name, image, age, gender, short intro, ratings. |
| **Service catalog** | Providers define custom services (name, description, duration, price). Each service can be assigned to one or more technicians. |
| **Booking flow** | Customer selects provider → service → technician (with gender/age filters) or “any available” → date/time → payment method (online via VNPay or cash). Booking request submitted for manual provider confirmation. |
| **Provider dashboard** | Provider registration (business details, location). Manage services (create, edit, assign technicians). Manage technicians (name, image, age, gender, intro). View booking requests. Confirm/decline (assign technician if “any available”). |
| **Payment & payout** | **Online:** VNPay integration; platform holds funds. **Cash:** Booking confirmed without prepayment. **Payout for online:** Daily batch, manual bank transfer file; after service + feedback timeout (3 days). **Commission:** Configurable per provider (default 15%). |
| **Feedback** | Post-service rating (1–5 stars) and comment for both technician and service. Aggregated ratings on technician profile and service page. |
| **Notifications** | Email, SMS (opt‑in), and internal (in‑app) notifications for booking status, reminders, feedback requests, and cancellations. |

### Should Have
- Manual availability indicators per technician.
- Service-level average rating displayed.
- Basic transaction history for providers.
- Basic search/filter (manual location input, price range).
- Thorough provider verification (license, call, in‑person).
- Customer self‑service cancellation with fee (10% capped) up to X hours before service.

### Could Have (Post‑MVP)
- Automated calendar sync, in‑app chat, promo codes, advanced analytics, service add-ons.

### Won’t Have (MVP Out of Scope)
- Provider self-service real‑time scheduling.
- Mobile native apps (responsive web app sufficient).
- Multi‑language / multi‑currency.
- Automated payout via API (manual bank transfer file).
- Commission auto‑deduction from cash bookings (invoiced separately).

---

## 4. User Stories

### Customer
- As a customer, I want to sign up so that I can book massages.
- As a customer, I want to browse providers by **service name** or **provider name** so that I find what I need.
- As a customer, I want to manually enter my city/zip so that I see providers nearby.
- As a customer, I want to view **service details** (duration, price, description) so that I understand what I'm booking.
- As a customer, I want to view technician profiles with **ratings by service** and see **age & gender** so that I can choose a technician I’m comfortable with.
- As a customer, I want to filter technicians by **gender and age range** when selecting a service.
- As a customer, I want to select a date/time and submit a booking request so that the provider can confirm.
- As a customer, I want to choose **pay online** or **pay cash** at the time of booking.
- As a customer, I want to receive confirmation once my booking is approved.
- As a customer, I want to cancel a confirmed booking **self‑service** with a clear fee policy.
- As a customer, I want to rate both the **technician and the service** after my appointment so that future customers can make informed choices.

### Provider
- As a provider, I want to register my business and go through **verification** so that customers trust my listing.
- As a provider, I want to define my **custom services** (name, price, duration) so that customers know what I offer.
- As a provider, I want to **assign technicians to specific services** so that customers only book qualified staff.
- As a provider, I want to view incoming booking requests with **service and technician details** (if selected) and confirm/decline.
- As a provider, I want to assign a technician when a customer chooses “any available” during confirmation.
- As a provider, I want to see upcoming confirmed bookings so that I can prepare.
- As a provider, I want to receive **daily batch payouts** for online bookings after service completion and feedback timeout.
- As a provider, I want to see my **cash booking invoices** and pay commission separately.
- As a provider, I want to see my earnings per service and commission deductions.

### Platform Admin
- As an admin, I want to **thoroughly verify providers** (license, call, in-person) before activation.
- As an admin, I want to view disputes and handle refunds (online bookings) via VNPay.
- As an admin, I want to generate a **daily bank transfer file** for payouts and mark them as paid.
- As an admin, I want to generate **weekly commission invoices** for providers with cash bookings.
- As an admin, I want to configure system parameters (confirmation window, feedback timeout, cancellation fee, commission rate).

---

## 5. Data Model

| Entity | Attributes |
|--------|------------|
| **Provider** | Business name, address (city/street), contact number, email, logo, verification status, commission rate (%) |
| **Service** | Name, description, duration (minutes), price (VND), provider ID |
| **Technician** | Name, profile image, short introduction, age, gender, provider ID |
| **Service‑Technician Assignment** | Technician ID, Service ID (many-to-many) |
| **Booking** | Customer ID, Service ID, Technician ID (optional), date/time, status, payment method (online/cash), payment status, amount, cancellation fee applied |
| **Review** | Booking ID, rating (1–5), comment, target type (technician or service) |
| **Payout** | Provider ID, batch date, total amount, commission deducted, status, manual file reference |
| **Cash Invoice** | Provider ID, period start/end, list of cash bookings, total commission due, status (unpaid/paid) |
| **Notification** | User ID, type, title, content, read status, created_at, link URL |

---

## 6. Stakeholder Map

| Stakeholder | Role | Influence | Key Needs |
|-------------|------|-----------|-----------|
| **Customer** | End user booking massage | High | Easy discovery by service type, trusted ratings, reliable service |
| **Provider (Business)** | Pays commission, manages services & technicians | High | Increased revenue, clear service performance data, simple confirmation flow |
| **Technician** | Performs service | Medium | Clear schedule, fair treatment, ratings visibility by service type |
| **Platform Admin** | Operator | High | Operational control, verification, dispute handling |
| **Payment Gateway (VNPay)** | Processes transactions | Medium | Secure integration, compliance |
| **Legal/Compliance** | Advisors | Low (initial) | Terms of service, privacy policy, commission agreements |

---

## 7. Gap Analysis & Mitigations

| Gap | Mitigation |
|-----|------------|
| **Manual confirmation delay** may frustrate customers | Configurable confirmation window (4h/8h) with auto‑cancel and refund. Short‑notice bookings (within window) auto‑cancel if not confirmed before service start. |
| **Service assignment complexity** | Provide clear onboarding guidance and simple UI for assignment. |
| **Commission collection & payment** | Use VNPay for online payments; manual payout file gives control. For cash bookings, invoice weekly and require payment before further bookings (or suspend). |
| **Provider adoption** requires critical mass | Launch in one city; pre‑onboard 10–15 providers with diverse service offerings before marketing to customers. |
| **Service + technician ratings** need volume | Incentivize first reviews with discount on future bookings; display ratings prominently. |
| **Cash payment commission collection** may be delayed | Weekly invoices; provider dashboard shows unpaid invoices; grace period then suspension. |

---

## 8. Workflows

### 8.1 Provider Onboarding & Service Catalog

#### Steps
1. **Registration:** Provider enters business name, address, contact, email, logo (optional). Account created with status *Pending*.
2. **Verification:** Admin reviews business license, calls, may visit. Approves or rejects. Approved providers receive activation email.
3. **Service Definition:** Provider creates custom services (name, description, duration, price).
4. **Technician Management:** Provider adds technicians (name, short intro, image, age, gender).
5. **Service‑Technician Assignment:** Provider assigns each service to one or more technicians.

#### Key Decisions
- Services are custom (free text).
- Technician fields include age and gender for filtering.
- Multi‑location providers = separate accounts per location.
- Verification is thorough (license, call, in‑person).

---

### 8.2 Customer Registration & Service Discovery

#### Steps
1. **Registration:** Customer signs up with email/phone, verification code.
2. **Search & Filters:** Search by service or provider name; manual location input (city/zip); price range filter.
3. **Provider Profile:** View provider details, list of services.
4. **Service & Technician Selection:** Choose a service, then view assigned technicians. Apply gender and age filters. Option to choose “any available technician”.

#### Key Decisions
- Search includes service and provider names only.
- Geolocation is manual input.
- Gender and age filters appear on technician selection screen.
- “Any available” option allowed; provider assigns at confirmation.

---

### 8.3 Booking Flow

#### Steps
1. **Customer Books:** Select date/time, payment method (online via VNPay or cash). Payment collected online or marked as cash.
2. **Booking Request:** Created with status *Pending Provider Confirmation*. Notifications sent.
3. **Provider Response:** Provider confirms or declines. If “any available”, provider must assign technician before confirming.
4. **Auto‑cancellation:** If provider does not respond within configurable window (4h or 8h), booking auto‑cancelled and refunded (if online). For short‑notice bookings (within window), auto‑cancel at service start if not confirmed.
5. **Self‑service Cancellation (post‑confirmation):** Customer can cancel up to configurable hours before service. Cancellation fee = 10% capped, deducted from refund. If cancelled later, no refund.
6. **Post‑Service:** After service, feedback triggered; payout released after feedback or timeout (3 days).

#### Key Decisions
- Confirmation window configurable (4h/8h) with short‑notice handling.
- Feedback timeout configurable (default 3 days).
- Self‑service cancellation with fee (10% capped).
- Cash bookings: no refund, commission invoiced separately.

---

### 8.4 Payment & Payout

#### Payment at Booking
- **Online:** VNPay integration; funds held by platform.
- **Cash:** Booking confirmed without prepayment; commission due later.

#### Refunds (Online Only)
- Full refund for provider decline or auto‑cancellation.
- Partial refund (minus cancellation fee) for customer self‑cancellation.
- Admin can initiate refunds for disputes.

#### Payout for Online Bookings
- **Eligibility:** Service completed + feedback timeout reached (or feedback submitted).
- **Daily batch:** System generates payout file with provider bank details and amounts.
- **Manual transfer:** Admin uses file to process bank transfers, then marks payouts as paid.
- **Commission:** Configurable per provider (default 15%) deducted from payout.

#### Cash Booking Commission
- Weekly invoice generated for each provider, listing cash bookings and total commission due.
- Provider pays platform separately; admin marks invoice paid.
- Grace period and suspension possible for unpaid invoices.

#### Key Decisions
- VNPay as gateway.
- Daily batch payout with manual bank transfer file.
- Currency VND.
- Platform absorbs chargebacks after payout.
- Commission configurable (default 15%).

---

### 8.5 Feedback & Ratings

#### Steps
1. **Trigger:** After service end + buffer (2h), customer receives notification to leave feedback.
2. **Submission:** Customer rates technician and service (1–5 stars, optional comments). One submission per booking.
3. **Aggregation:** Average ratings updated on technician profile and service page. Rating count displayed.
4. **Payout:** If feedback submitted within timeout (3 days), payout triggered immediately. If not, payout auto‑released after timeout.
5. **Moderation:** Admin can hide inappropriate reviews.

#### Key Decisions
- Both technician and service rated.
- Feedback timeout configurable (default 3 days).
- No edits allowed (MVP).
- No provider reply (post‑MVP).
- Ratings do not influence search ranking in MVP.

---

### 8.6 Notifications

#### Channels
- **Email** – mandatory for transactional messages.
- **SMS** – opt‑in, for urgent/time‑sensitive alerts.
- **Internal (in‑app)** – stored in notification center; always on.

#### Key Triggers
- **Customer:** Booking request submitted, confirmed, declined, auto‑cancelled, self‑cancellation, reminder, feedback request.
- **Provider:** New booking request, confirmed, declined, customer cancelled, auto‑cancelled, payout batch ready, cash invoice ready, invoice overdue.

#### Features
- Notification center with unread badge.
- Read/unread status.
- Click‑through to relevant booking or invoice.
- Retention period (e.g., 30 days).

#### Key Decisions
- Email mandatory; SMS opt‑in.
- Internal notifications always on.
- Reminder lead time configurable (e.g., 2 hours before service).
- Failure logging for email/SMS; internal always succeeds.

---

## 9. Recommended Next Steps

- [ ] **Finalize geographic scope and timeline.** Single city in Vietnam; 12‑week target.
- [ ] **Select technology stack.** Evaluate no‑code vs. custom development.
- [ ] **Draft legal agreements.** Commission rate, payout schedule, cancellation policy, cash payment terms.
- [ ] **Create wireframes / user flows.** Detailed UI/UX based on workflows.
- [ ] **Set up VNPay integration.** Obtain merchant account; test refund flow.
- [ ] **Define cash commission collection process.** Weekly invoicing, due date, suspension policy.
- [ ] **Onboard pilot providers.** Test full flow with 10–15 providers.

---

## 10. Pending Decisions

| Item | Status | Notes |
|------|--------|-------|
| Launch geography | To be confirmed | Single city in Vietnam |
| Commission rate | Configurable; default 15% | May offer different rates per provider |
| Timeline and budget | To be confirmed | 12‑week target |
| Tech approach | To be confirmed | No‑code vs. custom |
| Cancellation deadlines | To be confirmed | e.g., 2 hours before service for self‑cancellation with fee |
| Cash invoice due date | To be confirmed | e.g., 7 days after invoice |
| SMS provider | To be confirmed | Local Vietnam SMS gateway |
| Reminder lead time | To be confirmed | e.g., 2 hours before service |

---

*End of Document*