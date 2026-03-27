## Actors

- **Customer** – End user looking to book a massage.
    
- **Platform** – The system facilitating discovery.
    

---

## Current Workflow (No Platform)

- Customer discovers providers via word-of-mouth, social media, or walk‑in.
    
- No centralized discovery, service catalog, or technician reviews.
    

---

## MVP Workflow Steps

### 1. Customer Registration

|Step|Action|System Behavior|
|---|---|---|
|1.1|Customer visits platform homepage.|System displays options: “Browse Services” or “Sign Up / Log In”.|
|1.2|Customer clicks **“Sign Up”**.|System displays registration form: name, email/phone, password.|
|1.3|Customer enters details and submits.|System validates; sends verification code (email or SMS).|
|1.4|Customer enters verification code.|System activates account, logs customer in, redirects to homepage.|

_Note:_ Browsing is allowed without login, but booking and feedback require login.

---

### 2. Service Discovery & Search

|Step|Action|System Behavior|
|---|---|---|
|2.1|Customer lands on homepage.|System displays search bar and optionally popular service types.|
|2.2|Customer enters search term (e.g., “Thai massage”, “Spa name”).|System returns list of providers whose **service names** or **provider business names** match the term.|
|2.3|Customer applies **manual location input** (city or zip code).|System filters providers by address field (city/zip).|
|2.4|Customer applies **price range** filter (optional).|System filters services within that price range.|
|2.5|Customer views search results.|Each result shows provider name, location, average rating, and a few service names.|

---

### 3. View Provider Profile & Services

|Step|Action|System Behavior|
|---|---|---|
|3.1|Customer clicks on a provider.|System displays provider profile: business info, logo, location, map (optional), and a list of all services offered.|
|3.2|Customer browses services.|Each service shows name, description, duration, price, and average rating (if any).|
|3.3|Customer selects a service.|System expands service detail and shows a list of technicians who can perform this service.|

---

### 4. Technician Selection with Filters

|Step|Action|System Behavior|
|---|---|---|
|4.1|On the technician selection screen, customer sees filter options: **gender** (male/female/any) and **age range** (optional, e.g., 20–30, 30–40, 40+).|System displays only technicians matching selected gender and age range.|
|4.2|Customer can also view technician profiles.|Each technician shows: name, image, age, gender, short introduction, average rating.|
|4.3|Customer may choose **“Any available technician”**.|System notes that provider will assign a technician at confirmation.|
|4.4|Customer selects a specific technician (or chooses “any available”).|System proceeds to booking flow.|

---

### 5. Post-Booking Visibility (for “Any Available”)

- After the provider confirms the booking, the customer will see **which technician was assigned** in the booking details (to ensure transparency).
    

---

## Edge Cases & Considerations

|Scenario|Handling|
|---|---|
|No providers in customer’s area|Display friendly message: “No providers found in your area yet. Check back soon!”|
|Search yields no results|Show “No services match your search. Try different keywords or browse all providers.”|
|Technician has no ratings|Display “No reviews yet” to encourage first review.|
|Customer wants gender or age filter|Filters appear on technician selection screen, not on initial search.|
|Customer chooses “Any available technician”|Booking request sent without technician ID; provider will assign during confirmation. Customer sees assigned technician after confirmation.|

---

## Key Decisions Incorporated

|Decision|Implementation|
|---|---|
|**Search scope**|Search by service name and provider business name.|
|**Geolocation**|Manual input (city / zip code).|
|**Technician gender filter**|Yes – displayed after customer selects a service.|
|**Technician age filter**|Yes – displayed after customer selects a service.|
|**“Any available technician” option**|Yes – with visibility of assigned technician after confirmation.|
|**Service images**|Not included in MVP.|

---

*Workflow documented: 2026-03-27*