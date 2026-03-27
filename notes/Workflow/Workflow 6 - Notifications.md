## Actors
- **Customer** – Receives notifications about booking status, reminders, feedback requests, and cancellations.
- **Provider** – Receives notifications about new booking requests, confirmations, cancellations, and payout/invoice reminders.
- **Platform** – Triggers notifications based on system events.

---

## 1. Notification Channels

| Channel | Purpose | Implementation |
|---------|---------|----------------|
| **Email** | All non-urgent notifications (booking request, confirmation, reminders, feedback, payouts) | Send via SMTP or email service (e.g., SendGrid). Required for MVP. |
| **SMS** | Urgent/time‑sensitive notifications (booking request, confirmation, reminder, auto‑cancellation) | SMS gateway (e.g., Twilio, local provider). Required for MVP. |
| **Internal (In‑App)** | All notifications visible within the platform dashboard (web). Users can view a history of notifications. | Store notifications in database; display in a bell icon / notification center. Required for MVP. |

*Note:* Internal notifications are mandatory for both customers and providers. They serve as a fallback if email/SMS fail and provide a central place to review past notifications.

---

## 2. Notification Triggers & Templates (Updated)

For each trigger, we now have **internal notification** content in addition to email/SMS.

#### 2.1 Customer Notifications

| Trigger | Internal Notification Content |
|---------|-------------------------------|
| **Booking request submitted** | “Your booking request for [service] on [date] at [time] with [provider] has been sent. Awaiting provider confirmation.” |
| **Booking confirmed** | “Great news! Your booking for [service] on [date] at [time] with [technician] has been confirmed.” |
| **Booking declined** | “Your booking request for [service] on [date] at [time] was declined by the provider. Reason: [reason]. A full refund has been issued.” |
| **Booking auto‑cancelled** | “Your booking request for [service] on [date] at [time] was automatically cancelled because the provider did not respond in time. A full refund has been issued.” |
| **Self‑service cancellation** | “Your booking for [service] on [date] at [time] has been cancelled. A refund of [amount] has been processed (cancellation fee [fee] applied).” |
| **Reminder** | “Reminder: You have a massage appointment with [technician] at [provider] on [date] at [time].” |
| **Feedback request** | “How was your massage with [technician]? Rate your experience and help others choose.” (with link) |
| **Admin refund** | “A refund of [amount] has been processed for your booking on [date]. Contact support if you have questions.” |

#### 2.2 Provider Notifications

| Trigger | Internal Notification Content |
|---------|-------------------------------|
| **New booking request** | “New booking request from [customer] for [service] on [date] at [time]. Please confirm or decline within [X] hours.” |
| **Booking confirmed** (by provider) | “You confirmed booking #[id] for [customer] on [date] at [time]. Technician: [technician].” |
| **Booking declined** (by provider) | “You declined booking #[id] for [customer] on [date] at [time].” |
| **Customer cancelled** | “Customer [customer] cancelled booking #[id] for [service] on [date] at [time]. Cancellation fee applied: [fee].” |
| **Auto‑cancelled** | “Booking #[id] was automatically cancelled because you did not respond within [X] hours.” |
| **Payout batch ready** | “Your payout batch for [date] is ready. Total payout: [amount] VND. Download file from dashboard.” |
| **Cash invoice ready** | “Your cash booking commission invoice for [period] is ready. Total commission due: [amount] VND. Please pay by [due date].” |
| **Cash invoice overdue** | “Your cash commission invoice is overdue. Please pay by [date] to avoid service suspension.” |

---

## 3. Internal Notification Features

| Feature | Description |
|---------|-------------|
| **Notification Center** | A dedicated page (or dropdown) showing all notifications for the user, sorted by newest first. |
| **Unread indicator** | A badge on the bell icon showing number of unread notifications. |
| **Read/unread status** | Notifications can be marked as read manually or automatically when viewed. |
| **Click‑through** | Notifications related to a booking should link to the booking details page. |
| **Retention** | Notifications stored for at least 30 days (configurable) for user reference. |

---

## 4. Opt‑Outs & Preferences (Updated)

- **Email:** Mandatory for transactional messages. Users cannot opt out of critical transactional emails (booking request, confirmation, cancellation, refund). Marketing emails optional.
- **SMS:** Opt‑in required for both customers and providers. Can be toggled in profile settings.
- **Internal:** Always on; cannot be disabled.

---

## 5. Notification Failure Handling (Updated)

| Scenario | Handling |
|----------|----------|
| Email bounce | Log error; admin can review. No retry for MVP (simple logging). |
| SMS delivery failure | Log error; no automatic retry. Admin can manually resend if needed. |
| Internal notification | Always stored; no failure scenario. |

---

## 6. Open Questions (for refinement)

1. **SMS provider:** Which local SMS gateway will you use for Vietnam?  
2. **Reminder lead time:** Should it be configurable per provider? MVP: single global setting.  
3. **Cash invoice due date:** How many days after invoice generation should provider pay? (e.g., 7 days)  
4. **Notification retention period:** How long to keep internal notifications? (e.g., 30 days)

---

*Workflow 6 updated: 2026-03-27*

---

All six workflows now include internal notifications as a channel. Do you want me to now **consolidate all six workflows into a single comprehensive requirements document** (BRD/FRD) that you can hand off to developers?