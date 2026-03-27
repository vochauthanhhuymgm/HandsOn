## Actors

- **Customer** – Pays for booking (online or cash).
    
- **Platform** – Collects online payments, holds funds, processes payouts, invoices cash bookings.
    
- **Provider** – Receives payout for online bookings; pays commission on cash bookings.
    
- **Payment Gateway (VNPay)** – Processes customer online payments and refunds.
    

---

## Current Workflow (No Platform)

- Customer pays provider directly (cash, bank transfer, etc.).
    
- No commission collection or automated payouts.
    

---

## Key Decisions (Confirmed)

|Decision|Implementation|
|---|---|
|**Payment gateway**|VNPay (Vietnam).|
|**Cash payment**|Allowed. Customer can choose to pay cash to provider at time of service.|
|**Commission rate**|Configurable per provider; default 15%.|
|**Payout frequency**|Daily batch for online bookings.|
|**Payout method**|Generate file for manual bank transfer (admin processes).|
|**Currency**|VND (all amounts).|
|**Chargebacks after payout**|Platform absorbs; no clawback from provider.|

---

## Workflow Steps

### 1. Customer Chooses Payment Method at Booking

|Step|Action|System Behavior|
|---|---|---|
|1.1|After selecting service and technician, customer clicks **“Book Now”**.|System displays booking summary with total price.|
|1.2|Customer selects payment method: **Pay Online (VNPay)** or **Pay Cash to Provider**.|System records payment method.|
|1.3|If **Pay Online**:|System redirects to VNPay for payment. On success, booking status = **Pending Provider Confirmation**. Payment captured and held by platform.|
|1.4|If **Pay Cash**:|Booking status immediately = **Pending Provider Confirmation** (no payment collected). System notifies provider of booking with “Cash on delivery” flag.|

---

### 2. Handling Cash Payments

- **Customer pays provider in cash** at the time of service (or before).
    
- **Platform commission** is still due from the provider on cash bookings.
    
- **Commission collection for cash bookings:**
    
    - At the end of each week (or configurable interval), the platform generates an **invoice** for each provider listing all cash bookings completed in that period.
        
    - Provider pays the commission amount to the platform via bank transfer.
        
    - Alternatively, commission can be deducted from the provider’s future online payouts if they have a balance.
        

For MVP simplicity, we will:

- Track cash bookings separately.
    
- At weekly interval, admin generates a statement for each provider showing cash bookings and commission owed.
    
- Provider pays platform via bank transfer (manual process).
    
- Admin marks invoices as paid in the system.
    

---

### 3. Payment & Booking Statuses

|Status|Description|
|---|---|
|**Pending Provider Confirmation**|Booking created, payment either captured (online) or promised (cash).|
|**Confirmed**|Provider accepted. For cash bookings, commission still owed.|
|**Completed**|Service delivered. For online, payout eligible after feedback timeout. For cash, commission becomes due.|
|**Cancelled**|Various cancellation scenarios (refunds for online; for cash, no refund but commission not owed).|

---

### 4. Refunds (Online Payments Only)

Refunds apply only to online payments. Cash payments are not refunded via platform.

|Scenario|Handling|
|---|---|
|Provider declines booking|Full refund to customer via VNPay.|
|Provider does not respond within confirmation window|Auto‑cancel, full refund via VNPay.|
|Customer cancels (self‑service) within allowed period|Partial refund (full amount minus cancellation fee) via VNPay. Fee retained by platform.|
|Customer cancels outside allowed period|No refund; payment retained.|
|Admin‑initiated refund (dispute)|Admin can trigger full or partial refund via VNPay.|

_Cancellation fee:_ 10% of booking value, capped at $15 (or VND equivalent), retained by platform.

---

### 5. Payout to Provider (Online Bookings)

Payouts are processed **daily** for online bookings that are completed and have met the feedback timeout (3 days after service, configurable).

**Payout Process:**

1. Daily batch job identifies all online bookings where:
    
    - Service date/time passed.
        
    - Feedback timeout elapsed (or feedback submitted).
        
    - Payout status = **Not Paid**.
        
2. For each provider, sum eligible earnings.
    
3. Payout amount = Booking value – Platform commission (commission rate configurable per provider, default 15%).
    
4. System creates a payout record and generates a **bank transfer file** (CSV/Excel) with provider bank details and payout amount.
    
5. Admin manually processes the bank transfer using the file.
    
6. Admin marks payout as paid in the system. Provider dashboard updates.
    

**Payout Eligibility Conditions:**

- Booking must be **Completed** (service delivered, no dispute).
    
- Feedback timeout reached or feedback submitted.
    
- No refund or cancellation.
    

---

### 6. Commission Collection (Cash Bookings)

For cash bookings, commission is collected separately.

**Process:**

1. Weekly batch job (or on‑demand) generates a **commission invoice** for each provider, listing all cash bookings completed in the past week.
    
2. Invoice includes: booking IDs, service dates, customer names, amounts, and total commission due.
    
3. Admin sends invoice to provider (email or dashboard notification).
    
4. Provider pays commission via bank transfer to platform.
    
5. Admin marks invoice as paid; provider dashboard shows commission history.
    

_Note:_ If a provider has both online and cash bookings, the commission on cash bookings can be deducted from their online payout balance if preferred. For MVP simplicity, separate invoicing is acceptable.

---

### 7. Reconciliation & Reporting

|Report|Purpose|
|---|---|
|Transaction log|All payments, refunds, and payouts with timestamps and statuses.|
|Provider payout summary|Per provider: online bookings, commission, net payout.|
|Provider cash invoice summary|Per provider: cash bookings, commission owed, payment status.|
|Platform revenue report|Total commission collected (online + cash), cancellation fees retained, refunds issued.|

---

## Edge Cases & Considerations

|Scenario|Handling|
|---|---|
|Cash booking – customer doesn’t pay at service|Provider marks “not paid” in dashboard; platform may charge commission? Admin handles case‑by‑case.|
|Cash booking – provider doesn’t pay commission|Admin follows up; provider may be suspended.|
|Provider bank details missing/invalid for payout|Payout file generated but payout not executed; admin alerts provider to update details.|
|VNPay refund API failure|Log error; admin retries manually.|
|Booking with cash payment is cancelled|No refund needed; no commission due.|

---

## Open Questions

1. **Cash booking commission collection frequency:** Weekly invoice is recommended. Confirm.
    
2. **What if provider fails to pay commission on cash bookings?** Should platform suspend the provider until payment is made? (Recommend: yes, after a grace period.)
    
3. **Cash payment confirmation:** Should the provider be able to confirm in the dashboard that cash was received? (This could trigger commission invoicing.)
    
4. **Cash vs online availability:** Will all bookings offer both options, or can providers choose to accept only online payments?
    

---

*Workflow documented: 2026-03-27*