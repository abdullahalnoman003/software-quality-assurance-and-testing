
# Section 11: Introduction to Programming for SQA

> [!NOTE]
> To transition from manual QA to automation testing  using frameworks such as Playwright, Cypress, or Selenium  a QA engineer must understand programming fundamentals. This section introduces JavaScript, the most widely used language in modern test automation, covering variables, data types, modern ES6 syntax, the Node.js runtime environment, and Object-Oriented Programming concepts applied directly to the Page Object Model pattern.

---

## Table of Contents

### Topic 1: JavaScript Fundamentals
  * [What is JavaScript & Why SQA Engineers Must Learn It](#what-is-javascript--why-sqa-engineers-must-learn-it)
  * [Setting Up Your JavaScript Environment](#setting-up-your-javascript-environment)
  * [Variables & Data Types](#variables--data-types)
  * [Modern ES6 Syntax](#modern-es6-syntax)
  * [ES6 Modules  Import & Export](#es6-modules--import--export)

### Topic 2: Node.js & Core Programming Concepts
  * [Understanding Node.js](#understanding-nodejs)
  * [Setting Up Node.js & Writing Your First Script](#setting-up-nodejs--writing-your-first-script)
  * [Control Flow  Conditionals & Loops](#control-flow--conditionals--loops)
  * [Object-Oriented Programming for SQA](#object-oriented-programming-for-sqa)
  * [Real-World Node.js API Verification Script](#real-world-nodejs-api-verification-script)

* [Key Takeaways & Self-Assessment](#key-takeaways--self-assessment)

---

## Topic 1: JavaScript Fundamentals

### What is JavaScript & Why SQA Engineers Must Learn It

**JavaScript (JS)** is a lightweight, interpreted, dynamically-typed programming language with first-class functions. Originally designed to add interactivity to web pages, it has expanded into backend development via Node.js and has become the dominant language for modern test automation frameworks.

#### Why JavaScript is the Primary Language for SQA Automation

| Reason | Detail |
| :--- | :--- |
| **Framework Adoption** | The leading automation frameworks  Playwright, Cypress, and WebdriverIO  are built natively in JavaScript and TypeScript. Proficiency in JS is a direct prerequisite. |
| **Postman Test Scripts** | All Pre-request Scripts and Test assertion scripts in Postman are written in JavaScript using the `pm` object API. |
| **TypeScript Compatibility** | TypeScript is a typed superset of JavaScript. Learning JS first provides a seamless path to TypeScript, which is increasingly preferred in enterprise automation projects. |
| **Frontend Diagnostics** | Understanding JavaScript enables QA engineers to read browser console errors, inspect network request payloads, and debug frontend issues independently during manual testing. |
| **Full-Stack Visibility** | With Node.js, the same language applies to both the browser client and the server backend  giving QA engineers end-to-end comprehension of the stack they are testing. |

---

### Setting Up Your JavaScript Environment

JavaScript requires no complex compiler installation to begin. Two methods are available, suited to different contexts.

#### Method 1: The Browser Developer Console (Immediate, No Setup)

1. Open Google Chrome.
2. Right-click anywhere on the page and select **Inspect**, or press `F12`.
3. Navigate to the **Console** tab.
4. Type any JavaScript expression and press `Enter` to execute it immediately.

```javascript
console.log("Hello, SQA World!");
// Output: Hello, SQA World!
```

This method is ideal for quick experiments and learning syntax without installing any software.

#### Method 2: VS Code & Node.js (Recommended for Automation Work)

This is the professional setup used in real test automation projects.

1. Download and install **Visual Studio Code** from [code.visualstudio.com](https://code.visualstudio.com).
2. Download and install **Node.js** (LTS version) from [nodejs.org](https://nodejs.org).
3. Create a new file named `test.js` in VS Code.
4. Open the integrated terminal (`Ctrl + `` ` ``) and run the script:

```bash
node test.js
```

> [!TIP]
> Install the **ESLint** and **Prettier** extensions in VS Code. ESLint catches syntax errors as you type; Prettier auto-formats your code on save. Both are standard in professional JavaScript projects.

---

### Variables & Data Types

A variable is a named reference to a memory location that stores a value. In JavaScript, the way you declare a variable determines its mutability and scope.

#### Variable Declaration Keywords

| Keyword | Mutability | Scope | Recommendation |
| :--- | :--- | :--- | :--- |
| `const` | Immutable  value cannot be reassigned | Block-scoped | Use by default for all declarations |
| `let` | Mutable  value can be reassigned | Block-scoped | Use only when reassignment is required |
| `var` | Mutable  hoisted to function/global scope | Function-scoped | Avoid  legacy syntax with unpredictable scoping behavior |

```javascript
const apiBaseUrl = "https://api.example.com"; // Cannot be changed  correct for fixed config values
let retryCount = 0;                            // Can be incremented  correct for mutable counters
retryCount = 1;                                // Valid reassignment with `let`
```

#### JavaScript Data Types

JavaScript has seven primitive data types plus two structural types:

```javascript
// --- Primitive Types ---
let testTitle   = "Login flow validation";     // String   text data, always quoted
let httpStatus  = 200;                         // Number   integers and floating-point values
let isPassing   = true;                        // Boolean  logical true or false
let errorCode   = null;                        // Null     intentional absence of a value
let notAssigned;                               // Undefined  declared but not yet assigned

// --- Structural Types ---

// Array  an ordered, zero-indexed list of values
const testDevices = ["iPhone 15", "Samsung S24", "Pixel 8", "iPad Air"];

// Object  an unordered collection of key-value pairs
const bugReport = {
  id:       "BUG-254",
  title:    "Checkout fails with valid VISA card on iOS build v2.1",
  severity: "Critical",
  priority: "Blocker",
  reporter: "noman@test.com"
};

// Accessing object properties
console.log(bugReport.title);     // Dot notation
console.log(bugReport["id"]);     // Bracket notation  useful when key is dynamic
```

#### Type Checking in QA Scripts

```javascript
console.log(typeof "hello");     // "string"
console.log(typeof 42);          // "number"
console.log(typeof true);        // "boolean"
console.log(typeof null);        // "object"  known JS quirk; null is not truly an object
console.log(typeof undefined);   // "undefined"
console.log(typeof []);          // "object"   use Array.isArray() to distinguish arrays
console.log(Array.isArray([]));  // true
```

---

### Modern ES6 Syntax

ECMAScript 6 (ES2015) introduced a set of syntax improvements that are now universally used in modern JavaScript and test automation code. Familiarity with these features is required to read and write professional automation scripts.

#### 1. Arrow Functions

Arrow functions provide a concise syntax for writing function expressions. They are the standard in modern JS code.

```javascript
// Traditional function declaration
function calculateDiscount(price, rate) {
  return price - (price * rate);
}

// ES6 arrow function  equivalent behavior, cleaner syntax
const calculateDiscount = (price, rate) => price - (price * rate);

// Multi-line arrow function (requires explicit return statement)
const validateStatusCode = (status) => {
  if (status >= 200 && status < 300) return "Success";
  if (status >= 400 && status < 500) return "Client Error";
  return "Server Error";
};
```

#### 2. Template Literals

Template literals allow variable interpolation directly inside string values using backtick characters and `${}` syntax. They replace string concatenation entirely.

```javascript
const testCaseId = "TC-047";
const result     = "PASSED";
const duration   = 234;

// Old concatenation approach  verbose and error-prone
console.log("Test " + testCaseId + " " + result + " in " + duration + "ms.");

// Template literal  clean and readable
console.log(`Test ${testCaseId} ${result} in ${duration}ms.`);
// Output: Test TC-047 PASSED in 234ms.
```

#### 3. Destructuring Assignment

Destructuring extracts values from objects or arrays into individual variables, reducing repetitive property access.

```javascript
// Object destructuring  extract specific fields from an API response
const apiResponse = { id: 123, name: "John Doe", email: "john@example.com", role: "admin" };
const { id, name, email } = apiResponse;
console.log(name);  // "John Doe"

// Array destructuring  extract values by position
const [firstDevice, secondDevice] = ["iPhone 15", "Samsung S24", "Pixel 8"];
console.log(firstDevice);   // "iPhone 15"
console.log(secondDevice);  // "Samsung S24"
```

#### 4. Spread & Rest Operators

```javascript
// Spread  expand an array or object into individual elements
const devices    = ["iPhone", "Android"];
const allDevices = [...devices, "iPad", "Desktop"];
// Result: ["iPhone", "Android", "iPad", "Desktop"]

// Rest  collect remaining arguments into an array
function logTestResults(suiteName, ...results) {
  console.log(`Suite: ${suiteName}`);
  results.forEach(r => console.log(`  - ${r}`));
}
logTestResults("Login Suite", "TC-001 PASS", "TC-002 FAIL", "TC-003 PASS");
```

#### 5. Async/Await  Handling Asynchronous Operations

Modern API testing and browser automation involve asynchronous operations (network requests, DOM waits). `async/await` provides a clean, readable way to handle them.

```javascript
// async declares a function that will contain asynchronous operations
// await pauses execution until the Promise resolves
async function getUser(userId) {
  const response = await fetch(`https://api.example.com/users/${userId}`);
  const data     = await response.json();
  return data;
}
```

---

### ES6 Modules  Import & Export

Modules allow code to be split across multiple files and selectively shared between them. This is the foundation of scalable automation framework architecture  utilities, configuration, and page objects each live in their own file.

**File 1: `utils/apiHelper.js`  Exporting shared utilities**

```javascript
// Named export  each item is exported individually
export const BASE_URL = "https://api.example.com";

export function buildAuthHeader(token) {
  return { Authorization: `Bearer ${token}` };
}

export function assertStatusCode(actual, expected) {
  if (actual !== expected) {
    throw new Error(`Status mismatch: expected ${expected}, received ${actual}`);
  }
  console.log(`Status code assertion passed: ${actual}`);
}
```

**File 2: `tests/userTest.js`  Importing and using the utilities**

```javascript
// Named imports  import only what is needed
import { BASE_URL, buildAuthHeader, assertStatusCode } from './utils/apiHelper.js';

async function testGetUser() {
  const token    = "test_token_abc123";
  const headers  = buildAuthHeader(token);
  const response = await fetch(`${BASE_URL}/users/1`, { headers });

  assertStatusCode(response.status, 200);
  console.log("GET /users/1 test passed.");
}

testGetUser();
```

> [!NOTE]
> **CommonJS vs. ES Modules:** Older Node.js projects use `require()` / `module.exports` (CommonJS). Modern projects and test frameworks use ES Module syntax (`import` / `export`). Both are valid; always check the project's `package.json` for `"type": "module"` to determine which syntax to use.

---

## Topic 2: Node.js & Core Programming Concepts

### Understanding Node.js

**Node.js** is an open-source, cross-platform JavaScript runtime built on Google's V8 engine (the same engine that powers Google Chrome). It executes JavaScript code outside the browser  directly on the operating system  enabling JavaScript to be used for server-side logic, command-line tools, and test automation frameworks.

#### Key Node.js Properties for SQA Engineers

| Property | Description | SQA Relevance |
| :--- | :--- | :--- |
| **Asynchronous & Non-blocking** | Node.js can initiate multiple operations (API calls, file reads) concurrently without waiting for each to finish | Enables parallel test execution; prevents test runners from hanging on slow network calls |
| **npm Ecosystem** | The world's largest open-source software registry  over 2.5 million packages | Install Playwright (`npm install playwright`), Axios, Jest, and any other testing tool with a single command |
| **Cross-Platform** | Runs identically on Windows, macOS, and Linux | Test scripts written locally run without modification in CI/CD pipeline containers |
| **Built-in Test Runners** | Node.js supports Jest, Mocha, and Vitest natively | No external tooling required to execute and report test suites |

---

### Setting Up Node.js & Writing Your First Script

#### Installation

1. Navigate to [nodejs.org](https://nodejs.org) and download the **LTS (Long-Term Support)** release.
2. Run the installer, accepting default options.
3. Verify the installation by opening a terminal and running:

```bash
node --version
# Output: v22.x.x (or current LTS)

npm --version
# Output: 10.x.x (or current version)
```

#### Your First Node.js Script

```bash
# Create a project directory and navigate into it
mkdir qa-automation && cd qa-automation

# Initialize a Node.js project (creates package.json)
npm init -y
```

Create a file named `app.js` with the following content:

```javascript
// app.js  entry point for QA automation scripts
const testSuite = "User Authentication";
const testCases = ["TC-001: Valid Login", "TC-002: Invalid Password", "TC-003: Empty Credentials"];

console.log(`Running test suite: ${testSuite}`);
console.log(`Total test cases: ${testCases.length}`);

testCases.forEach((tc, index) => {
  console.log(`  ${index + 1}. ${tc}`);
});
```

Execute the script:

```bash
node app.js
# Output:
# Running test suite: User Authentication
# Total test cases: 3
#   1. TC-001: Valid Login
#   2. TC-002: Invalid Password
#   3. TC-003: Empty Credentials
```

---

### Control Flow  Conditionals & Loops

Control flow structures determine the logical path code takes based on data values  the core mechanism of any validation script.

#### Conditionals  `if`, `else if`, `else`

Used to make branching decisions in test assertions and validation logic.

```javascript
function assertHttpStatus(statusCode) {
  if (statusCode === 200) {
    console.log("PASS: 200 OK  Request succeeded.");
  } else if (statusCode === 201) {
    console.log("PASS: 201 Created  Resource created successfully.");
  } else if (statusCode === 401) {
    console.log("FAIL: 401 Unauthorized  Authentication required or failed.");
  } else if (statusCode === 404) {
    console.log("FAIL: 404 Not Found  Resource does not exist.");
  } else {
    console.log(`UNKNOWN: Unexpected status code received: ${statusCode}`);
  }
}

assertHttpStatus(200);   // PASS: 200 OK
assertHttpStatus(401);   // FAIL: 401 Unauthorized
assertHttpStatus(503);   // UNKNOWN: Unexpected status code received: 503
```

#### The Ternary Operator  Concise Conditionals

```javascript
const responseTime = 450; // milliseconds
const performanceResult = responseTime < 1000 ? "PASS" : "FAIL";
console.log(`Performance assertion: ${performanceResult}`);  // PASS
```

#### Loops  Iterating Test Data

```javascript
// for loop  iterate over an array of test endpoints
const endpoints = ["/api/users", "/api/products", "/api/orders"];

for (let i = 0; i < endpoints.length; i++) {
  console.log(`Testing endpoint ${i + 1} of ${endpoints.length}: ${endpoints[i]}`);
}

// forEach  cleaner syntax for array iteration
endpoints.forEach((endpoint, index) => {
  console.log(`[${index + 1}] Sending GET request to: ${endpoint}`);
});

// for...of  iterate directly over values (most readable)
for (const endpoint of endpoints) {
  console.log(`Validating: ${endpoint}`);
}
```

#### Switch Statement  Multiple Discrete Conditions

```javascript
function describeSeverity(level) {
  switch (level) {
    case "Blocker":
      return "System is down or deployment is broken. Immediate action required.";
    case "Critical":
      return "Core functionality broken with no workaround available.";
    case "Major":
      return "Important feature impaired but alternative paths exist.";
    case "Minor":
      return "Minor UX friction with no functional impact.";
    default:
      return "Unknown severity level.";
  }
}
```

---

### Object-Oriented Programming for SQA

**Object-Oriented Programming (OOP)** organizes code around objects that combine data (properties) and behavior (methods). In test automation, OOP is the foundation of the **Page Object Model (POM)**  the industry-standard pattern for structuring UI automation code.

#### The Page Object Model Explained

```text
  ┌──────────────────────────────────────────────────────┐
  │               CLASS: LoginPage                       │
  │                (Blueprint / Template)                │
  ├──────────────────────────────────────────────────────┤
  │  Properties (Locators / State):                      │
  │    - this.url           = "/login"                   │
  │    - this.emailInput    = "input[name='email']"      │
  │    - this.passwordInput = "input[name='password']"   │
  │    - this.submitButton  = "button#login-submit"      │
  ├──────────────────────────────────────────────────────┤
  │  Methods (Actions):                                  │
  │    + navigate()                                      │
  │    + fillCredentials(email, password)                │
  │    + submit()                                        │
  │    + getErrorMessage()                               │
  └──────────────────────┬───────────────────────────────┘
                         |
                new LoginPage()   <-- Instantiate
                         |
                         v
  ┌──────────────────────────────────────────────────────┐
  │           OBJECT: loginPage                          │
  │            (Concrete Instance)                       │
  │    loginPage.navigate()                              │
  │    loginPage.fillCredentials("user@test.com", "...")  │
  │    loginPage.submit()                                │
  └──────────────────────────────────────────────────────┘
```

#### JavaScript Class Implementation  Page Object Model

```javascript
// pages/LoginPage.js
class LoginPage {
  /**
   * @param {string} baseUrl - The base URL of the application under test
   */
  constructor(baseUrl) {
    this.url            = `${baseUrl}/login`;
    this.emailInput     = "input[name='email']";
    this.passwordInput  = "input[name='password']";
    this.submitButton   = "button#login-submit";
    this.errorMessage   = "div.alert-error";
    this.dashboardUrl   = `${baseUrl}/dashboard`;
  }

  navigate() {
    console.log(`Navigating to: ${this.url}`);
    // In a real Playwright test: await page.goto(this.url);
  }

  fillCredentials(email, password) {
    console.log(`Filling email field [${this.emailInput}] with: ${email}`);
    console.log(`Filling password field [${this.passwordInput}]`);
    // In a real Playwright test:
    // await page.fill(this.emailInput, email);
    // await page.fill(this.passwordInput, password);
  }

  submit() {
    console.log(`Clicking submit button: ${this.submitButton}`);
    // In a real Playwright test: await page.click(this.submitButton);
  }

  login(email, password) {
    this.navigate();
    this.fillCredentials(email, password);
    this.submit();
    return `Redirected to: ${this.dashboardUrl}`;
  }
}

// tests/loginTest.js  consuming the Page Object
const loginPage = new LoginPage("https://staging.supermart.com");
const result    = loginPage.login("qa.tester@supermart.com", "SecurePass#2026");
console.log(result);
// Output: Redirected to: https://staging.supermart.com/dashboard
```

#### OOP Core Principles in the Context of SQA

| OOP Principle | Definition | Application in Test Automation |
| :--- | :--- | :--- |
| **Encapsulation** | Bundling data and methods that operate on that data within a single class | All locators and interactions for a page are contained within one Page Object class |
| **Inheritance** | A child class inherits properties and methods from a parent class | A `BasePage` class holds common methods (`navigate`, `waitForElement`); all page classes extend it |
| **Abstraction** | Hiding implementation complexity behind simple method calls | A test calls `loginPage.login(email, pass)` without knowing the internal selector details |
| **Polymorphism** | Child classes can override parent class methods with specialized behavior | Each Page Object overrides `navigate()` with its specific URL |

---

### Real-World Node.js API Verification Script

The following script demonstrates a complete API verification workflow using `axios`  the most widely used HTTP client in Node.js automation projects.

```bash
# Install axios as a project dependency
npm install axios
```

```javascript
// tests/apiVerification.js
// ============================================================
//  API Verification Script
//  Tests: GET /users/1 from JSONPlaceholder public test API
// ============================================================
const axios = require("axios");

const BASE_URL = "https://jsonplaceholder.typicode.com";
let passCount = 0;
let failCount = 0;

function assert(condition, message) {
  if (condition) {
    console.log(`  PASS: ${message}`);
    passCount++;
  } else {
    console.log(`  FAIL: ${message}`);
    failCount++;
  }
}

async function verifyGetUserEndpoint() {
  console.log("========================================");
  console.log(" Test Suite: GET /users/1 Verification  ");
  console.log("========================================");

  try {
    const startTime = Date.now();
    const response  = await axios.get(`${BASE_URL}/users/1`);
    const duration  = Date.now() - startTime;

    // Assertion 1: HTTP Status Code
    assert(response.status === 200, `Status code is 200 OK (received: ${response.status})`);

    // Assertion 2: Response Time SLA
    assert(duration < 2000, `Response time under 2000ms (actual: ${duration}ms)`);

    // Assertion 3: Content-Type Header
    const contentType = response.headers["content-type"];
    assert(contentType.includes("application/json"), `Content-Type is application/json`);

    const user = response.data;

    // Assertion 4: Required Fields Present
    assert(user.id        !== undefined, `Response contains 'id' field`);
    assert(user.name      !== undefined, `Response contains 'name' field`);
    assert(user.email     !== undefined, `Response contains 'email' field`);
    assert(user.username  !== undefined, `Response contains 'username' field`);

    // Assertion 5: Data Integrity
    assert(user.id === 1, `User ID equals 1 (received: ${user.id})`);

    // Assertion 6: Email Format Validation
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    assert(emailRegex.test(user.email), `Email format is valid (received: ${user.email})`);

  } catch (error) {
    console.log(`  SCRIPT ERROR: ${error.message}`);
    failCount++;
  }

  console.log("----------------------------------------");
  console.log(` Results: ${passCount} passed, ${failCount} failed`);
  console.log("========================================\n");
}

verifyGetUserEndpoint();
```

**Expected Script Output:**

```text
========================================
 Test Suite: GET /users/1 Verification
========================================
  PASS: Status code is 200 OK (received: 200)
  PASS: Response time under 2000ms (actual: 187ms)
  PASS: Content-Type is application/json
  PASS: Response contains 'id' field
  PASS: Response contains 'name' field
  PASS: Response contains 'email' field
  PASS: Response contains 'username' field
  PASS: User ID equals 1 (received: 1)
  PASS: Email format is valid (received: Sincere@april.biz)
----------------------------------------
 Results: 9 passed, 0 failed
========================================
```

---

## Key Takeaways & Self-Assessment

### Key Takeaways

* **JavaScript** is the primary language for modern test automation. Frameworks such as Playwright, Cypress, and WebdriverIO, as well as Postman test scripts, are all written in JavaScript or TypeScript.
* **Variable declaration** uses `const` by default (immutable) and `let` only when reassignment is required. The legacy `var` keyword should be avoided in all modern code.
* **ES6 features**  arrow functions, template literals, destructuring, spread/rest, and async/await  are the standard syntax in all professional automation codebases and must be understood to read and write framework code.
* **Node.js** enables JavaScript to run outside the browser, directly in a terminal. It is the runtime environment for all Node-based test frameworks (Playwright, Jest, Mocha) and is therefore a mandatory installation for any SQA automation engineer.
* **OOP Classes** serve as blueprints for the **Page Object Model (POM)** pattern, encapsulating page locators and interaction methods into reusable, maintainable objects that are consumed by test scripts.
* Writing test scripts in Node.js using libraries such as `axios` demonstrates the practical convergence of manual QA knowledge (what to assert) and programming skills (how to assert it automatically).

---

### Self-Assessment Review Questions

1. When declaring a variable to store a database connection string that should never change during script execution, should you use `let` or `const`? Explain your reasoning.

2. Rewrite the following function using ES6 arrow function syntax:
   ```javascript
   function isEmailValid(email) {
     return email.includes("@");
   }
   ```

3. What is the difference between `null` and `undefined` in JavaScript? Write one example of each.

4. What is Node.js and why must SQA automation engineers install it on their machines to run Playwright or Cypress test suites?

5. Explain how the **Page Object Model** pattern uses OOP classes. What are the three main benefits of using POM in a large automation project?

6. In the real-world API verification script, what would happen if the API responded with a `404` status code? Trace through the code to explain the output.

---

**Document Control Metadata**

* **Target Knowledge Level:** Beginner to Intermediate Quality Assurance Practitioners
* **Estimated Self-Guided Completion Time:** 150 Minutes
* **Temporal Status Baseline:** 2026 Release Cycle Specifications
