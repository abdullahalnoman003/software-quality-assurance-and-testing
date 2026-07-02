# Section 8: Project Week 1 - Problem Identification & Planning

> **"If you fail to plan, you are planning to fail." — Benjamin Franklin**
> This section bridges the gap between software testing theory and practical project implementation. You will learn how to analyze a business problem, gather requirements, perform stakeholder analysis, write a Software Requirement Specification (SRS), create a Work Breakdown Structure (WBS), estimate time/resources, and manage risks.

---

##  Table of Contents
1. [Topic 1: Problem Identification & Requirement Gathering](#topic-1-problem-identification--requirement-gathering)
   - [Understanding the Problem Domain](#11-understanding-the-problem-domain)
   - [Gathering Functional & Non-Functional Requirements](#12-gathering-functional--non-functional-requirements)
   - [Stakeholder Analysis & Communication](#13-stakeholder-analysis--communication)
   - [Creating a Software Requirement Specification (SRS)](#14-creating-a-software-requirement-specification-srs)
2. [Topic 2: Project Planning & Risk Management](#topic-2-project-planning--risk-management)
   - [Work Breakdown Structure (WBS)](#21-work-breakdown-structure-wbs)
   - [Estimating Time and Resources](#22-estimating-time-and-resources)
   - [Risk Management & Mitigation Strategy](#23-risk-management--mitigation-strategy)
   - [Project Milestones & Deliverables](#24-project-milestones--deliverables)
3. [Summary & Self-Assessment Checklist](#summary--self-assessment-checklist)

---

# Topic 1: Problem Identification & Requirement Gathering

## 1.1 Understanding the Problem Domain

Before writing a single test case or testing code, a QA professional must understand the **business problem** the software is trying to solve. 

### Why is this important?
- If you don't understand the problem, you won't know if the solution actually works.
- Testing isn't just checking if buttons click; it's verifying if the software delivers **business value**.
- Early QA involvement in requirement reviews prevents up to **60% of coding bugs** before development even starts.

###  Real-World Case Study: "E-Grocery Express"
Imagine a physical supermarket chain named **SuperMart** that noticed a 30% drop in foot traffic because competitors are offering home delivery. 

```
┌────────────────────────────────────────────────────────┐
│                    BUSINESS PROBLEM                    │
│ "Customers find it inconvenient to visit physical      │
│  stores due to busy schedules, leading to sales loss." │
└───────────────────────────┬────────────────────────────┘
                            │
                            ▼
┌────────────────────────────────────────────────────────┐
│                   PROPOSED SOLUTION                    │
│ "Build a mobile app allowing customers to browse, cart,│
│  pay, and schedule delivery for groceries."            │
└────────────────────────────────────────────────────────┘
```

---

## 1.2 Gathering Functional & Non-Functional Requirements

Requirements are specifications of what the software must do. They are divided into two main categories:

| Feature | Functional Requirements (FR) | Non-Functional Requirements (NFR) |
| :--- | :--- | :--- |
| **Definition** | What the system **must do** (behaviors, features). | How the system **must perform** (quality, constraints). |
| **Focus** | User actions, inputs, outputs, processes. | Performance, security, usability, reliability. |
| **Analogy** | A car must have 4 wheels, a steering wheel, and brakes. | The car must go 0-60 mph in 5 seconds and be safe. |

### E-Grocery App Examples

#### Functional Requirements (FR):
1. **User Auth**: A user can register using an email, password, and phone number.
2. **Catalog Browser**: A user can search for items by name or filter by category (e.g., "Dairy", "Fruits").
3. **Cart Operations**: A user can add items to a cart, modify quantities, and see a running total cost.
4. **Checkout**: A user can pay using Mobile Financial Services (MFS) like bKash/Nagad or credit cards.
5. **Real-time Tracking**: A user can view the live status of their order (e.g., "Order Placed", "In Transit", "Delivered").

#### Non-Functional Requirements (NFR):
1. **Performance**: The app page transition time must not exceed **2 seconds** under normal load.
2. **Security**: All user passwords must be encrypted using SHA-256 before storing in the database.
3. **Availability**: The system must maintain a **99.9% uptime** (less than 8.76 hours of downtime per year).
4. **Scalability**: The application backend must support up to **50,000 concurrent users** during weekend peak hours (Friday-Saturday 7 PM - 10 PM).
5. **Usability**: The app must comply with WCAG 2.1 AA accessibility guidelines (contrast, screen-reader support, fonts >= 14px).

---

## 1.3 Stakeholder Analysis & Communication

### Who is a Stakeholder?
A stakeholder is anyone who has an interest in the project. For QA, collaboration with different stakeholders is key to understanding and testing the system correctly.

```
                  ┌────────────────────────┐
                  │      PRODUCT OWNER     │ (Defines requirements &
                  └───────────┬────────────┘  business goals)
                              │
  ┌────────────────────────┐  │  ┌────────────────────────┐
  │     DEVELOPMENT TEAM   │◄─┼─►│        QA TEAM         │ (Validates against
  └────────────────────────┘  │  └────────────────────────┘  the requirements)
 (Builds the functionality)   │
                  ┌───────────┴────────────┐
                  │       END USERS        │ (Use the application
                  └────────────────────────┘  in real world)
```

### Communication Strategy & Questionnaire
To capture requirements accurately, the QA and Product teams conduct interviews. Here is a sample questionnaire to ask during requirement gathering:

1. **Who is the end user?** (Ages, tech-savviness, language, device usage).
2. **What are the primary checkout channels?** (Mobile wallets, cards, Cash-on-Delivery).
3. **What is the failover mechanism if payment gateway goes down?** (Should the order save as "pending payment" or fail immediately?).
4. **Are there regional constraints?** (Delivery radius, location limits).

---

## 1.4 Creating a Software Requirement Specification (SRS)

A **Software Requirement Specification (SRS)** is a formal document describing what the software will do and how it will be expected to perform.

###  E-Grocery Express SRS Outline (Drafted for QA)

```markdown
# Software Requirement Specification (SRS) - E-Grocery Express v1.0

## 1. Introduction
This document outlines requirements for E-Grocery Express, a mobile delivery app.

## 2. Overall Description
The app connects retail grocery customers with regional inventory stores.

## 3. System Features & Functional Requirements
### 3.1 User Account Management
- **FR-001**: Register with validated mobile number (OTP verification).
- **FR-002**: Login with PIN/Password or Biometric (Fingerprint/FaceID).

### 3.2 Product Catalog
- **FR-003**: Display category tree.
- **FR-004**: Real-time stock status (Disable "Add to Cart" if stock = 0).

## 4. Non-Functional Requirements
- **NFR-001**: App must load within 1.5 seconds on 4G networks.
- **NFR-002**: Payment gateways must process transactions securely over SSL/TLS.
- **NFR-003**: Support 10,000 requests/minute.
```

---

# Topic 2: Project Planning & Risk Management

## 2.1 Work Breakdown Structure (WBS)

A **Work Breakdown Structure (WBS)** is a hierarchical decomposition of the total scope of work to be carried out by the project team. It breaks down large deliverables into manageable "work packages."

###  WBS for E-Grocery Express (QA & Dev Breakdown)

```
E-Grocery Express Project
├─ 1. Planning & Requirements
│  ├─ 1.1 Stakeholder Interviews
│  ├─ 1.2 SRS Document Drafting
│  └─ 1.3 SRS Walkthrough & Sign-off
├─ 2. System Design
│  ├─ 2.1 UI/UX Wireframes (Mobile screens)
│  ├─ 2.2 Database Schema Design (SQL/NoSQL models)
│  └─ 2.3 System Architecture Design (Microservices mapping)
├─ 3. Software Development
│  ├─ 3.1 Backend API Development (User, Cart, Payment, Order services)
│  ├─ 3.2 Frontend Mobile App (Android & iOS implementations)
│  └─ 3.3 Payment Gateway Integration (bKash, SSLCommerz)
├─ 4. Quality Assurance & Testing
│  ├─ 4.1 Test Plan Creation & Review
│  ├─ 4.2 Test Case Design (Functional, Integration, Edge cases)
│  ├─ 4.3 Test Execution (Manual passes on Android/iOS)
│  ├─ 4.4 Defect Lifecycle (Log, Retest, Close bugs)
│  └─ 4.5 Non-Functional Testing (Performance & Security runs)
└─ 5. Deployment & Release
   ├─ 5.1 Play Store & App Store submissions
   ├─ 5.2 Production Server Setup
   └─ 5.3 Live Smoke Testing
```

---

## 2.2 Estimating Time and Resources

For a project to succeed, estimates must be realistic. SQA engineers use several standard techniques to estimate testing timelines:

### Estimation Methods

1. **3-Point Estimation (PERT)**
   Calculate expected duration using three values:
   - **Optimistic ($O$)**: Everything goes perfectly.
   - **Pessimistic ($P$)**: Everything goes wrong.
   - **Most Likely ($M$)**: Normal conditions.
   
   $$\text{Expected Time (E)} = \frac{O + 4M + P}{6}$$

   *Example for writing 100 test cases:*
   - $O = 5\text{ hours}$, $P = 15\text{ hours}$, $M = 8\text{ hours}$.
   - $E = \frac{5 + 4(8) + 15}{6} = \frac{52}{6} = 8.67\text{ hours}$.

2. **Planning Poker (Agile)**
   The team meets, reads a user story, and simultaneously votes using cards (Fibonacci scale: 1, 2, 3, 5, 8, 13, 21) representing effort points. High and low estimators discuss until a consensus is reached.

### Resource Allocation Matrix

| Activity | Assigned Resource | Role | Allocated Effort |
| :--- | :--- | :--- | :--- |
| Test Strategy & Plan | QA Lead | Manager | 10 Hours |
| Functional Test Design | Junior QA Tester | Author | 40 Hours |
| API Automated Scripts | Automation QA | Developer | 30 Hours |
| Manual Execution | Junior QA Tester | Executor | 50 Hours |

---

## 2.3 Risk Management & Mitigation Strategy

A risk is any uncertain event that, if it occurs, has a negative effect on the project. SQA engineers perform risk analysis to prioritize testing.

### The Risk Assessment Matrix

```
   P R O B A B I L I T Y
H │ Medium Risk   │ High Risk     │ Critical Risk │
M │ Low Risk      │ Medium Risk   │ High Risk     │
L │ Low Risk      │ Low Risk      │ Medium Risk   │
  └───────────────┴───────────────┴───────────────
         L                M               H
             I M P A C T
```

###  E-Grocery Project Risk Register

| Risk ID | Risk Description | Probability | Impact | Mitigation Strategy (QA Role) |
| :--- | :--- | :--- | :--- | :--- |
| **RSK-001** | Payment gateway API is unstable or poorly documented. | High (H) | High (H) | **Mitigation**: Mock API responses early using Postman Mock Servers. Write test cases for failure/timeout responses. |
| **RSK-002** | App crashes when mobile network drops to 2G/No internet. | Medium (M) | Critical (C) | **Mitigation**: Conduct network throttling testing during manual passes. Verify offline data storage. |
| **RSK-003** | Core developers leave mid-project. | Low (L) | High (H) | **Mitigation**: Ensure strict coding guidelines, code documentation, and detailed test steps so anyone can execute. |
| **RSK-004** | Deliverables delayed by development. | High (H) | Medium (M) | **Mitigation**: Perform early API/Database testing before UI is ready. Test incrementally in sprints. |

---

## 2.4 Project Milestones & Deliverables

Milestones are key check-points used to monitor project health. Here is a sample QA timeline for E-Grocery Express:

```
Month 1: Planning & Design
├── Week 1: SRS Approved (Milestone 1)
└── Week 2: QA Test Plan Signed-off (Milestone 2)

Month 2: Development & QA Iterations
├── Week 4: API Testing Complete (Milestone 3)
└── Week 6: UI and payment gateway integrated

Month 3: Regression & Release
├── Week 8: System & Regression Testing Done (Milestone 4)
├── Week 9: User Acceptance Testing (UAT) Sign-off (Milestone 5)
└── Week 10: App Live on App Stores! (Milestone 6)
```

---

## Summary & Self-Assessment Checklist

### Key Takeaways
- **Requirement Analysis** is the first step in any testing lifecycle. QA must categorize requirements into Functional and Non-Functional types.
- **SRS Documents** guide developers on building features and guide testers on how to validate them.
- **WBS** breaks down tasks so that timelines can be calculated using models like **3-Point (PERT) Estimation**.
- **Risk registers** help identify critical items early to prevent bugs in production.

###  Review Questions
1. What is the key difference between a functional requirement and a non-functional requirement? Give one grocery app example of each.
2. If your team estimates a testing task with an Optimistic time of 3 days, Pessimistic time of 10 days, and Most Likely time of 5 days, what is the expected time ($E$) according to the PERT formula?
3. What is a "Risk Mitigation Strategy" and how does it help a QA engineer decide what to test first?
