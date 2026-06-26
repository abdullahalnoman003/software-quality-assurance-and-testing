
# Section 7: Manual API Testing

## Table of Contents

### Topic 1: API Fundamentals & Technologies
  * [What is an API?](#what-is-an-api)
  * [How APIs Work](#how-apis-work)
  * [HTTP Protocol & Methods](#http-protocol--methods)
  * [HTTP Status Codes](#http-status-codes)
  * [API Request Components](#api-request-components)
  * [API Response Structure](#api-response-structure)
  * [JSON Data Format](#json-data-format)
  * [Database Interaction & Schema](#database-interaction--schema)

### Topic 2: Hands-On Postman Testing
  * [Introduction to Postman](#introduction-to-postman)
  * [Setting Up Postman](#setting-up-postman)
  * [Testing HTTP Methods in Postman](#testing-http-methods-in-postman)
  * [Using Parameters & Headers](#using-parameters--headers)
  * [Postman Variables & Environments](#postman-variables--environments)
  * [Writing Automated Test Scripts](#writing-automated-test-scripts)
  * [Practical End-to-End Testing Scenarios](#practical-end-to-end-testing-scenarios)

* [Key Takeaways](#key-takeaways)

---

## Topic 1: API Fundamentals & Technologies

### What is an API?

#### Definition & Core Concept

**API (Application Programming Interface):** A formal contract — a defined set of rules and protocols — that allows two separate software applications to communicate and exchange data with each other reliably.

> [!NOTE]
> Think of an API as a **standardized communication channel**. Just like a socket defines how two plugs connect, an API defines exactly how two systems can talk — what requests are valid, what format data must be in, and what responses to expect.

#### Real-World Analogy: The Restaurant Model

The restaurant analogy is the most intuitive way to understand the client-server API model:

```text
  ┌─────────────────────────────────────────────────────────────────────┐
  │                       RESTAURANT MODEL                              │
  ├─────────────────────────────────────────────────────────────────────┤
  │                                                                     │
  │   CUSTOMER (Client App)                                             │
  │       │                                                             │
  │       │  "I'd like the grilled salmon, please."                     │
  │       │  (API Request — structured, standard format)                │
  │       ▼                                                             │
  │   WAITER (API Layer / Endpoint)                                     │
  │       │                                                             │
  │       │  Passes the order to the kitchen                            │
  │       │  (Routes request to the correct backend service)            │
  │       ▼                                                             │
  │   KITCHEN (Backend Server / Database)                               │
  │       │                                                             │
  │       │  Prepares the dish according to the order                   │
  │       │  (Processes business logic, queries database)               │
  │       ▼                                                             │
  │   WAITER (API Layer)                                                │
  │       │                                                             │
  │       │  Delivers the finished plate to the customer                │
  │       │  (API Response — structured, standard format)               │
  │       ▼                                                             │
  │   CUSTOMER (Client App)                                             │
  │       Receives exactly what was ordered                             │
  │                                                                     │
  └─────────────────────────────────────────────────────────────────────┘
```

#### Why APIs Are the Backbone of Modern Software

A single production application is not a monolith — it is an ecosystem of interconnected APIs working in concert.

```text
  E-Commerce Platform — API Dependency Map

  ┌───────────────┐     ┌───────────────┐     ┌───────────────┐
  │  User API     │     │  Product API  │     │  Cart API     │
  │  GET profile  │     │  GET listings │     │  POST item    │
  └───────────────┘     └───────────────┘     └───────────────┘
          │                     │                     │
          └─────────────────────┼─────────────────────┘
                                │
                      ┌─────────▼─────────┐
                      │  Payment API      │
                      │  POST transaction │
                      └─────────┬─────────┘
                                │
          ┌─────────────────────┼─────────────────────┐
          │                     │                     │
  ┌───────▼───────┐     ┌───────▼───────┐     ┌───────▼───────┐
  │  Email API    │     │ Inventory API │     │ Shipping API  │
  │  Send receipt │     │  Check stock  │     │  Calc delivery│
  └───────────────┘     └───────────────┘     └───────────────┘
```

#### API Classification Matrix

| Classification Axis | Type | Description | Example |
| :--- | :--- | :--- | :--- |
| **By Accessibility** | Public API | Open to all external developers | Google Maps API, Twitter API |
| | Private API | Internal use within an organization | Internal order management system |
| | Partner API | Shared only with authorized business partners | Stripe payment integration |
| **By Architecture** | REST | Uses standard HTTP methods; URL-based resources | Most modern web APIs |
| | SOAP | XML-based, strict contract (WSDL); enterprise-grade | Legacy banking & payment systems |
| | GraphQL | Query-based; client specifies exact data shape | GitHub API v4, Shopify |
| | WebSocket | Persistent, bidirectional real-time communication | Live chat, stock tickers |

> [!TIP]
> As a QA engineer, you will work primarily with **REST APIs**. REST is the dominant architectural style for web services and is what Postman is optimized to test.

---

### How APIs Work

#### The Full API Communication Lifecycle

```text
  ┌──────────────────────────────────────────────────────────────────┐
  │             API REQUEST / RESPONSE LIFECYCLE                     │
  └──────────────────────────────────────────────────────────────────┘

  PHASE 1 — CLIENT PREPARES REQUEST
  ├─ Identify the target endpoint URL
  ├─ Select the appropriate HTTP method (GET, POST, etc.)
  ├─ Attach authentication credentials (token/API key)
  ├─ Set required headers (Content-Type, Accept)
  └─ Construct the request body (if applicable)

  PHASE 2 — REQUEST TRAVELS OVER NETWORK
  ├─ HTTP packet is serialized and transmitted
  ├─ DNS resolution maps domain to server IP
  └─ Request arrives at the API server (avg. 50–300ms)

  PHASE 3 — SERVER PROCESSES REQUEST
  ├─ Parse and deserialize the request payload
  ├─ Authenticate the client (validate token/key)
  ├─ Authorize the action (check permissions)
  ├─ Execute business logic
  ├─ Query the database (if needed)
  └─ Construct the HTTP response

  PHASE 4 — SERVER SENDS RESPONSE
  ├─ Attach HTTP status code (200, 201, 404, etc.)
  ├─ Add response headers (Content-Type, Cache-Control)
  └─ Serialize and send the response body (usually JSON)

  PHASE 5 — CLIENT RECEIVES & PROCESSES RESPONSE
  ├─ Parse the HTTP status code
  ├─ Deserialize the JSON response body
  ├─ Validate the data structure and values
  └─ Update the UI or trigger follow-up logic
```

#### Client-Server Architecture Diagram

```text
  ┌──────────────────────┐                    ┌──────────────────────┐
  │       CLIENT         │                    │       SERVER         │
  │  (Consumer / App)    │                    │   (API Provider)     │
  │                      │                    │                      │
  │  • Web Browser       │  ──── Request ───► │  • Request Router    │
  │  • Mobile App        │                    │  • Auth Middleware   │
  │  • Desktop App       │  ◄─── Response ──  │  • Business Logic    │
  │  • Postman (Testing) │                    │  • Database Layer    │
  └──────────────────────┘                    └──────────────────────┘
```

#### Concrete Real-World API Call Trace

**Scenario:** A mobile app fetches a user profile for display.

**Step 1 — Client Request:**
```http
GET /api/v1/users/123 HTTP/1.1
Host: api.example.com
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
Accept: application/json
```

**Step 2 — Server Processing:**
```sql
-- Server authenticates token, then executes:
SELECT id, name, email, created_at FROM users WHERE id = 123;
```

**Step 3 — Server Response:**
```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 123,
  "name": "John Doe",
  "email": "john@example.com",
  "created_at": "2025-03-15T08:00:00Z"
}
```

---

### HTTP Protocol & Methods

#### What is HTTP/HTTPS?

**HTTP (HyperText Transfer Protocol):** The foundational communication protocol of the web. It defines the rules for how messages are formatted and transmitted between clients and servers.

**HTTPS:** HTTP with TLS/SSL encryption applied. All data in transit is encrypted, protecting it from interception. All production APIs must use HTTPS.

#### The Five Core HTTP Methods

##### 1. GET — Retrieve Data

**Purpose:** Read and retrieve an existing resource. Does not modify any server-side data.

| Property | Value |
| :--- | :--- |
| **Safe** |  Yes — does not alter server state |
| **Idempotent** |  Yes — calling it 100 times yields the same result |
| **Has Request Body** |  No — data is passed via URL/query parameters |
| **Cached by Browser** |  Yes |

```http
GET /api/users/123 HTTP/1.1
Host: api.example.com
Authorization: Bearer token_here
```

**Common Use Cases:** Fetch user profile, retrieve product catalog, get order status, search results.

##### 2. POST — Create a New Resource

**Purpose:** Submit data to the server to create a new resource. Calling POST twice typically creates two separate records.

| Property | Value |
| :--- | :--- |
| **Safe** |  No — modifies server state |
| **Idempotent** |  No — repeated calls create duplicate resources |
| **Has Request Body** |  Yes |
| **Cached by Browser** |  No |

```http
POST /api/users HTTP/1.1
Content-Type: application/json

{
  "name": "Jane Smith",
  "email": "jane@example.com",
  "password": "SecurePass#2026"
}
```

**Common Use Cases:** Create user account, submit order, upload file, submit a form.

##### 3. PUT — Replace an Entire Resource

**Purpose:** Send a complete replacement for an existing resource. All fields must be provided; missing fields are typically set to null or default values.

| Property | Value |
| :--- | :--- |
| **Safe** |  No |
| **Idempotent** |  Yes — sending same payload repeatedly = same state |
| **Has Request Body** |  Yes (full object required) |

```http
PUT /api/users/123 HTTP/1.1
Content-Type: application/json

{
  "name": "John Updated",
  "email": "john.new@example.com",
  "age": 31
}
```

**QA Note:** If you omit a field (e.g., `age`) in a PUT request, that field may be cleared on the server. Always send the full object.

##### 4. PATCH — Partial Update

**Purpose:** Send only the fields that need to change. The server merges the patch into the existing resource without affecting other fields.

| Property | Value |
| :--- | :--- |
| **Safe** |  No |
| **Idempotent** |  Yes |
| **Has Request Body** |  Yes (only changed fields) |

```http
PATCH /api/users/123 HTTP/1.1
Content-Type: application/json

{
  "email": "john.newemail@example.com"
}
```

**PUT vs. PATCH Comparison:**

| Scenario | PUT Payload | PATCH Payload |
| :--- | :--- | :--- |
| Change only the email | Must send all fields | Send only `{ "email": "..." }` |
| Risk of data loss |  High (omitted fields may be nulled) |  Low |
| Bandwidth efficiency |  Lower |  Higher |

##### 5. DELETE — Remove a Resource

**Purpose:** Permanently delete a specified resource from the server.

| Property | Value |
| :--- | :--- |
| **Safe** |  No |
| **Idempotent** |  Yes — deleting an already-deleted resource returns 404, not an error |
| **Has Request Body** |  Usually none |

```http
DELETE /api/users/123 HTTP/1.1
Authorization: Bearer token_here
```

**Common Use Cases:** Delete a user account, remove a product listing, cancel an order.

---

### HTTP Status Codes

Status codes are the server's standardized way of communicating the outcome of every request. As a QA engineer, reading status codes is a fundamental skill.

#### 2xx — Success

| Code | Name | Meaning | Typically Used With |
| :---: | :--- | :--- | :--- |
| `200` | OK | Request succeeded; response body contains the result. | GET, PUT, PATCH |
| `201` | Created | Resource was successfully created. | POST |
| `204` | No Content | Request succeeded; no response body returned. | DELETE |

#### 3xx — Redirection

| Code | Name | Meaning |
| :---: | :--- | :--- |
| `301` | Moved Permanently | Resource has a new permanent URL. Update your bookmarks/configs. |
| `304` | Not Modified | Cached version is still valid; no need to re-download. |

#### 4xx — Client Errors *(Your fault as the caller)*

| Code | Name | Meaning | Common Cause |
| :---: | :--- | :--- | :--- |
| `400` | Bad Request | Malformed syntax or invalid request payload. | Sending invalid JSON, missing required field |
| `401` | Unauthorized | Authentication is required or has failed. | Missing, expired, or invalid token |
| `403` | Forbidden | Authenticated but lacks permission for this resource. | Regular user accessing admin endpoint |
| `404` | Not Found | The requested resource does not exist. | Querying a deleted record or wrong ID |
| `405` | Method Not Allowed | HTTP method not supported on this endpoint. | Using POST on a GET-only endpoint |
| `422` | Unprocessable Entity | Request format is valid, but data fails business validation. | Invalid email format, age below minimum |
| `429` | Too Many Requests | Rate limit has been exceeded. | Sending too many requests per second |

#### 5xx — Server Errors *(Server's fault)*

| Code | Name | Meaning | Common Cause |
| :---: | :--- | :--- | :--- |
| `500` | Internal Server Error | Unhandled exception on the server. | Application bug, null pointer exception |
| `502` | Bad Gateway | A gateway or proxy received an invalid upstream response. | Downstream service failure |
| `503` | Service Unavailable | Server is temporarily down or overloaded. | Maintenance window, traffic spike |

> [!IMPORTANT]
> **The QA Golden Rule for Status Codes:**
> - **2xx** = The API did what you asked. Verify the response data.
> - **4xx** = You sent a bad request. Fix your test input and re-run.
> - **5xx** = The server is broken. Log a bug for the development team.

---

### API Request Components

#### Anatomy of a Complete HTTP Request

Every API request is composed of four distinct structural layers:

```text
┌───────────────────────────────────────────────────────┐
│                  HTTP REQUEST ANATOMY                 │
├───────────────────────────────────────────────────────┤
│  REQUEST LINE                                         │
│  POST /api/v1/users HTTP/1.1                          │
├───────────────────────────────────────────────────────┤
│  HEADERS                                              │
│  Host: api.example.com                                │
│  Authorization: Bearer eyJhbGciOiJIUzI1NiJ9...        │
│  Content-Type: application/json                       │
│  Accept: application/json                             │
├───────────────────────────────────────────────────────┤
│  [Empty Line — separates headers from body]           │
├───────────────────────────────────────────────────────┤
│  BODY (optional — used with POST, PUT, PATCH)         │
│  {                                                    │
│    "name": "John Doe",                                │
│    "email": "john@example.com",                       │
│    "age": 30                                          │
│  }                                                    │
└───────────────────────────────────────────────────────┘
```

#### 1. The Endpoint URL

Every URL can be decomposed into its constituent parts, each carrying specific meaning:

```text
https://api.example.com/api/v1/users/123?page=1&limit=10

  https://           → Protocol (secure)
  api.example.com    → Domain (the server host)
  /api/v1/           → Base path + API version
  /users             → Resource collection name
  /123               → Specific resource ID
  ?page=1&limit=10   → Query parameters (filtering/pagination)
```

**Standard RESTful Endpoint Patterns:**

| HTTP Method | Endpoint | Operation |
| :--- | :--- | :--- |
| `GET` | `/api/users` | Retrieve the full list of users |
| `GET` | `/api/users/123` | Retrieve a single user by ID |
| `GET` | `/api/users/123/orders` | Retrieve all orders belonging to user 123 |
| `POST` | `/api/users` | Create a new user |
| `PUT` | `/api/users/123` | Replace user 123 entirely |
| `PATCH` | `/api/users/123` | Partially update user 123 |
| `DELETE` | `/api/users/123` | Delete user 123 |
| `GET` | `/api/users?role=admin` | Filter users where role is admin |

#### 2. Request Headers

Headers transmit metadata about the request. They are key-value pairs that provide the server with context it needs to process the request correctly.

| Header | Purpose | Example Value |
| :--- | :--- | :--- |
| `Authorization` | Authenticate the caller | `Bearer eyJhbGciOiJIUzI1NiJ9...` |
| `Content-Type` | Format of the request body | `application/json` |
| `Accept` | Preferred response format | `application/json` |
| `X-API-Key` | Alternative API key authentication | `your_api_key_here` |
| `X-Request-ID` | Unique identifier for tracing a specific request | `a8f7-4c21-9d3b` |
| `User-Agent` | Identifies the client software | `PostmanRuntime/10.0` |

#### 3. Request Body

The body carries the payload data for write operations. It is not used with GET or DELETE requests.

```json
// JSON Body — Standard for REST APIs (most common)
{
  "name": "Jane Smith",
  "email": "jane@example.com",
  "age": 28,
  "address": {
    "street": "123 Main St",
    "city": "New York",
    "zip": "10001"
  },
  "roles": ["customer", "beta-tester"]
}
```

---

### API Response Structure

#### Anatomy of a Complete HTTP Response

```text
┌───────────────────────────────────────────────────────┐
│                 HTTP RESPONSE ANATOMY                 │
├───────────────────────────────────────────────────────┤
│  STATUS LINE                                          │
│  HTTP/1.1 201 Created                                 │
├───────────────────────────────────────────────────────┤
│  RESPONSE HEADERS                                     │
│  Content-Type: application/json                       │
│  Content-Length: 245                                  │
│  Date: Sat, 21 Jun 2026 10:30:00 GMT                  │
│  Cache-Control: no-store                              │
│  ETag: "a1b2c3d4e5f6"                                 │
├───────────────────────────────────────────────────────┤
│  [Empty Line]                                         │
├───────────────────────────────────────────────────────┤
│  RESPONSE BODY                                        │
│  {                                                    │
│    "id": 456,                                         │
│    "name": "Jane Smith",                              │
│    "email": "jane@example.com",                       │
│    "created_at": "2026-06-21T10:30:00Z"               │
│  }                                                    │
└───────────────────────────────────────────────────────┘
```

#### Common Response Body Patterns

**Single Object Response:**
```json
{
  "id": 123,
  "name": "John Doe",
  "email": "john@example.com",
  "active": true
}
```

**Paginated List Response:**
```json
{
  "data": [
    { "id": 1, "name": "John Doe" },
    { "id": 2, "name": "Jane Smith" }
  ],
  "pagination": {
    "page": 1,
    "per_page": 10,
    "total_records": 200,
    "total_pages": 20
  }
}
```

**Structured Error Response:**
```json
{
  "error": {
    "code": "VALIDATION_FAILED",
    "message": "The email address provided is not in a valid format.",
    "field": "email",
    "status": 422,
    "timestamp": "2026-06-21T10:30:00Z"
  }
}
```

#### Key Response Headers for QA

| Header | Meaning | QA Relevance |
| :--- | :--- | :--- |
| `Content-Type` | Format of the response body | Confirm it is `application/json` for JSON APIs |
| `Content-Length` | Size of the response body in bytes | Useful for detecting unexpectedly large payloads |
| `Cache-Control` | Caching policy for the response | Ensure sensitive endpoints use `no-store` |
| `Set-Cookie` | Sets a session or auth cookie | Verify secure flags are set (`Secure`, `HttpOnly`) |
| `ETag` | Entity tag for cache validation | Verify conditional requests work correctly |

---

### JSON Data Format

#### What is JSON?

**JSON (JavaScript Object Notation):** A lightweight, human-readable, and universally supported data interchange format. It is the de-facto standard body format for REST APIs.

#### JSON Data Types Reference

| Type | Syntax | Example | Notes |
| :--- | :--- | :--- | :--- |
| **String** | `"value"` | `"John Doe"` | Always double-quoted |
| **Number** | `42` / `3.14` | `30`, `99.99` | No quotes; supports integers and decimals |
| **Boolean** | `true` / `false` | `true` | Lowercase only |
| **Null** | `null` | `null` | Represents absence of a value; lowercase |
| **Object** | `{ "key": value }` | `{ "city": "NYC" }` | Unordered collection of key-value pairs |
| **Array** | `[item1, item2]` | `["admin", "user"]` | Ordered list of values |

#### Comprehensive JSON Structure Example

```json
{
  "id": 1001,
  "name": "Wireless Mechanical Keyboard",
  "price": 129.99,
  "inStock": true,
  "discount": null,
  "tags": ["electronics", "peripherals", "wireless"],
  "specs": {
    "weight": "850g",
    "connectivity": "Bluetooth 5.0",
    "batteryLife": "40 hours"
  },
  "reviews": [
    { "userId": 10, "rating": 5, "comment": "Excellent build quality." },
    { "userId": 11, "rating": 4, "comment": "Great, but pricey." }
  ]
}
```

#### JSON Validation: Valid vs. Invalid

```text
 VALID JSON (parses correctly)
{
  "name": "John",
  "age": 30,
  "active": true
}

 INVALID JSON — Common Mistakes
{
  name: "John",          ← Keys must be in double quotes
  'age': 30,             ← Single quotes are not allowed
  "active": true,        ← Trailing comma after last item is illegal
}
```

**The 5 Most Common JSON Errors (QA Checklist):**

| # | Error | Incorrect | Correct |
| :---: | :--- | :--- | :--- |
| 1 | Unquoted key | `{name: "John"}` | `{"name": "John"}` |
| 2 | Single-quoted key/value | `{'name': 'John'}` | `{"name": "John"}` |
| 3 | Trailing comma | `{"name": "John",}` | `{"name": "John"}` |
| 4 | Unquoted string value | `{"status": active}` | `{"status": "active"}` |
| 5 | Mismatched braces | `{"name": "John"` | `{"name": "John"}` |

> [!TIP]
> Use [jsonlint.com](https://jsonlint.com) or Postman's built-in JSON formatter to instantly validate any JSON payload before sending a request.

---

### Database Interaction & Schema

#### How API Calls Translate to Database Operations

Every API call typically maps to one or more database operations behind the scenes:

```text
  API REQUEST ──────────────► DATABASE OPERATION
  ─────────────────────────────────────────────────────────
  GET    /api/users/123      SELECT * FROM users WHERE id=123
  POST   /api/users          INSERT INTO users (name, email) VALUES (...)
  PUT    /api/users/123      UPDATE users SET name=..., email=... WHERE id=123
  PATCH  /api/users/123      UPDATE users SET email=... WHERE id=123
  DELETE /api/users/123      DELETE FROM users WHERE id=123
```

#### End-to-End QA Verification Workflow

**Scenario:** A QA engineer validates that a new order is correctly persisted after an API call.

**Step 1 — Send the API Request:**
```http
POST /api/orders HTTP/1.1
Content-Type: application/json

{
  "userId": 123,
  "items": [{ "productId": 100, "quantity": 2 }],
  "total": 199.98
}
```

**Step 2 — Verify the API Response:**
```json
// Expected: HTTP 201 Created
{
  "orderId": 5001,
  "userId": 123,
  "total": 199.98,
  "status": "pending",
  "createdAt": "2026-06-21T10:30:00Z"
}
```

**Step 3 — Validate Directly Against the Database:**
```sql
SELECT * FROM orders WHERE id = 5001;
```

| OrderID | UserID | Total | Status | CreatedAt |
| :---: | :---: | :---: | :--- | :--- |
| 5001 | 123 | 199.98 | pending | 2026-06-21 10:30:00 |

**Step 4 — QA Verification Checklist:**
-  Order record exists in the database
-  All field values match the API request body exactly
-  `status` is `pending` (correct initial state)
-  `total` value of `199.98` is arithmetically correct (`2 × $99.99`)
-  Foreign key integrity: `userId = 123` exists in the `users` table
-  Foreign key integrity: `productId = 100` exists in the `products` table
-  No duplicate order records were created

#### Understanding the E-Commerce Database Schema

A QA engineer who understands the underlying database schema can write far more comprehensive and targeted test cases.

```text
  users                    products                 orders
  ┌─────────┬────────────┐  ┌─────────┬──────────────┐  ┌─────────┬─────────┬──────────┬──────────┐
  │ UserID  │ Name       │  │ ProdID  │ Name         │  │ OrderID │ UserID  │ ProdID   │ Quantity │
  │ (PK)    │            │  │ (PK)    │              │  │ (PK)    │ (FK)    │ (FK)     │          │
  ├─────────┼────────────┤  ├─────────┼──────────────┤  ├─────────┼─────────┼──────────┼──────────┤
  │ 1       │ John       │  │ 100     │ Laptop       │  │ 5001    │ 1       │ 100      │ 1        │
  │ 2       │ Jane       │  │ 101     │ Mouse        │  │ 5002    │ 2       │ 101      │ 2        │
  │ 3       │ Bob        │  │ 102     │ Monitor      │  │ 5003    │ 3       │ 102      │ 1        │
  └─────────┴────────────┘  └─────────┴──────────────┘  └─────────┴─────────┴──────────┴──────────┘
            │                          │                            │         │
            └──────────────────────────┼────────────────────────────┘         │
                                       └──────────────────────────────────────┘
             PK = Primary Key (unique row identifier)
             FK = Foreign Key (references a PK in another table)
```

> [!IMPORTANT]
> **Why Schema Knowledge Matters for QA:** When testing an update endpoint (PATCH), you must verify not just that the targeted field changed, but also that **no other fields were inadvertently modified** and that **all relational foreign key constraints remain intact**.

---

## Topic 2: Hands-On Postman Testing

### Introduction to Postman

#### What is Postman?

**Definition:** A professional-grade, GUI-based API development and testing platform that allows QA engineers to construct, send, inspect, and automate HTTP requests without writing application code.

| Property | Detail |
| :--- | :--- |
| **Download** | [postman.com/downloads](https://www.postman.com/downloads/) |
| **License Model** | Free tier (fully sufficient for QA) → Paid (teams/enterprise) |
| **Supported Platforms** | Windows, macOS, Linux |
| **Learning Curve** | Low — approachable within a single working session |

#### Why Postman is the Industry Standard for Manual API QA

| Capability | Without Postman | With Postman |
| :--- | :--- | :--- |
| Sending requests | Must write `curl` commands or custom scripts | GUI-based; click to send |
| Organizing tests | Files scattered across directories | Structured collections and folders |
| Authentication | Manually craft auth headers each time | Saved once per environment |
| Response inspection | Raw terminal output | Formatted, syntax-highlighted JSON viewer |
| Automated checks | Manual comparison each time | Write assertions once; they run on every send |
| Environment switching | Rewrite every URL and token | One dropdown switch |

#### Postman Interface Overview

```text
  ┌──────────────────────────────────────────────────────────────────────────┐
  │  Postman Desktop Application                                             │
  ├─────────────────┬────────────────────────────────────────────────────────┤
  │  SIDEBAR        │  REQUEST BUILDER                                       │
  │                 │                                                        │
  │   Collections   │  Method: [POST ▼]  URL: {{baseUrl}}/api/users  [Send]  │
  │   Environments  │  ────────────────────────────────────────────────────  │
  │    History      │  [Params] [Authorization] [Headers] [Body] [Tests]     │
  │                 │                                                        │
  │  ┌───────────┐  │  ┌────────────────────────┐  ┌────────────────────┐    │
  │  │  E-Comm   │  │  │  BODY EDITOR           │  │  RESPONSE VIEWER   │    │
  │  │   GET     │  │  │  ● raw  ● JSON ▼       │  │  Status: 201 OK    │    │
  │  │   POST    │  │  │  {                     │  │  Time: 187ms       │    │
  │  │   PATCH   │  │  │    "name": "Jane",     │  │  Size: 1.2 KB      │    │
  │  │   DELETE  │  │  │    "email": "..."      │  │  ───────────────── │    │
  │  └───────────┘  │  │  }                     │  │  Pretty  Raw       │    │
  │                 │  └────────────────────────┘  └────────────────────┘    │
  └─────────────────┴────────────────────────────────────────────────────────┘
```

---

### Setting Up Postman

#### Installation Workflow

```text
  [ 1. Download ]  ==►  [ 2. Install ]  ==►  [ 3. Launch ]  ==►  [ 4. Create Collection ]
```

1. Navigate to [postman.com/downloads](https://www.postman.com/downloads/) and download the installer for your OS.
2. Run the installer — no special configuration required.
3. Open Postman. Creating an account is optional but enables cloud sync of your collections.

#### Organizing Your Work: Collection Structure

**Collections** are the primary organizational unit in Postman — think of them as project folders that group all related API requests.

**Recommended Collection Architecture for a QA Project:**

```text
   E-Commerce API Test Suite
  ├──  User Management
  │   ├──  GET All Users
  │   ├──  GET Single User by ID
  │   ├──  POST Create New User
  │   ├──  PUT Update Full User Profile
  │   ├──  PATCH Update User Email
  │   └──  DELETE Remove User Account
  ├──  Product Catalog
  │   ├──  GET All Products (Paginated)
  │   ├──  POST Create Product Listing
  │   └──  PATCH Update Product Price
  └──  Order Management
      ├──  POST Place New Order
      ├──  GET Retrieve Order by ID
      └──  PATCH Update Order Status
```

> [!TIP]
> **Best Practice:** Match your collection folder structure to the API's resource grouping. This makes it trivial for any team member to find and run the right tests without guidance.

---

### Testing HTTP Methods in Postman

#### GET — Retrieve a Resource

**Postman Setup:**

| Field | Value |
| :--- | :--- |
| Method | `GET` |
| URL | `{{baseUrl}}/api/users/123` |
| Authorization | Bearer Token → `{{token}}` |
| Body | None |

**Expected Response:**
```json
// HTTP/1.1 200 OK
{
  "id": 123,
  "name": "John Doe",
  "email": "john@example.com",
  "age": 30
}
```

**QA Verification Checklist:**
-  Status code is `200 OK`
-  Response body is valid JSON
-  All expected fields are present (`id`, `name`, `email`, `age`)
-  Data types are correct (`id` is a number, `name` is a string)
-  Values are accurate and match the seed/test data

---

#### POST — Create a New Resource

**Postman Setup:**

| Field | Value |
| :--- | :--- |
| Method | `POST` |
| URL | `{{baseUrl}}/api/users` |
| Header | `Content-Type: application/json` |
| Body (raw JSON) | `{ "name": "Jane Smith", "email": "jane@example.com", "password": "Secure#2026" }` |

**Expected Response:**
```json
// HTTP/1.1 201 Created
{
  "id": 456,
  "name": "Jane Smith",
  "email": "jane@example.com",
  "created_at": "2026-06-21T10:30:00Z"
}
```

**QA Verification Checklist:**
-  Status code is `201 Created`
-  A new `id` was auto-generated and returned
-  All submitted fields are reflected in the response
-  The `password` field is **NOT** returned in the response (security requirement)
-  A `created_at` timestamp is present and in valid ISO 8601 format

---

#### PUT — Replace an Entire Resource

**Postman Setup:**

| Field | Value |
| :--- | :--- |
| Method | `PUT` |
| URL | `{{baseUrl}}/api/users/123` |
| Body (raw JSON) | `{ "name": "John Updated", "email": "john.new@example.com", "age": 31 }` |

**Expected Response:**
```json
// HTTP/1.1 200 OK
{
  "id": 123,
  "name": "John Updated",
  "email": "john.new@example.com",
  "age": 31
}
```

**QA Verification Checklist:**
-  Status code is `200 OK`
-  The resource `id` is unchanged
-  All submitted fields have been updated with new values
-  No extraneous or unexpected fields appear in the response

---

#### PATCH — Partially Update a Resource

**Postman Setup:**

| Field | Value |
| :--- | :--- |
| Method | `PATCH` |
| URL | `{{baseUrl}}/api/users/123` |
| Body (raw JSON) | `{ "email": "john.newemail@example.com" }` |

**Expected Response:**
```json
// HTTP/1.1 200 OK
{
  "id": 123,
  "name": "John Updated",             ← Unchanged from previous state
  "email": "john.newemail@example.com", ← Only this field was modified
  "age": 31                           ← Unchanged from previous state
}
```

**QA Verification Checklist:**
-  Status code is `200 OK`
-  Only the targeted field (`email`) has changed
-  All other fields (`name`, `age`) remain at their pre-patch values
-  The resource `id` is unchanged

---

#### DELETE — Remove a Resource

**Postman Setup:**

| Field | Value |
| :--- | :--- |
| Method | `DELETE` |
| URL | `{{baseUrl}}/api/users/123` |
| Body | None required |

**Expected Response:**
```text
HTTP/1.1 204 No Content
(Empty body — this is correct and expected)
```

**QA Verification Checklist:**
-  Status code is `204 No Content` (or `200 OK` with a confirmation message)
-  A subsequent `GET /api/users/123` returns `404 Not Found` — confirming deletion
-  The record no longer exists in the database (direct DB query if access is available)

---

### Using Parameters & Headers

#### Query Parameters

Query parameters are key-value pairs appended to the URL after a `?` symbol. They are used to filter, sort, search, and paginate results without changing the resource itself.

**Syntax:** `?key1=value1&key2=value2`

**In Postman — Adding Parameters via the Params Tab:**

| Key | Value | Purpose |
| :--- | :--- | :--- |
| `page` | `1` | Return the first page of results |
| `limit` | `10` | Return 10 records per page |
| `role` | `admin` | Filter to users with the admin role |
| `sortBy` | `created_at` | Sort results by creation date |

Postman automatically constructs the URL:
```
https://api.example.com/api/users?page=1&limit=10&role=admin&sortBy=created_at
```

**QA Test Cases for Pagination Parameters:**

| Test Case | Input | Expected Status | Expected Behaviour |
| :--- | :--- | :---: | :--- |
| Valid first page | `page=1&limit=10` | `200` | Returns first 10 records |
| Valid second page | `page=2&limit=10` | `200` | Returns next 10 records |
| Beyond last page | `page=9999&limit=10` | `200` | Returns empty `data` array |
| Invalid page value | `page=0` | `400` | Returns validation error |
| Invalid limit value | `limit=-1` | `400` | Returns validation error |
| Invalid filter value | `role=superuser` | `400` or empty | Error or empty response |

#### Headers Testing

| Header | Purpose | QA Test Scenarios |
| :--- | :--- | :--- |
| `Authorization: Bearer <token>` | Authenticate the request | Valid token → `200`; no token → `401`; invalid token → `401`; expired token → `401` |
| `Content-Type: application/json` | Declare request body format | Missing on POST → `400 Bad Request` |
| `Accept: application/json` | Declare desired response format | Server respects the format preference |
| `X-API-Key: <key>` | Alternative API key auth | Invalid key → `403 Forbidden` |

> [!IMPORTANT]
> **Security-Focused Header Tests:** Always verify that endpoints requiring authentication return `401 Unauthorized` when the `Authorization` header is absent. This is a critical security test case that must never be skipped.

---

### Postman Variables & Environments

#### The Problem Variables Solve

Without variables, every request contains hardcoded values. When the environment changes (e.g., moving from Development to Staging), every request must be updated manually — a slow, error-prone process.

#### Variable Scope Hierarchy

```text
  Global Variables   → Available across all collections and environments
       │
       ▼
  Environment Variables → Active only when a specific environment is selected
       │
       ▼
  Collection Variables  → Scoped to a single collection
       │
       ▼
  Local Variables       → Scoped to a single request's script execution
```

#### Setting Up Environments

**Example: Development Environment Variables**

| Variable | Value |
| :--- | :--- |
| `baseUrl` | `https://dev-api.example.com` |
| `token` | `Bearer dev_token_abc123` |
| `userId` | `1` |
| `adminToken` | `Bearer dev_admin_token_xyz` |

**Example: Staging Environment Variables**

| Variable | Value |
| :--- | :--- |
| `baseUrl` | `https://staging-api.example.com` |
| `token` | `Bearer staging_token_def456` |
| `userId` | `1` |
| `adminToken` | `Bearer staging_admin_token_uvw` |

**Using Variables in a Request:**

```text
  Method: GET
  URL:    {{baseUrl}}/api/users/{{userId}}

  Headers:
    Authorization: {{token}}

  // When "Development" is active:
  // → Resolves to: GET https://dev-api.example.com/api/users/1
  // → Authorization: Bearer dev_token_abc123

  // Switch to "Staging" — no request changes needed!
  // → Resolves to: GET https://staging-api.example.com/api/users/1
  // → Authorization: Bearer staging_token_def456
```

#### Dynamically Setting Variables from Test Scripts

One of the most powerful Postman patterns is capturing a value from a response (such as a newly created resource ID) and storing it as a variable for use in subsequent requests:

```javascript
// In the "Tests" tab of a POST /api/users request:
pm.test("Save newly created user ID for subsequent requests", function () {
    var jsonData = pm.response.json();
    pm.expect(jsonData.id).to.exist;
    pm.environment.set("userId", jsonData.id); // Saved for all following requests
});
```

---

### Writing Automated Test Scripts

#### Why Automated Test Scripts Are Essential

Without test scripts, verification is entirely manual — you must visually inspect every response for every field, every time. Test scripts transform this into an automated, repeatable, and auditable process.

| Aspect | Manual Verification | Automated Test Scripts |
| :--- | :--- | :--- |
| Speed | Slow — inspect each field visually | Instant — milliseconds per assertion |
| Consistency | Prone to human oversight | 100% consistent on every run |
| Documentation | Undocumented | Scripts act as living documentation |
| CI/CD Integration | Not possible | Runs automatically in pipelines |
| Reporting | No audit trail | Clear pass/fail results with Postman Newman |

#### Core Test Script Patterns

All Postman tests are written in JavaScript using the `pm` (Postman) object inside the **Tests** tab.

**Pattern 1 — Assert Status Code:**
```javascript
pm.test("Status code is 200 OK", function () {
    pm.response.to.have.status(200);
});
```

**Pattern 2 — Assert Response Time (Performance Baseline):**
```javascript
pm.test("Response time is under 1000ms", function () {
    pm.expect(pm.response.responseTime).to.be.below(1000);
});
```

**Pattern 3 — Assert Field Existence:**
```javascript
pm.test("Response body contains 'id' field", function () {
    pm.expect(pm.response.json().id).to.exist;
});
```

**Pattern 4 — Assert Exact Field Value:**
```javascript
pm.test("User name matches expected value", function () {
    pm.expect(pm.response.json().name).to.equal("John Doe");
});
```

**Pattern 5 — Assert Content-Type Header:**
```javascript
pm.test("Response Content-Type is application/json", function () {
    pm.expect(pm.response.headers.get("Content-Type")).to.include("application/json");
});
```

#### Comprehensive Test Script: Create User Endpoint

```javascript
// ═══════════════════════════════════════════════════════════════
//  Endpoint: POST /api/users
//  Purpose:  Full verification suite for user creation
// ═══════════════════════════════════════════════════════════════

// Test 1: HTTP status code
pm.test("Status code is 201 Created", function () {
    pm.response.to.have.status(201);
});

// Test 2: Response time SLA
pm.test("Response time is under 2000ms", function () {
    pm.expect(pm.response.responseTime).to.be.below(2000);
});

// Test 3: Content-Type header
pm.test("Response is application/json", function () {
    pm.expect(pm.response.headers.get("Content-Type")).to.include("application/json");
});

// Test 4: Required fields present
pm.test("Response body contains required fields", function () {
    var jsonData = pm.response.json();
    pm.expect(jsonData.id).to.exist;
    pm.expect(jsonData.name).to.exist;
    pm.expect(jsonData.email).to.exist;
    pm.expect(jsonData.created_at).to.exist;
});

// Test 5: Data integrity validation
pm.test("Returned user data matches submitted payload", function () {
    var jsonData = pm.response.json();
    pm.expect(jsonData.name).to.equal("Jane Smith");
    pm.expect(jsonData.email).to.equal("jane@example.com");
});

// Test 6: Security — password must never be returned
pm.test("Password field is NOT exposed in response (security)", function () {
    pm.expect(pm.response.json().password).to.be.undefined;
});

// Test 7: Capture ID for chained requests
pm.test("User ID captured and stored as environment variable", function () {
    var userId = pm.response.json().id;
    pm.expect(userId).to.be.a("number");
    pm.environment.set("userId", userId);
});
```

**Expected Test Results Panel:**
```text
    Status code is 201 Created
    Response time is under 2000ms
    Response is application/json
    Response body contains required fields
    Returned user data matches submitted payload
    Password field is NOT exposed in response (security)
    User ID captured and stored as environment variable

  7 / 7 tests passed
```

---

### Practical End-to-End Testing Scenarios

#### Scenario 1: Complete User CRUD Lifecycle

This scenario validates the entire lifecycle of a user resource using a chained sequence of requests where each step depends on the previous one.

```text
  [ POST Create ]  ==►  [ GET Retrieve ]  ==►  [ PATCH Update ]  ==►  [ DELETE Remove ]  ==►  [ GET Verify 404 ]
```

| Step | Method | Endpoint | Assertion |
| :---: | :--- | :--- | :--- |
| 1 | `POST` | `/api/users` | Status `201`; capture `userId` |
| 2 | `GET` | `/api/users/{{userId}}` | Status `200`; name matches |
| 3 | `PATCH` | `/api/users/{{userId}}` | Status `200`; only email changed |
| 4 | `DELETE` | `/api/users/{{userId}}` | Status `204`; no body |
| 5 | `GET` | `/api/users/{{userId}}` | Status `404`; resource gone |

#### Scenario 2: Comprehensive Error & Edge Case Testing

Robust API testing is not just about the happy path — negative testing is equally critical.

| Test Category | Test Input | Endpoint | Expected Status | Validation |
| :--- | :--- | :--- | :---: | :--- |
| **Input Validation** | Empty `name` field in POST body | `POST /api/users` | `400` | Error message references `name` field |
| **Authentication** | Request with no `Authorization` header | `GET /api/users` | `401` | Response contains `"Unauthorized"` |
| **Authorization** | Regular user token accessing admin route | `GET /api/admin/users` | `403` | Response contains `"Forbidden"` |
| **Not Found** | Non-existent resource ID | `GET /api/users/99999` | `404` | Response contains `"not found"` |
| **Data Format** | Invalid email format in POST body | `POST /api/users` | `422` | Error message references `email` field |
| **Rate Limiting** | Rapid burst of 200 requests | `GET /api/users` | `429` | Response contains `Retry-After` header |
| **Idempotency** | Same `DELETE` request sent twice | `DELETE /api/users/123` | `204` then `404` | Second call returns `404` gracefully |

#### Scenario 3: Authentication Token Lifecycle

A targeted test matrix ensuring all authentication states are handled correctly by the API:

| Auth State | Header Value | Expected Status | Expected Behavior |
| :--- | :--- | :---: | :--- |
| Valid active token | `Authorization: Bearer <valid_token>` | `200` | Full access granted |
| No token provided | *(header absent)* | `401` | `"Authentication required"` error |
| Malformed token | `Authorization: Bearer INVALID123` | `401` | `"Invalid token"` error |
| Expired token | `Authorization: Bearer <expired_token>` | `401` | `"Token expired"` error |
| Wrong scheme | `Authorization: Basic <credentials>` | `401` | `"Invalid auth scheme"` error |

---

## Key Takeaways

* **APIs** are the standardized communication contracts that allow independent software systems to exchange data reliably. Modern applications are entirely composed of API calls.
* **HTTP Methods** define the *intent* of every API request: `GET` reads, `POST` creates, `PUT` replaces, `PATCH` partially updates, and `DELETE` removes.
* **HTTP Status Codes** are the server's structured response signal: `2xx` means success, `4xx` means the client made an error, and `5xx` means the server encountered an error.
* **JSON** is the universal language of REST APIs. A QA engineer must be able to read, validate, and construct valid JSON payloads confidently.
* **Postman** is the industry-standard tool for manual API testing. Its collection system, environment variables, and automated test scripts dramatically accelerate QA workflows.
* **Environment Variables** decouple your test configuration from your test logic, making it trivial to run the same suite against Development, Staging, and Production environments with a single click.
* **Test Scripts** elevate manual API testing to a semi-automated discipline. Writing assertions that run on every send ensures consistent, repeatable, and auditable verification.
* **Negative Testing** — validating error handling, authentication, authorization, and input validation — is as important as testing the happy path and must always be included in a comprehensive test suite.

---

**Document Control Metadata**

* **Target Knowledge Level:** Beginner to Intermediate Quality Assurance Practitioners
* **Estimated Self-Guided Completion Time:** 150 Minutes
* **Temporal Status Baseline:** 2026 Release Cycle Specifications
