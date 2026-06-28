
# Section 9: Database Testing

> [!IMPORTANT]
> **Data is the new oil — but only if it is correct, secure, and clean.**
> A modern QA engineer cannot rely solely on UI testing. The user interface may display "Order Placed Successfully," yet the underlying database could simultaneously hold duplicate records, incorrect pricing, or corrupted relational data. This section covers Relational (SQL) and Non-Relational (NoSQL) database concepts, query writing, and the systematic QA verification strategies used in professional testing environments.

---

## Table of Contents

### Topic 1: Database Testing Fundamentals
  * [What is Database Testing?](#what-is-database-testing)
  * [The ACID Transaction Model](#the-acid-transaction-model)
  * [SQL vs. NoSQL Databases](#sql-vs-nosql-databases)
  * [SQL Schemas, Tables & Key Constraints](#sql-schemas-tables--key-constraints)
  * [Core SQL Queries for QA](#core-sql-queries-for-qa)
  * [SQL JOINs Explained](#sql-joins-explained)

### Topic 2: Practical Database Testing & NoSQL
  * [Hands-On QA Verification Workflow](#hands-on-qa-verification-workflow)
  * [Introduction to NoSQL: MongoDB Basics](#introduction-to-nosql-mongodb-basics)
  * [MongoDB CRUD Operations](#mongodb-crud-operations)
  * [SQL vs. NoSQL QA Testing Scenarios](#sql-vs-nosql-qa-testing-scenarios)

* [Key Takeaways & Self-Assessment](#key-takeaways--self-assessment)

---

## Topic 1: Database Testing Fundamentals

### What is Database Testing?

**Database testing** is a category of backend verification that bypasses the user interface and directly interrogates the persistence layer of an application. It validates that the system correctly stores, retrieves, modifies, and deletes data — and that doing so never causes corruption, data loss, or integrity violations.

#### Why UI Testing Alone Is Not Sufficient

The user interface only surfaces what the application *chooses to show*. Critical defects can exist silently at the database level while the UI reports success.

| Defect Category | What the UI Shows | What the Database Contains |
| :--- | :--- | :--- |
| **Duplicate Records** | "Order placed successfully" | Two identical order rows created |
| **Incorrect Calculation** | "Total: $199.98" | Database stores `$19.998` (floating point error) |
| **Orphaned Records** | User deleted successfully | Related order records still exist with no parent |
| **Constraint Violation** | Form submitted OK | `NOT NULL` column silently receives a null value |
| **Stock Desynchronisation** | "In Stock" | Inventory table shows `stock_quantity = -3` |

#### Core Areas of Database Testing

| Testing Area | What is Validated |
| :--- | :--- |
| **Data Integrity** | Data saved matches data submitted; no corruption during write operations |
| **Data Validity** | Values adhere to defined formats, types, and business rules |
| **CRUD Operations** | Create, Read, Update, Delete all function correctly end-to-end |
| **Schema Compliance** | Tables, columns, data types, and constraints match the approved specification |
| **Referential Integrity** | Foreign key relationships are enforced; no orphaned or dangling records |
| **Transaction Safety** | Multi-step operations are atomic; partial failures roll back correctly |
| **Performance** | Queries execute within acceptable time thresholds under load |
| **Security** | Sensitive data (passwords, PII) is properly encrypted or hashed at rest |

---

### The ACID Transaction Model

In database management systems, **ACID** defines the four essential properties that guarantee reliable and safe transaction processing. Understanding ACID is fundamental for a QA engineer because it defines exactly what you are testing when you validate database behaviour.

> [!NOTE]
> A **transaction** is any logical unit of work that reads or writes to a database. For example, a bank transfer is a single transaction consisting of two writes: debit Account A and credit Account B.

| Property | Full Name | Core Guarantee | QA Test Scenario |
| :--- | :--- | :--- | :--- |
| **A** | Atomicity | "All or Nothing." If any step in a transaction fails, all prior steps are rolled back — the database is returned to its state before the transaction began. | Simulate a payment failure mid-transaction. Verify the debit from the customer account was rolled back. |
| **C** | Consistency | A transaction can only bring the database from one valid state to another. No rule, constraint, or trigger can be violated by a committed transaction. | Attempt to insert a row that violates a `CHECK` constraint. Verify the insert is rejected and no partial data is written. |
| **I** | Isolation | Concurrent transactions must execute as if they were sequential. Intermediate states of a transaction are invisible to other concurrent transactions. | Simulate two users purchasing the last item in stock simultaneously. Verify only one succeeds. |
| **D** | Durability | Once a transaction is committed, the data persists permanently — even in the event of a system crash, power failure, or server restart. | Commit a transaction, then simulate an abrupt server restart. Verify the committed data is present after recovery. |

---

### SQL vs. NoSQL Databases

Software applications use one of two primary database paradigms. As a QA engineer, you must understand both to write effective verification tests.

| Architectural Dimension | Relational Databases (SQL) | Non-Relational Databases (NoSQL) |
| :--- | :--- | :--- |
| **Data Model** | Tabular — rows and columns organized into structured tables. | Document-based, Key-Value, Graph, or Wide-Column stores. |
| **Schema** | Rigid, predefined schema declared before data can be inserted. | Dynamic schema — documents in the same collection can have different fields. |
| **Scalability Strategy** | Vertical scaling — increase CPU/RAM on a single powerful server. | Horizontal scaling — distribute load across many commodity servers. |
| **Transaction Model** | Full ACID compliance; ideal for financial and mission-critical data. | BASE model (Basically Available, Soft state, Eventually consistent); optimized for speed and scale. |
| **Relationship Handling** | Native JOIN operations across normalized tables. | Embedded documents or application-level references (`$lookup`). |
| **Query Language** | Standardized SQL (Structured Query Language). | Database-specific query APIs (e.g., MongoDB Query Language). |
| **Primary Use Cases** | Banking systems, ERP, e-commerce, HR platforms. | Real-time analytics, IoT data, content management, social networks. |
| **Leading Examples** | MySQL, PostgreSQL, SQL Server, Oracle Database. | MongoDB, Redis, Apache Cassandra, Amazon DynamoDB. |

> [!TIP]
> In practice, many modern enterprise systems use **both** — a relational database for transactional data and a NoSQL store for high-speed caching or unstructured content. A well-rounded QA engineer must be proficient with both paradigms.

---

### SQL Schemas, Tables & Key Constraints

A **database schema** is the formal blueprint of how data is organized — it defines tables, column names, data types, and the rules (constraints) that govern what data is acceptable.

#### Key Types & Their QA Significance

| Constraint Type | Definition | QA Verification |
| :--- | :--- | :--- |
| **Primary Key (PK)** | Uniquely identifies each row in a table. Cannot be `NULL` or duplicated. | Attempt to insert a duplicate PK value — must be rejected with a constraint error. |
| **Foreign Key (FK)** | A column that references the PK of another table, enforcing a relational link. | Delete a parent record — verify the database enforces `CASCADE`, `RESTRICT`, or `SET NULL` as configured. |
| **Unique Constraint** | Ensures all values in a column are distinct across all rows. | Attempt to register two users with the same email address — the second insert must fail. |
| **Not Null Constraint** | Prevents a column from accepting `NULL` as a value. | Submit a form without a required field — verify the database rejects the insert. |
| **Check Constraint** | Restricts column values to meet a defined logical condition. | Attempt to insert a negative product price — the constraint must reject the record. |
| **Default Constraint** | Assigns a predefined value to a column when no value is explicitly provided. | Insert a record omitting the constrained column — verify the default value is applied. |

#### Example E-Commerce Database Schema

```text
  ┌──────────────────────────────────────────────────────────────────────┐
  │                  E-COMMERCE DATABASE SCHEMA                          │
  └──────────────────────────────────────────────────────────────────────┘

  users                          products                   orders
  ┌──────────────┬────────────┐  ┌──────────────┬──────────┐  ┌──────────────┬──────────┬──────────┬───────────┐
  │ user_id  (PK)│ INT        │  │ product_id(PK│ INT      │  │ order_id (PK)│ INT      │          │           │
  │ first_name   │ VARCHAR(50)│  │ product_name │ VARCHAR  │  │ user_id  (FK)│ INT ─────┼──────────► users.user_id
  │ email    (UQ)│ VARCHAR(100│  │ price   (CHK)│ DECIMAL  │  │ product_id(FK│ INT ─────┼──────────► products.product_id
  │ password     │ VARCHAR(255│  │ stock_qty    │ INT      │  │ quantity     │ INT      │          │           │
  │ created_at   │ DATETIME   │  │ is_active    │ BOOLEAN  │  │ total_amount │ DECIMAL  │          │           │
  └──────────────┴────────────┘  └──────────────┴──────────┘  │ order_status │ VARCHAR  │          │           │
                                                               │ created_at   │ DATETIME │          │           │
                                                               └──────────────┴──────────┴──────────┴───────────┘
  PK = Primary Key  │  FK = Foreign Key  │  UQ = Unique  │  CHK = Check Constraint
```

---

### Core SQL Queries for QA

As a QA engineer, you will regularly write SQL queries to directly verify whether the application has correctly persisted data to the database after a UI action or API call.

#### The QA Verification Flow

```text
  ┌────────────────────────┐         POST /api/register
  │    APPLICATION / UI    │ ──────── { name: "Noman", email: "noman@test.com" } ────────►
  └────────────────────────┘
                                                                              │
                                                                              ▼
  ┌────────────────────────┐         SELECT * FROM users
  │       DATABASE         │ ◄─────── WHERE email = 'noman@test.com'; ────────────────────
  └────────────────────────┘
  (QA engineer runs this query to confirm the data was saved correctly)
```

#### 1. SELECT — Read & Retrieve Data

`SELECT` is the most frequently used SQL command in a QA context. It reads data without modifying anything, making it completely safe to run in any environment.

```sql
-- Retrieve all columns and rows from the users table
SELECT * FROM users;

-- Retrieve specific columns only
SELECT first_name, email, created_at
FROM users;

-- Apply a filter condition
SELECT first_name, email
FROM users
WHERE registration_date > '2026-01-01';

-- Count total records
SELECT COUNT(*) AS total_users FROM users;

-- Sort results
SELECT first_name, email
FROM users
ORDER BY created_at DESC;

-- Limit output to the 10 most recent users
SELECT first_name, email, created_at
FROM users
ORDER BY created_at DESC
LIMIT 10;
```

#### 2. INSERT — Write New Data

`INSERT` creates a new row in a table. In QA, this is typically used to seed test data before running a test scenario.

```sql
-- Insert a single new product record
INSERT INTO products (product_name, price, stock_quantity, is_active)
VALUES ('Premium Wireless Mouse', 45.99, 150, TRUE);

-- Insert multiple records in a single statement
INSERT INTO products (product_name, price, stock_quantity, is_active)
VALUES
  ('Mechanical Keyboard',  89.99,  75, TRUE),
  ('USB-C Hub',            29.99, 200, TRUE),
  ('27" Monitor',         299.99,  40, TRUE);
```

#### 3. UPDATE — Modify Existing Data

`UPDATE` modifies the values of one or more columns in existing rows.

```sql
-- Update the price of a specific product
UPDATE products
SET price = 39.99
WHERE product_id = 105;

-- Update multiple columns simultaneously
UPDATE users
SET first_name = 'Abdullah',
    email      = 'abdullah.new@test.com'
WHERE user_id = 42;

-- Deactivate all out-of-stock products
UPDATE products
SET is_active = FALSE
WHERE stock_quantity = 0;
```

> [!WARNING]
> **Always use a `WHERE` clause with `UPDATE` and `DELETE`.** Omitting the `WHERE` clause will apply the change to **every single row** in the table. As a best practice, always run the equivalent `SELECT` query first to confirm which rows will be affected before executing the write operation.

#### 4. DELETE — Remove Data

`DELETE` permanently removes one or more rows from a table.

```sql
-- Remove a single, specific order
DELETE FROM orders
WHERE order_id = 5001;

-- Remove all cancelled orders
DELETE FROM orders
WHERE order_status = 'Cancelled';

-- QA Safety Pattern: Preview before deleting
-- Run this SELECT first to confirm target rows:
SELECT * FROM orders WHERE order_status = 'Cancelled';
-- If the result set looks correct, then run the DELETE.
```

#### 5. Aggregation Functions for QA

Aggregation functions summarize data and are invaluable for verifying business logic calculations.

```sql
-- Verify the total revenue figure matches the dashboard
SELECT SUM(total_amount) AS total_revenue FROM orders
WHERE order_status = 'Completed';

-- Check the average order value
SELECT AVG(total_amount) AS average_order_value FROM orders;

-- Verify the count of pending orders
SELECT COUNT(*) AS pending_orders FROM orders
WHERE order_status = 'Pending';

-- Find the highest and lowest product prices
SELECT MAX(price) AS max_price, MIN(price) AS min_price FROM products;
```

---

### SQL JOINs Explained

In a normalized relational database, data is intentionally distributed across multiple tables to eliminate redundancy. **JOINs** are the mechanism that reassembles related data from separate tables into a single, unified result set for querying and verification.

#### Table Relationship Diagram

```text
  users                               orders
  ┌──────────────┬────────────┐       ┌──────────────┬────────────┐
  │ user_id (PK) │ first_name │       │ order_id (PK)│ total      │
  │            1 │ John       │       │         5001 │ 199.98     │
  │            2 │ Jane       │       │         5002 │  49.99     │
  │            3 │ Bob        │       │         5003 │ 299.99     │
  └──────┬───────┴────────────┘       └──────────────┴──────┬─────┘
         │                                                   │
         └──────────── user_id (FK) ────────────────────────┘
                       (Links each order to its owner)
```

#### JOIN Type Visual Reference

```text
  users (A)   orders (B)

    ┌───┐ ┌───┐                ┌───┐ ┌───┐                ┌───┐ ┌───┐                ┌───┐ ┌───┐
    │   │█│   │                │███│█│   │                │   │█│███│                │███│█│███│
    │   │█│   │                │███│█│   │                │   │█│███│                │███│█│███│
    └───┘ └───┘                └───┘ └───┘                └───┘ └───┘                └───┘ └───┘
    INNER JOIN                  LEFT JOIN                 RIGHT JOIN               FULL OUTER JOIN
  (matching rows only)     (all A + matching B)      (matching A + all B)         (all rows both)
```

#### 1. INNER JOIN — Matching Rows Only

Returns only the rows where the join condition is satisfied in **both** tables. Rows with no match on either side are excluded.

```sql
-- Get a combined view of users and their orders
SELECT
    u.first_name,
    u.email,
    o.order_id,
    o.total_amount,
    o.order_status
FROM users u
INNER JOIN orders o ON u.user_id = o.user_id;
```

**QA Use Case:** Verify that every completed order in the `orders` table has a valid, existing user record.

#### 2. LEFT (OUTER) JOIN — All Left Rows + Matches

Returns **all rows** from the left table, plus the matching rows from the right table. If there is no match, the right-side columns return `NULL`.

```sql
-- Find all users, including those who have never placed an order
SELECT
    u.first_name,
    u.email,
    o.order_id,      -- Will be NULL for users with no orders
    o.total_amount   -- Will be NULL for users with no orders
FROM users u
LEFT JOIN orders o ON u.user_id = o.user_id;
```

**QA Use Case:** Identify newly registered users who have not yet placed an order. A `NULL` `order_id` confirms they have no purchase history.

#### 3. RIGHT (OUTER) JOIN — Matches + All Right Rows

Returns **all rows** from the right table, plus the matching rows from the left table. Columns from the left side return `NULL` where no match exists.

```sql
-- Find all orders, including any that may be orphaned (no matching user)
SELECT
    u.first_name,    -- Will be NULL if no matching user exists
    o.order_id,
    o.total_amount
FROM users u
RIGHT JOIN orders o ON u.user_id = o.user_id;
```

**QA Use Case:** Detect **orphaned order records** — orders that reference a `user_id` that no longer exists in the `users` table. These indicate a referential integrity failure.

#### 4. FULL (OUTER) JOIN — All Rows from Both Tables

Returns all rows from both tables. Where there is no match, the non-matching side returns `NULL`.

```sql
-- Get a complete picture of all users and all orders regardless of match
SELECT
    u.first_name,
    o.order_id,
    o.total_amount
FROM users u
FULL OUTER JOIN orders o ON u.user_id = o.user_id;
```

**QA Use Case:** Full audit — simultaneously detect users with no orders **and** orders with no users in a single query.

#### JOIN Selection Guide

| Scenario | Recommended JOIN |
| :--- | :--- |
| Verify order data with user name shown | `INNER JOIN` |
| Find users who have never purchased anything | `LEFT JOIN` (filter where right side IS NULL) |
| Detect orphaned records in a child table | `RIGHT JOIN` (filter where left side IS NULL) |
| Full data audit across two related tables | `FULL OUTER JOIN` |

---

## Topic 2: Practical Database Testing & NoSQL

### Hands-On QA Verification Workflow

#### The API-to-Database Cross-Verification Pattern

The most fundamental database testing technique is the **cross-system verification**: trigger an action through the API or UI, then query the database directly to confirm the data was persisted correctly.

```text
  ┌────────────────────────────────────────────────────────────────┐
  │             API-TO-DATABASE VERIFICATION WORKFLOW              │
  └────────────────────────────────────────────────────────────────┘

  STEP 1: CAPTURE BASELINE
  ├─ Query: SELECT stock_quantity FROM inventory WHERE product_id = 12;
  └─ Result: 50 units in stock

  STEP 2: TRIGGER THE APPLICATION ACTION
  ├─ Send: POST /api/orders  {productId: 12, quantity: 2}
  └─ Response: HTTP 201 Created  {orderId: 5001, total: 199.98}

  STEP 3: VERIFY API RESPONSE DATA
  ├─ Status code is 201 Created
  ├─ orderId is present and is a number
  └─ total is 199.98 (correct: 2 × $99.99)

  STEP 4: QUERY DATABASE DIRECTLY
  ├─ Query: SELECT * FROM orders WHERE order_id = 5001;
  ├─ Query: SELECT stock_quantity FROM inventory WHERE product_id = 12;
  └─ Query: SELECT * FROM users WHERE user_id = 123; (FK validation)

  STEP 5: CROSS-VALIDATE RESULTS
  ├─ Order row exists in the database
  ├─ All order fields match the API request payload exactly
  ├─ Stock quantity is now 48 (50 - 2 = 48 ✓)
  └─ Foreign key user_id = 123 exists in users table
```

#### Practical SQL QA Verification Script

```sql
-- ════════════════════════════════════════════════════════
--  QA VERIFICATION SCRIPT — Order Creation Test
--  Test Case: TC-047 | Order flow — purchase 2 Laptops
-- ════════════════════════════════════════════════════════

-- Step 1: Capture pre-test inventory baseline
SELECT product_id, product_name, stock_quantity
FROM inventory
WHERE product_id = 12;
-- Expected: stock_quantity = 50

-- (Trigger API: POST /api/orders — purchase 2 Laptops)

-- Step 2: Verify the order record was created
SELECT order_id, user_id, total_amount, order_status, created_at
FROM orders
WHERE order_id = 5001;
-- Expected: Row exists, total_amount = 199.98, status = 'Pending'

-- Step 3: Verify inventory was decremented correctly
SELECT stock_quantity
FROM inventory
WHERE product_id = 12;
-- Expected: stock_quantity = 48 (50 - 2)

-- Step 4: Verify relational integrity
SELECT u.user_id, u.first_name, u.email
FROM users u
INNER JOIN orders o ON u.user_id = o.user_id
WHERE o.order_id = 5001;
-- Expected: Returns 1 row — the user who placed the order
```

#### SQL Constraint Verification Test Cases

| Test Case | SQL to Execute | Expected Outcome |
| :--- | :--- | :--- |
| Duplicate email rejection | `INSERT INTO users (email) VALUES ('existing@test.com');` | Error: Unique constraint violation |
| Null in required field | `INSERT INTO orders (user_id) VALUES (NULL);` | Error: NOT NULL constraint violation |
| Negative price rejection | `INSERT INTO products (price) VALUES (-9.99);` | Error: CHECK constraint violation |
| Orphaned record detection | `DELETE FROM users WHERE user_id = 1;` (with FK orders) | Error: FK constraint violation (or CASCADE delete) |
| Data truncation check | `INSERT INTO users (first_name) VALUES (REPEAT('A', 300));` | Error: Value exceeds VARCHAR limit |

---

### Introduction to NoSQL: MongoDB Basics

#### What is MongoDB?

**MongoDB** is the world's most widely used NoSQL database. It stores data as **BSON documents** (Binary JSON) within **collections**, making it highly flexible and naturally suited to hierarchical or schema-variable data.

| SQL Concept | MongoDB Equivalent | Description |
| :--- | :--- | :--- |
| Database | Database | The top-level container — same concept in both |
| Table | Collection | A group of related documents |
| Row | Document | A single record — a JSON-like object |
| Column | Field | A key-value pair within a document |
| Primary Key | `_id` field | Auto-generated `ObjectId` by default |
| Table Join | Embedded Doc / `$lookup` | Data is either embedded directly or joined via aggregation |
| INDEX | Index | Same concept — speeds up query performance |

#### Example MongoDB Document Structure

MongoDB's document model allows nested objects and arrays within a single record, eliminating the need for multiple JOIN operations in many scenarios.

```json
{
  "_id": "ObjectId(\"65c1bc23e4b02931fc801a22\")",
  "name": "Abdullah Al Noman",
  "email": "noman@test.com",
  "role": "QA Engineer",
  "age": 28,
  "skills": ["Manual Testing", "API Testing", "SQL", "MongoDB"],
  "address": {
    "street": "45 Mirpur Road",
    "city": "Dhaka",
    "country": "Bangladesh",
    "zip": "1216"
  },
  "certifications": [
    { "name": "ISTQB Foundation", "year": 2025 },
    { "name": "Postman API Expert", "year": 2026 }
  ],
  "is_active": true,
  "created_at": "2026-01-15T09:00:00Z"
}
```

> [!NOTE]
> **Key Structural Difference:** In a relational database, this data would be split across at least three tables (`users`, `user_skills`, `user_certifications`) and require JOIN operations to reassemble. In MongoDB, all of it lives in a **single document** — a fundamental architectural trade-off between normalization and read performance.

---

### MongoDB CRUD Operations

MongoDB operations are executed using **mongosh** (the official CLI shell) or **MongoDB Compass** (the official GUI application). The syntax uses JavaScript-style method calls on collection objects.

#### 1. Create — Insert Documents

```javascript
// Insert a single document
db.users.insertOne({
  name: "Sadia Islam",
  email: "sadia@test.com",
  role: "QA Engineer",
  age: 26,
  skills: ["Selenium", "Postman"],
  is_active: true,
  created_at: new Date()
});
// Returns: { acknowledged: true, insertedId: ObjectId("...") }

// Insert multiple documents in a single operation
db.users.insertMany([
  { name: "Rahim Uddin",  email: "rahim@test.com",  role: "Developer" },
  { name: "Karim Hossain", email: "karim@test.com", role: "DevOps"    }
]);
// Returns: { acknowledged: true, insertedCount: 2 }
```

#### 2. Read — Query Documents

```javascript
// Retrieve all documents in the collection
db.users.find();

// Retrieve with a specific filter (equality match)
db.users.find({ email: "sadia@test.com" });

// Combine multiple filter conditions
db.users.find({ role: "QA Engineer", age: { $gt: 25 } });
// $gt = "greater than"

// Project specific fields only (1 = include, 0 = exclude)
db.users.find(
  { is_active: true },
  { name: 1, email: 1, role: 1, _id: 0 }
);

// Sort results (1 = ascending, -1 = descending)
db.users.find().sort({ created_at: -1 });

// Limit results
db.users.find().limit(10);

// Count matching documents
db.users.countDocuments({ role: "QA Engineer" });
```

**MongoDB Comparison Operators Reference:**

| Operator | Meaning | Example |
| :---: | :--- | :--- |
| `$eq` | Equal to | `{ age: { $eq: 28 } }` |
| `$ne` | Not equal to | `{ role: { $ne: "Admin" } }` |
| `$gt` | Greater than | `{ price: { $gt: 100 } }` |
| `$gte` | Greater than or equal | `{ rating: { $gte: 4 } }` |
| `$lt` | Less than | `{ stock: { $lt: 10 } }` |
| `$lte` | Less than or equal | `{ discount: { $lte: 50 } }` |
| `$in` | Matches any value in array | `{ role: { $in: ["QA", "Dev"] } }` |
| `$exists` | Field exists in document | `{ phone: { $exists: true } }` |

#### 3. Update — Modify Documents

```javascript
// Update a single document — change role
db.users.updateOne(
  { email: "sadia@test.com" },     // Filter: which document
  { $set: { role: "Senior QA" } }  // Update: what to change
);

// Update multiple documents at once
db.users.updateMany(
  { role: "QA Engineer" },
  { $set: { department: "Quality Assurance" } }
);

// Increment a numeric field
db.inventory.updateOne(
  { product_id: 12 },
  { $inc: { stock_quantity: -2 } }  // Decrement by 2 (order fulfillment)
);

// Add an item to an array field
db.users.updateOne(
  { email: "sadia@test.com" },
  { $push: { skills: "Cypress" } }  // Add "Cypress" to the skills array
);
```

#### 4. Delete — Remove Documents

```javascript
// Delete a single specific document by ObjectId
db.users.deleteOne({
  _id: ObjectId("65c1bc23e4b02931fc801a22")
});

// Delete a single document by a field filter
db.users.deleteOne({ email: "sadia@test.com" });

// Delete all documents matching a condition
db.orders.deleteMany({ status: "Cancelled" });

// ⚠ Delete ALL documents in a collection (use with extreme caution)
db.temp_test_data.deleteMany({});
```

> [!WARNING]
> `db.collection.deleteMany({})` with an empty filter `{}` deletes **every document** in the collection. Always apply a specific filter condition. When working in shared environments, run `find({})` with the same filter first to preview what will be deleted.

---

### SQL vs. NoSQL QA Testing Scenarios

A comprehensive database test suite must address the specific failure modes and validation requirements of each database paradigm.

#### SQL Database QA Testing Checklist

| # | Test Area | Test Description | Verification Method |
| :---: | :--- | :--- | :--- |
| 1 | **Data Integrity** | Verify that a value inserted via the API exactly matches the value stored in the DB | `SELECT` the row; compare each field against the API payload |
| 2 | **Null Constraint** | Attempt to submit a form without a required field | Confirm DB rejects with a NOT NULL constraint error |
| 3 | **Unique Constraint** | Register two users with the same email address | Confirm second insert is rejected with a duplicate key error |
| 4 | **Check Constraint** | Submit a product with a negative price | Confirm DB rejects with a CHECK constraint violation |
| 5 | **Data Truncation** | Insert a string exceeding a column's `VARCHAR(n)` limit | Confirm DB rejects (or truncates, depending on config) |
| 6 | **Referential Integrity** | Delete a parent `users` record that has child `orders` | Confirm CASCADE behavior matches the schema specification |
| 7 | **Orphaned Records** | Query for order records where the linked `user_id` no longer exists | `RIGHT JOIN users ON orders` — any `NULL` user side is an orphan |
| 8 | **Transaction Rollback** | Simulate a failure during a multi-step transaction | Confirm all steps from the transaction are reversed |
| 9 | **Calculation Accuracy** | Verify order `total_amount` is arithmetically correct | `SELECT quantity * unit_price` and compare to stored `total_amount` |
| 10 | **Audit Trail** | Verify `created_at` and `updated_at` timestamps are set correctly | `SELECT` the record and validate timestamp fields are populated |

#### MongoDB NoSQL QA Testing Checklist

| # | Test Area | Test Description | Verification Method |
| :---: | :--- | :--- | :--- |
| 1 | **Document Persistence** | Verify an API-created document exists in the collection | `db.collection.findOne({ field: value })` |
| 2 | **Data Type Validation** | Attempt to write a string to a date field (if schema validation is active) | Confirm the insert is rejected with a validation error |
| 3 | **Array Field Operations** | Add an item to an array field via the API | `find()` the document and confirm the array contains the new item |
| 4 | **Nested Document Integrity** | Update a nested field via the API | Confirm the correct nested field changed and sibling fields are intact |
| 5 | **Index Verification** | Query a high-traffic field without an index; compare response times with an index enabled | `db.collection.getIndexes()` and use `explain("executionStats")` |
| 6 | **Optional Field Handling** | Submit a document missing optional nested fields | Confirm the application processes the document without errors |
| 7 | **Upsert Behaviour** | Send an update for a document ID that does not exist | Confirm `upsert: true` creates the document; `upsert: false` returns no-op |
| 8 | **`$inc` Accuracy** | Purchase an item via the API; verify stock decremented correctly | `db.inventory.findOne({ product_id: X })` — check `stock_quantity` |
| 9 | **Duplicate `_id` Rejection** | Attempt to insert a document with an existing `_id` | Confirm MongoDB throws a duplicate key exception |
| 10 | **Concurrent Write Safety** | Simulate two simultaneous purchases of the last unit in stock | Confirm only one succeeds; final `stock_quantity` is `0`, not negative |

---

## Key Takeaways & Self-Assessment

### Key Takeaways

* **Database testing** is an essential QA discipline that catches defects invisible at the UI level — data corruption, orphaned records, constraint violations, and calculation errors.
* **ACID properties** (Atomicity, Consistency, Isolation, Durability) define the four guarantees of safe transaction processing. Every database test implicitly validates one or more of these properties.
* **SQL** is the language of relational databases. A QA engineer must be proficient in `SELECT`, `INSERT`, `UPDATE`, `DELETE`, and JOIN operations to effectively verify backend data.
* **SQL JOINs** are the mechanism for cross-table verification. `INNER JOIN` finds matching records; `LEFT JOIN` detects records with no corresponding partner; `RIGHT JOIN` detects orphaned child records.
* **MongoDB** stores data as flexible JSON-like documents within collections, offering a different paradigm from tabular SQL. QA engineers must know how to perform CRUD operations using its query language and comparison operators.
* **API-to-Database cross-verification** is the gold standard for backend QA: trigger an action through the API, then query the database directly to confirm the correct data was persisted, all relationships are intact, and business logic (e.g., calculations, stock decrements) is accurate.

---

### Self-Assessment Review Questions

1. A bank transfer debits Account A but fails before crediting Account B. The entire transaction is rolled back. Which ACID property is responsible for this behavior?

2. Write a SQL `SELECT` query that retrieves all columns from the `orders` table where `order_status` is `'Pending'`, sorted by `created_at` descending.

3. You run a `LEFT JOIN` between `users` and `orders`. Some rows return `NULL` for all order columns. What does this tell you about those users?

4. Write a MongoDB query to find all documents in the `products` collection where `price` is less than `$10.00`.

5. A QA engineer submits a registration form with no email address. The UI shows a success message. What SQL query would you run to verify whether the record was actually saved to the database?

6. What is the difference between `db.users.deleteOne()` and `db.users.deleteMany()` in MongoDB? When would you use each?

7. You need to verify that deleting a user account also correctly handles all related order records. Which SQL JOIN type would help you detect orphaned orders after the deletion?

---

**Document Control Metadata**

* **Target Knowledge Level:** Beginner to Intermediate Quality Assurance Practitioners
* **Estimated Self-Guided Completion Time:** 120 Minutes
* **Temporal Status Baseline:** 2026 Release Cycle Specifications
