## Actors
- **Customer** – Requests a booking.
- **Provider** – Confirms or declines the request.
- **Platform** – Manages booking state, payments, notifications, and self‑service cancellations.

---

## Current Workflow (No Platform)
- Customer calls or walks in to book.
- Provider manually notes appointment details.
- No online payment or automated confirmation.

---

## MVP Workflow Steps

### 1. Customer Submits Booking Request

| Step | Action | System Behavior |
|------|--------|-----------------|
| 1.1 | After selecting service and technician (or “any available”), customer clicks **“Book Now”**. | System displays booking summary: provider name, service, technician (if selected), date/time picker, total price. |
| 1.2 | Customer selects desired date and time (date picker, time slot). | System validates that the date/time is not in the past. |
| 1.3 | Customer may add special instructions (optional). | System captures notes for provider. |
| 1.4 | Customer clicks **“Proceed to Payment”**. | System redirects to payment gateway (e.g., Stripe). |
| 1.5 | Customer completes payment. | System captures payment, creates booking record with status **Pending Provider Confirmation**. |
| 1.6 | System sends notifications: | - Customer: “Your booking request has been sent. Awaiting provider confirmation.”<br>- Provider: “New booking request received. Please confirm or decline within **X hours**.” (X is configurable, e.g., 4 or 8 hours) |

---

### 2. Provider Reviews & Responds to Booking Request

| Step | Action | System Behavior |
|------|--------|-----------------|
| 2.1 | Provider logs in and navigates to **“Booking Requests”** (dashboard). | System displays list of pending requests with customer name, service, requested time, technician (if selected), and any special instructions. |
| 2.2 | Provider clicks on a request to view details. | System shows full details. |
| 2.3 | Provider chooses: **Confirm** or **Decline**. | |
| 2.4 | If confirming: | - System updates booking status to **Confirmed**.<br>- If “any available technician” was selected, provider must select a technician before confirming.<br>- System sends confirmation notification to customer (with technician name if newly assigned).<br>- System adds booking to provider’s calendar. |
| 2.5 | If declining: | Provider may add a reason (optional). System updates booking status to **Declined**. System initiates **full refund** to customer’s original payment method. System sends decline notification to customer with reason. |

---

### 3. Auto‑Cancellation Handling

- **Confirmation window** (configurable, e.g., 4 or 8 hours):
  - If provider does not respond within the configured window, the booking is **automatically cancelled and fully refunded**.
- **Special case:** If the customer books a time slot that is **less than the confirmation window** away from the booking time (e.g., booking at 2pm for a 3pm service with a 4‑hour window), the system treats the window as the time remaining until the service start. If provider does not confirm before the service start, the booking auto‑cancels at service start.

| Scenario | Handling |
|----------|----------|
| Provider responds within window | Proceed to confirmation/decline. |
| Provider does not respond within window | Auto‑cancel, full refund, notify both parties. |
| Service start is less than window away | Auto‑cancel if no response by service start. |

*Configuration:* Both the default window (e.g., 4h, 8h) and the behavior for short‑notice bookings are admin‑configurable.

---

### 4. Post‑Confirmation Actions

| Step | Action | System Behavior |
|------|--------|-----------------|
| 4.1 | Booking is confirmed. | System holds customer’s payment (no payout yet). |
| 4.2 | Provider prepares for service. | (Offline) |
| 4.3 | Customer may cancel the booking **self‑service** according to cancellation policy. | See Section 5 below. |
| 4.4 | Service is delivered. | |

---

### 5. Self‑Service Cancellation (Post‑Confirmation)

To reduce support burden, customers can cancel a confirmed booking themselves up to a defined time before the service start.

**Cancellation Policy (MVP):**
- **Cancellation fee:** A configurable fee applies (e.g., 10% of booking value, capped at a maximum amount). The fee is deducted from the refund.
- **Cancellation window:** Customer can cancel **up to X hours before service start** (configurable, e.g., 2 hours) without additional penalty beyond the fee.
- **If cancelled within the final hours** (e.g., less than 2 hours before service), no refund is issued (or full charge kept). This is configurable.

| Step | Action | System Behavior |
|------|--------|-----------------|
| 5.1 | Customer goes to “My Bookings” and selects a confirmed booking. | System displays cancellation option with policy summary (fee, deadline). |
| 5.2 | Customer initiates cancellation. | System checks timing relative to service start and applies policy. |
| 5.3 | If within allowed cancellation period: | System processes partial refund (full amount minus fee). Updates booking status to **Cancelled by Customer**. Notifies provider. |
| 5.4 | If outside allowed period (too close to service start): | System shows message: “Cancellation no longer possible. Please contact support.” |
| 5.5 | Provider is notified of cancellation. | Provider sees cancelled booking in dashboard. |

*Note:* For MVP, cancellation fees are retained by the platform as revenue (or could be passed to provider depending on business model – to be decided).

---

### 6. Post‑Service & Feedback

| Step | Action | System Behavior |
|------|--------|-----------------|
| 6.1 | After the scheduled service time, system waits a **configurable buffer** (default 2 hours) then prompts customer to leave feedback. | Customer receives email/SMS: “How was your massage with [technician]?” |
| 6.2 | Customer leaves rating (1–5 stars) and comment for both **technician** and **service**. | System stores reviews; updates average ratings on technician and service profiles. |
| 6.3 | **Feedback timeout:** If customer does not leave feedback within **3 days** (configurable) after the service, the system automatically releases the payout to the provider. | Payment is transferred to provider (minus commission). |

---

### 7. Payout to Provider

- Payout is triggered either:
  - Immediately after customer submits feedback (if feedback is left within timeout), or
  - Automatically after the feedback timeout period (default 3 days) if no feedback is left.
- Payout amount = booking value – platform commission.

---

## Edge Cases & Considerations

| Scenario | Handling |
|----------|----------|
| Provider does not respond within confirmation window | Auto‑cancel, full refund. |
| Customer books with less than window to service start | Auto‑cancel if provider does not respond before service start. |
| Provider declines after payment | Full refund automatically. |
| Payment fails | Booking not created; customer sees error. |
| “Any available technician” assignment | Provider must assign technician before confirming; system blocks confirmation without assignment. |
| Duplicate booking attempts | System prevents double‑booking same technician at overlapping times (basic check). |
| Cancellation fee retention | For MVP, cancellation fees are kept by platform (simpler accounting). Future version could share with provider. |
| Customer cancels after provider has already begun service | Not allowed; customer must contact support. |

---

## Key Decisions Incorporated

| Decision | Implementation |
|----------|----------------|
| **Confirmation window** | Configurable (4h or 8h). Special handling for short‑notice bookings. |
| **Feedback timeout** | Configurable, default 3 days. |
| **Post‑confirmation cancellation** | Self‑service with fee. Fee structure configurable (e.g., 10% capped). Cancellation deadline configurable. |
| **Payout trigger** | After feedback or after timeout. |
| **Auto‑cancel/refund** | For non‑response or provider decline. |

---

## Open Questions (to finalize)

1. **Cancellation fee specifics:**  
   - Fixed percentage (e.g., 10%) or fixed amount?  
   - Cap (e.g., max $20)?  
   - Who keeps the fee – platform, provider, or split?  
   *Recommendation for MVP: 10% fee, capped at $15, kept by platform to cover payment processing and support costs.*

2. **Cancellation deadline:**  
   - How many hours before service start can customer cancel with fee? (e.g., 2 hours)  
   - If within that window, is there any refund? (e.g., no refund)  

3. **Short‑notice booking behavior:**  
   - If customer books within the confirmation window (e.g., 2 hours before service), should the provider confirmation window be shortened to the time until service start, or remain at full window? We’ll implement as described.

---

*Workflow documented: 2026-03-27*