# Topic 1 & 2: Software Testing Life Cycle (STLC) & Test Planning

##  Table of Contents
### Topic 1: Flow of STLC & Identify the Problem Statement
1. [What is STLC?](#what-is-stlc)
2. [Difference Between SDLC & STLC](#difference-between-sdlc-and-stlc)
3. [Phases of STLC](#phases-of-stlc)
4. [QA Involvement in Each Phase](#qa-involvement-in-each-phase)
5. [Understanding the Application](#understanding-the-application)
6. [Identifying the Problem Statement](#identifying-the-problem-statement)
7. [Real-World Bug Discovery](#real-world-bug-discovery)
8. [Importance of Early Testing](#importance-of-early-testing)

### Topic 2: Test Plan Design, Test Cases Design & Test Checklist
9. [What is a Test Plan?](#what-is-a-test-plan)
10. [Key Elements of Test Plan](#key-elements-of-test-plan)
11. [Writing Basic Test Plans](#writing-basic-test-plans)
12. [Introduction to Test Cases](#introduction-to-test-cases)
13. [Writing Effective Test Cases](#writing-effective-test-cases)
14. [Test Checklist Importance](#test-checklist-importance)
15. [Common Test Case Writing Mistakes](#common-test-case-writing-mistakes)
16. [Manual vs Template-Based Checklists](#manual-vs-template-based-checklists)

---

## TOPIC 1: STLC & PROBLEM IDENTIFICATION

## What is STLC?

### Definition
**Software Testing Life Cycle (STLC)** is a structured series of steps and activities that quality assurance professionals perform to ensure software quality throughout the development lifecycle.

### Easy Example
Think of STLC like **quality control in manufacturing**:

```
Car Manufacturing Quality Control:
1. Plan quality checks → What to check?
2. Design tests → Which tests?
3. Prepare test environment → Setup testing area
4. Execute tests → Perform checks
5. Report issues → Document problems
6. Closure → Finalize quality

Testing Lifecycle:
1. Requirement Analysis → Understand what to test
2. Test Planning → How to test?
3. Test Design → Create test cases
4. Test Environment Setup → Prepare testing area
5. Test Execution → Run tests
6. Test Closure → Finalize testing
```

---

## Difference Between SDLC and STLC

### Quick Comparison

| Aspect | SDLC | STLC |
|--------|------|------|
| **Focus** | Building software | Testing software |
| **Who** | Developers & architects | QA engineers |
| **Input** | Business requirements | SDLC deliverables |
| **Output** | Working software | Quality report |
| **Timeline** | Entire project | Testing phase mainly |
| **Activities** | Design, code, build | Test, verify, report |
| **Goal** | Functional software | Quality assurance |

### Detailed Explanation

#### SDLC (Software Development Life Cycle)
```
Focus: HOW TO BUILD
Asks: What features should we build?
      How should we structure the code?
      What technology should we use?

Owner: Developers and architects
Output: Working software
```

#### STLC (Software Testing Life Cycle)
```
Focus: HOW TO TEST
Asks: What should we test?
      How much testing should we do??
      What issues should we look for?

Owner: QA engineers
Output: Tested, quality-assured software
```


## Phases of STLC

### Complete STLC Flow

```
Phase 1: REQUIREMENT ANALYSIS
        ↓
Phase 2: TEST PLANNING
        ↓
Phase 3: TEST CASE DESIGN
        ↓
Phase 4: TEST ENVIRONMENT SETUP
        ↓
Phase 5: TEST EXECUTION
        ↓
Phase 6: TEST CLOSURE
```

### Detailed Phase Breakdown

#### Phase 1: Requirement Analysis
**Goal**: Understand what needs to be tested

**Activities**:
```
1. Review requirements document
   "What features must the software have?"

2. Identify testable requirements
   "Which of these can we actually test?"

3. Clarify ambiguities
   "What does 'fast' mean? < 1 second? < 5 seconds?"

4. Identify test conditions
   "What scenarios should we test?"

5. Risk assessment
   "Which features are critical? Which are risky?"
```

**Example: E-commerce Login Feature**
```
Requirement: "Users should be able to login securely"

Testable Requirements:
1. Valid credentials accepted
2. Invalid credentials rejected
3. Password encrypted
4. Login page loads in < 2 seconds
5. Session timeout after 30 minutes
6. SQL injection attacks prevented
7. Maximum 5 login attempts allowed

Risks:
- Critical: Security (SQL injection, password encryption)
- Medium: Performance (page load time)
- Low: UI layout
```

**QA Deliverable**: 
- Requirements Traceability Matrix (RTM)
- List of testable requirements
- Risk assessment

---

#### Phase 2: Test Planning
**Goal**: Create a plan for how to test

**Activities**:
```
1. Define testing strategy
   "Manual, automated, or both?"

2. Estimate effort
   "How many hours to test this feature?"

3. Identify resources
   "How many testers do we need?"

4. Select tools
   "What testing tools will we use?"

5. Create timeline
   "When will testing start and end?"

6. Identify exit criteria
   "When is testing done? (0 bugs? < 5 bugs?)"
```

**Test Plan Document Includes**:
```
1. Scope: What features to test, what not to test
2. Objectives: Why are we testing?
3. Schedule: When each testing activity happens
4. Resources: How many testers, tools, budget
5. Risk & Mitigation: Risks and how to address
6. Entry Criteria: Conditions for testing to start
7. Exit Criteria: Conditions for testing to end
8. Testing Types: Manual, automation, performance, etc.
```

**Example Test Plan Outline**:
```
TEST PLAN: E-commerce Website v2.0

1. SCOPE
   - Test all features in requirements
   - Exclude: Admin backend features
   - Priority: Login, checkout, payment

2. OBJECTIVES
   - Ensure all features work correctly
   - Find critical bugs before deployment
   - Achieve 95% code coverage

3. TESTING TYPES
   - Functional testing (manual)
   - Performance testing (automated load)
   - Security testing (automated)
   - Usability testing (manual)
   - Regression testing (automated)

4. SCHEDULE
   - Week 1: Test planning & design
   - Week 2: Environment setup
   - Week 3-4: Execution
   - Week 5: Reporting & closure

5. RESOURCES
   - 4 QA engineers
   - 1 QA lead
   - Selenium for automation
   - JMeter for performance
   - Budget: $50,000

6. ENTRY CRITERIA
   - Requirements finalized
   - Build stable for testing
   - Test environment ready

7. EXIT CRITERIA
   - All high-priority tests executed
   - Critical bugs found and fixed
   - Code coverage > 90%
```

**QA Deliverable**:
- Test Plan document
- Resource allocation
- Testing schedule

---

#### Phase 3: Test Case Design
**Goal**: Create test cases based on requirements

**Activities**:
```
1. Identify test scenarios
   "What situations should we test?"

2. Design test cases
   "Step-by-step, what to do and expect"

3. Review and approve
   "Manager reviews test cases for quality"

4. Prepare test data
   "What data do we need for testing?"
```

**Example Test Case**:
```
TEST CASE ID: TC_001
Test Title: Valid Login
Precondition: User is on login page
             Database has user with email noman@example.com

Steps:
1. Enter email: noman@example.com
2. Enter password: SecurePass123
3. Click "Login" button

Expected Result:
- Login successful
- Redirect to dashboard
- User name shown in top right
- No error message

Actual Result: [To be filled during execution]
Pass/Fail: [To be filled during execution]
```

**QA Deliverable**:
- Test case document (500+ test cases possible)
- Test data
- Test scenarios

---

#### Phase 4: Test Environment Setup
**Goal**: Prepare environment for testing

**Activities**:
```
1. Setup hardware
   "Install computers for testing"

2. Install software
   "Install the application to be tested"

3. Configure database
   "Setup test database with test data"

4. Configure tools
   "Setup testing tools (Selenium, Postman, etc.)"

5. Verify readiness
   "Confirm everything works before testing"
```

**Example Setup**:
```
Test Environment for E-commerce:

Hardware:
├─ 4 computers for QA testers
├─ 1 server for test database
└─ 1 machine for automation tests

Software:
├─ Application version 2.0
├─ Testing tools (Selenium, Postman)
├─ Browsers (Chrome, Firefox, Safari)
└─ OS (Windows, Mac, Linux)

Database:
├─ Test MySQL database
├─ 1000 test users
├─ 10,000 test products
├─ Sample orders and transactions

Network:
├─ Isolated from production
├─ High-speed connectivity
└─ VPN access for remote testers
```

**QA Deliverable**:
- Environment ready certification
- Access credentials
- Test data ready

---

#### Phase 5: Test Execution
**Goal**: Execute test cases and report bugs

**Activities**:
```
1. Execute test cases
   "Run each test case step by step"

2. Compare results
   "Expected vs Actual - are they same?"

3. Report defects
   "Document bugs found in tool like JIRA"

4. Log test results
   "Record pass/fail for each test"

5. Retest fixes
   "Verify bugs were fixed"
```

**Test Execution Example**:
```
Day 1: Execute tests
├─ Test Case TC_001: Valid Login
│  ├─ Step 1: Enter email 
│  ├─ Step 2: Enter password 
│  ├─ Step 3: Click login 
│  ├─ Expected: Redirect to dashboard 
│  └─ Result: PASS 

├─ Test Case TC_002: Invalid Login
│  ├─ Step 1: Enter wrong email 
│  ├─ Step 2: Enter wrong password 
│  ├─ Step 3: Click login 
│  ├─ Expected: Error message 
│  └─ Result: PASS 

├─ Test Case TC_003: Add to Cart
│  ├─ Step 1: Select product 
│  ├─ Step 2: Click Add to Cart 
│  ├─ Step 3: Verify in cart ✗ (BUG!)
│  ├─ Expected: Product in cart
│  ├─ Actual: Product NOT in cart
│  └─ Result: FAIL

Day 2: Report bug and retest
├─ BUG: "Add to cart not working"
│  ├─ Severity: HIGH
│  ├─ Assigned to: Developer John
│  └─ Status: NEW
│
├─ Developer fixes bug
├─ QA retests TC_003
├─ Result: PASS 
└─ Bug closed

Progress:
- Total tests: 500
- Passed: 485
- Failed: 15
- Pass rate: 97%
```

**QA Deliverable**:
- Execution report
- Bug reports
- Test coverage metrics

---

#### Phase 6: Test Closure
**Goal**: Finalize testing and hand over to deployment

**Activities**:
```
1. Review test results
   "What's the overall quality?"

2. Create closure report
   "Summary of testing done"

3. Lessons learned
   "What did we learn? How to improve?"

4. Archive test data
   "Save all test cases for future reference"

5. Handover to deployment
   "Ready to go live?"
```

**Closure Report Includes**:
```
TESTING SUMMARY REPORT

1. EXECUTION SUMMARY
   Total test cases: 500
   Passed: 485
   Failed: 15
   Pass rate: 97%

2. DEFECT SUMMARY
   Critical bugs: 0 (fixed all)
   Major bugs: 0 (fixed all)
   Minor bugs: 2 (3 deferred to next release)
   Total bugs found: 185
   Bugs fixed: 182
   Bug fix rate: 98.4%

3. COVERAGE
   Feature coverage: 100%
   Code coverage: 94%
   Risk coverage: 100%

4. QUALITY METRICS
   Defect density: 2 bugs per 1000 lines
   Average bug fix time: 2 days
   Test execution efficiency: 98%

5. DEPLOYMENT READINESS
   Recommendation: READY TO DEPLOY 
   Rationale: All critical bugs fixed, coverage excellent

6. LESSONS LEARNED
   What went well:
   - Good communication with developers
   - Automated tests saved time

   What to improve:
   - Need more test environment setup time
   - Should start testing earlier

7. NEXT STEPS
   - Deploy to production
   - Setup monitoring
   - Support users
   - Plan for next release
```

---

## QA Involvement in Each Phase

### SDLC Phase vs STLC Phase Mapping

```
SDLC: Requirement Analysis
       ↓
STLC: Requirement Analysis (QA involved)
      - Review requirements for testability
      - Identify test conditions
      - Create RTM (Requirements Traceability Matrix)

SDLC: Design
       ↓
STLC: Test Planning
      - Create test plan
      - Estimate testing effort
      - Select testing tools

SDLC: Development
       ↓
STLC: Test Case Design
      - Design test cases
      - Prepare test data
      - Setup test environment (partial)

SDLC: Testing (traditionally)
       ↓
STLC: Full Testing Phase
      - Complete environment setup
      - Execute tests
      - Report bugs
      - Test closure

SDLC: Deployment
       ↓
STLC: Smoke Testing
      - Quick validation of deployment
      - Sanity testing

SDLC: Maintenance
       ↓
STLC: Regression Testing
      - Test after fixes
      - Continuous testing
```

---

## Understanding the Application

### Before Testing Begins

#### 1. **Read Requirements**
```
QA Task: Fully understand what software should do

Example Requirements:
"User can add products to shopping cart"

QA Questions:
- How many products can be added? (Limit?)
- Can they remove products from cart?
- What if quantity = 0? (Delete item?)
- What if cart is empty? (Error message?)
- Can prices change while product in cart?
- Time limit before cart expires?
```

#### 2. **Understand Business Logic**
```
E-commerce Cart Example:

Business Rules:
1. Each user has 1 cart
2. Cart expires after 24 hours
3. Quantity max: 999 per product
4. Min purchase: $10
5. Delivery available in 5 states only
6. Can't checkout with expired payment

QA must know ALL these rules to test properly
```

#### 3. **Understand User Workflow**
```
Happy Path (Normal flow):
1. Browse products
2. Add to cart
3. Review cart
4. Proceed to checkout
5. Enter delivery address
6. Enter payment
7. Confirm order
8. Receive confirmation

Sad Path (What if things go wrong):
1. Network fails during checkout → Handle gracefully
2. Payment fails → Retry option
3. Server error → Friendly error message
4. User leaves mid-checkout → Save cart
```

#### 4. **Technical Understanding**
```
What QA Needs to Know:
- Frontend: Web, mobile app, desktop app?
- Backend: Which technologies?
- Database: What data stored where?
- APIs: How do systems communicate?
- Security: Encryption, authentication?

Why?
- Understand what could go wrong
- Know how to test thoroughly
- Communicate effectively with developers
```

---

## Identifying the Problem Statement

### What is a Problem Statement?

**Definition**: A clear description of what needs to be tested and why

### How to Identify Problem Statement

#### Step 1: Analyze Requirements
```
Raw Requirement:
"User should be able to login"

Questions:
1. With what? (email, username, both?)
2. How? (password, 2FA, biometric?)
3. Success criteria? (dashboard shown?)
4. Error handling? (wrong password, account locked?)
5. Security? (password hashed, session secure?)
```

#### Step 2: Define Testable Scenarios
```
Login Feature Problem Statement:

User should be able to:
1. Login with valid credentials (email + password)
2. Logout after login
3. Receive error if credentials invalid
4. Have session timeout after 30 minutes
5. Reset password if forgotten
6. Login should be secure (HTTPS, encrypted)

Each becomes a test scenario
```

#### Step 3: Identify Risks
```
Login Feature Risks:

High Risk:
- Hackers can guess passwords (brute force)
- Password exposed in transit
- Session can be hijacked

Medium Risk:
- Login page load time slow
- Not accessible on mobile

Low Risk:
- UI colors not perfect
- Font not ideal
```

#### Step 4: Create Test Conditions
```
Test Conditions for Login:

Valid Credentials Tests:
- TC1: Login with correct email and password
- TC2: Login with username instead of email
- TC3: Login with special characters in password

Invalid Credentials Tests:
- TC4: Login with wrong password
- TC5: Login with non-existent email
- TC6: Login with empty email/password

Security Tests:
- TC7: SQL injection in email field
- TC8: XSS attack in password field
- TC9: Brute force attack (10+ attempts)

Performance Tests:
- TC10: Login page load < 2 seconds
- TC11: Login processing < 1 second
```

---

## Real-World Bug Discovery

### Case Study: Payment Feature Testing

#### Scenario
```
Feature: Process Payment
Requirement: "User can pay securely using credit card"

Phase 1: Requirement Analysis (QA involved)
├─ QA reads requirement
├─ QA asks: "How secure?" 
│  Dev: "PCI-DSS compliant"
├─ QA: "What happens if payment fails?"
│  Dev: "Retry automatically"
├─ QA: "What's the limit?"
│  Dev: "$10,000 max"

QA Identifies Risk: 
- Payment is critical, high security risk
- Must test thoroughly for security

Phase 2: Test Planning (QA involved)
├─ QA plans: "Extra security testing needed"
├─ QA adds: SQL injection tests, encryption tests
├─ QA allocates: 20% of time to security

Phase 3: Test Design (QA involved)
├─ QA designs test case: "SQL injection in card field"
│  Input: ' OR '1'='1
│  Expected: Rejected securely
│  Actual: [To be filled]

Phase 4: Environment Setup (QA involved)
├─ QA sets up: Secure test environment
├─ QA ensures: Test data encrypted
├─ QA verifies: Isolated from production

Phase 5: Execution (Testing starts!)
├─ QA runs: SQL injection test
├─ Result: FAIL! SQL injection worked!
│  ├─ BUG FOUND: Payment not secure!
│  ├─ Severity: CRITICAL
│  ├─ Impact: Hackers can steal data
│  └─ Reported immediately

Phase 6: Fix & Retest
├─ Dev: "This is critical, fixing now"
├─ Dev: Fixed SQL injection vulnerability
├─ QA: Re-tests → PASS 
├─ QA: "Now secure, ready to deploy"

RESULT: Critical security bug found in test phase!
If not found: Would've been hacked in production
Saved: Millions in damages, customer trust, reputation
```

---

## Importance of Early Testing 

### Testing Throughout SDLC

####  Best Practice: Early Testing Involvement
```
SDLC Phase      Testing Activity
─────────────────────────────────
Requirements    Review for testability
Design          Review design for testability
Development     Unit testing, integration testing
Testing Phase   Full QA testing
Deployment      Smoke testing
Maintenance     Regression testing

Result: Quality built throughout, not tested in at end
```

####  Traditional Approach: Late Testing
```
SDLC Phase      Testing Activity
─────────────────────────────────
Requirements    (QA not involved)
Design          (QA not involved)
Development     (QA not involved, dev tests own code)
Testing Phase   QA starts here! (LATE!)
Deployment      (May not be ready)
Maintenance     (Bugs found in production)

Result: Many bugs missed, late discovery, expensive fixes
```

### Benefits of Early Testing

#### 1. **Find Issues Early**
```
Cost of Fixing Bug:
- Requirements phase: $100
- Design phase: $500
- Development phase: $1,000
- Testing phase: $5,000
- Production: $50,000

Early testing = Cheap fixes
Late testing = Expensive fixes
```

#### 2. **Prevent Costly Mistakes**
```
Example: Scalability issue
Phase: Design
If caught: "Redesign architecture" (2 weeks)
If missed to production: "Rebuild entire system" (3 months)
```

#### 3. **Better Requirements**
```
QA in Requirement Phase:
"How do we test this?"
This question reveals:
- Ambiguous requirements
- Missing details
- Misunderstandings

Result: Better, clearer requirements
```

#### 4. **Testable Design**
```
QA in Design Phase:
"Can we test this design?"
Good: "Yes, clear interface"
Bad: "No, too complex, untestable"

Result: Design improved for testability
```

#### 5. **Quality Culture**
```
When QA involved from start:
- Developers code with testing in mind
- Code is cleaner
- Code is more testable
- Fewer bugs by design

Result: Better quality overall
```

---

## TOPIC 2: TEST PLANNING & TEST CASES

## What is a Test Plan?

### Definition
**Test Plan** is a comprehensive document that outlines the testing strategy, scope, resources, schedule, and approach for a software project.

### Why Test Plans Matter

```
Without Test Plan:
├─ No clear strategy
├─ Unclear scope (what to test?)
├─ Unallocated resources
├─ Unknown timeline
└─ Chaos! Bugs missed, quality poor

With Test Plan:
├─ Clear testing strategy
├─ Defined scope (know what to test)
├─ Allocated resources (right people)
├─ Clear timeline
└─ Organized approach, quality assured
```

### Test Plan vs Test Case

```
Test Plan:     "How will we test?"
               Strategy, approach, timeline
               Used by: QA Lead, managers
               
Test Cases:    "What specific things will we test?"
               Step-by-step test instructions
               Used by: QA engineers, testers
```

---

## Key Elements of Test Plan

### 1. **Test Objectives**
```
Why are we testing?

Examples:
- Ensure all features work correctly
- Find critical bugs before deployment
- Validate performance requirements
- Verify security standards met
- Confirm usability acceptable
```

### 2. **Scope**
```
What will be tested? What won't be?

Example:
Test:
-  Login feature
-  Checkout feature
-  Payment processing
-  Mobile app

Don't Test:
-  Admin backend (separate QA)
-  Third-party APIs (their responsibility)
-  Integrations with other systems (later)
```

### 3. **Testing Types**
```
What types of testing will we do?

- Functional testing: Does it work?
- Performance testing: Is it fast?
- Security testing: Is it secure?
- Usability testing: Is it easy to use?
- Compatibility testing: Works on all devices?
- Regression testing: Did we break something?
```

### 4. **Schedule**
```
When will each phase happen?

Example:
Week 1: Planning (40 hours)
Week 2: Design (60 hours)
Week 3: Environment setup (20 hours)
Week 4-5: Execution (80 hours)
Week 6: Reporting (20 hours)

Total: 220 hours = ~5 weeks for one tester
```

### 5. **Resources**
```
Who, what, and how much?

People:
- 4 QA testers
- 1 QA lead
- 1 QA architect

Tools:
- Selenium (automation)
- Postman (API testing)
- JIRA (bug tracking)
- JMeter (performance)

Budget:
- Tools license: $20,000
- People cost: $80,000
- Infrastructure: $10,000
- Total: $110,000
```

### 6. **Entry Criteria**
```
When can testing start?

Must have:
-  Requirements finalized
-  Build available and stable
-  Test environment setup
-  Test data prepared
-  Team trained on tools
-  Approval to start

If any missing: Wait! Don't start testing yet
```

### 7. **Exit Criteria**
```
When is testing done?

Must achieve:
-  All planned test cases executed
-  Critical bugs fixed and verified
-  Code coverage > 90%
-  No known critical defects
-  Quality metrics acceptable
-  Stakeholder approval

If not met: Continue testing! Don't deploy yet
```

### 8. **Risk & Mitigation**
```
What could go wrong?

Risk 1: Not enough testers
├─ Likelihood: Medium
├─ Impact: Low (timeline slips)
└─ Mitigation: Hire contractors if needed

Risk 2: Unstable build
├─ Likelihood: High
├─ Impact: High (testing blocked)
└─ Mitigation: Daily builds, quick fix process

Risk 3: Complex feature hard to test
├─ Likelihood: Medium
├─ Impact: Medium (bugs missed)
└─ Mitigation: Extra testing time, specialist involved
```

---

## Writing Basic Test Plans

### Simple Test Plan Template

```
PROJECT: [Project Name]
DATE: [Date]
CREATED BY: [QA Lead Name]
VERSION: 1.0

1. OBJECTIVE
   - Ensure E-commerce website quality before launch
   - Find and fix critical bugs
   - Achieve 95% pass rate

2. SCOPE
   Test:
   - User registration
   - Login/Logout
   - Product browsing
   - Shopping cart
   - Checkout & payment
   - Order history
   - Account management

   Don't Test:
   - Admin backend
   - Email service (third-party)
   - Payment gateway (third-party responsibility)

3. TESTING TYPES
   - Functional: All features work correctly
   - Performance: Page loads < 2 seconds
   - Security: No SQL injection, XSS vulnerabilities
   - Compatibility: Chrome, Firefox, Safari, Edge
   - Mobile: iOS and Android apps
   - Usability: User-friendly interface

4. SCHEDULE
   Week 1: Planning & design (40 hrs)
   Week 2: Environment setup (30 hrs)
   Week 3-4: Execution (160 hrs)
   Week 5: Reporting & closure (30 hrs)
   Total: 260 hours

5. RESOURCES
   Team:
   - 4 QA Engineers
   - 1 QA Lead
   Total: 260 hours = 6.5 weeks (one person)
   
   Tools:
   - Selenium WebDriver
   - Postman
   - JIRA
   - JMeter

6. ENTRY CRITERIA
    Requirements finalized
    Build stable for testing
    Test environment ready
    Test data prepared
    Team trained

7. EXIT CRITERIA
    All 500+ test cases executed
    Critical bugs = 0 (all fixed)
    Major bugs < 5
    Code coverage > 90%
    Manager approval

8. RISKS & MITIGATION
   Risk: Unstable build
   Mitigation: Daily build validation
   
   Risk: Not enough testers
   Mitigation: Hire contractors
   
   Risk: Late requirement changes
   Mitigation: Freeze requirements 1 week before testing

9. APPROVAL
   QA Lead: ________________  Date: ______
   Manager: ________________  Date: ______
```

---

## Introduction to Test Cases

### What is a Test Case?

**Definition**: A detailed set of steps to execute a test, with expected and actual results.

### Test Case Structure

```
TEST CASE ID: TC_001

TEST Case Scenario: Varify User login with email

Test Title: User can login with valid email

Precondition: 
  - User on login page
  - User account exists (noman@example.com)

Test Steps:
  1. Enter email: noman@example.com
  2. Enter password: SecurePass123
  3. Click "Login" button
  4. Wait for page load (< 2 seconds)

Test Data:
   -noman@example.com<Valid user Email>
   -SecurePass <Valid User password>

Expected Result:
  - Login successful
  - Redirected to dashboard
  - User name visible in top-right
  - No error messages

Actual Result:
  [Tester fills this during execution]

Pass/Fail:
  [Tester marks during execution]

Notes:
  - Tested on Chrome browser
  - Tested on Windows 10
  - Works as expected
```

---

## Writing Effective Test Cases

### Good Test Case Characteristics

#### 1. **Clear and Specific**
```
Bad:  "Test login"
Good: "Test user can login with valid email and password"

Bad:  "Enter data"
Good: "Enter email 'noman@example.com' and password 'SecurePass123'"
```

#### 2. **Independent**
```
Bad: "First test login, then test add to cart"
     (Test 2 depends on test 1)

Good: "Test add to cart (assume already logged in)"
      (Each test works alone)
```

#### 3. **Measurable**
```
Bad: "Page should load fast"
Good: "Page should load in < 2 seconds"

Bad: "User should see their products"
Good: "User should see exactly 5 products in list"
```

#### 4. **Repeatable**
```
Bad: Test result depends on time of day
Good: Test gives same result every time

Example:
Bad: "Test email arrival (may delay)"
Good: "Test email is in queue (immediate)"
```

#### 5. **Maintainable**
```
Bad Test Case:
  Step 1: Enter email, password, etc. (no detail)
  Expected: Works (vague)

Good Test Case:
  Step 1: Click email field
  Step 2: Enter 'noman@example.com'
  Step 3: Click password field
  Step 4: Enter 'SecurePass123'
  Step 5: Click login button
  Expected: Redirect to dashboard within 2 seconds
```

### Test Case Writing Tips

#### 1. **Use Consistent Terminology**
```
Good:
- Click (not "select", "press", "hit")
- Enter (not "input", "type", "put")
- Verify (not "check", "see", "look at")
- Expected Result (not "should be", "might be")
```

#### 2. **Include Test Data**
```
Bad:  "Enter email"
Good: "Enter email: testuser@example.com"

Bad:  "Enter password"
Good: "Enter password: SecurePass@123"
```

#### 3. **Numbering Steps**
```
Step 1: Action 1
Step 2: Action 2
Step 3: Action 3

Easy to follow, easy to execute
```

#### 4. **Document Prerequisites**
```
Precondition:
- User already logged in
- User has items in cart
- Browser: Chrome v90+
- OS: Windows 10
- Network: Good connectivity
```

#### 5. **Include Expected Results**
```
Bad:  "Product added"
Good: "Product added to cart
       - Cart count increased by 1
       - Total price updated correctly
       - Confirmation message shown
       - No errors"
```

---

## Test Checklist Importance

### What is a Test Checklist?

**Definition**: A simple list of items to verify, without detailed steps

### Test Checklist vs Test Case

```
Test Case:
├─ Detailed steps
├─ Expected results for each step
├─ Preconditions
├─ Test data
└─ Time to execute: 10-15 minutes

Test Checklist:
├─ Just items to verify
├─ Quick reference
├─ No detailed steps
└─ Time to execute: 5 minutes
```

### Example Test Checklist

```
LOGIN FEATURE CHECKLIST:

Basic Functionality:
  Can login with valid credentials
  Cannot login with invalid credentials
  Error message shown for wrong password
  Can logout after login

Security:
  Password field masked (not visible)
  No password stored in browser history
  SSL/HTTPS used (secure connection)
  Session timeout works

Performance:
  Login page loads < 2 seconds
  Login processing < 1 second
  No lag with 1000 users

UI/UX:
  Login form visible and centered
  Error messages clear and helpful
  "Forgot password" link present
  Mobile responsive

Cross-Browser:
  Works on Chrome
  Works on Firefox
  Works on Safari
  Works on Edge
```

### When to Use Checklist

```
Use Checklist for:
- Smoke testing (quick validation)
- Regression testing (repeated tests)
- Quick UAT (user acceptance)
- Exploratory testing (reference)

Don't use for:
- Detailed functional testing (need steps)
- Performance testing (need measurements)
- Security testing (need specific payloads)
```

---

## Common Test Case Writing Mistakes

###  Mistake 1: Too Vague

```
BAD:
Step 1: Enter information
Expected: It works

GOOD:
Step 1: Click email field
Step 2: Enter 'noman@example.com'
Step 3: Click password field
Step 4: Enter 'SecurePass123'
Expected: Login successful, redirect to dashboard
```

---

###  Mistake 2: Missing Preconditions

```
BAD:
Test: Add product to cart
Step 1: Click Add button
Expected: Product in cart

GOOD:
Precondition: User logged in, on product page
Test: Add product to cart
Step 1: Click Add button
Expected: Product in cart
```

---

###  Mistake 3: Not Measurable

```
BAD:
Expected: Page should be fast

GOOD:
Expected: Page loads within 2 seconds (measured with browser dev tools)
```

---

###  Mistake 4: Too Complex

```
BAD:
Step 1: Login, add product, checkout, pay (all in one!)
Expected: Order placed

GOOD:
Separate test cases:
TC1: Login
TC2: Add to cart
TC3: Checkout
TC4: Payment
```

---

###  Mistake 5: Not Repeatable

```
BAD:
Step 1: Wait for someone to send email
Expected: Email arrives

GOOD:
Step 1: Verify email template in database
Expected: Template exists with correct content
```

---

## Manual vs Template-Based Checklists

### Manual Checklists

```
Format: Paper or simple text list

Example:
 Login works
 Password protected
 Mobile responsive
 Fast load time

Pros:
+ Quick to create
+ Easy to use
+ No tool required

Cons:
- Hard to track history
- No metrics
- Easy to forget items
- Loss of information
```

---

### Template-Based Checklists

```
Format: Structured document or tool

Example in Excel:
┌──────────────────┬────────┬──────────────┐
│ Checklist Item   │ Status │ Date Checked │
├──────────────────┼────────┼──────────────┤
│ Login works      │  tick  │  2024-01-15  │
│ Password protect │  tick  │  2024-01-15  │
│ Mobile responsive│  tick  │  2024-01-15  │
│ Fast load time   │ cross  │  2024-01-15  │
└──────────────────┴────────┴──────────────┘

Pros:
+ Organized structure
+ Easy to track
+ Can generate metrics
+ Professional documentation

Cons:
- Takes time to create template
- May be overkill for simple tests
```

---

## Key Takeaways

###  STLC Summary

1. **STLC is Structured**
   - 6 clear phases
   - Each phase has purpose
   - QA involved throughout

2. **Test Planning is Critical**
   - Define scope clearly
   - Plan resources
   - Set realistic timeline

3. **Test Cases are Foundation**
   - Detailed step-by-step
   - Clear expected results
   - Repeatable

4. **Early Testing Saves Money**
   - Bugs found early cost less
   - Better quality overall
   - Customer satisfaction higher

5. **Checklists are Helpful**
   - Quick reference
   - Useful for regression
   - Track what's tested

---

## Summary Table

| Phase | Input | Output | QA Role |
|-------|-------|--------|---------|
| Requirement Analysis | Requirements | RTM, Test conditions | Review for testability |
| Test Planning | Requirements | Test plan | Create strategy |
| Test Case Design | Requirements | Test cases | Design detailed tests |
| Environment Setup | Needs | Ready environment | Prepare and verify |
| Test Execution | Test cases | Bug reports | Execute and report |
| Test Closure | Results | Closure report | Summarize and archive |

---

##  Next Steps

1. **Review**: Understand all STLC phases
2. **Practice**: Create a simple test plan
3. **Apply**: Write test cases for a feature
4. **Prepare**: Move to Manual Testing (Module 2)

---

**Last Updated**: 2026  
**Difficulty Level**: Beginner-Intermediate  
**Time to Complete**: 90 minutes

---

> **"Good test cases are the foundation of quality software testing."**

> **"Testing early prevents expensive problems later."**
 
