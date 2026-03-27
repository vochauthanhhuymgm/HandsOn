## Actors

- **Customer** – Leaves feedback after service.
    
- **Platform** – Manages review submission, aggregation, and display.
    
- **Provider** – Views feedback on technicians and services (read‑only).
    
- **Technician** – Indirectly receives ratings (visible on profile).
    

---

## Current Workflow (No Platform)

- No structured feedback.
    
- Customers rely on word‑of‑mouth or informal reviews.
    

---

## MVP Workflow Steps

### 1. Post‑Service Trigger

|Step|Action|System Behavior|
|---|---|---|
|1.1|After the scheduled service time, system waits a **configurable buffer** (default 2 hours).|System marks booking as **Eligible for Feedback**.|
|1.2|System sends notification to customer (email and/or SMS): “How was your massage with [technician]? Rate your experience.”|Notification includes a direct link to the feedback form.|
|1.3|Customer can also access feedback from **“My Bookings”** page at any time after the service.|Feedback link remains available until feedback is submitted or timeout expires.|

---

### 2. Feedback Submission

|Step|Action|System Behavior|
|---|---|---|
|2.1|Customer navigates to feedback form (via link or booking history).|System displays the booking details (provider, service, technician, date).|
|2.2|Customer rates **technician** (1–5 stars) and optionally writes a comment.|System captures technician rating and comment.|
|2.3|Customer rates **service** (1–5 stars) and optionally writes a comment.|System captures service rating and comment.|
|2.4|Customer submits feedback.|System stores reviews; updates average ratings for the technician and service.|
|2.5|System sends confirmation to customer (optional).|“Thank you for your feedback!”|

_Note:_ Both technician and service ratings are mandatory for MVP? (Recommend: at least star rating required; comments optional to reduce friction.)

---

### 3. Feedback Timeout & Payout Release

|Step|Action|System Behavior|
|---|---|---|
|3.1|If customer does not submit feedback within the **configurable timeout** (default 3 days after service), the system **auto‑releases** the payout for online bookings.|Booking is marked as **Feedback Timeout**. Payout is triggered (for online bookings).|
|3.2|Customer can still submit feedback after timeout, but it will not delay payout.|Reviews can be added later; ratings update aggregates.|
|3.3|If customer submits feedback within the timeout, payout is triggered immediately after submission.|Booking status: **Feedback Submitted**.|

---

### 4. Ratings Aggregation & Display

**Technician Ratings:**

- Average rating = sum of all technician ratings / number of ratings.
    
- Displayed on technician profile, in technician selection list, and in search results (if applicable).
    
- Rating breakdown: show star distribution? (MVP: just average with star icon and count.)
    

**Service Ratings:**

- Average rating = sum of all service ratings / number of ratings.
    
- Displayed on service card within provider profile.
    
- Helps customers compare services within same provider.
    

**Provider‑Level Rating:**

- Not explicitly required for MVP, but can be derived as average of all service ratings (or technician ratings) for the provider. Optional for future.
    

---

### 5. Review Moderation (MVP)

|Scenario|Handling|
|---|---|
|Customer leaves inappropriate content (profanity, spam)|Admin can **hide** review from public view. Review remains in system for record.|
|Provider disputes a review|Admin reviews; can remove or keep based on policy. No automated dispute flow in MVP.|

---

### 6. Impact on Discovery (Future State)

For MVP, ratings do not influence search ranking (e.g., highest-rated first). That is a **Should Have** for post‑MVP.

---

## Edge Cases & Considerations

|Scenario|Handling|
|---|---|
|Customer never receives feedback notification|They can still leave feedback via booking history.|
|Customer submits feedback multiple times|Only first submission counted; subsequent edits not allowed (or allow edit within 24h? MVP: no edits).|
|Booking was cancelled or declined|No feedback allowed.|
|Multiple services booked in one booking?|MVP assumes one service per booking. Future: separate ratings per service if bundled.|
|Customer rates technician 1 star but service 5 stars|Both are stored; no conflict.|
|Technician leaves provider|Past ratings remain visible on technician profile (archived).|

---

## Key Decisions Incorporated

|Decision|Implementation|
|---|---|
|**Ratings targets**|Both technician and service.|
|**Feedback timeout**|Configurable, default 3 days.|
|**Payout trigger**|After feedback or timeout.|
|**Rating display**|Average with star icons and count.|
|**Moderation**|Admin can hide inappropriate reviews.|
|**Edits**|Not allowed in MVP (one submission only).|

---

## Open Questions

1. **Mandatory comment:** Should comments be required for ratings below a certain threshold (e.g., 3 stars) to encourage constructive feedback? (Recommend: not required for MVP, but can be added later.)
    
2. **Provider reply to reviews:** Should providers be able to respond to customer reviews? (Post‑MVP feature.)
    
3. **Rating weighting:** Should newer ratings have more weight? (Not for MVP.)
    
4. **Anonymous reviews:** Allow anonymous? (MVP: reviews tied to booking, but name may be hidden. Probably show “Verified Customer” rather than full name for privacy.)
    

---

*Workflow documented: 2026-03-27*