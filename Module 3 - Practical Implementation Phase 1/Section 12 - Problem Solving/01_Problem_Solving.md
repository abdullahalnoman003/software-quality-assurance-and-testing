
# Section 12: Problem Solving for SQA Engineers

> [!NOTE]
> Writing automation code is not primarily about syntax  it is about logic. SQA engineers write scripts to validate business rules, calculate pricing, parse API responses, verify data integrity, and check input formats. This section details how to systematically analyze problem statements, identify edge cases and boundaries, plan solutions using pseudocode and flowcharts, and implement them in JavaScript with professional code quality.

---

## Table of Contents

### Topic 1: Problem Analysis & Solution Planning
  * [The Input-Constraint-Output Framework](#the-input-constraint-output-framework)
  * [Identifying Edge Cases & Boundary Conditions](#identifying-edge-cases--boundary-conditions)
  * [Drafting Solutions with Pseudocode & Flowcharts](#drafting-solutions-with-pseudocode--flowcharts)
  * [The Pre-Coding Analytical Checklist](#the-pre-coding-analytical-checklist)

### Topic 2: Logic Implementation & Practice
  * [Problem 1: E-Commerce Membership Discount Calculator](#problem-1-e-commerce-membership-discount-calculator)
  * [Problem 2: Password Strength Validator](#problem-2-password-strength-validator)
  * [Problem 3: Email Syntax Validator](#problem-3-email-syntax-validator)
  * [Practice Exercises](#practice-exercises)

* [Key Takeaways & Self-Assessment](#key-takeaways--self-assessment)

---

## Topic 1: Problem Analysis & Solution Planning

### The Input-Constraint-Output Framework

When presented with a business rule to implement, the first discipline is to resist writing code immediately. Every problem must be decomposed into three fundamental components before any implementation begins.

```text
  ┌──────────────────────────────────────────────────────────────┐
  │                          INPUTS                              │
  │  What data enters the system?                                │
  │  Define: data types, expected formats, valid ranges          │
  │  Example: cartTotal (Number, must be >= 0)                   │
  └─────────────────────────────┬────────────────────────────────┘
                                |
                                v
  ┌──────────────────────────────────────────────────────────────┐
  │                        CONSTRAINTS                           │
  │  What rules and boundaries must the logic enforce?           │
  │  Define: limits, conditions, exclusions, business rules      │
  │  Example: No discount applies if cartTotal < $10             │
  └─────────────────────────────┬────────────────────────────────┘
                                |
                                v
  ┌──────────────────────────────────────────────────────────────┐
  │                          OUTPUTS                             │
  │  What must the function return?                              │
  │  Define: data type, format, valid range, error format        │
  │  Example: Number (final price, 2 decimal places) or String   │
  └──────────────────────────────────────────────────────────────┘
```

#### Worked Example: Delivery Fee Calculation

**Business Rule:** Standard delivery fee is $5.00. If the cart total is $50.00 or greater, delivery is free.

| Component | Analysis |
| :--- | :--- |
| **Input** | `cartTotal`  a number representing the order value in USD |
| **Input Constraints** | Must be a valid number; must not be negative |
| **Business Rule** | If `cartTotal >= 50`, fee = 0. If `cartTotal < 50`, fee = 5. |
| **Output** | `deliveryFee`  a number: either `0` or `5` |
| **Error Output** | If `cartTotal < 0` or is not a number, return an error message string |

---

### Identifying Edge Cases & Boundary Conditions

An **edge case** is a scenario that occurs at the extreme boundary of valid input ranges or at unexpected combinations of conditions. The overwhelming majority of software defects are discovered at these boundaries  not in the typical happy path.

#### The Four Categories of Edge Cases

| Category | Description | Example for a Quantity Input Field |
| :--- | :--- | :--- |
| **Null / Empty Input** | The user submits nothing at all | `quantity = null`, `quantity = ""`, `quantity = undefined` |
| **Boundary Values** | Values exactly at, just below, and just above the valid limit | `quantity = 0`, `quantity = 1`, `quantity = 99`, `quantity = 100`, `quantity = 101` |
| **Data Type Mismatch** | The wrong data type is provided | `quantity = "five"`, `quantity = true`, `quantity = []` |
| **Special Characters & Format** | Unexpected characters in text fields | `email = "user@"`, `email = "@domain.com"`, `name = "<script>alert(1)</script>"` |

#### Boundary Value Analysis (BVA)  Systematic Edge Case Identification

BVA is a formal test design technique that targets values at partition boundaries where logic transitions occur.

**Example:** A discount applies when `cartTotal >= 50`.

| Test Input | Category | Expected Behavior |
| :---: | :--- | :--- |
| `-1` | Invalid (below zero) | Return error: "Invalid cart amount" |
| `0` | Boundary (minimum valid) | No discount; fee = $5.00 |
| `9.99` | Below discount threshold | No discount applied |
| `49.99` | Just below boundary | No discount; fee = $5.00 |
| `50.00` | Exact boundary value | Discount applies; fee = $0.00 |
| `50.01` | Just above boundary | Discount applies; fee = $0.00 |
| `999.99` | High valid value | Discount applies; fee = $0.00 |

> [!IMPORTANT]
> Always test the value **at** the boundary (`50.00`), **one unit below** (`49.99`), and **one unit above** (`50.01`). These three points reveal the most off-by-one logic errors that plague boundary conditions in production code.

---

### Drafting Solutions with Pseudocode & Flowcharts

**Pseudocode** is a structured, language-agnostic description of an algorithm written in plain English. It allows you to validate logic completeness before investing time in syntax.

#### Example: Delivery Fee Calculator  Full Analysis

**Step 1  Pseudocode:**

```text
FUNCTION calculateDeliveryFee(cartTotal):

  1. VALIDATE INPUT
     - IF cartTotal is NOT a number: RETURN "Error: Cart total must be a number"
     - IF cartTotal < 0:            RETURN "Error: Cart total cannot be negative"

  2. APPLY BUSINESS RULE
     - IF cartTotal >= 50:
         SET deliveryFee = 0
     - ELSE:
         SET deliveryFee = 5

  3. RETURN deliveryFee
```

**Step 2  Flowchart:**

```text
                     [ START ]
                         |
                         v
               [ Receive: cartTotal ]
                         |
                         v
          / Is cartTotal a valid number? \
          \                              /
           No --------------------------> [ Return: "Error: Invalid input" ]
               |
               | Yes
               v
          / Is cartTotal < 0? \
          \                   /
           Yes --------------> [ Return: "Error: Cannot be negative" ]
               |
               | No
               v
          / Is cartTotal >= 50? \
          \                     /
           Yes ----------------> [ deliveryFee = 0 ]
               |                          |
               | No                       |
               v                          |
      [ deliveryFee = 5 ] ----------------+
               |
               v
       [ Return: deliveryFee ]
               |
               v
             [ END ]
```

**Step 3  Manual Trace Verification (before writing code):**

| `cartTotal` Input | Path Taken | Expected Output |
| :---: | :--- | :--- |
| `"hello"` | Failed type check | Error message |
| `-10` | Failed negative check | Error message |
| `0` | Valid; below threshold | `5` |
| `49.99` | Valid; below threshold | `5` |
| `50.00` | Valid; at threshold | `0` |
| `150.00` | Valid; above threshold | `0` |

---

### The Pre-Coding Analytical Checklist

Before writing any implementation code, complete this checklist to validate your solution design:

- [ ] Have I listed every valid input format and data type?
- [ ] Have I identified the minimum and maximum valid input values?
- [ ] Have I accounted for null, undefined, and empty string inputs?
- [ ] Have I tested my logic mentally with at least three sample inputs?
- [ ] Have I covered all boundary values (at, below, and above each threshold)?
- [ ] Have I defined what the function should return for every valid path?
- [ ] Have I defined what the function should return for every error path?
- [ ] Does my pseudocode cover all logical branches without any unreachable code?

---

## Topic 2: Logic Implementation & Practice

### Problem 1: E-Commerce Membership Discount Calculator

#### Business Rules

A discount is applied at checkout based on the customer's membership tier:

| Condition | Rule |
| :--- | :--- |
| Cart total is below `$10.00` | No discount is applied regardless of membership type |
| Membership type is `"VIP"` | A 20% discount is applied |
| Membership type is `"Regular"` | A 5% discount is applied |
| Membership type is `"Guest"` or unrecognized | No discount is applied |
| Cart total is negative or not a number | Return an error message |

#### Edge Cases to Cover

| Input | Expected Output |
| :--- | :--- |
| `cartTotal = -5, "VIP"` | Error: Invalid total |
| `cartTotal = 8, "VIP"` | `8`  no discount (below $10 threshold) |
| `cartTotal = 100, "VIP"` | `80.00`  20% discount applied |
| `cartTotal = 40, "Regular"` | `38.00`  5% discount applied |
| `cartTotal = 40, "Guest"` | `40.00`  no discount for Guest tier |
| `cartTotal = "abc", "VIP"` | Error: Invalid total |

#### JavaScript Implementation

```javascript
/**
 * Calculates the final price after applying membership discount.
 * @param {number} cartTotal - The pre-discount cart value in USD.
 * @param {string} membershipType - The customer's membership tier: "VIP", "Regular", or "Guest".
 * @returns {number|string} The discounted final price, or an error message string.
 */
function calculateDiscount(cartTotal, membershipType) {
  // Guard: Input type validation
  if (typeof cartTotal !== "number" || isNaN(cartTotal)) {
    return "Error: cartTotal must be a valid number";
  }

  // Guard: Negative value rejection
  if (cartTotal < 0) {
    return "Error: cartTotal cannot be negative";
  }

  // Business Rule: No discount for orders under $10
  if (cartTotal < 10) {
    return cartTotal;
  }

  // Determine discount rate based on membership tier
  const discountRates = {
    VIP:     0.20,
    Regular: 0.05,
    Guest:   0.00
  };

  const discountRate = discountRates[membershipType] ?? 0; // Default to 0 for unknown types
  const finalPrice   = cartTotal * (1 - discountRate);

  return Number(finalPrice.toFixed(2)); // Round to 2 decimal places
}

// --- Test Suite ---
console.log(calculateDiscount(100, "VIP"));       // Expected: 80       (20% off)
console.log(calculateDiscount(40, "Regular"));    // Expected: 38       (5% off)
console.log(calculateDiscount(40, "Guest"));      // Expected: 40       (0% off)
console.log(calculateDiscount(8, "VIP"));         // Expected: 8        (below $10 threshold)
console.log(calculateDiscount(-5, "Regular"));    // Expected: "Error: cartTotal cannot be negative"
console.log(calculateDiscount("abc", "VIP"));     // Expected: "Error: cartTotal must be a valid number"
```

---

### Problem 2: Password Strength Validator

#### Business Rules

A user registration form must validate password strength against the following minimum criteria:

| Rule | Requirement |
| :--- | :--- |
| Minimum length | Must be at least 8 characters |
| Numeric digit | Must contain at least one digit (`0`–`9`) |
| Special character | Must contain at least one character from: `! @ # $ % ^ & * ( )` |
| Uppercase letter | Must contain at least one uppercase letter (`A`–`Z`) |

#### Edge Cases to Cover

| Input | Expected Output |
| :--- | :--- |
| `""` (empty string) | "Weak: Password cannot be blank" |
| `null` | "Weak: Password cannot be blank" |
| `"abc12"` | "Weak: Minimum 8 characters required" |
| `"abcdefgh12"` | "Weak: Must contain a special character" |
| `"abcdefgh!!"` | "Weak: Must contain at least one number" |
| `"abcdefgh1!"` | "Weak: Must contain an uppercase letter" |
| `"SecurePass123!"` | "Strong" |

#### JavaScript Implementation

```javascript
/**
 * Evaluates whether a password meets the minimum security requirements.
 * @param {string} password - The password string to validate.
 * @returns {string} "Strong" if all rules pass; a descriptive failure message otherwise.
 */
function validatePassword(password) {
  // Guard: Empty or non-string input
  if (!password || typeof password !== "string") {
    return "Weak: Password cannot be blank";
  }

  // Rule 1: Minimum length
  if (password.length < 8) {
    return "Weak: Password must be at least 8 characters long";
  }

  // Rule 2: Must contain at least one uppercase letter
  if (!/[A-Z]/.test(password)) {
    return "Weak: Password must contain at least one uppercase letter";
  }

  // Rule 3: Must contain at least one numeric digit
  if (!/\d/.test(password)) {
    return "Weak: Password must contain at least one number (0-9)";
  }

  // Rule 4: Must contain at least one special character
  if (!/[!@#$%^&*(),.?":{}|<>]/.test(password)) {
    return "Weak: Password must contain at least one special character (!@#$%...)";
  }

  return "Strong";
}

// --- Test Suite ---
console.log(validatePassword(null));              // Weak: Password cannot be blank
console.log(validatePassword("abc12"));           // Weak: Minimum 8 characters
console.log(validatePassword("abcdefgh12"));      // Weak: No uppercase letter
console.log(validatePassword("Abcdefgh12"));      // Weak: No special character
console.log(validatePassword("Abcdefgh!!"));      // Weak: No number
console.log(validatePassword("SecurePass123!"));  // Strong
```

> [!NOTE]
> **Why Regular Expressions?** Regular expressions (`/pattern/.test(string)`) provide a concise and reliable way to search for character patterns within strings. `/\d/` matches any digit character; `/[A-Z]/` matches any uppercase letter. Learning regex fundamentals is a high-value skill for any SQA engineer writing validation logic.

---

### Problem 3: Email Syntax Validator

#### Business Rules

Validate whether a string conforms to standard email syntax without relying on external libraries.

| Rule | Requirement |
| :--- | :--- |
| Contains exactly one `@` | Splitting by `@` must produce exactly 2 parts |
| Non-empty local part | The text before `@` must not be empty |
| Valid domain | The text after `@` must contain a `.` that is not the first or last character |
| No whitespace | The email must not contain any space characters |

#### Edge Cases to Cover

| Input | Expected Output | Reason |
| :--- | :---: | :--- |
| `"noman@test.com"` | `true` | Valid format |
| `""` (empty) | `false` | Empty input |
| `null` | `false` | Null input |
| `"noman@com"` | `false` | Domain has no dot |
| `"@test.com"` | `false` | Empty local part |
| `"noman@test."` | `false` | Domain ends with dot |
| `".noman@test.com"` | `false` | Local part starts with dot |
| `"noman@@test.com"` | `false` | Multiple @ symbols |
| `"noman @test.com"` | `false` | Contains a space |

#### JavaScript Implementation

```javascript
/**
 * Validates whether a string conforms to basic email address syntax.
 * @param {string} email - The email address string to validate.
 * @returns {boolean} true if the email passes all syntax rules; false otherwise.
 */
function isValidEmail(email) {
  // Guard: Empty or non-string input
  if (!email || typeof email !== "string") return false;

  // Guard: No whitespace allowed in an email address
  if (email.includes(" ")) return false;

  // Rule 1: Must contain exactly one "@" symbol
  const parts = email.split("@");
  if (parts.length !== 2) return false;

  const localPart = parts[0];
  const domain    = parts[1];

  // Rule 2: Local part must not be empty and must not start with a dot
  if (localPart.length === 0 || localPart.startsWith(".")) return false;

  // Rule 3: Domain must contain a dot, not at the start, not at the end
  if (
    !domain.includes(".")    ||
    domain.startsWith(".")   ||
    domain.endsWith(".")
  ) {
    return false;
  }

  return true;
}

// --- Test Suite ---
console.log(isValidEmail("noman@test.com"));      // true   valid
console.log(isValidEmail(null));                  // false  null input
console.log(isValidEmail(""));                    // false  empty string
console.log(isValidEmail("noman@com"));           // false  no dot in domain
console.log(isValidEmail("@test.com"));           // false  empty local part
console.log(isValidEmail("noman@test."));         // false  dot at end of domain
console.log(isValidEmail("noman@@test.com"));     // false  multiple @ symbols
console.log(isValidEmail("noman @test.com"));     // false  contains space
console.log(isValidEmail(".noman@test.com"));     // false  local part starts with dot
```

---

### Practice Exercises

The following exercises are designed to be implemented independently in a local Node.js project. Apply the Input-Constraint-Output framework and define edge cases before writing any code.

#### Exercise 1: Delivery ETA Calculator

**Function Signature:** `getDeliveryETA(distanceKm, orderHour24)`

**Business Rules:**

| Condition | Return Value |
| :--- | :--- |
| `orderHour24 >= 22` (after 10 PM) | `"Next Day"`  regardless of distance |
| `distanceKm < 5` | `"30 Minutes"` |
| `distanceKm >= 5` and `distanceKm <= 15` | `"1 Hour"` |
| `distanceKm > 15` | `"Next Day"` |
| `distanceKm < 0` or non-number | Return an error message |
| `orderHour24 < 0` or `> 23` | Return an error message |

**Required Test Cases to Write:**

| Input | Expected Output |
| :--- | :--- |
| `(3, 14)` | `"30 Minutes"` |
| `(10, 14)` | `"1 Hour"` |
| `(20, 14)` | `"Next Day"` |
| `(3, 23)` | `"Next Day"` (time override) |
| `(-5, 14)` | Error message |
| `(10, 25)` | Error message |

---

#### Exercise 2: Inventory Stock Decrementor

**Function Signature:** `deductStock(inventoryCount, purchaseQty)`

**Business Rules:**

| Condition | Return Value |
| :--- | :--- |
| `purchaseQty` is negative or not a number | Return an error message |
| `purchaseQty > inventoryCount` | Return `"Insufficient Stock"` |
| `purchaseQty === 0` | Return `inventoryCount` unchanged |
| Valid purchase | Return `inventoryCount - purchaseQty` |

**Required Test Cases to Write:**

| Input | Expected Output |
| :--- | :--- |
| `(50, 10)` | `40` |
| `(50, 0)` | `50` |
| `(50, 50)` | `0` |
| `(50, 51)` | `"Insufficient Stock"` |
| `(50, -5)` | Error message |
| `(50, "ten")` | Error message |

---

#### Exercise 3: Order Total Formatter

**Function Signature:** `formatOrderTotal(subtotal, taxRate, promoCode)`

**Business Rules:**

| Condition | Rule |
| :--- | :--- |
| `taxRate` is between 0 and 1 (e.g., `0.10` = 10%) | Applied to subtotal |
| `promoCode === "SAVE10"` | Apply an additional 10% off the subtotal before tax |
| `promoCode === "FREESHIP"` | Add 0 delivery instead of standard $5 fee |
| Invalid `subtotal` or `taxRate` | Return an error message |
| Output format | Return an object: `{ subtotal, discount, tax, total }` |

---

## Key Takeaways & Self-Assessment

### Key Takeaways

* Effective problem solving begins with decomposing every requirement into its **Inputs**, **Constraints**, and **Outputs** before writing a single line of code.
* **Edge cases**  null inputs, boundary values, type mismatches, and special characters  are where the majority of software defects originate. They must be systematically identified and tested.
* **Pseudocode** and **flowcharts** are invaluable pre-coding tools that expose logical gaps and unreachable branches before implementation begins. A defect caught in pseudocode is free; a defect caught in production is expensive.
* **Boundary Value Analysis (BVA)**  testing at, just below, and just above every logical threshold  is a formal and highly effective test design technique.
* **Regular expressions** (`/pattern/.test(string)`) are the standard tool for format validation in JavaScript. Familiarity with basic regex syntax is a required skill for any SQA engineer writing validation logic.
* Professional functions handle all error paths explicitly, returning descriptive error messages rather than silently returning incorrect values or crashing.

---

### Self-Assessment Review Questions

1. Define an "edge case" in the context of software testing. Identify three edge case scenarios for a user age input field on a registration form.

2. A business rule states: "Users with more than 500 loyalty points receive a 15% discount." Using Boundary Value Analysis, list every specific input value you would test and the expected output for each.

3. What are the advantages of writing pseudocode and tracing logic manually before beginning implementation? Describe a situation where this practice would prevent a production defect.

4. In the Password Strength Validator, explain what the regular expression `/[A-Z]/` tests for and why `.test(password)` is the appropriate method to call on it.

5. Trace the execution of `isValidEmail("noman@@test.com")` step by step through the `isValidEmail` function, identifying exactly which condition causes the function to return `false`.

6. For Exercise 1 (Delivery ETA Calculator), write the complete pseudocode before attempting any code. List all edge cases you would test before declaring the implementation complete.

---

**Document Control Metadata**

* **Target Knowledge Level:** Beginner to Intermediate Quality Assurance Practitioners
* **Estimated Self-Guided Completion Time:** 120 Minutes
* **Temporal Status Baseline:** 2026 Release Cycle Specifications
