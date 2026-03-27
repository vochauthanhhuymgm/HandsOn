## Epic Summary
This table provides a high-level roadmap for development planning and sprint prioritization. Let me know if you need it exported to another format or if you’d like to adjust any estimates or dependencies.
I have combined the refined story updates into the epic summary table below. An additional column **Key Story Updates** captures the finalized decisions and any affected stories, making it easy to see the impact at the epic level.

---

### Epic Summary Table with Refined Decisions

| Epic ID | Epic Name                             | Stories   | Goal                                                                                                   | Key Story Updates (with finalized decisions)                                                                                                                                                             |
| ------- | ------------------------------------- | --------- | ------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **E-1** | Provider Onboarding & Service Catalog | 5 stories | Enable providers to register, get verified, and manage their services and technicians.                 | • Services priced in VND (default currency). • No geographic constraints beyond single-city launch.                                                                                                      |
| **E-2** | Customer Registration & Discovery     | 4 stories | Allow customers to sign up, search for services, and view provider/technician details.                 | • Search location filter limited to city/zip within launch city (single city in Vietnam).                                                                                                                |
| **E-3** | Booking Flow                          | 5 stories | Let customers book a service, providers confirm/assign technicians, and handle cancellations.          | • **Cancellation deadline:** 2 hours before service start (Story E-3.5). • **Cancellation fee:** 10% capped. • **Confirmation window:** configurable (default 4h) for auto-cancellation (Story E-3.3).   |
| **E-4** | Payment & Payout                      | 5 stories | Process online payments (VNPay), manage refunds, calculate commissions, and generate payouts/invoices. | • **Commission rate:** default 15%, configurable per provider (Stories E-4.3, E-4.5). • **Cash invoice due date:** 7 days after invoice (Story E-4.5).                                                   |
| **E-5** | Feedback & Ratings                    | 3 stories | Capture post-service ratings for technicians and services, and display aggregated scores.              | • No decision changes; feedback timeout remains configurable.                                                                                                                                            |
| **E-6** | Notifications                         | 3 stories | Deliver transactional and reminder notifications via email, SMS, and in-app center.                    | • **Reminder lead time:** 2 hours before service (Story E-6.1). • **SMS provider:** TBD – story remains but implementation deferred (Story E-6.2).                                                       |
| **E-7** | Admin Operations                      | 3 stories | Provide admin tools for provider verification, dispute handling, and system configuration.             | • System configuration now includes: confirmation window, cancellation deadline (2h), cancellation fee cap, commission rates, reminder lead time (2h), feedback timeout, invoice due days (Story E-7.3). |

---
### Complete Story Lookup Table

| Epic | Story ID | Story Title | Estimate | Dependencies (Story IDs) |
|------|----------|-------------|----------|--------------------------|
| **E-1** | E-1.1 | Provider Registration | S | None |
| | E-1.2 | Admin Verification | S | E-1.1 |
| | E-1.3 | Manage Services | M | E-1.2 |
| | E-1.4 | Manage Technicians | M | E-1.2 |
| | E-1.5 | Assign Technicians to Services | S | E-1.3, E-1.4 |
| **E-2** | E-2.1 | Customer Registration | S | None |
| | E-2.2 | Search & Filter Providers | M | E-1.2, E-1.3 |
| | E-2.3 | View Provider Profile & Services | S | E-2.2, E-5 |
| | E-2.4 | View Technician Profiles | M | E-1.4, E-1.5, E-5 |
| **E-3** | E-3.1 | Select Date/Time & Submit Booking | S | E-2.3, E-2.4 |
| | E-3.2 | Provider View & Respond to Booking Requests | M | E-3.1, E-1.5 |
| | E-3.3 | Auto-Cancellation of Unconfirmed Bookings | S | E-3.2 |
| | E-3.4 | Short-Notice Booking Handling | S | E-3.2, E-3.3 |
| | E-3.5 | Customer Self-Service Cancellation | M | E-3.2 |
| **E-4** | E-4.1 | VNPay Integration – Online Payment at Booking | L | E-3.1 |
| | E-4.2 | Refund Processing | M | E-4.1, E-3.5, E-3.3, E-3.4 |
| | E-4.3 | Provider Payout Calculation & File Generation | M | E-4.1, E-5 |
| | E-4.4 | Admin Mark Payout as Paid | S | E-4.3 |
| | E-4.5 | Cash Booking Commission Invoicing | M | E-3.5 |
| **E-5** | E-5.1 | Post-Service Feedback Submission | S | E-3.2, E-6 |
| | E-5.2 | Display Aggregated Ratings | S | E-5.1 |
| | E-5.3 | Admin Moderation of Reviews | S | E-5.1 |
| **E-6** | E-6.1 | Transactional Email Notifications | L | All previous epics (events) |
| | E-6.2 | SMS Notifications (Opt-in) | M | E-6.1 |
| | E-6.3 | In-App Notification Center | M | E-6.1, E-6.2 |
| **E-7** | E-7.1 | Provider Verification Management | S | E-1.1 |
| | E-7.2 | Dispute & Refund Handling | M | E-4.1, E-4.2 |
| | E-7.3 | System Configuration | M | None |

---
## Epic E-1: Provider Onboarding & Service Catalog

**Goal:** Enable massage providers to register their business, go through admin verification, and set up their services and technicians so that customers can discover them.

### Story E-1.1: Provider Registration  
**As a** provider business owner, **I want** to create an account with my business details (name, address, contact, email, logo), **so that** I can start listing my services.  
**Acceptance Criteria:**  
- Given I am a new provider, when I submit registration form with required fields, then my account is created with status “Pending”.  
- Given I submit incomplete data, when I attempt to register, then I see validation errors.  
- Given I register, when I use an email already in use, then I am notified and cannot create duplicate.  
**Dependencies:** None  
**Estimate:** S

### Story E-1.2: Admin Verification  
**As a** platform admin, **I want** to view pending provider registrations, review documents, and approve or reject them, **so that** only legitimate businesses are activated.  
**Acceptance Criteria:**  
- Given a provider in “Pending” status, when I open the admin panel, then I see their submitted details.  
- When I approve a provider, then their status becomes “Active” and they receive an activation email.  
- When I reject a provider, then their status becomes “Rejected” and they receive a rejection email (with reason).  
**Dependencies:** Story E-1.1  
**Estimate:** S

### Story E-1.3: Manage Services  
**As a** provider, **I want** to create, edit, and delete my custom services (name, description, duration, price), **so that** customers see what I offer.  
**Acceptance Criteria:**  
- Given I am an active provider, when I create a new service, then it appears in my service list.  
- Given I have services, when I edit a service, then changes are saved and visible to customers.  
- Given I have services, when I delete a service that has no bookings, then it is removed.  
- Given I delete a service with existing bookings, then I am prevented (or soft-deleted with warning).  
**Dependencies:** Story E-1.2  
**Estimate:** M

### Story E-1.4: Manage Technicians  
**As a** provider, **I want** to add technicians with their name, profile image, short intro, age, and gender, **so that** customers can choose a technician they’re comfortable with.  
**Acceptance Criteria:**  
- Given I am an active provider, when I add a technician with all required fields, then the technician is added to my list.  
- When I edit a technician, then the updated info is reflected.  
- When I delete a technician, then they are removed from any service assignments (future bookings affected).  
**Dependencies:** Story E-1.2  
**Estimate:** M

### Story E-1.5: Assign Technicians to Services  
**As a** provider, **I want** to assign one or more technicians to each service, **so that** customers only see qualified staff for that service.  
**Acceptance Criteria:**  
- Given I have services and technicians, when I assign technicians to a service, then that service shows only those technicians during customer booking.  
- When I remove an assignment, then the technician no longer appears for that service.  
- Given a technician is assigned to a service, when the technician is deleted, then the assignment is automatically removed.  
**Dependencies:** Stories E-1.3, E-1.4  
**Estimate:** S

---

## Epic E-2: Customer Registration & Discovery

**Goal:** Allow customers to sign up, search/filter providers, and view detailed service and technician information.

### Story E-2.1: Customer Registration  
**As a** customer, **I want** to sign up using email or phone number, **so that** I can book massages.  
**Acceptance Criteria:**  
- Given I am a new user, when I register with email, then I receive a verification code and can activate my account.  
- When I register with phone number, I receive an SMS with verification code.  
- When I attempt to register with an already used email/phone, I am shown an error.  
**Dependencies:** None  
**Estimate:** S

### Story E-2.2: Search & Filter Providers  
**As a** customer, **I want** to search for providers by service name or provider name and filter by city/zip and price range, **so that** I find relevant options.  
**Acceptance Criteria:**  
- Given I am logged in, when I enter a search term matching a service name, then I see providers offering that service.  
- When I enter a search term matching a provider name, then I see those providers.  
- When I apply a city/zip filter, only providers in that location are shown.  
- When I apply a price range filter, only services within that price range are shown.  
**Dependencies:** E-1.2, E-1.3 (services exist)  
**Estimate:** M

### Story E-2.3: View Provider Profile & Services  
**As a** customer, **I want** to view a provider’s profile and see their list of services with details (duration, price, description), **so that** I understand what they offer.  
**Acceptance Criteria:**  
- Given I click on a provider, I see business name, logo, address, contact info.  
- I see a list of all services offered by that provider.  
- For each service, I see duration, price, description, and average rating (from E-5).  
**Dependencies:** E-2.2, E-5  
**Estimate:** S

### Story E-2.4: View Technician Profiles  
**As a** customer, **I want** to view technician profiles including name, image, age, gender, short intro, and service-specific ratings, **so that** I can choose a technician I’m comfortable with.  
**Acceptance Criteria:**  
- Given I have selected a service, when I view the list of assigned technicians, I see each technician’s name, image, age, gender, and average rating for that specific service.  
- When I click on a technician, I see their full profile details and all reviews (from E-5).  
**Dependencies:** E-1.4, E-1.5, E-5  
**Estimate:** M

---

## Epic E-3: Booking Flow

**Goal:** Enable customers to submit booking requests, providers to confirm/decline and assign technicians, and handle cancellations automatically or by user.

### Story E-3.1: Select Date/Time & Submit Booking  
**As a** customer, **I want** to choose a date/time for a selected service and technician (or “any available”) and submit a booking request, **so that** the provider can confirm it.  
**Acceptance Criteria:**  
- Given I have selected a service and technician, when I pick a future date/time, then the booking request is created with status “Pending Provider Confirmation”.  
- When I choose “any available technician”, the booking is created without a specific technician.  
- After submission, I see a confirmation page and receive a notification (E-6).  
**Dependencies:** E-2.3, E-2.4  
**Estimate:** S

### Story E-3.2: Provider View & Respond to Booking Requests  
**As a** provider, **I want** to see incoming booking requests with service and customer details, and confirm or decline them, **so that** I manage my availability.  
**Acceptance Criteria:**  
- Given a booking is created, when I open my dashboard, I see it in “Pending” tab with date/time, service, customer name, and technician (if selected).  
- When I confirm a booking that already had a technician, the status changes to “Confirmed”.  
- When I confirm a booking that had “any available”, I must assign a technician from the service’s assigned list before confirming.  
- When I decline a booking, the status changes to “Declined” and the customer is notified; online payments are automatically refunded (E-4).  
**Dependencies:** E-3.1, E-1.5  
**Estimate:** M

### Story E-3.3: Auto-Cancellation of Unconfirmed Bookings  
**As a** platform, **I want** to automatically cancel booking requests that are not confirmed by the provider within a configurable window (e.g., 4 hours), **so that** customers are not left waiting.  
**Acceptance Criteria:**  
- Given a booking status is “Pending Provider Confirmation” and the confirmation window has passed, when the system runs the auto-cancel job, then the booking status changes to “Auto-Cancelled”.  
- If payment was online, a full refund is initiated (E-4).  
- Customer and provider receive notifications.  
- Short‑notice bookings (e.g., within the confirmation window) are handled separately (see Story E-3.4).  
**Dependencies:** E-3.2  
**Estimate:** S

### Story E-3.4: Short-Notice Booking Handling  
**As a** platform, **I want** to treat bookings where the service start time is less than the confirmation window away as requiring confirmation before start, **so that** providers don’t miss last-minute requests.  
**Acceptance Criteria:**  
- Given a booking’s start time is less than the confirmation window from the moment of booking, when the provider does not confirm before the start time, then the booking is auto-cancelled at the start time (with refund).  
- If the provider confirms before the start time, it proceeds normally.  
**Dependencies:** E-3.2, E-3.3  
**Estimate:** S

### Story E-3.5: Customer Self-Service Cancellation  
**As a** customer, **I want** to cancel a confirmed booking up to X hours before the service (e.g., 2 hours) and get a refund minus a fee, **so that** I have flexibility while ensuring providers are compensated for last-minute changes.  
**Acceptance Criteria:**  
- Given a confirmed booking with start time > cancellation deadline (2h), when I cancel, then the booking status becomes “Cancelled by Customer”.  
- For online payments, a refund is processed minus cancellation fee (10% capped).  
- For cash payments, no refund is due, but the cancellation is recorded.  
- If cancellation occurs after the deadline, no refund is given (online) and booking status is “Cancelled by Customer” with full payment kept.  
- Provider is notified.  
**Dependencies:** E-3.2  
**Estimate:** M

---

## Epic E-4: Payment & Payout

**Goal:** Integrate VNPay for online payments, manage funds, calculate commissions, and generate payout files and cash invoices.

### Story E-4.1: VNPay Integration – Online Payment at Booking  
**As a** customer, **I want** to pay for my booking online using VNPay, **so that** I can secure my appointment without cash.  
**Acceptance Criteria:**  
- Given I choose “pay online” at booking, when I submit, I am redirected to VNPay payment page.  
- After successful payment, the booking is created with payment status “Paid”.  
- If payment fails, the booking is not created and I am shown an error.  
- For cash payment, booking is created with payment status “Cash”.  
**Dependencies:** E-3.1  
**Estimate:** L

### Story E-4.2: Refund Processing  
**As a** platform, **I want** to automatically issue refunds via VNPay for eligible cancellations (provider decline, auto-cancellation, customer cancellation with fee), **so that** customers are correctly reimbursed.  
**Acceptance Criteria:**  
- Given a booking is declined by provider or auto-cancelled, when the refund job runs, then a full refund is initiated.  
- Given a customer cancels before deadline, when the refund job runs, then a refund of (amount – cancellation fee) is initiated.  
- Refund status is tracked on the booking.  
- If VNPay refund fails, an admin is alerted.  
**Dependencies:** E-4.1, E-3.5, E-3.3, E-3.4  
**Estimate:** M

### Story E-4.3: Provider Payout Calculation & File Generation  
**As a** platform, **I want** to calculate daily payouts for providers based on completed online bookings (after service completion and feedback timeout) and generate a payout file, **so that** admins can transfer money manually.  
**Acceptance Criteria:**  
- Given a booking is confirmed, service date passed, and either feedback submitted or timeout (3 days) reached, when the daily payout job runs, then the booking amount (minus commission) is added to the provider’s payout batch for that day.  
- The system generates a CSV (or Excel) file listing provider bank details and net amounts.  
- The payout batch is recorded with status “Pending”.  
- Commission rate per provider is configurable (default 15%).  
**Dependencies:** E-4.1, E-5  
**Estimate:** M

### Story E-4.4: Admin Mark Payout as Paid  
**As a** admin, **I want** to upload the manual bank transfer confirmation and mark payouts as paid, **so that** the system reflects completed payments.  
**Acceptance Criteria:**  
- Given a payout batch is pending, when I upload a proof of transfer and mark as paid, then the batch status changes to “Paid”.  
- The provider dashboard shows the payout as paid.  
**Dependencies:** E-4.3  
**Estimate:** S

### Story E-4.5: Cash Booking Commission Invoicing  
**As a** platform, **I want** to generate weekly invoices for cash bookings, listing each booking and the commission due, **so that** providers can pay separately.  
**Acceptance Criteria:**  
- Given a booking with payment method “Cash” is completed, when the weekly invoice job runs, then it is included in the provider’s invoice for that week.  
- The invoice shows total commission due, due date (e.g., 7 days).  
- Provider receives notification and can view invoice in dashboard.  
- Admin can mark invoice as paid, and record payment date.  
**Dependencies:** E-3.5 (booking completion)  
**Estimate:** M

---

## Epic E-5: Feedback & Ratings

**Goal:** Collect and display ratings for technicians and services, and trigger payout after feedback.

### Story E-5.1: Post-Service Feedback Submission  
**As a** customer, **I want** to rate the technician and service (1–5 stars) and leave a comment after my appointment, **so that** future customers can make informed choices.  
**Acceptance Criteria:**  
- Given a booking is confirmed and the service date has passed, when the feedback window opens (e.g., 2 hours after service), I receive a notification to rate.  
- I can submit one rating per booking for both technician and service.  
- After submission, the ratings are saved and aggregated into the technician and service profiles.  
- If I don’t submit within timeout (3 days), the system auto-releases payout and does not display a review.  
**Dependencies:** E-3.2, E-6  
**Estimate:** S

### Story E-5.2: Display Aggregated Ratings  
**As a** customer, **I want** to see average ratings and number of reviews for technicians and services, **so that** I can choose the best option.  
**Acceptance Criteria:**  
- Given a technician has reviews, when I view the technician profile, I see the average rating (rounded to one decimal) and review count.  
- Given a service has reviews, when I view the service details, I see its average rating and review count.  
- When no reviews exist, I see “No reviews yet”.  
**Dependencies:** E-5.1  
**Estimate:** S

### Story E-5.3: Admin Moderation of Reviews  
**As a** admin, **I want** to hide inappropriate reviews, **so that** the platform maintains quality content.  
**Acceptance Criteria:**  
- Given a review is flagged or reported, when I open admin review moderation, I can mark it as hidden.  
- Hidden reviews are not displayed to customers but remain in the database for audit.  
**Dependencies:** E-5.1  
**Estimate:** S

---

## Epic E-6: Notifications

**Goal:** Deliver timely notifications to customers and providers via email, SMS (opt-in), and internal in-app center.

### Story E-6.1: Transactional Email Notifications  
**As a** user (customer or provider), **I want** to receive email notifications for key booking events, **so that** I stay informed.  
**Acceptance Criteria:**  
- Customer receives emails for: booking request submitted, confirmed, declined, auto-cancelled, self-cancellation, reminder (X hours before), feedback request.  
- Provider receives emails for: new booking request, confirmed, declined, customer cancelled, auto-cancelled, payout batch ready, cash invoice ready, invoice overdue.  
- All emails include relevant details and a link to the platform.  
**Dependencies:** All previous epics (events)  
**Estimate:** L (many templates)

### Story E-6.2: SMS Notifications (Opt-in)  
**As a** customer, **I want** to receive SMS for urgent/time-sensitive events (e.g., booking confirmation, cancellation, reminder) if I opt in, **so that** I don’t miss important updates.  
**Acceptance Criteria:**  
- Given I have opted in for SMS, when a trigger event occurs (e.g., booking confirmed), then an SMS is sent to my phone number.  
- SMS content is short and actionable.  
- If SMS fails, the system logs the failure and retries once.  
**Dependencies:** E-6.1  
**Estimate:** M

### Story E-6.3: In-App Notification Center  
**As a** user, **I want** to see all notifications in a central inbox with read/unread status, **so that** I can catch up on messages I might have missed.  
**Acceptance Criteria:**  
- Given I am logged in, when I open notification center, I see a list of all notifications (email, SMS, and internal) with title, content, timestamp, and link to relevant page.  
- Unread notifications are highlighted and have a badge count.  
- When I click a notification, it is marked as read.  
- Notifications older than 30 days are automatically purged.  
**Dependencies:** E-6.1, E-6.2  
**Estimate:** M

---

## Epic E-7: Admin Operations

**Goal:** Provide administrative tools for managing providers, disputes, and system configuration.

### Story E-7.1: Provider Verification Management  
**As a** admin, **I want** to see a list of pending providers, view their documents, and approve/reject, **so that** I control who can list services.  
**Acceptance Criteria:**  
- Admin dashboard shows pending providers with submitted data.  
- Approval sends activation email and makes provider active.  
- Rejection sends email with reason.  
**Dependencies:** E-1.1  
**Estimate:** S

### Story E-7.2: Dispute & Refund Handling  
**As a** admin, **I want** to view disputed bookings and manually initiate refunds via VNPay, **so that** I can resolve customer issues.  
**Acceptance Criteria:**  
- Given a booking with online payment, when I open admin dispute panel, I can see booking details and initiate a full or partial refund.  
- Refund is processed through VNPay and status updated.  
- A reason field is captured for audit.  
**Dependencies:** E-4.1, E-4.2  
**Estimate:** M

### Story E-7.3: System Configuration  
**As a** admin, **I want** to configure system parameters (confirmation window, feedback timeout, cancellation deadline, cancellation fee cap, commission rates per provider), **so that** I can adapt business rules without code changes.  
**Acceptance Criteria:**  
- Admin can set global values for confirmation window (hours), feedback timeout (days), cancellation deadline (hours before service), cancellation fee (percentage, cap amount).  
- Admin can override commission rate per provider (default 15%).  
- Changes take effect for new bookings only (or with clear communication).  
**Dependencies:** None  
**Estimate:** M

---

## Dependencies & Ordering for MVP

The following order is recommended to deliver a working MVP incrementally:

1. **E-7.3 (System Configuration)** – foundational to set rules.
2. **E-1 (Provider Onboarding & Service Catalog)** – must be complete before any booking can happen.
3. **E-2 (Customer Registration & Discovery)** – allows customers to find providers.
4. **E-3 (Booking Flow)** – core transaction flow; can be tested with manual payment handling initially.
5. **E-4 (Payment & Payout)** – online payments and commission logic; can be integrated after booking flow is stable.
6. **E-5 (Feedback & Ratings)** – depends on completed bookings.
7. **E-6 (Notifications)** – can be added in parallel but essential for user experience; prioritize transactional emails early.
8. **E-7.1 & E-7.2 (Admin)** – necessary for provider verification and dispute resolution; can be developed alongside other epics.

**MVP Scope :  
- All **Must Have** items from the document are covered in the epics above.  
- The MVP should include:  
  - Provider onboarding (with manual admin verification)  
  - Customer registration and basic search (city/zip, service name)  
  - Booking with manual provider confirmation and “any available” assignment  
  - Online payment (VNPay) and cash payment  
  - Basic notifications (email for critical events)  
  - Feedback submission and display of aggregated ratings  
  - Admin tools for verification and payout file generation  

Stories marked with **Should Have** (e.g., advanced search filters, service-level average ratings) can be added after MVP or as part of subsequent sprints.

---
## Estimation

- **Estimate sizes:** S = Small (1–3 days), M = Medium (3–8 days), L = Large (8–12 days).
- **Dependencies** are listed by story ID; stories within the same epic may depend on earlier stories in that epic.
- **E-6.1** (Transactional Email Notifications) depends on events from all other epics, so it should be developed after the core flows are stable, or built incrementally.
## Next Steps & Open Questions

Before development, please finalize the **pending decisions** listed in the document:
- Launch geography (assumed: one city in Vietnam)  
- Commission rate (default 15%, configurable)  
- Cancellation deadlines (e.g., 2 hours)  
- Cash invoice due date (e.g., 7 days)  
- SMS provider  
- Reminder lead time (e.g., 2 hours)  

Once these are confirmed, the user stories above can be refined with specific values in the acceptance criteria.

Would you like me to adjust any epic or story based on your priorities, or would you like to proceed with this decomposition?