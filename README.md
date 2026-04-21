
# Restful Booker — API Test Suite

A complete Postman test collection for the [Restful Booker API](https://restful-booker.herokuapp.com), covering authentication, full CRUD operations, edge cases, and security checks.


---

## What's covered

| Category | Tests | Description |
|---|---|---|
| Authentication | 3 | Valid login, invalid credentials, missing fields |
| Read (GET) | 6 | All bookings, filters, single booking, invalid IDs |
| Create (POST) | 5 | Valid payload, missing fields, edge cases, XSS check |
| Update (PUT/PATCH) | 4 | Full update, partial update, auth enforcement |
| Delete (DELETE) | 3 | Valid delete, no auth, idempotency check |
| Health Check | 1 | API uptime ping |
| **Total** | **22** | |

---

## Test categories explained

### Functional tests
Verify that each endpoint returns the correct status code, response structure, and data when given valid input.

### edge case tests
Probe boundary conditions that may not be obvious — negative prices, checkout dates before checkin dates, and missing required fields. These often reveal validation gaps that only appear in real user behaviour.

###  Auth enforcement tests
Every protected endpoint (PUT, PATCH, DELETE) is tested both with and without a valid auth token to confirm that unauthenticated requests are correctly rejected with 403.

###  Security spot checks
Basic XSS payload injected into string fields to verify the API sanitises input and does not reflect raw script tags in responses.

---

## Findings & observations

| # | Type | Endpoint | Observation |
|---|---|---|---|
| 1 | Bug candidate | POST /booking | API accepted a negative `totalprice` value without error |
| 2 | Bug candidate | POST /booking | API accepted a `checkout` date earlier than `checkin` date |
| 3 | API quirk | POST /booking | Returns 200 (not 201) on successful creation — inconsistent with REST conventions |
| 4 | API quirk | DELETE /booking | Returns 201 Created on successful deletion — unexpected status code |
| 5 | API quirk | POST /auth | Returns 200 (not 401) for invalid credentials — error is in the body, not the status code |

> These observations are documented as part of the QA process. In a real project, items 1 and 2 would be filed as bugs with severity ratings. Items 3–5 would be flagged as design inconsistencies for the development team to decide on.

---

## How to run this collection

### Prerequisites
- [Postman](https://www.postman.com/downloads/) (desktop or web)

### Steps

1. Clone or download this repository
2. Open Postman → click **Import** → select `restful_booker_collection.json`
3. Open the collection → go to **Variables** and confirm `base_url` is set to:
   ```
   https://restful-booker.herokuapp.com
   ```
4. Run the **Auth → Create Token (valid credentials)** request first — this automatically saves the token to the collection variable
5. Run the **Bookings — Create → Create Booking (valid full payload)** request next — this saves a `booking_id` for use in update and delete tests
6. Run remaining requests in order, or use the **Collection Runner** to run all tests at once

### Running with Collection Runner
1. Right-click the collection → **Run collection**
2. Set iteration count to 1
3. Click **Run** — you'll see pass/fail results for all 22 tests

---

## Project structure

```
restful-booker-api-tests/
├── restful_booker_collection.json   # Postman collection (import this)
└── README.md                        # This file
```

---

## About

**Author:** Tiraj Basnet  
**Role:** AI & SaaS QA Engineer  
**Tools:** Postman, JavaScript (pm.test assertions)  
**API under test:** [Restful Booker](https://restful-booker.herokuapp.com) by Mark Winteringham  

This project is part of a QA portfolio demonstrating API testing methodology for SaaS and web application projects.