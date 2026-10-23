# Product Requirements Document (PRD)

# VELORA (فيلورا) — Training Center Management Platform

**Document Version:** 1.0.0  
**Status:** Approved for Implementation (MVP)  
**Target Environment:** Web Application (Responsive Desktop & Mobile)  
**Primary Language:** English  
**Date:** October 2026  

---

## Table of Contents
1. [Product Overview](#1-product-overview)
2. [Problem Statement](#2-problem-statement)
3. [Product Goals](#3-product-goals)
4. [Non-Goals](#4-non-goals)
5. [Target Users](#5-target-users)
6. [User Personas](#6-user-personas)
7. [User Needs](#7-user-needs)
8. [User Stories](#8-user-stories)
9. [MVP Scope](#9-mvp-scope)
10. [Core Features](#10-core-features)
11. [Functional Requirements](#11-functional-requirements)
12. [Non-Functional Requirements](#12-non-functional-requirements)
13. [User Flows](#13-user-flows)
14. [Pages / Screens](#14-pages--screens)
15. [Roles & Permissions](#15-roles--permissions)
16. [Data Requirements](#16-data-requirements)
17. [Integrations](#17-integrations)
18. [Edge Cases](#18-edge-cases)
19. [Error States](#19-error-states)
20. [Security & Privacy Requirements](#20-security--privacy-requirements)
21. [Analytics & Tracking](#21-analytics--tracking)
22. [Success Metrics / KPIs](#22-success-metrics--kpis)
23. [Acceptance Criteria](#23-acceptance-criteria)
24. [Future Features](#24-future-features)
25. [Risks & Dependencies](#25-risks--dependencies)
26. [Open Questions](#26-open-questions)
27. [Recommended Development Phases](#27-recommended-development-phases)

---

## 1. Product Overview

### 1.1 Product Name & Brand Identity
- **Product Name:** **VELORA** (فيلورا)
- **Tagline:** The Unified Operating System for Modern Training Centers.
- **Classification:** Multi-Tenant SaaS Web Application for Educational Administration, Operational Follow-Up, and Financial Reconciliation.

### 1.2 Product Vision & Summary
VELORA is a centralized, multi-tenant web platform engineered specifically for independent and multi-branch training centers. It bridges the critical operational gap between academic delivery, student success tracking, sales operations, and internal financial reconciliation. 

Unlike traditional Learning Management Systems (LMS) that prioritize virtual classroom software and automated grading, VELORA focuses on **operational excellence, rigorous student tracking, administrative clarity, and offline financial lifecycle management**. It provides training center directors, sales officers, instructors, and enrolled trainees with dedicated, role-tailored dashboards to conduct end-to-end course administration without relying on fragmented spreadsheets, unstructured instant messaging groups, or physical paperwork.

### 1.3 Architecture & Tenancy Model
- **Tenancy Architecture:** Logical Multi-Tenancy utilizing a single shared application codebase and a single shared PostgreSQL database instance.
- **Tenant Isolation:** Every operational business entity is strictly partitioned by a first-class tenant identifier (`CenterID`). Tenant isolation is enforced server-side at the API, service, and database query layers. Client-supplied tenant identifiers are never trusted. PostgreSQL Row-Level Security (RLS) is evaluated as an additional defense-in-depth isolation layer.
- **Preferred Technology Stack:**
  - **Frontend:** Next.js (React), Tailwind CSS, TypeScript (Responsive Web UI supporting 360px mobile viewports up to 4K desktop displays).
  - **Backend:** PHP / Laravel (RESTful API, modular domain architecture, Policy/Gate-based authorization, ACID transaction management).
  - **Database:** PostgreSQL (Relational schema, foreign keys, composite unique constraints, specialized indexes).
  - **Storage:** Local or S3-compatible Object Storage for course materials and task submissions (strictly protected by authenticated authorization gates).

### 1.4 High-Level Operational Lifecycle
```
Center Registration & Configuration
  │
  ▼
Course Creation & Multi-Instructor Assignment
  │
  ▼
Student Registration, Course Enrollment & Installment Plan Setup
  │
  ▼
Lecture/Session Scheduling & Course Material Publishing
  │
  ▼
Session-Based Attendance Recording & Task/Homework Assignment
  │
  ▼
Student Task Submission & Private Instructor Feedback
  │
  ▼
Offline Student Payment/Installment Logging & Balance Calculation
  │
  ▼
Automated Instructor Compensation Dues Calculation & Settlement Tracking
  │
  ▼
Executive Dashboard Summaries & Administrative Audit Reporting
```

---

## 2. Problem Statement

### 2.1 The Current Operational Dilemma
Training centers frequently operate in an environment characterized by disconnected tools, ad-hoc communication, and manual record-keeping:
1. **Data Fragmentation Across Spreadsheets:** Student rosters, contact information, attendance sheets, and fee collection balances are kept in multiple conflicting Excel or Google Sheets workbooks.
2. **Assignment & Follow-up Chaos:** Course materials and homework assignments are distributed across instant messaging groups (e.g., WhatsApp, Telegram) and personal emails. Submissions are routinely lost, untracked, or received without time-stamped proof of delivery.
3. **Attendance Blindspots:** Trainee attendance is recorded on paper sheets or not aggregated centrally, preventing center management and sales representatives from spotting drop-out risks early.
4. **Financial Leakage & Installment Disputes:** Courses are sold with installment arrangements. Without an automated ledger calculating `Base Price - Discount - Total Paid = Remaining Balance`, centers experience lost revenue, uncollected balances, and frequent payment disputes.
5. **Instructor Compensation Confusion:** Centers use varied compensation agreements (fixed fee, percentage of collected course revenue, or per-session payment). Calculating instructor compensation manually at the end of each cohort is error-prone, disputed, and time-consuming.
6. **Administrative Overhead:** Center managers spend excessive hours reconciling spreadsheets and following up with staff rather than expanding course offerings or improving quality.

### 2.2 The Solution
VELORA consolidates these fragmented processes into an integrated web portal. By establishing a unified relational database scoped to each center, VELORA brings order to course management, student follow-up, attendance, task accountability, offline payment recording, and instructor compensation tracking.

---

## 3. Product Goals

### 3.1 Primary Goals
- **G-01 (Operational Centralization):** Replace disparate spreadsheets, paper records, and unstructured chat threads with a single operational web system.
- **G-02 (Rigorous Academic Tracking):** Provide instructors and administrators with transparent visibility into lecture-by-lecture attendance, task delivery rates, and qualitative trainee feedback.
- **G-03 (Accurate Financial Reconciliation):** Provide an offline payment ledger that tracks student installments, calculates outstanding balances automatically, logs historical payment receipts, and eliminates double-entry errors.
- **G-04 (Automated Instructor Dues):** Accurately compute instructor financial compensation based on pre-configured contract terms (`FixedAmount`, `PercentageOfCollectedRevenue`, or `PerSession`).
- **G-05 (Multi-Role Operational Clarity):** Ensure that each user role (Manager, Sales, Instructor, Student) accesses a purpose-built workspace containing only the tools and data relevant to their responsibilities.

### 3.2 MVP Success Objective
Successfully deploy an MVP that enables a training center to operate a full cohort cycle—from course creation and student enrollment through lecture attendance, task submissions, offline payment collection, and instructor payout settlement—with 100% data integrity and zero reliance on external spreadsheets.

---

## 4. Non-Goals

To maintain high development velocity, minimize architectural complexity, and deliver an MVP that directly solves back-office chaos, the following features are **explicitly excluded** from the MVP:

- **NG-01 (Full eLearning / LMS Capabilities):** No SCORM/xAPI packaging, no interactive video progression locks, no automated code execution or essay autograding.
- **NG-02 (In-App Live Streaming & Video Conferencing):** No embedded WebRTC, Zoom SDK, or Google Meet room hosting inside the platform. Lecture session locations or external video links may be communicated via course materials or session notes.
- **NG-03 (Advanced Exam & Quiz Engine):** No timed online tests, randomized question banks, automated grading rubrics, or anti-cheating proctoring software.
- **NG-04 (In-App Direct Chat & Messaging):** No peer-to-peer instant messaging, group chat feeds, or forum discussions between users. Communication is strictly structured around task submissions and instructor-to-student feedback.
- **NG-05 (Standalone Native Mobile Applications):** No native iOS or Android apps in the MVP. The web application must be fully responsive, functional, and touch-optimized for mobile browsers (from 360px viewport width).
- **NG-06 (Double-Entry General Ledger ERP):** No general ledger accounting, automated tax filings, balance sheet equity tracking, or asset depreciation modules.
- **NG-07 (Online Payment Gateway Integration):** No live payment processing (e.g., Stripe, PayPal, Tap, Paymob) in the MVP. All payment records represent manual, verified offline transactions (cash, bank transfer, point-of-sale card terminal).
- **NG-08 (Automated Certificate Generation Engine):** No visual certificate designer, automated PDF generation, or verifiable QR-code credential verification in MVP.
- **NG-09 (Artificial Intelligence / GenAI Features):** No AI chatbots, automated homework evaluations, or AI-generated lesson plans.

---

## 5. Target Users

| User Segment | Description | Primary Value Received |
| :--- | :--- | :--- |
| **Training Center Owners / General Managers** | Executive operators managing business revenue, staffing, course rosters, and center reputation. | Total visibility over course progress, instructor liabilities, outstanding student fees, and team accountability. |
| **Academic Coordinators / Administrators** | Operational staff responsible for day-to-day cohort scheduling, room allocation, and instructor assignments. | A structured workflow to schedule lectures, assign instructors, manage materials, and track student enrollment status. |
| **Sales & Follow-Up Team Members** | Front-line administrative staff focused on trainee intake, registration inquiries, and fee installment collection. | Fast student search, clear visibility into outstanding fee balances, and follow-up notes without accessing sensitive academic grading. |
| **Instructors / Trainers** | Subject matter experts delivering lectures, evaluating assignments, and providing qualitative feedback. | Clean course interface to publish materials, issue tasks, inspect student submissions, take session attendance, and view calculated dues. |
| **Enrolled Students / Trainees** | Adult learners and professional trainees seeking structured course content and clear feedback. | Frictionless mobile-friendly portal to download materials, submit homework before deadlines, view attendance records, and verify fee balances. |

---

## 6. User Personas

### Persona 1: Ahmed — Center General Manager
- **Demographics:** 42 years old, manages a private professional training center with 450 active students and 18 freelance instructors.
- **Pain Points:** Spends hours cross-referencing WhatsApp messages and Excel sheets to determine if an instructor has completed their sessions, how much revenue was collected, and what compensation is owed. Fears unrecorded cash payments.
- **Needs:** Executive dashboard summarizing active cohorts, uncollected student balances, pending instructor dues, and an audit trail of all financial movements.
- **Tech Savviness:** Moderate. Values clarity, fast table sorting, and reliable calculations over complex UI widgets.

### Persona 2: Sarah — Sales & Student Follow-Up Specialist
- **Demographics:** 27 years old, handles trainee registrations, phone inquiries, and installment payment reminders.
- **Pain Points:** Struggles to identify which students have overdue installments without asking the accountant. Often calls students who have already paid because physical receipts take days to be logged.
- **Needs:** Searchable student directory, clear installment balances (`Total Payable`, `Total Paid`, `Remaining Balance`), ability to log manual payment receipts instantly, and enrollment status updates.
- **Tech Savviness:** High with web forms and mobile devices.

### Persona 3: Mohammed — Senior Technical Instructor
- **Demographics:** 35 years old, freelance software instructor teaching two evening cohorts across the center.
- **Pain Points:** Students submit homework via personal WhatsApp or email at all hours of the night. Has no centralized way to record attendance during the lecture, leading to lost records. Unclear how much the center owes him at month-end.
- **Needs:** Single portal to upload lecture slides, publish tasks with clear deadlines, review student submissions in one place, submit session attendance with one click, and view pending financial earnings.
- **Tech Savviness:** Very high. Demands fast response times, drag-and-drop file uploads, and zero clutter.

### Persona 4: Layla — Enrolled Trainee (Student)
- **Demographics:** 24 years old, junior graphic designer taking an evening UI/UX Bootcamp while working a full-time job.
- **Pain Points:** Misses homework deadlines because assignments are scattered across chat groups. Doesn't know if the instructor received her file or what grade/feedback was provided.
- **Needs:** Mobile-friendly dashboard displaying registered courses, lecture slides, upcoming task deadlines, submission upload button with upload confirmation, and personal payment balance receipts.
- **Tech Savviness:** Moderate mobile-first user.

---

## 7. User Needs

### 7.1 Center Manager Needs
- **UN-M01:** Centralized control over all center users, course offerings, and operational statuses.
- **UN-M02:** Ability to assign multiple instructors to a single course with distinct compensation models.
- **UN-M03:** Real-time visibility into overall financial health: total invoiced, total cash collected, remaining student receivables, and total instructor liabilities.
- **UN-M04:** Strict prevention of unauthorized record deletion (e.g., hard-deleting posted payments).

### 7.2 Sales & Follow-Up Team Needs
- **UN-S01:** Fast, responsive student search by full name, phone number, email, or enrollment ID.
- **UN-S02:** Immediate visibility into student financial balances to facilitate payment reminders.
- **UN-S03:** Ability to record verified offline payments (cash, POS, bank transfer) and issue reference numbers.
- **UN-S04:** Safeguarded permissions: restricted from viewing confidential instructor dues, instructor feedback, or altering academic materials.

### 7.3 Instructor Needs
- **UN-I01:** Simplified course workspace showing only courses they are actively assigned to teach.
- **UN-I02:** Ability to schedule sessions, upload lecture resources (PDF, ZIP, PPTX), and create tasks with deadlines.
- **UN-I03:** Fast digital attendance sheet to record student presence (`Present` / `Absent`) per lecture session.
- **UN-I04:** Clean review queue for submitted student tasks with direct private feedback notes.
- **UN-I05:** Full transparency into personal earnings and payout statuses (`Pending`, `PartiallyPaid`, `Paid`, `Adjusted`).

### 7.4 Student Needs
- **UN-ST01:** Accessible course dashboard listing enrolled active courses and recent lecture sessions.
- **UN-ST02:** One-click download of authorized course materials.
- **UN-ST03:** Clear countdown or deadline indicator for course tasks with frictionless file upload.
- **UN-ST04:** Transparent view of personal attendance records and instructor feedback.
- **UN-ST05:** Clear financial receipt ledger showing total fees, applied discounts, payments made, and remaining balance.

---

## 8. User Stories

### 8.1 Center Manager & Administrator Stories
- **US-M01:** As a Center Manager, I want to create a course with defined start/end dates, pricing, and capacity so that our center can launch new educational programs.
- **US-M02:** As a Center Manager, I want to assign one or more instructors to a course with a selected compensation model (Fixed, % Revenue, Per Session) so that instructor payouts are calculated accurately.
- **US-M03:** As a Center Manager, I want to enroll students into active courses and apply custom discounts so that the final payable balance is established immediately.
- **US-M04:** As a Center Manager, I want to view an executive dashboard showing revenue collected, outstanding student fees, and pending instructor dues so that I can make data-driven financial decisions.
- **US-M05:** As a Center Manager, I want to archive completed courses rather than deleting them so that historical academic and financial records remain permanently accessible for auditing.

### 8.2 Sales & Follow-Up Stories
- **US-S01:** As a Sales Employee, I want to search and filter students by course and payment status so that I can identify who has unpaid installments.
- **US-S02:** As a Sales Employee, I want to record student offline payments (cash, bank transfer, card receipt) so that student balances are updated in real-time.
- **US-S03:** As a Sales Employee, I want to register new student profiles with accurate contact information so that our center maintains a clean directory.
- **US-S04:** As a Sales Employee, I want to enroll students into available courses so that their seat is secured upon paying their deposit.

### 8.3 Instructor Stories
- **US-I01:** As an Instructor, I want to view only my assigned courses so that I can focus entirely on my teaching responsibilities.
- **US-I02:** As an Instructor, I want to upload lecture slides, code samples, and reference files (up to 25 MB) so that my students have access to the learning content.
- **US-I03:** As an Instructor, I want to create tasks with clear descriptions and deadlines so that students understand their homework requirements.
- **US-I04:** As an Instructor, I want to review submitted student files and attach written feedback so that students can learn from their mistakes.
- **US-I05:** As an Instructor, I want to record session attendance (`Present` or `Absent`) with a single click so that attendance is logged immediately after each lecture.
- **US-I06:** As an Instructor, I want to view my calculated earnings and payment statuses so that I have clear visibility into my dues.

### 8.4 Student Stories
- **US-ST01:** As a Student, I want to log in and view my enrolled courses so that I know what lectures and materials are available to me.
- **US-ST02:** As a Student, I want to download lecture presentations and files so that I can study independently.
- **US-ST03:** As a Student, I want to upload my homework assignment before the deadline so that my instructor can evaluate my work.
- **US-ST04:** As a Student, I want to view private feedback provided by my instructor so that I know how to improve.
- **US-ST05:** As a Student, I want to check my attendance record and fee balance so that I am always aware of my academic and payment standing.

---

## 9. MVP Scope

### 9.1 Must Have (Core MVP)
The following capabilities represent the non-negotiable functional core required for launch:
- Multi-tenant data isolation by `CenterID` validated server-side.
- Secure email/password authentication with session tokens and role-based route protection.
- Four distinct user roles: `Manager`, `Sales`, `Instructor`, `Student`.
- Complete Course Lifecycle management (`Draft`, `Active`, `Completed`, `Archived`).
- Multi-Instructor course assignments with compensation model configuration.
- CourseSession / Lecture tracking (date, time, topic, assigned instructor).
- Student Profile management and Course Enrollment (with discount handling and duplicate-prevention).
- Course Materials file uploads (up to 25 MB; PDF, Office docs, Images, ZIP) with protected access.
- Task creation with optional deadlines and submission upload handling.
- Deadline enforcement with authorized instructor/manager late submission override (flagged as `Late`).
- Session-based attendance logging (`Present`, `Absent`) with uniqueness constraint per student/session.
- Private 1-to-1 instructor-to-student feedback tied to course and student.
- Manual Student Payment tracking with installment support, payment method logging, and remaining balance calculations.
- Instructor Dues automated calculation and settlement lifecycle (`Pending`, `PartiallyPaid`, `Paid`, `Adjusted`).
- Immutable financial transaction audit rule (posted payments cannot be hard-deleted; require reversals/adjustments).
- Role-specific dashboards and essential operational/financial summary reports.
- Comprehensive pagination, search, and filtering across all list views.

### 9.2 Should Have (Post-MVP Fast Follows)
- In-app notification center for deadlines, newly posted materials, and logged payments.
- CSV / Excel export for student lists, attendance records, and financial ledger reports.
- Basic user activity history log visible to Center Managers.
- Instructor session notes and lesson plan drafts.

### 9.3 Nice to Have (Future Enhancements)
- Automated email / SMS reminders for upcoming task deadlines and overdue installments.
- Multi-center organizational switcher for enterprise owners managing multiple training centers.
- File versioning for student task submissions.
- Advanced graphical cohort analytics and student retention forecasting.

---

## 10. Core Features

### Feature 1: Authentication & Role-Based Access Control (RBAC)
- **Feature ID:** `FEAT-AUTH`
- **Feature Name:** Authentication & Session Authorization
- **Purpose:** Provide secure, tenant-scoped user authentication and restrict access to authorized role workflows.
- **User Story:** As a user, I want to log into my center's portal securely using my email and password so that I can access my designated dashboard and tools.
- **Priority:** Must Have

#### Functional Requirements
- **FR-AUTH-01:** System must authenticate users via email and password using secure hashing algorithms (Argon2id or bcrypt).
- **FR-AUTH-02:** System must verify the user's account status (`Active`, `Suspended`, `Archived`) before granting access. Suspended users must be rejected with an appropriate message.
- **FR-AUTH-03:** System must assign each user one primary active role (`Manager`, `Sales`, `Instructor`, `Student`) per center.
- **FR-AUTH-04:** System must issue secure, HTTP-only, SameSite session cookies or tokens containing encrypted claims.
- **FR-AUTH-05:** System must automatically redirect users to their designated role dashboard upon successful login.
- **FR-AUTH-06:** System must allow authenticated users to log out safely, invalidating the session immediately.

#### Business Rules
- **BR-AUTH-01:** Client applications must never determine authorization. All endpoint requests must be verified server-side against the user's role and associated `CenterID`.
- **BR-AUTH-02:** A user cannot hold privileges outside their assigned role unless explicitly granted through a center-level permission policy.
- **BR-AUTH-03:** A single physical user identity (`User`) links 1-to-1 with role profiles (`StudentProfile`, `InstructorProfile`). Duplicate login credentials for the same user are prohibited.

#### Edge Cases & Error States
- **EC-AUTH-01 (Inactive Account):** A user whose status is set to `Suspended` attempts login. System halts authentication and displays: *"Your account is currently inactive. Please contact your training center administrator."*
- **EC-AUTH-02 (Password Brute Force):** 5 consecutive failed login attempts trigger a 15-minute rate limit on the account/IP.
- **EC-AUTH-03 (Unauthorized Route Access):** A student attempts to access `/manager/financial-reports`. The backend returns HTTP `403 Forbidden` and redirects to the student dashboard.

#### Acceptance Criteria
- **Scenario AC-AUTH-01:** Valid Login
  - **Given** an active user exists with email `manager@center.com` and valid password `SecretPassword123` for Center A,
  - **When** the user submits valid credentials on the login screen,
  - **Then** the system establishes an authenticated session and redirects the user to `/manager/dashboard`.
- **Scenario AC-AUTH-02:** Invalid Password
  - **Given** an active user exists,
  - **When** they submit an incorrect password,
  - **Then** authentication fails, HTTP `401 Unauthorized` is returned, and an error message *"Invalid email or password"* is displayed without revealing if the email exists.

---

### Feature 2: Multi-Tenant Center Isolation
- **Feature ID:** `FEAT-TENANT`
- **Feature Name:** Tenant Scoping & Center Isolation
- **Purpose:** Ensure strict logical isolation of all operational and financial records across distinct training centers sharing the same PostgreSQL database.
- **User Story:** As a Center Manager, I want all our students, courses, materials, and financial records to remain completely private to our center so that no unauthorized staff or external centers can access our proprietary data.
- **Priority:** Must Have

#### Functional Requirements
- **FR-TEN-01:** System must associate every organizational record (`Course`, `User`, `Enrollment`, `Material`, `Task`, `Payment`, `InstructorDue`, `AuditLog`) with a unique `CenterID`.
- **FR-TEN-02:** Backend API must automatically extract the authorized `CenterID` from the authenticated session context and inject it into all database read, write, and update queries.
- **FR-TEN-03:** System must reject any API request containing a mismatched or foreign `CenterID` with HTTP `403 Forbidden`.
- **FR-TEN-04:** Center-owned database tables must include foreign keys and composite indexes on `CenterID` to optimize filtering and ensure relational consistency.

#### Business Rules
- **BR-TEN-01:** The backend must NEVER accept or trust a client-supplied `CenterID` parameter in request bodies or query parameters for authorization.
- **BR-TEN-02:** Cross-center data linking is strictly forbidden. A course belonging to Center A cannot enroll a student belonging to Center B or assign an instructor belonging to Center B.
- **BR-TEN-03:** Super-administrative functions (platform-wide maintenance) are isolated from tenant-level operational logic.

#### Edge Cases & Error States
- **EC-TEN-01 (Tampered URL ID):** A manager authenticated under Center A manipulates the URL to view `/courses/999` (belonging to Center B). The backend query automatically scopes `WHERE id = 999 AND center_id = CenterA_ID`, finds zero records, and returns HTTP `404 Not Found` (or `403 Forbidden`), preventing data leakage.

#### Acceptance Criteria
- **Scenario AC-TEN-01:** Cross-Center Data Isolation
  - **Given** Manager Alice is authenticated under Center 101, and Course #505 belongs to Center 102,
  - **When** Manager Alice sends a GET or PATCH request for Course #505,
  - **Then** the backend rejects the request with HTTP `404 Not Found` or `403 Forbidden`, and no data from Center 102 is exposed.

---

### Feature 3: Course & Session Management
- **Feature ID:** `FEAT-COURSE`
- **Feature Name:** Course Lifecycle & Session (Lecture) Administration
- **Purpose:** Allow administrators to create, update, organize, and archive training courses and their component lecture sessions.
- **User Story:** As a Center Manager, I want to create and manage training courses and schedule lecture sessions so that the center can deliver organized academic programs.
- **Priority:** Must Have

#### Functional Requirements
- **FR-CRS-01:** Manager must be able to create a course by providing: Name, Code/Identifier, Description, Base Price, Start Date, End Date, and Capacity.
- **FR-CRS-02:** System must maintain an explicit course lifecycle status: `Draft`, `Active`, `Completed`, `Archived`.
- **FR-CRS-03:** Manager must be able to update course metadata when the course is in `Draft` or `Active` status.
- **FR-CRS-04:** Manager and assigned Instructors must be able to create, update, and reorder `CourseSession` (lecture) entities within a course, specifying: Session Date, Start Time, End Time, Topic Title, and Assigned Instructor.
- **FR-CRS-05:** Manager must be able to change course status to `Archived`. An archived course must retain all historical enrollments, attendance, payments, and task submissions in read-only format.
- **FR-CRS-06:** System must prevent hard deletion of any course that has associated enrollments, sessions, payments, or task submissions.

#### Business Rules
- **BR-CRS-01:** Each course must belong to exactly one center (`CenterID`).
- **BR-CRS-02:** Course Base Price must be a non-negative decimal number (`>= 0.00`).
- **BR-CRS-03:** A course must have at least one assigned instructor before its status can transition from `Draft` to `Active`.
- **BR-CRS-04:** Course dates must be logically valid: `EndDate` must be equal to or greater than `StartDate`.
- **BR-CRS-05:** Course sessions must belong to exactly one parent course and must fall within a reasonable timeframe relative to the course's start and end dates.

#### Edge Cases & Error States
- **EC-CRS-01 (Attempt to Delete Active Course):** User attempts to delete a course with 15 enrolled students. System blocks action: *"Course cannot be deleted because it contains active enrollments. You may archive the course instead."*
- **EC-CRS-02 (End Date Prior to Start Date):** Form validation displays inline error: *"Course End Date cannot be earlier than Course Start Date."*

#### Acceptance Criteria
- **Scenario AC-CRS-01:** Course Status Transition & Archiving
  - **Given** an Active course with 20 enrollments, 10 sessions, and historical payments,
  - **When** the Center Manager selects "Archive Course",
  - **Then** the course status changes to `Archived`, active modifications are locked, and all historical records remain accessible for reports and audit queries.

---

### Feature 4: Instructor Assignment & Compensation Configuration
- **Feature ID:** `FEAT-INST-ASSIGN`
- **Feature Name:** Multi-Instructor Assignment & Compensation Models
- **Purpose:** Support assigning multiple instructors to a single course while establishing distinct, transparent compensation calculation terms.
- **User Story:** As a Center Manager, I want to assign one or more instructors to a course and define their compensation terms so that their earnings are calculated automatically.
- **Priority:** Must Have

#### Functional Requirements
- **FR-INS-01:** System must allow assigning one or multiple instructors to a course via a `CourseInstructor` junction entity.
- **FR-INS-02:** For each assignment, Manager must designate an explicit compensation model:
  1. `FixedAmount`: A set total currency value paid for the entire course.
  2. `PercentageOfCollectedRevenue`: A set percentage (0.01% – 100.00%) calculated based on the total cash payments actually collected from enrolled students.
  3. `PerSession`: A set currency value paid per completed lecture session taught by that instructor.
- **FR-INS-03:** Manager must be able to designate one instructor as the "Lead Instructor" for operational clarity.
- **FR-INS-04:** Assigned instructors must gain immediate read/write access to that course's materials, sessions, tasks, attendance, and feedback.
- **FR-INS-05:** System must prevent assigning the same instructor more than once to the same course.

#### Business Rules
- **BR-INS-01:** An instructor can only access courses to which they have an active `CourseInstructor` record.
- **BR-INS-02:** Compensation model parameters cannot be negative values.
- **BR-INS-03:** If `PercentageOfCollectedRevenue` is selected, the calculation formula strictly computes against `SUM(Payment.Amount)` where `Status = Verified/Completed`, not against theoretical or invoiced receivables.

#### Acceptance Criteria
- **Scenario AC-INS-01:** Multi-Instructor Assignment
  - **Given** Course "Full-Stack Web" exists in Center A,
  - **When** Manager assigns Instructor Mohammed with `FixedAmount = 1500` and Instructor Youssef with `PerSession = 50`,
  - **Then** both assignments are persisted, both instructors can view the course in their portals, and duplicate assignments for either instructor are prevented.

---

### Feature 5: Student Management & Course Enrollment
- **Feature ID:** `FEAT-STUDENT`
- **Feature Name:** Student Profiles & Enrollment Lifecycle
- **Purpose:** Manage trainee identity records, enroll trainees into courses, configure pricing discounts, and establish student financial accounts.
- **User Story:** As a Center Manager or Sales Specialist, I want to register students and enroll them into courses with appropriate discounts so that their seats are confirmed and their payable balances are generated.
- **Priority:** Must Have

#### Functional Requirements
- **FR-STU-01:** Staff must be able to create student profiles capturing: Full Name, Email, Phone Number, National/Civil ID (optional), Notes, and Status (`Active`, `Inactive`).
- **FR-STU-02:** Staff must be able to enroll an active student into an active course, specifying: Enrollment Date, Base Price (inherited from course), and optional Discount (Fixed Amount or Percentage).
- **FR-STU-03:** System must compute and store:
  - `BasePrice`: Course catalog price at time of enrollment.
  - `DiscountAmount`: Calculated currency value of discount applied.
  - `FinalPayableAmount`: `BasePrice - DiscountAmount`.
  - `TotalPaidAmount`: Initialized to `0.00`.
  - `RemainingBalance`: `FinalPayableAmount - TotalPaidAmount`.
- **FR-STU-04:** System must maintain enrollment status: `Active`, `Completed`, `Withdrawn`, `Suspended`.
- **FR-STU-05:** System must strictly enforce uniqueness for active logical enrollments using `(CenterID, StudentID, CourseID)`. Duplicate active enrollments for the same student in the same course must be rejected.

#### Business Rules
- **BR-STU-01:** `DiscountAmount` cannot exceed `BasePrice`. `FinalPayableAmount` cannot be less than `0.00`.
- **BR-STU-02:** A student may enroll in multiple different courses concurrently within the same center.
- **BR-STU-03:** Withdrawing a student changes status to `Withdrawn` and preserves all historical payment and attendance records.

#### Edge Cases & Error States
- **EC-STU-01 (Duplicate Active Enrollment):** Staff attempts to enroll a student in a course they are already actively attending. System blocks submission: *"Student is already actively enrolled in this course."*
- **EC-STU-02 (Discount Exceeds Price):** Staff enters a discount of $1200 on a $1000 course. System blocks: *"Discount amount cannot exceed the course base price."*

#### Acceptance Criteria
- **Scenario AC-STU-01:** Enrollment Balance Calculation
  - **Given** Course "Data Science" has a Base Price of $1,000,
  - **When** Sales enrolls Student Layla with a fixed discount of $150,
  - **Then** the enrollment record is created with `FinalPayableAmount = $850.00`, `TotalPaidAmount = $0.00`, and `RemainingBalance = $850.00`.

---

### Feature 6: Course Materials Management
- **Feature ID:** `FEAT-MAT`
- **Feature Name:** Course Educational Materials Distribution
- **Purpose:** Enable instructors and managers to upload, organize, and distribute educational resources securely to enrolled students.
- **User Story:** As an Instructor, I want to upload lecture presentations, PDFs, and code archives so that enrolled students can access study materials.
- **Priority:** Must Have

#### Functional Requirements
- **FR-MAT-01:** Instructor and Manager must be able to upload educational resources to a course, specifying: Title, Description, Associated Session (optional), and File.
- **FR-MAT-02:** System must accept files up to a maximum size of 25 MB per file.
- **FR-MAT-03:** System must restrict file types to: PDF, DOC, DOCX, PPT, PPTX, XLS, XLSX, JPG, JPEG, PNG, and ZIP.
- **FR-MAT-04:** Backend must strictly validate both file extension and MIME type before persisting files.
- **FR-MAT-05:** File downloads must be gated behind authentication. Unenrolled students or external users must be denied access.
- **FR-MAT-06:** Enrolled students must be able to view and download all materials published under their active courses.

#### Business Rules
- **BR-MAT-01:** Materials must always be associated with exactly one course.
- **BR-MAT-02:** Students can only view and download materials for courses where they have an active or completed enrollment.
- **BR-MAT-03:** Files must be stored securely with randomized, unguessable storage paths; direct public web access to storage URLs is prohibited.

#### Edge Cases & Error States
- **EC-MAT-01 (Oversized File):** User attempts to upload a 35 MB video file. System blocks upload before processing: *"File size exceeds the 25 MB limit."*
- **EC-MAT-02 (Disallowed Extension/MIME):** User attempts to upload `.exe` or `.sh`. System rejects: *"File type not permitted. Allowed formats: PDF, Office documents, Images, ZIP."*

#### Acceptance Criteria
- **Scenario AC-MAT-01:** Valid Material Upload & Access Verification
  - **Given** Instructor Mohammed is assigned to Course A,
  - **When** he uploads a 12 MB PDF named `Lecture1_Notes.pdf`,
  - **Then** the file is saved, linked to Course A, and immediately accessible for download by Student Layla (enrolled in Course A), but blocked for Student Omar (not enrolled in Course A).

---

### Feature 7: Tasks & Student Submissions
- **Feature ID:** `FEAT-TASK`
- **Feature Name:** Homework Tasks & Student Submission Tracking
- **Purpose:** Enable instructors to assign homework tasks, enforce submission deadlines, track student delivery, and handle late submission exceptions.
- **User Story:** As an Instructor, I want to create tasks with deadlines and review submitted files so that I can evaluate student progress.
- **Priority:** Must Have

#### Functional Requirements
- **FR-TSK-01:** Instructor and Manager must be able to create tasks within a course, specifying: Title, Instructions, Optional Attachment, Creation Date, and Optional Deadline (`DueDateTime`).
- **FR-TSK-02:** Enrolled students must see a clear list of pending and completed tasks with due dates and submission statuses.
- **FR-TSK-03:** Students must be able to submit their work by uploading a file (up to 25 MB; PDF, DOC, DOCX, ZIP, PNG, etc.) with optional student notes.
- **FR-TSK-04:** Students must be allowed to resubmit/replace their uploaded file at any time **before** the deadline expires.
- **FR-TSK-05:** System must block student submissions after the deadline passes by default.
- **FR-TSK-06:** Instructor or Manager must have an explicit override toggle to "Allow Late Submission" for a specific student or task. When submitted after the deadline under an override, the submission must be stored with status `Late`.
- **FR-TSK-07:** Instructor must have a submission review screen showing: Student Name, Submission Date/Time, Status (`On-Time`, `Late`, `Pending Review`), and File Download.

#### Business Rules
- **BR-TSK-01:** A task must belong to exactly one course and may optionally link to a specific `CourseSession`.
- **BR-TSK-02:** Students can only view and submit tasks for courses in which they are actively enrolled.
- **BR-TSK-03:** Each student may have only one active recorded submission per task (the latest version). Historical versions may be preserved in audit logs.
- **BR-TSK-04:** Sales staff are strictly prohibited from viewing student task submission files or evaluation workflows.

#### Edge Cases & Error States
- **EC-TSK-01 (Submission Past Deadline):** Student attempts to upload homework 2 hours after deadline without an instructor override. System rejects upload: *"The deadline for this task has passed. Submissions are closed."*
- **EC-TSK-02 (Late Override Active):** Instructor enables late override for Student Layla. Layla uploads file 3 hours late. System accepts file and flags status as `Late`.

#### Acceptance Criteria
- **Scenario AC-TSK-01:** On-Time Submission and Resubmission
  - **Given** Task 1 has a deadline of tomorrow at 23:59,
  - **When** Enrolled Student Layla uploads `Homework_v1.zip` today at 14:00, and later uploads `Homework_v2.zip` at 18:00,
  - **Then** the submission is accepted, updated to `Homework_v2.zip`, and marked with status `On-Time`.

---

### Feature 8: Session Attendance Tracking
- **Feature ID:** `FEAT-ATT`
- **Feature Name:** Lecture Session Attendance Recording
- **Purpose:** Enable instructors and managers to record and track student attendance on a session-by-session basis.
- **User Story:** As an Instructor, I want to mark students as Present or Absent during each lecture so that our center maintains accurate attendance records.
- **Priority:** Must Have

#### Functional Requirements
- **FR-ATT-01:** Instructor and Manager must be able to open an attendance roster for any scheduled `CourseSession`.
- **FR-ATT-02:** Roster must list all active enrolled students in that course with radio/toggle buttons for `Present` and `Absent`.
- **FR-ATT-03:** System must allow quick-marking actions (e.g., "Mark All Present").
- **FR-ATT-04:** System must persist exactly one attendance record per `(CourseSessionID, StudentID)` combination.
- **FR-ATT-05:** Instructor and Manager must be able to edit past session attendance records if corrections are required, logging the change in audit history.
- **FR-ATT-06:** Students must be able to view their personal attendance history (total sessions held, sessions attended, sessions absent, attendance percentage).

#### Business Rules
- **BR-ATT-01:** Attendance is strictly session-based; attendance cannot be recorded against a course without selecting a valid `CourseSession`.
- **BR-ATT-02:** Attendance statuses in the MVP are strictly binary: `Present` or `Absent`. (Late arrival notes can be captured in session remarks).
- **BR-ATT-03:** Database unique constraint on `(session_id, enrollment_id)` must prevent duplicate attendance records.

#### Acceptance Criteria
- **Scenario AC-ATT-01:** Attendance Recording Integrity
  - **Given** Session 3 of Course A has 15 enrolled students,
  - **When** Instructor marks 14 students as `Present` and 1 student as `Absent` and clicks Save,
  - **Then** exactly 15 records are saved, each student's attendance summary updates immediately, and duplicate entries are prevented.

---

### Feature 9: Instructor Feedback System
- **Feature ID:** `FEAT-FEEDBACK`
- **Feature Name:** Private Instructor-to-Student Qualitative Feedback
- **Purpose:** Enable instructors to communicate qualitative evaluations, academic notes, and encouragement directly and privately to individual trainees.
- **User Story:** As an Instructor, I want to send private written feedback to a student regarding their performance or homework so that they understand their strengths and weaknesses.
- **Priority:** Must Have

#### Functional Requirements
- **FR-FDB-01:** Instructor and Manager must be able to submit qualitative feedback for a student, specifying: Course, Student, Optional Task or Session Link, and Feedback Content (text up to 2,000 characters).
- **FR-FDB-02:** System must store timestamp and author identity (`InstructorID`) for every feedback note.
- **FR-FDB-03:** Student must be able to view all feedback addressed to them within their course dashboard.
- **FR-FDB-04:** Feedback must remain strictly private between the authoring instructor, the recipient student, and the Center Manager.

#### Business Rules
- **BR-FDB-01:** Feedback cannot be empty or contain whitespace only.
- **BR-FDB-02:** Other students enrolled in the same course must NEVER see feedback directed to another student.
- **BR-FDB-03:** Sales personnel cannot access private academic feedback unless explicitly granted permission by Center Management.

#### Acceptance Criteria
- **Scenario AC-FDB-01:** Private Feedback Delivery
  - **Given** Instructor Mohammed writes a feedback note for Student Layla on Course A,
  - **When** the feedback is submitted,
  - **Then** Student Layla can read the feedback note on her portal, while Student Omar (enrolled in the same course) cannot view or query this note.

---

### Feature 10: Student Financial Tracking & Installments
- **Feature ID:** `FEAT-FIN-STUDENT`
- **Feature Name:** Student Payments & Installment Ledger
- **Purpose:** Manage offline student fee collection, record installment payments, track reference numbers, and calculate outstanding balances automatically.
- **User Story:** As a Manager or Sales Specialist, I want to record student payments and monitor remaining installment balances so that the center collects all revenue accurately.
- **Priority:** Must Have

#### Functional Requirements
- **FR-FIN-01:** System must maintain a financial ledger for each student enrollment:
  - `BasePrice`: Catalog course price.
  - `DiscountAmount`: Applied discount.
  - `FinalPayableAmount`: `BasePrice - DiscountAmount`.
  - `TotalPaidAmount`: Running sum of verified payments.
  - `RemainingBalance`: `FinalPayableAmount - TotalPaidAmount`.
  - `PaymentStatus`: `Unpaid` ($0 paid), `PartiallyPaid` (Paid > 0 and Remaining > 0), `FullyPaid` (Remaining = 0), `Overpaid` (Remaining < 0).
- **FR-FIN-02:** Staff must be able to log a payment entry against an enrollment by specifying: Amount, Payment Date, Payment Method (`Cash`, `BankTransfer`, `POS_Card`, `Cheque`), Transaction Reference / Receipt Number, and Internal Notes.
- **FR-FIN-03:** System must allow students to pay fees in multiple installments. Each installment creates a discrete `Payment` record.
- **FR-FIN-04:** System must block or require explicit manager confirmation if a payment entry exceeds the current `RemainingBalance`.
- **FR-FIN-05:** Immutable Transaction Policy: Posted financial transactions must NEVER be hard-deleted from the database. Corrections must be handled via authorized adjustment/reversal entries with mandatory audit rationale.
- **FR-FIN-06:** Students must be able to view their personal payment ledger (receipts, amounts paid, dates, remaining balance).

#### Business Rules
- **BR-FIN-01:** All logged payment amounts must be strictly positive numbers (`Amount > 0.00`).
- **BR-FIN-02:** Every payment record must link to exactly one valid `Enrollment`, `Student`, and `Center`.
- **BR-FIN-03:** Payment adjustments must reference the original payment ID and document the correcting user and justification.

#### Edge Cases & Error States
- **EC-FIN-01 (Overpayment Attempt):** Staff attempts to log a $500 payment on a remaining balance of $200. System prompts warning modal: *"Payment exceeds remaining balance by $300. Proceed with overpayment credit or adjust amount?"*
- **EC-FIN-02 (Zero or Negative Payment):** Staff enters `-50`. Validation error: *"Payment amount must be greater than 0.00."*

#### Acceptance Criteria
- **Scenario AC-FIN-01:** Installment Math & Balance Recalculation
  - **Given** an enrollment with `BasePrice = 1000.00` and `DiscountAmount = 100.00` (Payable = `900.00`),
  - **When** Sales records an initial deposit payment of `300.00`,
  - **Then** the system updates `TotalPaidAmount = 300.00`, `RemainingBalance = 600.00`, and sets `PaymentStatus = PartiallyPaid`.
- **Scenario AC-FIN-02:** Immutable Transaction Protection
  - **Given** a posted payment of $300 exists in the database,
  - **When** any user attempts a SQL `DELETE` or API delete call on this record,
  - **Then** the request is blocked by database constraints and API authorization gates, requiring an adjustment transaction instead.

---

### Feature 11: Instructor Dues & Settlement Tracking
- **Feature ID:** `FEAT-FIN-INST`
- **Feature Name:** Instructor Compensation Calculation & Settlement
- **Purpose:** Calculate accrued instructor earnings based on assigned compensation terms, track settlement payments, and provide financial transparency.
- **User Story:** As a Center Manager, I want the system to calculate instructor compensation automatically so that we can settle dues accurately without disputes.
- **Priority:** Must Have

#### Functional Requirements
- **FR-INSFIN-01:** System must maintain an `InstructorDue` ledger entry for each course assignment, calculating accrued dues based on the configured compensation model:
  - If `FixedAmount`: Accrues the full agreed amount upon course activation or completion.
  - If `PercentageOfCollectedRevenue`: Dynamically computes `AccruedAmount = (Percentage / 100) * SUM(Verified Student Payments for this Course)`.
  - If `PerSession`: Dynamically computes `AccruedAmount = RatePerSession * Count(Completed Sessions Taught by Instructor)`.
- **FR-INSFIN-02:** System must track dues status: `Pending` (unpaid), `PartiallyPaid`, `Paid` (fully settled), `Adjusted`.
- **FR-INSFIN-03:** Manager must be able to log settlement payout disbursements made to the instructor (Amount, Date, Payment Method, Reference, Notes).
- **FR-INSFIN-04:** Instructor must have a "My Financial Dues" view showing: Assigned Courses, Compensation Terms, Accrued Amount, Payouts Received, and Current Outstanding Due.
- **FR-INSFIN-05:** Manager must have a consolidated "Instructor Liabilities Report" showing total pending dues across all instructors.

#### Business Rules
- **BR-INSFIN-01:** Instructor dues calculations must run within atomic transactions to prevent stale numbers.
- **BR-INSFIN-02:** When student payment refunds or adjustments occur, percentage-based instructor dues must automatically re-evaluate.
- **BR-INSFIN-03:** Instructors can only view their own compensation dues; they can never view terms or dues of peer instructors.

#### Acceptance Criteria
- **Scenario AC-INSFIN-01:** Per-Session Dues Calculation
  - **Given** Instructor Mohammed is assigned to Course B at a rate of $60 per session,
  - **When** Mohammed conducts and logs attendance for 5 sessions,
  - **Then** the system calculates his accrued dues as `5 * $60 = $300.00`, displaying status `Pending`.

---

### Feature 12: Role-Specific Dashboards & Basic Reporting
- **Feature ID:** `FEAT-DASH`
- **Feature Name:** Executive & Operational Dashboards
- **Purpose:** Present immediate, high-priority operational and financial metrics tailored specifically to the user's role.
- **User Story:** As a user, I want a tailored dashboard upon logging in so that I immediately see my critical tasks, metrics, and schedules.
- **Priority:** Must Have

#### Functional Requirements
- **FR-DSH-01:** **Manager Dashboard** must display:
  - Total Active Courses, Total Enrolled Students, Total Instructors.
  - Financial Summary Cards: Total Invoiced, Total Cash Collected, Total Outstanding Student Receivables, Total Pending Instructor Liabilities.
  - Urgent Operational Alerts: Courses starting this week, students with overdue balances, unassigned courses.
- **FR-DSH-02:** **Sales Dashboard** must display:
  - Recent Student Registrations and Incomplete Enrollments.
  - Outstanding Fee Collection Queue: Filterable list of students with `RemainingBalance > 0`.
  - Fast search bar for instant student lookup.
- **FR-DSH-03:** **Instructor Dashboard** must display:
  - Active Assigned Courses cards with quick links to Sessions, Materials, and Tasks.
  - Upcoming Sessions Schedule.
  - Submissions Pending Review counter.
  - Personal Financial Earnings snapshot (`Pending` vs. `Paid`).
- **FR-DSH-04:** **Student Dashboard** must display:
  - Active Enrolled Courses with progress indicators.
  - Upcoming Task Deadlines counter and alerts.
  - Recent Materials published.
  - Personal Financial Summary (`Total Paid`, `Remaining Balance`).
  - Attendance summary percentage.

#### Acceptance Criteria
- **Scenario AC-DSH-01:** Role Dashboard Redirection
  - **Given** User Sarah has the `Sales` role,
  - **When** she completes login,
  - **Then** she is redirected to `/sales/dashboard` displaying student follow-up queues and payment search tools, with no links to instructor dues or academic materials management.

---

### Feature 13: Audit Trail & Data Archive Management
- **Feature ID:** `FEAT-AUDIT`
- **Feature Name:** System Audit Logging & Non-Destructive Archiving
- **Purpose:** Maintain historical accountability and regulatory audit readiness across all business-critical operations.
- **User Story:** As a Center Manager, I want all sensitive financial and status changes logged so that we can review historical modifications and maintain complete integrity.
- **Priority:** Must Have

#### Functional Requirements
- **FR-AUD-01:** System must generate immutable `AuditLog` entries for:
  - User login events and permission changes.
  - Course status transitions (`Draft` -> `Active` -> `Archived`).
  - Student enrollment status changes (`Active` -> `Withdrawn`).
  - All Payment creations, adjustments, and reversals.
  - Instructor Dues settlement entries.
- **FR-AUD-02:** Each audit record must capture: `Timestamp`, `CenterID`, `UserID`, `IPAddress`, `ActionType`, `EntityName`, `EntityID`, `OldValues` (JSON), and `NewValues` (JSON).
- **FR-AUD-03:** Audit log entries must be read-only and restricted exclusively to Center Managers.
- **FR-AUD-04:** Archiving entities (`Course`, `Student`, `Instructor`) must soft-archive records by setting an archived timestamp/status, preserving full relational integrity with historical child records.

#### Acceptance Criteria
- **Scenario AC-AUD-01:** Payment Adjustment Audit Logging
  - **Given** an existing payment record of $200,
  - **When** Manager posts an authorized adjustment entry reducing it to $150,
  - **Then** an `AuditLog` record is created containing the prior values, new values, manager ID, timestamp, and explanation.

---

## 11. Functional Requirements

### 11.1 Complete Traceability Matrix

| Requirement ID | Module / Domain | Requirement Description | Role | Priority |
| :--- | :--- | :--- | :--- | :--- |
| **FR-001** | Identity & Auth | Secure user login via email and hashed password. | All | Must Have |
| **FR-002** | Identity & Auth | Role-based access control enforcing 4 explicit roles. | All | Must Have |
| **FR-003** | Multi-Tenancy | Server-side `CenterID` scoping on all database queries. | All | Must Have |
| **FR-004** | Course Mgmt | Create, edit, and view courses with dates and base price. | Manager | Must Have |
| **FR-005** | Course Mgmt | Transition course statuses (`Draft`, `Active`, `Completed`, `Archived`). | Manager | Must Have |
| **FR-006** | Course Mgmt | Schedule and manage `CourseSession` (lecture) records. | Manager, Instructor | Must Have |
| **FR-007** | Instructor Mgmt | Assign multiple instructors to a course. | Manager | Must Have |
| **FR-008** | Instructor Mgmt | Configure instructor compensation model (Fixed, %, Per Session). | Manager | Must Have |
| **FR-009** | Student Mgmt | Create and maintain student profiles with contact info. | Manager, Sales | Must Have |
| **FR-010** | Student Mgmt | Enroll students into courses with discount configuration. | Manager, Sales | Must Have |
| **FR-011** | Student Mgmt | Block duplicate active enrollments for the same student/course. | System | Must Have |
| **FR-012** | Materials | Upload educational files (up to 25 MB; PDF, Office, Images, ZIP). | Manager, Instructor | Must Have |
| **FR-013** | Materials | Enforce server-side MIME type and extension validation on uploads. | System | Must Have |
| **FR-014** | Materials | Securely download materials scoped to enrolled courses. | Student | Must Have |
| **FR-015** | Tasks | Create tasks with description, attachments, and optional deadlines. | Manager, Instructor | Must Have |
| **FR-016** | Tasks | Submit task files before deadline with replacement allowed. | Student | Must Have |
| **FR-017** | Tasks | Block task submission after deadline by default. | System | Must Have |
| **FR-018** | Tasks | Allow authorized late submission override (flagged as `Late`). | Manager, Instructor | Must Have |
| **FR-019** | Tasks | View and evaluate student task submissions. | Manager, Instructor | Must Have |
| **FR-020** | Attendance | Open session roster and record `Present` / `Absent` per student. | Manager, Instructor | Must Have |
| **FR-021** | Attendance | Enforce uniqueness: exactly one attendance entry per student/session. | System | Must Have |
| **FR-022** | Attendance | View personal attendance summary and lecture history. | Student | Must Have |
| **FR-023** | Feedback | Submit private written feedback for a student on a course. | Manager, Instructor | Must Have |
| **FR-024** | Feedback | View personal instructor feedback notes in private portal. | Student | Must Have |
| **FR-025** | Student Finance | Calculate `Payable = BasePrice - Discount` and `Remaining Balance`. | System | Must Have |
| **FR-026** | Student Finance | Record offline student payments (Cash, Transfer, Card, Cheque). | Manager, Sales | Must Have |
| **FR-027** | Student Finance | Support multi-installment fee payments with unique receipt logs. | Manager, Sales | Must Have |
| **FR-028** | Student Finance | Block hard deletion of posted financial transactions. | System | Must Have |
| **FR-029** | Student Finance | Record authorized payment adjustments/reversals with audit logs. | Manager | Must Have |
| **FR-030** | Instructor Finance| Accrue instructor compensation based on assigned model terms. | System | Must Have |
| **FR-031** | Instructor Finance| Track instructor payout disbursements and settlement lifecycle. | Manager | Must Have |
| **FR-032** | Instructor Finance| View personal dues, payout history, and outstanding balance. | Instructor | Must Have |
| **FR-033** | Reporting | Executive dashboard showing cash collections, receivables, dues. | Manager | Must Have |
| **FR-034** | Reporting | Sales dashboard showing overdue installment follow-up queues. | Sales | Must Have |
| **FR-035** | Reporting | Instructor dashboard showing courses, sessions, review queue. | Instructor | Must Have |
| **FR-036** | Reporting | Student dashboard showing courses, deadlines, materials, balance. | Student | Must Have |
| **FR-037** | Audit & History | Immutable audit logging for financial, role, and status changes. | System | Must Have |
| **FR-038** | Search & Filter | Server-side pagination, search, and filtering across all lists. | All | Should Have |
| **FR-039** | Export | Export student lists and financial summaries to CSV format. | Manager | Should Have |
| **FR-040** | Profile Mgmt | View and update personal profile contact information. | All | Should Have |

---

## 12. Non-Functional Requirements

### 12.1 Performance Requirements
- **NFR-PERF-01 (API Response Latency):** 95% of standard authenticated API requests (excluding large file uploads/downloads) must complete within **500 ms** under nominal test load (50 concurrent active users per center).
- **NFR-PERF-02 (Page Load Speed):** Initial dashboard and list views must achieve a First Contentful Paint (FCP) of less than **1.2 seconds** and Largest Contentful Paint (LCP) under **2.0 seconds** on standard 4G mobile connections.
- **NFR-PERF-03 (Upload Feedback):** All file upload interactions must render real-time progress indicators (percentage bars) and validate size client-side before transmission.
- **NFR-PERF-04 (Database Query Efficiency):** No unindexed sequential table scans on multi-tenant tables. Complex financial summaries must execute in under **200 ms** using optimized aggregate queries.

### 12.2 Scalability & Architecture Requirements
- **NFR-SCAL-01 (Multi-Tenancy Readiness):** System architecture must support up to 100 distinct training centers on a single shared PostgreSQL database instance without data leakage or performance degradation.
- **NFR-SCAL-02 (Data Pagination):** All endpoints returning record collections (`Students`, `Courses`, `Payments`, `Sessions`, `AuditLogs`) must enforce mandatory server-side pagination with a default page size of 20 (maximum 100).
- **NFR-SCAL-03 (Stateless Application Layer):** Backend services must operate statelessly to allow horizontal container scaling behind a standard load balancer.

### 12.3 Reliability & Data Integrity Requirements
- **NFR-REL-01 (ACID Transactions):** Multi-step operations (e.g., student enrollment with balance initialization; payment posting with balance recalculation; attendance sheet submission) must execute within atomic database transactions (`BEGIN...COMMIT`). Failed operations must roll back completely leaving zero partial records.
- **NFR-REL-02 (No Orphaned Records):** Foreign key constraints must enforce referential integrity across all relationships (`ON DELETE RESTRICT` for financial and enrollment records).
- **NFR-REL-03 (Availability):** Target operational uptime of **99.5%** during training center business hours (08:00 to 22:00 local time).

### 12.4 Usability & Responsiveness Requirements
- **NFR-USE-01 (Mobile Responsiveness):** All primary operational workflows (taking attendance, submitting a task, checking student balance, viewing dashboard) must be fully usable on mobile screens starting from **360px** viewport width up to **4K** desktop monitors.
- **NFR-USE-02 (Intuitive Navigation):** Role-specific navigation bars must display only routes authorized for that role, preventing navigation clutter.
- **NFR-USE-03 (Clear Error States):** User-facing validation messages must be presented inline adjacent to form fields, written in plain, actionable language.

### 12.5 Accessibility Requirements
- **NFR-ACC-01 (Color Contrast):** User interface elements must maintain a minimum contrast ratio of **4.5:1** for standard text and **3:1** for large text/interactive elements in compliance with WCAG 2.1 Level AA.
- **NFR-ACC-02 (Form Controls):** All form input controls must possess explicit `<label>` tags, visible `:focus-visible` outlines, and ARIA attributes for screen reader compatibility.

### 12.6 Security & Privacy Requirements
- **NFR-SEC-01 (Password Encryption):** User passwords must be hashed using Argon2id or bcrypt (cost factor >= 12). Plaintext passwords must never be logged or stored.
- **NFR-SEC-02 (Session Cookie Security):** Authentication cookies must enforce flags: `HttpOnly`, `Secure`, and `SameSite=Lax` (or `Strict`).
- **NFR-SEC-03 (Tenant Isolation Defense):** All database queries must enforce `WHERE center_id = ?`. PostgreSQL Row-Level Security (RLS) is evaluated as an additional layer of defense.
- **NFR-SEC-04 (File Storage Security):** Stored files must reside outside the public web root. File download requests must pass through an authentication and course-enrollment authorization check before streaming bytes.

---

## 13. User Flows

### 13.1 Flow 1: Course Creation, Instructor Assignment & Lecture Scheduling
```
[Center Manager]
       │
       ▼
Logs in -> Navigates to Courses -> Clicks "Create Course"
       │
       ▼
Fills Form (Title, Code, Base Price = $1000, Start/End Dates) -> Saves (Status: Draft)
       │
       ▼
Opens Course Details -> Clicks "Assign Instructor"
       │
       ▼
Selects Mohammed -> Sets Model: "PerSession" ($50/session) -> Confirms
       │
       ▼
Navigates to "Sessions" Tab -> Clicks "Add Session"
       │
       ▼
Enters Date, Time, Topic: "Intro to UI Design", Assigned Instructor -> Saves Session
       │
       ▼
Clicks "Publish Course" -> Status changes from Draft to Active
```

### 13.2 Flow 2: Student Registration, Course Enrollment & Installment Payment
```
[Sales Specialist]
       │
       ▼
Searches Student Directory -> (If not found: Clicks "New Student" -> Enters Name & Phone)
       │
       ▼
Selects Student Layla -> Clicks "Enroll in Course"
       │
       ▼
Selects Active Course "UI Design" (Base Price: $1000)
       │
       ▼
Applies Discount: Fixed Amount = $100 -> Final Payable Amount = $900
       │
       ▼
Confirms Enrollment -> System initializes: Total Paid = $0, Remaining Balance = $900
       │
       ▼
Layla pays $300 cash deposit -> Sales clicks "Record Payment"
       │
       ▼
Enters Amount: $300, Method: Cash, ReceiptRef: "REC-8841" -> Submits
       │
       ▼
System executes ACID Transaction:
  1. Creates Payment record
  2. Updates Enrollment: Total Paid = $300, Remaining Balance = $600
  3. Updates Enrollment Status = PartiallyPaid
  4. Creates Audit Log entry
```

### 13.3 Flow 3: Session Attendance & Instructor Feedback
```
[Instructor]
       │
       ▼
Logs in -> Clicks assigned course "UI Design" -> Opens "Sessions"
       │
       ▼
Selects "Session 1" -> Clicks "Take Attendance"
       │
       ▼
Roster displays all 20 enrolled students (all defaulted to Present)
       │
       ▼
Marks Student Omar as "Absent" -> Clicks "Submit Attendance"
       │
       ▼
System verifies unique constraint (session_id, student_id) & saves 20 records
       │
       ▼
Instructor clicks Student Layla -> Clicks "Add Feedback"
       │
       ▼
Types: "Excellent participation in today's wireframing exercise!" -> Submits
       │
       ▼
System stores Feedback -> Layla receives immediate notification on her dashboard
```

### 13.4 Flow 4: Task Creation, Student Submission & Late Override
```
[Instructor]                                  [Enrolled Student]
     │                                                │
Creates Task "Wireframe Project"                      │
Deadline: Friday 23:59                                │
     │                                                │
     ├────────────────── Published ──────────────────►│
     │                                                │
     │                                    Logs in -> Sees Task & Deadline
     │                                                │
     │                                    Friday 20:00: Uploads Wireframe_v1.zip
     │                                    System records: Status = "On-Time"
     │                                                │
     ▼                                                │
(Deadline Passes: Friday 23:59)                       │
     │                                                │
     │                                    Saturday 10:00: Attempts upload
     │                                    System blocks: "Deadline has passed"
     │                                                │
Instructor enables                                    │
"Allow Late Submission" toggle ──────────────────────►│
     │                                                │
     │                                    Student re-uploads Wireframe_Final.zip
     │                                    System saves: Status = "Late"
     │◄────────────────── Updated ────────────────────┤
     │
Instructor reviews file & writes feedback
```

### 13.5 Flow 5: Instructor Dues Calculation & Settlement
```
[System & Center Manager]
       │
       ▼
Instructor Mohammed completes 8 scheduled sessions ($50 / session)
       │
       ▼
System computes Accrued Dues: 8 * $50 = $400.00
       │
       ▼
Center Manager navigates to "Instructor Dues" Ledger
       │
       ▼
Locates Mohammed -> Views Breakdown: 8 Sessions, Accrued = $400.00, Status: Pending
       │
       ▼
Manager issues bank transfer payment of $400.00 to Mohammed
       │
       ▼
Manager clicks "Record Settlement Payout"
       │
       ▼
Enters Amount: $400, Method: Bank Transfer, Ref: "TRX-99021" -> Confirms
       │
       ▼
System updates Instructor Due Status: "Paid" -> Mohammed's portal updates
```

---

## 14. Pages / Screens

### 14.1 Shared / Public Screens
- **SCR-01 (Login Page):** Clean login card with Center Branding, Email input, Password input with toggle visibility, "Sign In" button, and validation alerts.
- **SCR-02 (User Profile Page):** View authenticated identity details (Name, Email, Role, Center Affiliation), change password form, session logout.

### 14.2 Center Manager Screens
- **SCR-M01 (Manager Executive Dashboard):** Top statistical cards (Active Courses, Active Students, Invoiced Fees, Cash Collected, Receivables, Instructor Liabilities), upcoming course schedule, pending actions table.
- **SCR-M02 (Courses Directory):** Paginated course list with filters (Status, Instructor, Date Range), "Create Course" modal/button, quick-action menus.
- **SCR-M03 (Course Workspace / Details):** Tabbed interface:
  - *Overview Tab:* Course metadata, base price, dates, capacity.
  - *Instructors Tab:* Assigned instructors list, compensation terms, "Assign Instructor" modal.
  - *Sessions Tab:* Lecture schedule, session creation modal, launch attendance sheet button.
  - *Students Tab:* Enrolled students roster, enrollment status, remaining balance badge.
  - *Materials Tab:* Published resources list, upload modal.
  - *Tasks Tab:* Tasks list, submission stats, create task modal.
- **SCR-M04 (Students Management):** Searchable student directory, "Add Student" modal, link to Student Profile & Financial History.
- **SCR-M05 (Student Enrollment & Financial Profile):** Detailed student view: enrolled courses, applied discounts, complete installment ledger, "Record Payment" modal.
- **SCR-M06 (Instructors Directory):** List of instructors, active course assignments, contact details, "Add Instructor" modal.
- **SCR-M07 (Student Payments Ledger):** Centralized financial register showing all historical student payments, receipt numbers, payment methods, search, and adjustment tools.
- **SCR-M08 (Instructor Dues Ledger):** Overview of accrued instructor dues by course and instructor, settlement logging modal, historical payout receipts.
- **SCR-M09 (Financial & Operational Reports):** High-level revenue summary, collected cash vs. outstanding receivables report, instructor payout liabilities report.
- **SCR-M10 (Audit Trail Log):** Immutable table of system actions, filters by date, user, and entity type.

### 14.3 Sales & Follow-Up Screens
- **SCR-S01 (Sales Dashboard):** Quick summary of overdue installment accounts, recent registrations, and fast search.
- **SCR-S02 (Student Search & Directory):** Real-time filterable student directory (by name, phone, course, payment status).
- **SCR-S03 (Student Intake & Enrollment Modal):** Simple form to register a new student and enroll them into an available course cohort with discount entry.
- **SCR-S04 (Student Fee Ledger & Payment Entry):** View student balance breakdown (`Total Payable`, `Total Paid`, `Remaining Balance`) and log offline payment entries with receipt reference.

### 14.4 Instructor Screens
- **SCR-I01 (Instructor Dashboard):** Assigned course cards, upcoming lecture sessions widget, counter of pending task submissions needing review, personal dues summary card.
- **SCR-I02 (My Courses):** Directory of courses assigned to the logged-in instructor.
- **SCR-I03 (Instructor Course Workspace):**
  - *Sessions & Attendance Tab:* Session list, "Take Attendance" sheet with Present/Absent toggles.
  - *Materials Tab:* Upload educational files, view download counts.
  - *Tasks & Submissions Tab:* Create homework tasks, view submission table, download student files, toggle late submission override.
  - *Feedback Tab:* Send private feedback notes to students.
- **SCR-I04 (My Financial Dues):** Personal earnings ledger broken down by course: Accrued Amount, Payouts Received, and Current Outstanding Due.

### 14.5 Student Screens
- **SCR-ST01 (Student Dashboard):** Enrolled active courses cards, upcoming task deadlines counter, recent lecture notes, personal fee balance overview card.
- **SCR-ST02 (My Courses List):** View all active and completed course enrollments.
- **SCR-ST03 (Student Course Workspace):**
  - *Lectures & Materials Tab:* Download published lecture slides, PDFs, code files.
  - *Tasks & Homework Tab:* View task instructions, deadline countdown, file upload button, submission status badge (`Submitted On-Time`, `Submitted Late`, `Pending`).
  - *My Attendance Tab:* Personal attendance history per session (`Present` / `Absent`).
  - *Instructor Feedback Tab:* View private feedback notes written by instructors.
- **SCR-ST04 (My Payments & Receipts):** Personal ledger showing Course Base Price, Discount, Final Payable, Total Paid, Remaining Balance, and list of verified payment receipts.

---

## 15. Roles & Permissions

### 15.1 Role-Based Access Control (RBAC) Matrix

| Module / Action | Manager | Sales | Instructor | Student |
| :--- | :---: | :---: | :---: | :---: |
| **System & User Administration** | | | | |
| View Center Users Directory | Full | Read-Only (Students) | Read-Only (Peers) | None |
| Create / Edit Staff Users | Yes | No | No | No |
| Create / Edit Student Profiles | Yes | Yes | No | Self (Contact Only) |
| **Course Administration** | | | | |
| Create / Edit Courses | Full | View Only | View Assigned | View Enrolled |
| Archive Courses | Yes | No | No | No |
| Assign Instructors & Set Terms | Yes | No | No | No |
| Schedule Course Sessions | Yes | No | Yes (Assigned) | View Enrolled |
| **Enrollment Management** | | | | |
| Enroll Student in Course | Yes | Yes | No | No |
| Apply Enrollment Discount | Yes | Yes (Within limits) | No | No |
| Withdraw / Cancel Enrollment | Yes | No | No | No |
| **Course Materials** | | | | |
| Upload / Delete Materials | Yes | No | Yes (Assigned) | No |
| View / Download Materials | Yes | View Only | Yes (Assigned) | Yes (Enrolled) |
| **Tasks & Submissions** | | | | |
| Create / Edit Tasks | Yes | No | Yes (Assigned) | No |
| Submit Task File | No | No | No | Yes (Enrolled) |
| View Student Submissions | Yes | No (Strictly Blocked) | Yes (Assigned) | Self Only |
| Allow Late Submission Override | Yes | No | Yes (Assigned) | No |
| **Attendance** | | | | |
| Record / Edit Attendance Sheet | Yes | No | Yes (Assigned) | No |
| View Attendance Records | Full | View Summary | View Assigned | Self Only |
| **Feedback** | | | | |
| Submit Qualitative Feedback | Yes | No | Yes (Assigned) | No |
| View Student Feedback | Full | No (Strictly Blocked) | View Authored | Self Only |
| **Student Financial Ledger** | | | | |
| View Student Remaining Balances| Full | Full | No | Self Only |
| Record Offline Student Payments | Full | Full | No | No |
| Adjust / Reverse Posted Payments| Yes | No | No | No |
| View Financial Aggregate Reports| Full | No (Strictly Blocked) | No | No |
| **Instructor Compensation** | | | | |
| Configure Compensation Models | Yes | No | No | No |
| View Instructor Dues Ledger | Full | No (Strictly Blocked) | Self Only | No |
| Record Settlement Disbursements | Yes | No | No | No |
| **Audit Logs** | | | | |
| View System Audit Trail | Full | No | No | No |

---

## 16. Data Requirements

### 16.1 Conceptual Data Model & Entity Relationships
```
[Center]
   │ 1
   ├────────── N ──────────► [User] ◄─── 1:1 ───► [StudentProfile]
   │                           │    ◄─── 1:1 ───► [InstructorProfile]
   │ 1                         │ N
   ├────────── N ──────────► [Course] ◄── N ─── [CourseInstructor]
   │                           │ 1
   │                           ├──── N ───► [CourseSession] ◄── 1 ── [Attendance]
   │                           ├──── N ───► [Material]                 ▲
   │                           ├──── N ───► [Task] ◄── 1 ── [Submission]
   │                           │ 1                             ▲
   │ 1                         ▼ N                             │
   ├────────── N ──────────► [Enrollment] ─────────────────────┤
   │                           │ 1                             │
   │                           ├──── N ───► [Payment]          │
   │                           └──── N ───► [Feedback] ────────┘
   │ 1
   ├────────── N ──────────► [InstructorDue]
   │ 1
   └────────── N ──────────► [AuditLog]
```

### 16.2 Entity Dictionary & Schema Specifications

#### Entity: `Center`
- `id`: UUID, Primary Key
- `name`: VARCHAR(255), Not Null
- `code`: VARCHAR(50), Unique, Not Null
- `status`: ENUM(`Active`, `Suspended`), Default `Active`
- `created_at`, `updated_at`: TIMESTAMPTZ

#### Entity: `User`
- `id`: UUID, Primary Key
- `center_id`: UUID, Foreign Key -> `Center(id)`, Not Null
- `full_name`: VARCHAR(255), Not Null
- `email`: VARCHAR(255), Not Null (Composite Unique: `center_id, email`)
- `password_hash`: VARCHAR(255), Not Null
- `role`: ENUM(`Manager`, `Sales`, `Instructor`, `Student`), Not Null
- `status`: ENUM(`Active`, `Suspended`, `Archived`), Default `Active`
- `created_at`, `updated_at`: TIMESTAMPTZ

#### Entity: `StudentProfile`
- `id`: UUID, Primary Key
- `user_id`: UUID, Foreign Key -> `User(id)`, Unique, Not Null
- `phone`: VARCHAR(50), Nullable
- `civil_id`: VARCHAR(50), Nullable
- `notes`: TEXT, Nullable

#### Entity: `InstructorProfile`
- `id`: UUID, Primary Key
- `user_id`: UUID, Foreign Key -> `User(id)`, Unique, Not Null
- `phone`: VARCHAR(50), Nullable
- `specialization`: VARCHAR(255), Nullable
- `bio`: TEXT, Nullable

#### Entity: `Course`
- `id`: UUID, Primary Key
- `center_id`: UUID, Foreign Key -> `Center(id)`, Not Null
- `title`: VARCHAR(255), Not Null
- `code`: VARCHAR(50), Not Null
- `description`: TEXT, Nullable
- `base_price`: DECIMAL(10, 2), Not Null, Check (`base_price >= 0.00`)
- `start_date`: DATE, Not Null
- `end_date`: DATE, Not Null, Check (`end_date >= start_date`)
- `capacity`: INT, Nullable
- `status`: ENUM(`Draft`, `Active`, `Completed`, `Archived`), Default `Draft`
- `created_at`, `updated_at`: TIMESTAMPTZ

#### Entity: `CourseInstructor`
- `id`: UUID, Primary Key
- `course_id`: UUID, Foreign Key -> `Course(id)`, Not Null
- `instructor_id`: UUID, Foreign Key -> `InstructorProfile(id)`, Not Null
- `compensation_model`: ENUM(`FixedAmount`, `PercentageOfCollectedRevenue`, `PerSession`), Not Null
- `model_value`: DECIMAL(10, 2), Not Null, Check (`model_value >= 0.00`)
- `is_lead`: BOOLEAN, Default `false`
- Composite Unique Constraint: `(course_id, instructor_id)`

#### Entity: `CourseSession`
- `id`: UUID, Primary Key
- `course_id`: UUID, Foreign Key -> `Course(id)`, Not Null
- `session_date`: DATE, Not Null
- `start_time`: TIME, Not Null
- `end_time`: TIME, Not Null
- `topic`: VARCHAR(255), Not Null
- `instructor_id`: UUID, Foreign Key -> `InstructorProfile(id)`, Nullable
- `created_at`, `updated_at`: TIMESTAMPTZ

#### Entity: `Enrollment`
- `id`: UUID, Primary Key
- `center_id`: UUID, Foreign Key -> `Center(id)`, Not Null
- `student_id`: UUID, Foreign Key -> `StudentProfile(id)`, Not Null
- `course_id`: UUID, Foreign Key -> `Course(id)`, Not Null
- `base_price`: DECIMAL(10, 2), Not Null
- `discount_amount`: DECIMAL(10, 2), Default `0.00`, Check (`discount_amount >= 0.00`)
- `final_payable`: DECIMAL(10, 2), Not Null, Check (`final_payable >= 0.00`)
- `total_paid`: DECIMAL(10, 2), Default `0.00`, Check (`total_paid >= 0.00`)
- `remaining_balance`: DECIMAL(10, 2), Not Null
- `status`: ENUM(`Active`, `Completed`, `Withdrawn`), Default `Active`
- `created_at`, `updated_at`: TIMESTAMPTZ
- Unique Constraint on active enrollment: `(center_id, student_id, course_id, status)` where status = 'Active'

#### Entity: `Material`
- `id`: UUID, Primary Key
- `course_id`: UUID, Foreign Key -> `Course(id)`, Not Null
- `session_id`: UUID, Foreign Key -> `CourseSession(id)`, Nullable
- `title`: VARCHAR(255), Not Null
- `description`: TEXT, Nullable
- `file_path`: VARCHAR(500), Not Null
- `file_name`: VARCHAR(255), Not Null
- `file_size_bytes`: BIGINT, Not Null, Check (`file_size_bytes <= 26214400`) -- 25 MB
- `mime_type`: VARCHAR(100), Not Null
- `created_at`: TIMESTAMPTZ

#### Entity: `Task`
- `id`: UUID, Primary Key
- `course_id`: UUID, Foreign Key -> `Course(id)`, Not Null
- `session_id`: UUID, Foreign Key -> `CourseSession(id)`, Nullable
- `title`: VARCHAR(255), Not Null
- `instructions`: TEXT, Not Null
- `attachment_path`: VARCHAR(500), Nullable
- `due_datetime`: TIMESTAMPTZ, Nullable
- `allow_late_submission`: BOOLEAN, Default `false`
- `created_at`, `updated_at`: TIMESTAMPTZ

#### Entity: `Submission`
- `id`: UUID, Primary Key
- `task_id`: UUID, Foreign Key -> `Task(id)`, Not Null
- `enrollment_id`: UUID, Foreign Key -> `Enrollment(id)`, Not Null
- `file_path`: VARCHAR(500), Not Null
- `file_name`: VARCHAR(255), Not Null
- `submitted_at`: TIMESTAMPTZ, Not Null
- `status`: ENUM(`On-Time`, `Late`), Not Null
- `notes`: TEXT, Nullable
- Composite Unique Constraint: `(task_id, enrollment_id)`

#### Entity: `Attendance`
- `id`: UUID, Primary Key
- `session_id`: UUID, Foreign Key -> `CourseSession(id)`, Not Null
- `enrollment_id`: UUID, Foreign Key -> `Enrollment(id)`, Not Null
- `status`: ENUM(`Present`, `Absent`), Not Null
- `recorded_by`: UUID, Foreign Key -> `User(id)`, Not Null
- `created_at`, `updated_at`: TIMESTAMPTZ
- Composite Unique Constraint: `(session_id, enrollment_id)`

#### Entity: `Feedback`
- `id`: UUID, Primary Key
- `course_id`: UUID, Foreign Key -> `Course(id)`, Not Null
- `enrollment_id`: UUID, Foreign Key -> `Enrollment(id)`, Not Null
- `instructor_id`: UUID, Foreign Key -> `InstructorProfile(id)`, Not Null
- `content`: TEXT, Not Null
- `created_at`: TIMESTAMPTZ

#### Entity: `Payment`
- `id`: UUID, Primary Key
- `center_id`: UUID, Foreign Key -> `Center(id)`, Not Null
- `enrollment_id`: UUID, Foreign Key -> `Enrollment(id)`, Not Null
- `amount`: DECIMAL(10, 2), Not Null, Check (`amount > 0.00`)
- `payment_date`: DATE, Not Null
- `payment_method`: ENUM(`Cash`, `BankTransfer`, `POS_Card`, `Cheque`), Not Null
- `receipt_reference`: VARCHAR(100), Nullable
- `notes`: TEXT, Nullable
- `status`: ENUM(`Completed`, `Adjusted`), Default `Completed`
- `created_by`: UUID, Foreign Key -> `User(id)`, Not Null
- `created_at`: TIMESTAMPTZ

#### Entity: `InstructorDue`
- `id`: UUID, Primary Key
- `center_id`: UUID, Foreign Key -> `Center(id)`, Not Null
- `course_instructor_id`: UUID, Foreign Key -> `CourseInstructor(id)`, Not Null
- `accrued_amount`: DECIMAL(10, 2), Default `0.00`, Check (`accrued_amount >= 0.00`)
- `paid_amount`: DECIMAL(10, 2), Default `0.00`, Check (`paid_amount >= 0.00`)
- `outstanding_due`: DECIMAL(10, 2), Not Null
- `status`: ENUM(`Pending`, `PartiallyPaid`, `Paid`, `Adjusted`), Default `Pending`
- `last_calculated_at`: TIMESTAMPTZ

#### Entity: `AuditLog`
- `id`: UUID, Primary Key
- `center_id`: UUID, Foreign Key -> `Center(id)`, Not Null
- `user_id`: UUID, Foreign Key -> `User(id)`, Nullable
- `action`: VARCHAR(100), Not Null
- `entity_type`: VARCHAR(100), Not Null
- `entity_id`: UUID, Not Null
- `old_values`: JSONB, Nullable
- `new_values`: JSONB, Nullable
- `ip_address`: VARCHAR(45), Nullable
- `created_at`: TIMESTAMPTZ

### 16.3 File Storage & Handling Specifications
- **Maximum File Size:** 25 MB (26,214,400 bytes).
- **Supported File Types:**
  - Documents: `.pdf`, `.doc`, `.docx`, `.ppt`, `.pptx`, `.xls`, `.xlsx`
  - Images: `.jpg`, `.jpeg`, `.png`
  - Archives: `.zip`
- **Validation Protocol:** Backend must validate both file extension and MIME type. Disallowed MIME types (e.g., `application/x-msdownload`, `text/javascript`, `application/x-sh`) must be rejected.
- **Access Protocol:** Uploaded assets are saved with non-guessable UUID filenames in protected directories. File download routes require authentication and active course enrollment.

---

## 17. Integrations

### 17.1 MVP Architecture (Zero External Integrations)
To preserve simplicity and ensure rapid, reliable deployment, the **MVP operates as an entirely self-contained system with zero mandatory external API dependencies**. All workflows—user authentication, fee recording, attendance, file storage, and reporting—operate natively using the local PHP/Laravel backend, PostgreSQL database, and protected local file storage.

### 17.2 Post-MVP Integration Candidates

| Target Integration | Service / Protocol | Future Purpose |
| :--- | :--- | :--- |
| **Email Delivery** | SMTP / Amazon SES / SendGrid | Automated notifications for homework deadlines and fee receipts. |
| **Instant Messaging** | WhatsApp Business API / Twilio | Direct automated WhatsApp alerts for payment reminders and lecture updates. |
| **Online Payments** | Stripe / Tap / Paymob | Trainee self-service online installment checkout via credit card or digital wallets. |
| **Cloud Object Storage** | Amazon S3 / Google Cloud Storage | Offloading course file storage to scalable cloud buckets with pre-signed URLs. |
| **Video Conferencing** | Zoom API / Google Meet API | Automated lecture meeting creation and attendance synchronization. |

---

## 18. Edge Cases

| Edge Case ID | Scenario | Expected System Behavior |
| :--- | :--- | :--- |
| **EC-001** | Student already actively enrolled in Course A is submitted for re-enrollment in Course A. | System blocks request with HTTP `409 Conflict`: *"Student is already actively enrolled in this course."* |
| **EC-002** | User attempts to delete a Course containing existing student enrollments or payment logs. | System rejects hard delete: *"Cannot delete course with active enrollments. Course can only be archived."* |
| **EC-003** | Course is created without assigning any instructor, and Manager attempts to set status to `Active`. | System blocks status change: *"Course must have at least one assigned instructor before activation."* |
| **EC-004** | Student attempts to upload a 30 MB file for a task assignment. | Client-side validation stops upload; backend enforces a 25 MB hard limit, returning HTTP `413 Payload Too Large`. |
| **EC-005** | Student attempts to submit homework 5 minutes after deadline without an instructor override. | System rejects submission: *"Task submission deadline has passed. Submissions are closed."* |
| **EC-006** | Student submits homework after deadline when the instructor has enabled late submissions. | System accepts file, records submission timestamp, and marks status explicitly as `Late`. |
| **EC-007** | Staff attempts to log a student payment of $500 on an enrollment with a remaining balance of $200. | System prompts warning modal: *"Payment exceeds remaining balance by $300."* Requires explicit confirmation or amount correction. |
| **EC-008** | Staff enters a negative number or zero in the payment amount field. | Form validation blocks submission: *"Payment amount must be greater than 0.00."* |
| **EC-009** | Instructor attempts to submit attendance for a lecture session twice. | System performs an `upsert` or updates existing records; uniqueness constraint on `(session_id, enrollment_id)` prevents duplicate records. |
| **EC-010** | Student tries to access or download materials from a course they are not enrolled in by guessing the URL. | Backend authorization gate verifies course enrollment and returns HTTP `403 Forbidden`. |
| **EC-011** | Manager from Center A attempts to view or edit a course belonging to Center B via API ID manipulation. | Query is scoped with `WHERE center_id = CenterA_ID`, finding no record and returning HTTP `404 Not Found`. |
| **EC-012** | Database connection fails midway through a multi-step enrollment payment creation. | Database transaction rolls back completely; zero partial records are saved; user receives clear failure notification. |

---

## 19. Error States

### 19.1 Error Handling Architecture
1. **HTTP Status Code Standardization:** Clear usage of `400 Bad Request`, `401 Unauthorized`, `403 Forbidden`, `404 Not Found`, `409 Conflict`, `422 Unprocessable Entity`, and `500 Internal Server Error`.
2. **Standardized JSON Error Payload:**
```json
{
  "success": false,
  "error_code": "ERR_DUPLICATE_ENROLLMENT",
  "message": "The student is already actively enrolled in this course.",
  "errors": {
    "student_id": ["Student is already enrolled in course ID 505."]
  }
}
```

### 19.2 Standard Error Catalog

| Error Code | Trigger Condition | User-Facing Message | Corrective Action |
| :--- | :--- | :--- | :--- |
| `ERR_AUTH_INVALID` | Incorrect email or password submitted. | *"Invalid email or password. Please try again."* | Re-enter credentials. |
| `ERR_AUTH_SUSPENDED`| Inactive or suspended user attempts login. | *"Your account has been deactivated. Please contact your center administrator."* | Contact center manager. |
| `ERR_FORBIDDEN` | User attempts to access unauthorized role route or data. | *"You do not have permission to access this resource."* | Return to authorized dashboard. |
| `ERR_NOT_FOUND` | Requested entity ID does not exist in tenant database. | *"The requested record was not found."* | Refresh page or check directory. |
| `ERR_FILE_TOO_LARGE`| Uploaded file exceeds 25 MB limit. | *"File exceeds maximum allowed size of 25 MB."* | Compress file before re-uploading. |
| `ERR_FILE_MIME` | File extension or MIME type not in allowed list. | *"Unsupported file format. Please upload PDF, Office documents, Images, or ZIP."* | Convert file to allowed format. |
| `ERR_DEADLINE_PASSED`| Student submits homework after due date without override. | *"Submission closed. The deadline for this task has passed."* | Contact instructor for late override. |
| `ERR_INVALID_PAYMENT`| Payment amount <= 0 or invalid currency format. | *"Please enter a valid payment amount greater than 0.00."* | Correct amount input. |
| `ERR_DELETE_BLOCKED`| Attempt to hard-delete posted payment or active course. | *"This record cannot be deleted because historical records depend on it."* | Use adjustment or archive option. |
| `ERR_SERVER_ERROR` | Unexpected backend or database exception. | *"An unexpected error occurred. Our team has been notified. Please try again later."* | Retry action after delay. |

---

## 20. Security & Privacy Requirements

### 20.1 Authentication & Session Security
- **SEC-01 (Password Security):** All user passwords must be hashed using Argon2id or bcrypt (cost factor >= 12). Plaintext passwords must never be stored, logged, or cached.
- **SEC-02 (Session Security):** Authentication sessions must use secure, HTTP-only, SameSite cookies with strict domain scoping.
- **SEC-03 (Brute-Force Rate Limiting):** Login endpoints must be rate-limited to 5 failed attempts per IP/account per 15-minute window.

### 20.2 Authorization & Multi-Tenant Scoping
- **SEC-04 (Server-Side Authorization):** All access decisions must be made server-side using Laravel Policies and Gates. Client-side route guards are treated purely as UI conveniences.
- **SEC-05 (Tenant Parameter Protection):** The application must never trust a `CenterID` passed in query parameters or request bodies. Tenant context must always be derived from the authenticated session.
- **SEC-06 (Row-Level Security Defense-in-Depth):** In addition to application-level query scoping, PostgreSQL Row-Level Security (RLS) policies should be evaluated to enforce database-level tenant isolation.

### 20.3 Data Protection & Storage Security
- **SEC-07 (Protected File Downloads):** Course materials and homework files must be stored outside public web directories. File downloads must stream through an authorization gate verifying enrollment.
- **SEC-08 (MIME Sniffing Prevention):** All file download responses must include headers: `X-Content-Type-Options: nosniff` and appropriate `Content-Disposition`.
- **SEC-09 (SQL & Script Injection Prevention):** All database interactions must use parameterized queries and ORM prepared statements. User inputs must be sanitized to prevent Cross-Site Scripting (XSS).

### 20.4 Privacy & Data Minimization
- **SEC-10 (Confidentiality of Financials):** Student fee balances are strictly visible to Managers, Sales, and the individual student. Instructors must never have access to student financial records.
- **SEC-11 (Confidentiality of Instructor Dues):** Instructor compensation rates, earnings, and payout histories are strictly visible to Managers and the individual instructor.

---

## 21. Analytics & Tracking

### 21.1 MVP Telemetry & System Event Logs
To monitor operational health and system usage without adding third-party tracking overhead, VELORA logs internal system events:

| Event Identifier | Trigger Action | Logged Data Attributes |
| :--- | :--- | :--- |
| `EVT_USER_LOGIN` | User completes authentication. | `user_id, center_id, role, ip_address, timestamp` |
| `EVT_COURSE_CREATED` | Manager creates course. | `course_id, center_id, title, base_price, timestamp` |
| `EVT_STUDENT_ENROLLED` | Student enrolled in course. | `enrollment_id, student_id, course_id, final_payable, timestamp` |
| `EVT_MATERIAL_UPLOADED` | Instructor uploads resource. | `material_id, course_id, file_size, mime_type, timestamp` |
| `EVT_TASK_SUBMITTED` | Student uploads homework. | `submission_id, task_id, enrollment_id, is_late, timestamp` |
| `EVT_ATTENDANCE_LOGGED`| Session attendance submitted. | `session_id, course_id, total_present, total_absent, timestamp` |
| `EVT_PAYMENT_RECORDED` | Student payment logged. | `payment_id, enrollment_id, amount, payment_method, timestamp` |
| `EVT_INSTRUCTOR_PAID` | Settlement payout logged. | `due_id, instructor_id, amount_paid, timestamp` |

---

## 22. Success Metrics / KPIs

### 22.1 Core Business & Operational Metrics

| Metric ID | KPI Name | Definition / Calculation | MVP Target |
| :--- | :--- | :--- | :--- |
| **KPI-01** | **Operational Adoption** | Percentage of active courses managed entirely through VELORA vs. external spreadsheets. | **100%** within 30 days of center onboarding. |
| **KPI-02** | **Task Delivery Rate** | Percentage of enrolled students submitting assigned tasks before the deadline. | **>= 75%** average cohort submission rate. |
| **KPI-03** | **Attendance Completeness**| Percentage of completed lecture sessions with recorded attendance sheets. | **>= 95%** of all completed sessions. |
| **KPI-04** | **Receivable Tracking Accuracy** | Discrepancy between physical cash/bank receipts and VELORA logged balances. | **0%** reconciliation discrepancy. |
| **KPI-05** | **Instructor Dues Accuracy** | Percentage of instructor payouts settled without manual calculation disputes. | **100%** dispute-free settlements. |
| **KPI-06** | **Administrative Time Saved** | Reduction in hours spent by Center Managers reconciling spreadsheets weekly. | **>= 60%** reduction in manual reporting time. |

---

## 23. Acceptance Criteria

### 23.1 Testable Scenarios (Given / When / Then)

#### AC-01: Multi-Tenant Data Isolation
- **Given** Manager Alice is logged into Center A, and Course #202 belongs to Center B,
- **When** Alice attempts to request details for Course #202 via URL or direct API call,
- **Then** the backend automatically applies tenant scoping, returns HTTP `404 Not Found`, and exposes zero records from Center B.

#### AC-02: Multi-Instructor Course Assignment
- **Given** Course "Full-Stack Web" exists in Center A,
- **When** Manager assigns Instructor Mohammed (`FixedAmount = $1500`) and Instructor Youssef (`PerSession = $50`),
- **Then** both assignments are persisted, both instructors can access course materials and sessions, and assigning either instructor a second time is blocked by unique constraints.

#### AC-03: Student Enrollment Math & Balance Initialization
- **Given** a Course has a Base Price of $1,000,
- **When** Sales enrolls Student Layla with a fixed discount of $100,
- **Then** the system creates the enrollment with `BasePrice = $1,000.00`, `DiscountAmount = $100.00`, `FinalPayableAmount = $900.00`, `TotalPaidAmount = $0.00`, and `RemainingBalance = $900.00`.

#### AC-04: Student Installment Payment Processing
- **Given** Student Layla has an enrollment with `RemainingBalance = $900.00`,
- **When** Sales records an offline cash payment of $300.00 with receipt reference "REC-101",
- **Then** the system logs the payment record, recalculates `TotalPaidAmount = $300.00`, `RemainingBalance = $600.00`, and updates payment status to `PartiallyPaid`.

#### AC-05: Non-Destructive Financial Immutability
- **Given** a posted student payment record of $300.00 exists in the database,
- **When** any user attempts a DELETE API request against this payment record,
- **Then** the system rejects the operation with HTTP `403 Forbidden` / `405 Method Not Allowed`, ensuring posted transactions cannot be hard-deleted.

#### AC-06: Task Deadline & Late Submission Enforcement
- **Given** a Task has a deadline of 2026-10-15 23:59 and late submission is disabled,
- **When** an enrolled student attempts to submit a file at 2026-10-16 00:05,
- **Then** the system blocks the upload with an error *"The deadline for this task has passed."*
- **When** the instructor enables the "Allow Late Submission" toggle for this student,
- **Then** the upload is accepted and stored with status `Late`.

#### AC-07: Session Attendance Uniqueness
- **Given** Course Session #1 has 10 enrolled students,
- **When** an instructor marks attendance for all 10 students and saves the sheet,
- **Then** exactly 10 attendance records are created, and any subsequent submission updates existing records rather than inserting duplicate rows.

#### AC-08: Sales Role Permission Boundaries
- **Given** User Sarah is logged in with the `Sales` role,
- **When** she navigates to a student profile,
- **Then** she can view contact information, enrollment status, and payment balances, but cannot access homework submissions, instructor feedback notes, or general financial profit reports.

---

## 24. Future Features

The following features represent the strategic roadmap for post-MVP releases:

### Phase 2: Enhanced Communications & Operations
- **F-01 (In-App Notification Center):** Real-time bell notifications for new materials, assignment deadlines, and payment confirmations.
- **F-02 (Automated WhatsApp Alerts):** Integration with WhatsApp Business API to send automated installment reminders and session schedule changes.
- **F-03 (Automated Email Delivery):** Automated transactional email receipts and welcome packs upon course enrollment.
- **F-04 (File Versioning for Submissions):** Preserve historical revisions of student homework uploads with side-by-side comparison.

### Phase 3: Online Commerce & Certification
- **F-05 (Online Payment Gateways):** Integrate Stripe, Paymob, and Tap to allow trainees to pay fees online via credit card and Apple Pay.
- **F-06 (Digital Certificate Builder):** Create customizable certificate templates, automated PDF generation, and public QR-code verification links.
- **F-07 (Student Attendance Analytics):** Graphical drop-out risk indicators flagging students with consecutive absences.

### Phase 4: Mobile Apps & Advanced Academic Tools
- **F-08 (Dedicated Mobile Apps):** Native iOS and Android apps for instructors and students built with React Native / Flutter.
- **F-09 (Interactive Quizzes & Question Banks):** Timed objective quizzes with automated scoring.
- **F-10 (Multi-Center Enterprise Hierarchy):** Unified organization switcher allowing enterprise owners to manage multiple training centers under one corporate umbrella.

---

## 25. Risks & Dependencies

### 25.1 Project Risks & Mitigations

| Risk ID | Risk Description | Severity | Probability | Mitigation Strategy |
| :--- | :--- | :---: | :---: | :--- |
| **RSK-01** | **Scope Creep (LMS Features):** Stakeholders requesting live video streaming, auto-grading, or chat during MVP development. | High | High | Strict adherence to Section 4 (Non-Goals). Reiterate that MVP focus is operational and financial centralization. |
| **RSK-02** | **Tenant Data Leakage:** Accidental cross-center data exposure caused by missing query filters. | Critical | Low | Enforce centralized Laravel query scopes, automated integration tests verifying cross-tenant access rejection, and evaluate PostgreSQL Row-Level Security. |
| **RSK-03** | **Unrecorded Cash Collections:** Sales staff receiving cash from students without logging entries into VELORA. | High | Medium | Center management enforce mandatory student receipt verification policy. Students check portal balances and flag missing receipts. |
| **RSK-04** | **Instructor Non-Adoption:** Instructors continuing to collect homework via personal WhatsApp. | Medium | Medium | Simple, mobile-optimized UI for taking attendance and reviewing tasks. Center ties compensation payouts to session completion in the system. |
| **RSK-05** | **Storage Bloat from Large Files:** Users uploading oversized files or unsupported formats. | Medium | Low | Hard 25 MB file size limit enforced on both frontend and backend; strict MIME validation; scheduled cleanups of unlinked scratch files. |

### 25.2 Technical & Operational Dependencies
- **DEP-01 (PostgreSQL 15+):** Relational database with support for UUIDs, JSONB audit logging, and Row-Level Security.
- **DEP-02 (Secure File Storage):** Dedicated protected filesystem or private S3-compatible bucket for uploaded documents.
- **DEP-03 (Center Policy Alignment):** Agreement from participating training centers on standard compensation models and offline payment collection procedures.

---

## 26. Open Questions

The following decisions require explicit business confirmation before database implementation:

1. **OQ-01 (Multi-Center User Accounts):**  
   *Current Assumption:* A user account belongs to exactly one training center.  
   *Question for Stakeholders:* Do instructors frequently teach across multiple distinct training centers? If yes, should we support a single login credentials entity linking to multiple center memberships, or keep credentials strictly isolated per center for the MVP?
2. **OQ-02 (Sales Payment Logging Workflow):**  
   *Current Assumption:* Sales staff can record payments directly, immediately updating the student's remaining balance.  
   *Question for Stakeholders:* Does center management require an explicit "Approval Workflow" where payments entered by Sales remain `Pending Verification` until confirmed by an Accountant or Manager?
3. **OQ-03 (Late Submission Policy Granularity):**  
   *Current Assumption:* Late submissions are blocked by default and permitted only when an instructor or manager enables an override.  
   *Question for Stakeholders:* Should tasks support an optional "Grace Period" (e.g., allow submissions up to 24 hours late automatically, but flag them as `Late`), or is strict manual override preferred?
4. **OQ-04 (Partial Session Compensation for Instructors):**  
   *Current Assumption:* For `PerSession` compensation, dues accrue only when a session is marked completed with an attendance record.  
   *Question for Stakeholders:* How should cancelled sessions or substitute instructors be handled within the dues calculation?

---

## 27. Recommended Development Phases

To ensure steady progress and high software quality, development is structured into five progressive phases:

### Phase 1: Foundation, Architecture & Multi-Tenant Setup (Weeks 1 – 2)
- Set up PostgreSQL schema with `CenterID` scoping, composite unique constraints, and foreign keys.
- Implement User Authentication (Argon2id/bcrypt), JWT/Session cookie handling, and role middleware.
- Configure Laravel Policies, Base Controller scoping, and global tenant query filters.
- Establish responsive Next.js layout shells, design tokens, and role-based route guards.

### Phase 2: Core Academic Operations & Course Management (Weeks 3 – 4)
- Build Course CRUD, status lifecycle transitions (`Draft`, `Active`, `Archived`), and pricing configuration.
- Implement Multi-Instructor assignment junction and `CourseSession` (lecture) scheduling.
- Build Student Profile management and Course Enrollment with discount balance calculations.
- Implement Session Attendance sheet with unique constraints and real-time summary calculations.

### Phase 3: Learning Interactions, Materials & Tasks (Weeks 5 – 6)
- Develop protected file storage pipeline with 25 MB size limits, MIME type validation, and authenticated streaming.
- Build Course Materials upload and download interfaces for instructors and students.
- Build Task creation, deadline enforcement, student file submission, and late submission override workflows.
- Implement 1-to-1 private Instructor Feedback system.

### Phase 4: Financial Management, Instructor Dues & Dashboards (Weeks 7 – 8)
- Build Student Payment ledger, installment tracking, and immutable transaction audit protection.
- Implement automated Instructor Dues calculation engine (`FixedAmount`, `Percentage`, `PerSession`).
- Build Instructor settlement payout tracking and personal dues views.
- Develop role-specific dashboards (Manager executive metrics, Sales follow-up queue, Instructor schedule, Student portal).

### Phase 5: Testing, Hardening & MVP Launch (Weeks 9 – 10)
- End-to-end integration testing of cross-tenant isolation and security boundary testing.
- Usability and mobile responsiveness audits across 360px – 1440px viewports.
- Performance testing: Verify sub-500ms API responses under concurrent simulated center load.
- Production deployment, database migration verification, and training center pilot onboarding.

---
*End of Product Requirements Document.*