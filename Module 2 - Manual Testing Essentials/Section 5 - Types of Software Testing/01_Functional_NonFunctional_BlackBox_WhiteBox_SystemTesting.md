# Section 5: Types of Software Testing

## Table of Contents

 ### Topic 1: Functional & Non-Functional Testing, Black Box vs White Box, System Testing, UAT
* [Functional Testing](#functional-testing)
* [Non-Functional Testing](#non-functional-testing)
* [Black Box Testing](#black-box-testing)
* [White Box Testing](#white-box-testing)
* [Comparison of Black Box vs White Box](#comparison-of-black-box-vs-white-box)
* [System Testing](#system-testing)
* [Acceptance Testing (UAT)](#acceptance-testing-uat)


 ### Topic 2: Regression Testing, Sanity vs Smoke Testing, Exploratory Testing
* [Regression Testing](#regression-testing)
* [Smoke Testing](#smoke-testing)
* [Sanity Testing](#sanity-testing)
* [Sanity vs Smoke Comparison](#sanity-vs-smoke-comparison)
* [Exploratory Testing](#exploratory-testing)
* [When to Use Each Testing Type](#when-to-use-each-testing-type)


* [Testing Types in Action: Real Project Blueprint](#testing-types-in-action-real-project-blueprint)
* [Key Takeaways](#key-takeaways)

---

## Topic 1: Functional & Non-Functional Testing

### Functional Testing

#### What is Functional Testing?

**Definition:** Testing the application's functional components against specified requirements to ensure features perform exactly as intended.

**In Simple Terms:** Does the software do what it is explicitly expected to do based on the product requirements?

#### Focus Areas

```text
Question: Does the feature work?

 Examples:
 - Can a user log in with valid credentials?
 - Can a user add a product to the cart?
 - Does the payment gateway process transactions correctly?
 - Does the search bar return accurate, sorted results?
 - Does the system trigger the appropriate email notifications?

 NOT concerned with:
 - Speed or load limits (Performance Testing)
 - Visual pixel alignment or styling rules (UI/UX Design)
 - Underlying code structural efficiency (White Box Architecture)

```

#### Functional Testing Techniques

##### 1. Boundary Value Analysis (BVA)

Test at the exact boundaries (edges) of input ranges where code logic flips.

* **Example - Age input field (Valid range: 18 to 65 inclusive):**
* **Test 17:** (Below boundary - Invalid) =>  Should reject with error
* **Test 18:** (Lower boundary - Valid)    =>  Should accept cleanly
* **Test 65:** (Upper boundary - Valid)    =>  Should accept cleanly
* **Test 66:** (Above boundary - Invalid)  =>  Should reject with error



> **Why?** Developers frequently use incorrect conditional operators (e.g., using `>` instead of `>=`).

##### 2. Equivalence Partitioning (EP)

Group inputs into identical categories (partitions) that should trigger the same system behavior.

* **Example - Email string validation:**

| Valid Partition (Expect: Success) | Invalid Partition (Expect: Rejection) |
| --- | --- |
| `noman@test.com` | `noman@` (Missing domain) |
| `abdullah+tag@example.org` | `@test.com` (Missing local name) |

> **Rule:** Test just one representative value from each partition to maximize efficiency.

##### 3. Decision Table Testing

Used to document complex business logic involving combinations of multiple inputs.

* **Example - E-Commerce Purchase Eligibility:**

| Conditions | Rule 1 | Rule 2 | Rule 3 | Rule 4 |
| --- | --- | --- | --- | --- |
| **User Logged In?** | Yes | Yes | No | No |
| **Item in Stock?** | Yes | No | Yes | No |
| **Expected Outcome** | Dispatch Product | Show Out-of-Stock Error | Redirect to Login | Redirect to Login |

##### 4. State Transition Testing

Verifies how a system changes states across a lifecycle based on input triggers.

* **Example - Order Processing Workflow:**

```text
[ New ] =>  [ Processing ] =>  [ Shipped ] =>  [ Delivered ]

```

* **Valid Transition Test:** `New` =>  `Processing` (Pass)
* **Invalid Transition Test:** `New` =>  `Delivered` (Fail - cannot bypass shipping)

#### Functional Testing Examples

##### Example 1: E-commerce Login

 **TC1: Valid Login Match**
 **Input:** `email=noman@example.com`, `password=SecurePass123`* **Expected:** Authentication success, session token generated, redirect to dashboard.


 **TC2: Malformed Email String**
- **Input:** `email=invalid-email-format`, `password=SecurePass123`
- **Expected:** Inline error reading "Invalid email format".


 **TC3: Password Mismatch**
- **Input:** `email=noman@example.com`, `password=WrongPass`
- **Expected:** Global warning banner reading "Invalid credentials".


 **TC4: Null Input Processing**
- **Input:** `email=""`, `password=""`
- **Expected:** Validation flags highlighting required fields.


 **TC5: Account Security Threshold**
- **Input:** Trigger 6 consecutive failed authentication attempts.
- **Expected:** Account record locked flag updates to `TRUE`, show recovery wizard link.



##### Example 2: Shopping Cart

 **TC1: Core Single Addition**
- **Action:** Select an item, click "Add to Cart".
- **Expected:** Cart session updates, navbar item count indicator increments to 1.


 **TC2: Bulk Distinct Additions**
- **Action:** Add 3 distinct products to the tray.
- **Expected:** Subtotal items counter reads 3; global mathematical balance sums perfectly.


 **TC3: Inline Quantity Modification**
- **Action:** Modify quantity field selector from 1 to 5.
- **Expected:** Base line-item price multiplies by 5; tax and shipping recalculate.


 **TC4: Partial Tray Eviction**
- **Action:** Press "Remove" toggle on a specific line item.
- **Expected:** Selected row is deleted; subtotal balance falls instantly.


 **TC5: Total Cart Evacuation**
- **Action:** Click "Clear All Items".
- **Expected:** Session records purge; cart switches back to placeholder "Cart is empty".



---

### Non-Functional Testing

#### What is Non-Functional Testing?

**Definition:** Testing non-operational attributes of the application, such as raw speed, security compliance, user experience comfort, and cross-device compatibility.

**In Simple Terms:** How well does the software perform its duties beyond standard feature checklists?

#### Types of Non-Functional Testing

##### 1. Performance Testing

```text
Question: How fast, stable, and scalable is the software structure?

Core Architectural Metrics:
  Page Render Latency (Time to First Byte)
  Concurrent API Payload Capacity (Throughput)
  Hardware Strain (CPU ceiling, Memory allocations)

```

* **Load Testing:** Simulates normal, expected real-world usage levels to establish baseline comfort metrics.
* *Scenario:* 1,000 concurrent checkout sessions =>  System response must remain under 2 seconds.


* **Stress Testing:** Pushes past the planned infrastructure limits until the software reaches an absolute breaking point.
* *Scenario:* Scale up from 10k to 50k users =>  Identify memory leaks or application server crashes.


* **Spike Testing:** Subjects the software to sudden, dramatic surges in user traffic within seconds.
* *Scenario:* Ticket sales launch or flash sale events =>  Ensure databases don't lock down.


* **Endurance (Soak) Testing:** Operates the application under sustained load over an extended period (e.g., 24-48 hours).
* *Scenario:* Uncover gradual degradation issues like slow memory accumulation leaks.



##### 2. Security Testing

```text
Question: Can the platform repel malicious attempts to exploit assets or steal data?

Core Vulnerability Checks:
 - Injection Probing (SQLi, Command Injections)
 - Cross-Site Scripting vulnerabilities (XSS)
 - Transport Security (SSL cipher verification, encryption-at-rest)
 - Session Token Expiry and Authorization Bypass controls

```

* *Example SQLi Payload Test:*
* **Input Field:** `' OR '1'='1`
* **Expected:** WAF or backend engine catches pattern, rejects data, logs attempt, denies entry.



##### 3. Usability Testing

* **Focus:** Is the user flow intuitive, or does it create friction for real people?
* **Elements:** User onboarding speed, layout logic, and clarity of system error feedback.

##### 4. Compatibility Testing

* **Focus:** Does the web application look and act identically across different environments?
* **Matrix Auditing:** Cross-Browser Engines (Chromium, Gecko, WebKit) and Operating Systems (Windows, macOS, Linux, iOS, Android).

##### 5. Accessibility Testing

* **Focus:** Is the digital experience usable for people with visual, auditory, or motor impairments?
* **Compliance Anchors:** WCAG 2.1 AA Standards (Screen reader tree structures, strict keyboard tab navigation, and visual contrast checks).

##### 6. Reliability Testing

* **Focus:** Is the software reliable and resilient over continuous operational periods?
* **Elements:** System stability under consistent use, fault tolerance, and recovery behaviors during unexpected service dropouts.

---

### Black Box Testing

#### What is Black Box Testing?

**Definition:** A testing strategy where the engineer examines software functionality entirely from the outside, without any access or visibility into internal code architectures, databases, or logic structures.

```text
The System as an Opaque Container:

Inputs =>  ┌───────────────────────────┐ =>  Outputs
           │   [ Opaque Black Box ]    │
           │   Internal code hidden    │
           └───────────────────────────┘

```

* **Tester Focus:** Verify that input vectors yield exactly the expected output results.
* **Who Conducts It:** Functional QA Manual Testers, Business Analysts, End-User Beta Panels, Product Managers.

#### Advantages & Disadvantages

* **Pros:**
* No deep software engineering or coding skills needed.
* Tests replicate genuine user interaction patterns accurately.
* Unbiased evaluation unaffected by developer architectural choices.


* **Cons:**
* Testing all input pathways is functionally impossible (infinite permutations).
* Cannot target precise hidden structural bugs or branch dead ends inside code.
* Replicating structural edge cases manually can be incredibly time-consuming.



---

### White Box Testing

#### What is White Box Testing?

**Definition:** A testing method where the engineer has complete visibility into the internal source code, control paths, and architectural structures of the program.

```text
The System as a Transparent Container:

Inputs =>  ┌───────────────────────────┐ =>  Outputs
           │   if (x > y) { pathA(); } │
           │   else { pathB(); }       │
           └───────────────────────────┘

```

* **Tester Focus:** Verify execution coverage across all lines of code, loops, and conditions.
* **Who Conducts It:** Software Engineers / Developers, SDETs (Software Development Engineers in Test), Systems Architects.

#### Core White Box Target Areas

##### 1. Unit Testing

Testing the smallest standalone fragments of code—such as distinct methods or functions—isolated from external dependencies using stubs and mocks.

##### 2. Integration Testing

Verifies interaction boundaries and data formatting passed between distinct modules or microservices.

* **White Box Integration:** Validating component-to-component interfaces, API endpoints, or data-layer code dependencies.
* **Black Box Integration:** Validating end-to-end data flows across completed system interfaces or third-party service gateways without reading code.

##### 3. Code Coverage Analysis

```text
Metric: What exact percentage of the code statement branches are validated by tests?

function verifyDiscount(userType) {
  if (userType === 'PREMIUM') {
     return 0.20; // Code Branch A
  }
  return 0.00;    // Code Branch B
}

If test suite only passes 'PREMIUM', Branch B remains uncovered (50% Statement Coverage).

```

---

### Comparison of Black Box vs White Box

| Characteristic | Black Box Testing | White Box Testing |
| --- | --- | --- |
| **Code Knowledge** | None. Completely blind to internal structures. | Full. Complete access to code logic. |
| **Primary Actor** | QA Testers, Business Analysts, End Users. | Software Engineers, Architects, SDETs. |
| **Testing Goal** | Validates user requirements and behaviors. | Validates structural accuracy and code safety. |
| **Granularity** | Macro-level (End-to-end user workflows). | Micro-level (Functions, classes, variables). |
| **Triggers** | Driven by specifications, user stories, and mockups. | Driven by actual source code and design architecture. |
| **Defect Types Found** | Usability issues, missing features, workflow hitches. | Memory leaks, syntax issues, unhandled runtime errors. |

---

### System Testing

#### What is System Testing?

**Definition:** Evaluating the completely assembled, fully integrated application environment as a single cohesive unit to verify compliance with end-to-end system specs.

```text
System Testing Execution Phase:
Unit Tested Code =>  Integrated Components =>  [ SYSTEM TESTING ] =>  UAT =>  Production

```

#### System Testing Scope

System testing merges all software modules, hardware configurations, live database connections, network infrastructure, and external API interfaces to evaluate the complete application ecosystem under realistic real-world conditions.

```text
E-Commerce System-Wide Lifecycle Run:
[Browse Catalog] =>  [Add To Cart] =>  [Process Visa Gateway] =>  [Update DB Stock] =>  [Fire Email Worker]

```

>  **Critical Note:** If even one module fails during this chain, the overall System Test fails.

---

### Acceptance Testing (UAT)

#### What is Acceptance Testing?

**Definition:** The final testing phase before production release, where real-world users or business stakeholders validate that the software satisfies actual day-to-day business operational requirements.

#### The Core Difference: System Testing vs. UAT

* **System Testing (The Engineering View):** Tests against technical documentation. *"Does the system run exactly how the technical specs described it?"*
* **UAT (The Business View):** Tests against user workflows. *"Does this system actually solve our real-world problems and save us time?"*

```text
UAT Sign-Off & Release Lifecycle:
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│ Draft UAT Cases │ =>  │ User Execution  │ =>  │ Formal Sign-Off │ =>  RELEASE!
└─────────────────┘     └─────────────────┘     └─────────────────┘

```

---

## Topic 2: Regression, Sanity, Smoke & Exploratory Testing

### Regression Testing

#### What is Regression Testing?

**Definition:** Re-running existing tests against a modified software build to confirm that new code additions, optimizations, or bug fixes haven't broken any previously stable features.

```text
The Regression Trap:
1. Feature A works perfectly.
2. Developer injects a code patch to fix a bug in Feature B.
3. Feature B is fixed, but Feature A unexpectedly breaks due to shared dependencies.
Action Required: Continuous Regression Testing sweeps of Feature A.

```

#### Scope Strategies

* **Full Regression:** Re-runs every test case in the suite. This is typically executed prior to major system releases.
* **Partial/Targeted Regression:** Focuses strictly on modules directly related to or dependent on the modified code.

```text
The Automation Reality:
Manual Regression: 500 test cases x 10 mins = 83 Hours of work (Impractical)
Automated Regression: CI/CD pipeline runs regression scripts =>  Completed in 15 Minutes.

```

---

### Smoke Testing

#### What is Smoke Testing?

**Definition:** A **broad and shallow** suite of tests executed on initial software builds to verify that the absolute core functionalities run without crashing or halting the deployment pipeline.

* **Scope:** Broad and Shallow (covers the entire application surface, but at a very high level).
* **Objective:** Build acceptance validation. It answers: *"Is this build stable enough to accept further testing, or should we reject it immediately?"*

```text
Smoke Testing Sequence:
Is Build Ready? =>  Boot Application =>  Navigate to Login =>  Run Basic Core Flow
If it crashes on launch =>  SMOKE TEST FAILS =>  Reject build immediately. Do not waste QA hours.

```

---

### Sanity Testing

#### What is Sanity Testing?

**Definition:** A **narrow and deep** evaluation performed on a relatively stable build to verify that a specific bug fix or minor modification behaves logically before running broader testing.

* **Scope:** Narrow and Deep (focuses heavily on the specific modified component and its immediate neighbors).
* **Objective:** Fast validation of specific changes. It answers: *"Did the developer successfully fix this specific issue without making the component unstable?"*

```text
Sanity Testing Scenario:
Bug Reported: Checkout button doesn't respond to clicks.
Developer Pushes Fix Build.
Sanity Action: Click checkout button 20x using different item configurations.
If it works logically =>  SANITY PASSES =>  Proceed to running full regression testing.

```

---

### Sanity vs. Smoke Comparison

| Attribute | Smoke Testing | Sanity Testing |
| --- | --- | --- |
| **Focus Dimension** | **Broad and Shallow** (covers all core app areas at a high level). | **Narrow and Deep** (focuses heavily on changed components). |
| **Build Maturity** | Early stage. Evaluates completely raw, unverified builds. | Mature stage. Run on relatively stable builds following minor updates. |
| **Main Objective** | Confirms build stability for testing teams. | Confirms logical behavior of recent code changes. |
| **Automation Suitability** | Excellent. Ideal for automated CI/CD deployment checks. | Mostly manual. Handled quickly by a tester on the fly. |
| **Suite Subsystem** | Often considered a subset of the Acceptance Suite. | Often considered a subset of the Regression Suite. |

#### Build Intake Decision Workflow

```text
                   [ Incoming Software Build ]
                                │
                                ▼
                       { Run Smoke Test }
                                │
         ┌──────────────────────┴──────────────────────┐
         ▼ FAIL                                        ▼ PASS
   [ Reject Build ]                              { Run Sanity Test }
 (Return to Engineering)                               │
                                        ┌──────────────┴──────────────┐
                                        ▼ FAIL                        ▼ PASS
                                  [ Reject Patch ]              [ Run Regression Suite ]
                               (Return to Developer)             (Full Scale Validation)

```

---

### Exploratory Testing

#### What is Exploratory Testing?

**Definition:** An unscripted testing style where test design, test execution, and application learning occur simultaneously. Testers use their intuition, domain knowledge, and creativity rather than following predefined step-by-step test cases.

```text
Scripted Testing:    [ Read Test Case ]   =>  [ Execute Step ]  =>  [ Log Static Outcome ]
Exploratory Testing:  [ Learn Behavior ]  =>  [ Pivot Strategy ] =>  [ Investigate Edge Case ]

```

#### Core Methodologies

##### 1. Error Guessing

Leveraging experience to target classic developer pitfalls, such as pasting excessively long inputs, double-clicking submission inputs rapidly, or feeding null vectors into mandatory fields.

##### 2. Session-Based Testing (SBT)

Structuring exploratory work by testing within uninterrupted, timed windows (e.g., 90 minutes) focused on a specific feature charter, complete with documented session notes.

##### 3. Tour-Based Testing

Navigating the application with distinct personas or goals:

* **The Money Tour:** Focus exclusively on payment options, vouchers, and tax calculations.
* **The Chaos Tour:** Intentionally enter invalid data, interrupt connection states, and press back buttons.

---

### When to Use Each Testing Type

```text
FUNCTIONAL TESTING:
 --> When: Testing feature compliance against explicit specification lists.
 --> Example: Verifying that clicking the "Reset" button clears the form.

NON-FUNCTIONAL TESTING:
 --> When: Evaluating performance, safety thresholds, or operational constraints.
 --> Example: Running a vulnerability scan or simulating peak traffic.

BLACK BOX TESTING:
 --> When: Validating user requirements from an external consumer perspective.
 --> Example: Standard functional testing pipelines handled by QA.

WHITE BOX TESTING:
 --> When: Validating structural code safety, path paths, and optimization.
 --> Example: Writing unit tests and checking branch coverage goals.

SYSTEM TESTING:
 --> When: Verifying completely integrated multi-module configurations end-to-end.
 --> Example: Simulating a user buying an item from catalog search to invoice generation.

UAT:
 --> When: Securing final business verification prior to production release windows.
 --> Example: Product Owners and client panels confirming a release build's operational readiness.

REGRESSION TESTING:
 --> When: Ensuring that new changes have not caused side effects in stable features.
 --> Example: Running automated test suites after deploying code updates.

SMOKE TESTING:
 --> When: Verifying whether a brand new software build is stable enough to test.
 --> Example: Automatically running high-level checks upon build creation.

SANITY TESTING:
 --> When: Checking if a minor patch or specific bug fix behaves logically.
 --> Example: Deeply retesting a single component after a developer fix.

EXPLORATORY TESTING:
 --> When: Uncovering hidden or complex bugs missed by static scripts.
 --> Example: Pre-release bug hunting sessions led by experienced testers.

```

---

## Testing Types in Action: Real Project Blueprint

### Sprint Timeline Strategy (2-Week Iteration)

```text
======================================================================================
WEEK 1: Active Development Phase
======================================================================================
  ├─ Engineering writes new feature branches.
  --> Developers run local WHITE BOX unit test suites to clear a minimum 80% coverage threshold.

======================================================================================
WEEK 2: Feature Complete Phase (The QA Gate)
======================================================================================
  ├─ Code merges into Stage Branch =>  Automated SMOKE TEST kicks off.
  │     --> Outcome: Build compiles cleanly and starts up successfully. (Pass)
  │
  ├─ Testers intercept specific bug fixes and perform localized SANITY validations.
  │     --> Outcome: The modified components behave logically. (Pass)
  │
  ├─ Deep functional BLACK BOX test cases are executed against user requirements.
  ├─ SYSTEM TESTING validates end-to-end user workflows across all connected databases.
  ├─ Automated REGRESSION suites run to guarantee older legacy features remain stable.
  --> Specialist teams run NON-FUNCTIONAL scans (Load metrics, security compliance checks).

======================================================================================
RELEASE WINDOW: Deployment Prep
======================================================================================
  ├─ QA conducts EXPLORATORY sessions to seek out hidden bugs that scripted test cases missed.
  ├─ Stakeholders run User Acceptance Testing (UAT) to confirm business readiness.
  --> Sign-off is secured =>  Build deploys live to production.

```

---

## Key Takeaways

* **Functional Testing** validates *what* the system does; **Non-Functional Testing** validates *how well* it does it.
* **Black Box** is conducted from the outside (blind to code); **White Box** is conducted from the inside (full code visibility).
* **System Testing** checks the entire integrated application ecosystem; **UAT** secures final business stakeholder sign-off.
* **Smoke Testing** is **broad and shallow** to verify build readiness; **Sanity Testing** is **narrow and deep** to quickly verify code fixes.
* **Regression Testing** ensures new updates haven't broken existing, stable application features.
* **Exploratory Testing** relies on a tester's intuition and creativity to discover hidden edge-case bugs.

##  Next Steps

1. **Review**: Understand each testing type
2. **Practice**: Identify which type for different scenarios
3. **Apply**: Use appropriate testing for your features
4. **Prepare**: Move to Test Management Tools (Section 6)

---

**Last Updated**: 2026  
**Difficulty Level**: Beginner-Intermediate  
**Time to Complete**: 120 minutes

---

> **"There's no single best testing type. The best testing is the right combination of all types."**

You now understand the full landscape of software testing! 
