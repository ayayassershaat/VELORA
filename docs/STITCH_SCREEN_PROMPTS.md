# Google Stitch Screen Prompts — VELORA (فيلورا) MVP

**Document Version:** 1.0.0  
**Author:** Senior Google Stitch UI/UX Prompt Engineer  
**Status:** Approved for MVP UI Generation  
**Date:** October 2026  
**Source Truth:** [PRD.md](PRD.md) | [SCREEN_INVENTORY.md](SCREEN_INVENTORY.md) | [UI_UX_REQUIREMENTS.md](UI_UX_REQUIREMENTS.md) | [UX_BLUEPRINT.md](UX_BLUEPRINT.md) | [STITCH_MASTER_PROMPT.md](STITCH_MASTER_PROMPT.md)

---

## Executive Summary & Engineering Usage Guide

This document contains **35 production-ready, individually copyable Google Stitch prompts** covering every screen in the approved VELORA MVP scope.

Each prompt is formatted as a self-contained, fenced block ready to copy and paste directly into **Google Stitch** or any modern AI UI prototyping tool. Every prompt strictly adheres to the approved Design System, Tenant Scoping (`CenterID`), Role-Based Access Control (RBAC), and offline financial constraints.

### How to Use in Google Stitch:
1. Select and copy the entire text within any screen's fenced ````text code block.
2. Paste the block into Google Stitch.
3. Stitch will render that specific screen with high-fidelity components, exact typography, responsive breakpoints, realistic domain mock data, and full accessibility compliance matching the master design tokens.

---

## Master Design System & Shared Architectural Rules

Every prompt in this specification automatically inherits and enforces these shared visual and architectural tokens:

```
+-----------------------------------------------------------------------------------+
| SHARED TOP HEADER (Fixed 64px, z-40, bg-white, border-b border-slate-200)         |
| [VELORA Logo] [Center Name Badge]   [Dashboard Link]    [Notifications Bell] [User Dropdown]|
+-----------------------+-----------------------------------------------------------+
| SHARED SIDEBAR        | MAIN CONTENT CANVAS (Scrollable, bg-slate-50, p-6 to p-8)   |
| (w-64, bg-white,      | - Page Header (Title, Subtitle, Primary Action CTAs)       |
| border-r slate-200,   | - Metric KPI Summary Cards (4-col grid, gap-4)            |
| Role-Filtered Links)  | - Primary Data Tables / Workspace Forms                   |
|                       | - Modals / Drawers (Centered z-50, dark overlay)          |
+-----------------------+-----------------------------------------------------------+
```

### 1. Global Visual & Style Tokens
- **Design Philosophy:** Clean enterprise administrative SaaS. High information density, scannable data grids, clear typographical hierarchy, zero decorative clutter.
- **Color Palette:**
  - **Canvas Background:** `#F8FAFC` (Tailwind `slate-50`)
  - **Card / Surface:** `#FFFFFF` (White) with subtle border `#E2E8F0` (`slate-200`)
  - **Primary Brand / Action:** `#4F46E5` (`indigo-600`), Hover `#4338CA` (`indigo-700`), Soft Tint `#EEF2FF` (`indigo-50`)
  - **Text Colors:** Primary `#0F172A` (`slate-900`), Secondary `#334155` (`slate-700`), Muted `#64748B` (`slate-500`)
  - **Status Accents:**
    - **Success (Approved, Paid, Present, Completed):** `#16A34A` / `#DCFCE7` (`green-600` / `green-100`)
    - **Warning (Pending Approval, Partially Paid, Overdue):** `#D97706` / `#FEF3C7` (`amber-600` / `amber-100`)
    - **Critical / Danger (Rejected, Absent, Excess Refund, Deadline Passed):** `#DC2626` / `#FEE2E2` (`red-600` / `red-100`)
    - **Neutral / Informational (Draft, Waitlist, Scheduled):** `#2563EB` / `#DBEAFE` (`blue-600` / `blue-100`)
- **Typography:** `Inter`, `system-ui`, `-apple-system`, `sans-serif`.
  - H1 / Page Header: `24px` (`text-2xl`), bold (`font-bold`), tracking-tight
  - H2 / Section / Modal Header: `18px` (`text-lg`), semibold (`font-semibold`)
  - Body Text: `14px` (`text-sm`), regular (`font-normal`), leading-5
  - Captions / Badges / Meta: `12px` (`text-xs`), medium (`font-medium`)
  - Numeric KPI Values: `30px` - `36px` (`text-3xl`), extrabold (`font-extrabold`)
- **Spacing Grid:** 4px baseline scale (`p-2`, `p-3`, `p-4`, `p-6`, `gap-4`, `gap-6`).
- **Corner Radii & Shadows:** `rounded-lg` (8px) for buttons, inputs, and dropdowns; `rounded-xl` (12px) for cards, tables, and dialogs. Elevation uses subtle shadows (`shadow-sm`, `shadow-md`, `shadow-xl` for modals).

### 2. Core Non-Negotiable MVP Constraints
- **Strict Role-Based Access Control (RBAC):** Navigation links outside a role's permissions are completely hidden from the sidebar, never shown as disabled items.
- **Multi-Tenant Data Isolation:** Scoped to the active center (`CenterID: CTR-001`). All metrics, courses, and students belong exclusively to the active center.
- **Offline Financial Operations Only:** All payments are offline (`Cash`, `Bank Transfer`, `POS Card`, `Cheque`). Sales creates payments in `Pending Approval` status; only Center Manager can approve/reject.
- **Automatic Overpayment Handling:** Excess payment amounts (`Amount > RemainingBalance`) immediately auto-generate a linked `Refund Request` in `Pending Refund` status.
- **Academic Privacy:** Instructor qualitative feedback is strictly 1:1 and private between instructor and student. Sales has zero access to academic feedback or instructor compensation.
- **Instructor Compensation:** Automatically calculated according to pre-assigned contracts (`Fixed`, `PercentageOfCollectedRevenue`, or `PerSession`). `PerSession` accruals only trigger upon completed lecture sessions with submitted attendance.

---

## Complete User-Flow Order Mapping (35 Screens)

The screens are sorted according to the operational user journey:

```
[Phase 1: Authentication & Entry]
 └── SCR-AUTH-01: Login & Session Authentication

[Phase 2: Administrative Foundation & Course Setup — Manager Flow]
 ├── SCR-M-01: Manager Dashboard
 ├── SCR-M-10: Staff & Users Management
 ├── SCR-M-04: Instructors Directory & Compensation
 ├── SCR-M-02: Courses Catalog & Lifecycle
 └── SCR-M-03: Course Details, Sessions & Activation

[Phase 3: Student Intake, Enrollment & Payment Collection — Sales Flow]
 ├── SCR-S-01: Sales Operations Dashboard
 ├── SCR-S-02: Student Intake & Directory (Sales)
 ├── SCR-S-03: Course Enrollment, Discounts & Waitlist (Sales)
 └── SCR-S-04: Offline Payment Entry & Overpayment Check (Sales)

[Phase 4: Center Oversight, Financial Reconciliation & Payouts — Manager Flow]
 ├── SCR-M-05: Center Students Directory
 ├── SCR-M-06: Center Enrollments & Waitlist Management
 ├── SCR-M-07: Payment Approvals & Excess Refund Queue
 ├── SCR-M-08: Instructor Dues & Settlement Payouts
 ├── SCR-M-09: Center Operational & Financial Reports
 └── SCR-M-11: Immutable Audit Trail

[Phase 5: Academic Delivery, Content & Evaluation — Instructor Flow]
 ├── SCR-I-01: Instructor Academic Dashboard
 ├── SCR-I-02: My Assigned Courses
 ├── SCR-I-03: Course Workspace & Delivery Hub
 ├── SCR-I-04: Sessions & Attendance Roster
 ├── SCR-I-05: Course Materials Library (Instructor)
 ├── SCR-I-06: Tasks, Deadlines & Submissions Review
 ├── SCR-I-07: 1:1 Private Student Feedback
 └── SCR-I-08: My Earnings & Accruals

[Phase 6: Trainee Portal, Learning & Balance Tracking — Student Flow]
 ├── SCR-ST-01: Student Portal Dashboard
 ├── SCR-ST-02: My Enrolled Courses
 ├── SCR-ST-03: Course Learning Hub (Student)
 ├── SCR-ST-04: Course Materials & Downloads (Student)
 ├── SCR-ST-05: Tasks & Homework Submissions (Student)
 ├── SCR-ST-06: Personal Attendance Record
 ├── SCR-ST-07: Private Instructor Feedback View
 └── SCR-ST-08: Student Tuition Ledger & Receipts

[Phase 7: Global Account & Communication Hub — Shared Flow]
 ├── SCR-SHARED-01: Notifications Center
 ├── SCR-SHARED-02: User Profile Management
 └── SCR-SHARED-03: Account & Display Settings
```

---

# SECTION 1: AUTHENTICATION & ENTRY FLOW

---

### SCR-AUTH-01: Login & Session Authentication

````text
# SCREEN IDENTIFIER: SCR-AUTH-01
# SCREEN TITLE: Login & Session Authentication
# TARGET ROUTE: /login
# PRIMARY ACTOR: All Roles (Unauthenticated User: Manager, Sales, Instructor, Student)
# PURPOSE: Securely authenticate user credentials (email & password), validate center tenancy, establish an active session, and automatically route the user to their authorized role dashboard.

# VISUAL & LAYOUT HIERARCHY:
- Layout: Auth Layout (Centered standalone container, no app header, no sidebar).
- Container: Clean, elevated modal-style card (max-w-md w-full mx-auto p-8 bg-white rounded-2xl shadow-xl border border-slate-200).
- Backdrop: Subtle textured neutral canvas (bg-slate-50 min-h-screen flex flex-col justify-center items-center px-4).
- Structure:
  1. Header: VELORA Brand Logo (indigo geometric mark), Platform Name "VELORA", Subtitle "Training Center Management Portal".
  2. Tenant Badge: Dynamic Center selector/indicator "Beacon Training Center (Ramallah Branch)".
  3. Form Card: Email field, Password field (with visibility toggle), Remember Me checkbox, Forgot Password link.
  4. Primary CTA: "Sign In to Workspace" button (solid Indigo, full width).
  5. Feedback Area: Inline alert container for validation errors, inactive account warnings, or rate-limiting countdowns.
  6. Footer: Security watermark "Protected by Role-Based Access Control & 256-Bit Tenant Encryption".

# DOMAIN DATA & MOCK ENTITIES:
- Email Placeholder: "name@beacontraining.com"
- Password Placeholder: "••••••••••••"
- Sample Pre-filled Credentials for Testing:
  - Manager: "ahmad.manager@beacon.edu" | Role: MANAGER
  - Sales: "sara.sales@beacon.edu" | Role: SALES
  - Instructor: "omar.dev@beacon.edu" | Role: INSTRUCTOR
  - Student: "tariq.student@gmail.com" | Role: STUDENT
- Tenant Context: CenterID: "CTR-001", CenterName: "Beacon Training Center", CenterStatus: "Active"

# DESIGN SYSTEM COMPONENTS:
- CardContainer (bg-white, rounded-2xl, border-slate-200, shadow-xl)
- InputField (Label: "Work Email", type: "email", icon: "Mail", required)
- InputField (Label: "Password", type: "password", icon: "Lock", rightAction: "EyeToggle", required)
- Checkbox (Label: "Keep me signed in on this device")
- Button (Primary, Indigo-600, w-full, h-12, text-sm font-semibold, rounded-lg, shadow-sm)
- InlineAlert (Semantic Danger: Red-50 background, Red-700 text, Red-200 border, AlertTriangle icon)

# CALLS TO ACTION (CTAs):
- Primary CTA: "Sign In" (Submits login form, triggers credential validation, starts session).
- Secondary CTA: "Forgot your password?" (Opens support contact modal: "Please contact your Center Manager to reset your account password").

# FORM VALIDATION & BUSINESS RULES:
- Email Validation: Required, RFC-5322 regex format. Cannot be empty.
- Password Validation: Required, minimum 8 characters.
- Tenant & Account Status Verification:
  - If user account is marked "Inactive" or "Suspended" -> Reject with error: "Your account has been deactivated. Please contact your center administrator."
  - Rate Limiting: 5 consecutive failed attempts lock the login form for 60 seconds with countdown badge.

# NAVIGATION & ROUTING RULES:
- On Successful Auth -> Evaluate User.Role and redirect immediately:
  - Role == "MANAGER" -> Redirect to `/manager/dashboard`
  - Role == "SALES" -> Redirect to `/sales/dashboard`
  - Role == "INSTRUCTOR" -> Redirect to `/instructor/dashboard`
  - Role == "STUDENT" -> Redirect to `/student/dashboard`

# COMPREHENSIVE STATES:
- Default State: Empty form inputs with cursor auto-focused on Email field.
- Loading State: "Sign In" button disabled, displays spinner icon + label "Verifying Credentials...", inputs read-only.
- Error State (Invalid Credentials): Red outline on email/password inputs, top inline alert: "Invalid email or password. Please verify and try again."
- Error State (Rate Limited): Form disabled with amber countdown banner: "Too many login attempts. Please wait 45 seconds before retrying."
- Success State: Button turns green with checkmark "Signed In! Loading Workspace...", smooth fade-out into dashboard.

# RESPONSIVE BEHAVIOR:
- Mobile (<640px): Card spans full width (px-4), padding adjusts to 24px, logo scales down to 32px height, touch targets >=48px.
- Tablet (641px-1024px): Centered card (max-w-md), 48px vertical padding.
- Desktop (1025px+): Centered card (max-w-md), elevated shadow with subtle indigo ambient glow.

# ACCESSIBILITY (A11y) & WCAG 2.1 AA:
- Semantic tags: `<main role="main">`, `<form aria-label="Sign In Form">`, `<fieldset>`
- Focus management: Clear visible ring `focus:ring-2 focus:ring-indigo-500 focus:outline-none`.
- Labels: Explicit `<label for="email">` and `<label for="password">`.
- Error announcements: Inline error container has `role="alert"` and `aria-live="assertive"`.
- Keyboard support: Tab moves between inputs, Enter key submits form.
````

---

# SECTION 2: ADMINISTRATIVE FOUNDATION & COURSE SETUP (MANAGER FLOW)

---

### SCR-M-01: Manager Dashboard

````text
# SCREEN IDENTIFIER: SCR-M-01
# SCREEN TITLE: Center Manager Dashboard
# TARGET ROUTE: /manager/dashboard
# PRIMARY ACTOR: Center Manager (`MANAGER`)
# PURPOSE: Provide center directors with an executive operational and financial cockpit, highlighting active cohorts, enrollment ratios, collected revenue vs receivables, pending instructor liabilities, and urgent operational alerts.

# VISUAL & LAYOUT HIERARCHY:
- Layout: Standard App Shell (Fixed Top Header + Shared Left Sidebar filtered for MANAGER).
- Top Bar: Logo, Center Badge "Beacon Training Center", Dashboard link (active), Notifications (3 unread), User Avatar "Ahmad Qasim (Manager)".
- Sidebar: Dashboard (active), Courses, Instructors, Students, Enrollments, Payments, Instructor Dues, Reports, Staff & Users, Audit Log.
- Main Canvas Structure:
  1. Page Header: Title "Executive Dashboard", Subtitle "Operational & Financial Overview for Beacon Training Center", Right Actions: "Create Course" (Primary button), "Download Summary" (Secondary outline).
  2. Financial Health Summary (4-Card Metric Grid): Invoiced Revenue, Collected Revenue, Outstanding Receivables, Accrued Instructor Liabilities.
  3. Operations KPI Row (3-Card Metric Grid): Active Courses, Total Enrolled Students, Active Instructors.
  4. Two-Column Working Section:
     - Left Column (60%): "Urgent Operational Alerts & Actions" (Pending payment approvals, Courses starting this week, Full-capacity waitlists).
     - Right Column (40%): "Recent Cash Receipts & Payouts" (List of latest 5 transactions with status badges).

# DOMAIN DATA & MOCK ENTITIES:
- Center Info: ID: "CTR-001", Name: "Beacon Training Center", Currency: "NIS" (New Israeli Shekels)
- Financial Metrics:
  - Total Invoiced Revenue: "₪ 184,500"
  - Total Collected Revenue: "₪ 138,200" (74.9% collection rate, +12% vs last month)
  - Outstanding Receivables: "₪ 46,300" (from 28 enrolled students with pending installments)
  - Pending Instructor Liabilities: "₪ 18,750" (accrued across 8 active cohorts)
- Operational Metrics:
  - Active Courses: "12 Cohorts" (2 starting in next 7 days)
  - Enrolled Students: "186 Trainees" (Avg 15.5 per cohort)
  - Active Instructors: "14 Trainers" (9 on Fixed, 3 on Percentage, 2 on PerSession)
- Operational Alerts:
  - Alert 1: "5 Offline Payment Vouchers Awaiting Manager Approval (Total: ₪ 4,200)" -> Action: "Review Queue"
  - Alert 2: "Course 'Full-Stack Web Dev - Cohort 4' reached 100% capacity (20/20) with 4 students on waitlist" -> Action: "View Waitlist"
  - Alert 3: "Course 'UI/UX Design Spring' starts in 3 days with unassigned lead instructor" -> Action: "Assign Instructor"

# DESIGN SYSTEM COMPONENTS:
- MetricCard (Icon, Title, Numerical Value, Trend Indicator, Subtext)
- AlertBanner (Warning Amber: Icon, Bold Message, Action Link)
- ActionCard (Elevated border-slate-200, hover:border-indigo-400 transition)
- StatusBadge (Success: Green, Warning: Amber, Danger: Red, Neutral: Slate)
- PrimaryButton (Indigo-600, Plus icon, "New Course")
- SecondaryButton (White, border-slate-300, text-slate-700, "View Reports")

# CALLS TO ACTION (CTAs):
- Primary CTA: "Create New Course" -> Opens `/manager/courses` with New Course drawer.
- Alert CTA 1: "Review Payments" -> Routes directly to `/manager/payments` (Approval Queue tab).
- Alert CTA 2: "View Course Cohort" -> Routes to `/manager/courses/CRS-FSW-004`.
- Transaction Row Click -> Opens payment receipt modal with full audit metadata.

# FORM VALIDATION & BUSINESS RULES:
- Scope: Read-only aggregated dashboard. Strictly scoped to current CenterID (`CTR-001`).
- Calculations: `Receivables = Total Invoiced - Total Collected`. `Collection Rate % = (Collected / Invoiced) * 100`.
- All monetary amounts formatted with currency symbol `₪` and comma separators.

# NAVIGATION & ROUTING RULES:
- Clicking "Courses" card -> Routes to `/manager/courses`.
- Clicking "Students" card -> Routes to `/manager/students`.
- Clicking "Instructor Liabilities" -> Routes to `/manager/instructor-dues`.

# COMPREHENSIVE STATES:
- Default State: Populated metrics, 3 action alerts, recent transaction table.
- Loading State: Skeleton cards with shimmer pulse animation (`animate-pulse bg-slate-200`) across all metric grids.
- Empty State (New Center): Metric cards show "₪ 0.00" and "0 Active Courses", alert section shows "Welcome to VELORA! Get started by setting up your staff and creating your first course cohort" with "Create Course" CTA.
- Error State: Error toast: "Failed to load real-time center metrics. [Retry]"

# RESPONSIVE BEHAVIOR:
- Mobile (<640px): Metrics stack in single column; Alerts collapse into accordion items; tables convert to stacked card lists.
- Tablet (641px-1024px): 2-column metric grid; alerts stack above recent transactions.
- Desktop (1025px+): 4-column metric grid; 60/40 split working canvas; fixed sidebar.

# ACCESSIBILITY (A11y) & WCAG 2.1 AA:
- Semantic markup: `<header>`, `<nav>`, `<main>`, `<section aria-labelledby="financial-summary">`.
- KPI headings have `aria-level="2"`.
- Contrast ratio: All text meets WCAG AA (Slate-900 on White = 16:1, Green-700 on Green-50 = 7.2:1).
````

---

### SCR-M-10: Staff & Users Management

````text
# SCREEN IDENTIFIER: SCR-M-10
# SCREEN TITLE: Staff & User Account Management
# TARGET ROUTE: /manager/staff
# PRIMARY ACTOR: Center Manager (`MANAGER`)
# PURPOSE: Provide center managers with full administrative control over center staff accounts (`Manager`, `Sales`, `Instructor`), including user creation, role assignment, active/suspended status toggling, and contact details updating.

# VISUAL & LAYOUT HIERARCHY:
- Layout: App Shell (Shared Header + Manager Sidebar with "Staff & Users" highlighted).
- Main Canvas:
  1. Page Header: Title "Staff & User Accounts", Description "Manage employee logins, center access permissions, and roles", Right Action: "Add Staff Member" button (Primary).
  2. Search & Filter Bar: Text search (Search by Name, Email, or Phone), Role Filter (All, Manager, Sales, Instructor), Status Filter (All, Active, Suspended).
  3. Staff Data Table: Full-width responsive table listing all staff in center.
  4. Slide-Over Drawer / Modal: "Create / Edit Staff Account" form.

# DOMAIN DATA & MOCK ENTITIES:
- Center Scope: CenterID: "CTR-001"
- Sample Staff Directory:
  - User 1: ID: "USR-001", Name: "Ahmad Qasim", Email: "ahmad.manager@beacon.edu", Phone: "+970-599-112233", Role: "MANAGER", Status: "Active", Created: "2026-01-10"
  - User 2: ID: "USR-002", Name: "Sara Al-Khatib", Email: "sara.sales@beacon.edu", Phone: "+970-598-445566", Role: "SALES", Status: "Active", Created: "2026-02-01"
  - User 3: ID: "USR-003", Name: "Omar Farooq", Email: "omar.dev@beacon.edu", Phone: "+970-597-778899", Role: "INSTRUCTOR", Status: "Active", Created: "2026-02-15"
  - User 4: ID: "USR-004", Name: "Leila Mansour", Email: "leila.design@beacon.edu", Phone: "+970-599-880011", Role: "INSTRUCTOR", Status: "Suspended", Created: "2026-03-01"

# DESIGN SYSTEM COMPONENTS:
- DataTable (Columns: User Name & Avatar, Email, Phone, Role Badge, Status Badge, Last Active, Actions Menu)
- RoleBadge (Manager: Indigo, Sales: Amber, Instructor: Blue)
- StatusBadge (Active: Green, Suspended: Red)
- SlideOverDrawer (Title: "New Staff Member", Width: 480px, Form controls, Sticky bottom action bar)
- InputField, SelectDropdown, SwitchToggle (Account Active / Suspended)

# CALLS TO ACTION (CTAs):
- Primary CTA: "Add Staff Member" (Opens slide-over creation drawer).
- Row Action 1: "Edit Details" (Opens drawer populated with user's data).
- Row Action 2: "Suspend / Activate" (Toggles user login status with confirmation modal).
- Drawer CTA: "Save Account" (Submits form, triggers server validation, updates table).

# FORM VALIDATION & BUSINESS RULES:
- Full Name: Required, minimum 3 characters.
- Email Address: Required, valid email format, must be strictly unique within the center.
- Phone Number: Required, valid international format (`+970...` or `059...`).
- Role Selection: Mandatory, restricted strictly to `MANAGER`, `SALES`, or `INSTRUCTOR`. (Student accounts cannot be created here; created via Student Intake).
- Self-Lockout Protection: A manager cannot suspend or revoke their own active Manager account.

# NAVIGATION & ROUTING RULES:
- Sidebar "Staff & Users" navigates to `/manager/staff`.
- Saving account refreshes `/manager/staff` table with updated record and success toast.

# COMPREHENSIVE STATES:
- Default State: Populated staff table with 4 rows, active search bar, pagination (Showing 1-4 of 4).
- Loading State: Table rows replaced by 4 skeleton row shimmers.
- Empty State (No Search Match): "No staff members found matching '[query]'. [Clear Filters]".
- Error State: Form drawer error message: "Email address already registered to another staff member in this center."
- Success State: Toast banner: "Staff account for [Name] successfully created. Login credentials dispatched."

# RESPONSIVE BEHAVIOR:
- Mobile (<640px): Table collapses into individual staff contact cards with quick call/email buttons; drawer takes full screen width.
- Tablet (641px-1024px): Responsive table with horizontal scroll for metadata columns.
- Desktop (1025px+): Full table layout with right-aligned action menus.

# ACCESSIBILITY (A11y) & WCAG 2.1 AA:
- Table uses `<th>` with `scope="col"`, `<caption>` for table summary.
- Drawer traps focus when open, `Escape` key closes drawer.
- Status toggle has explicit `aria-checked="true/false"` and descriptive `aria-label`.
````

---

### SCR-M-04: Instructors Directory & Compensation Overview

````text
# SCREEN IDENTIFIER: SCR-M-04
# SCREEN TITLE: Instructors Directory & Compensation Overview
# TARGET ROUTE: /manager/instructors
# PRIMARY ACTOR: Center Manager (`MANAGER`)
# PURPOSE: Provide center managers with a centralized view of all academic instructors, their active course assignments, their compensation agreements (`Fixed`, `Percentage`, `PerSession`), and accrued compensation liabilities.

# VISUAL & LAYOUT HIERARCHY:
- Layout: Standard App Shell (Manager Sidebar, "Instructors" active).
- Main Canvas:
  1. Page Header: Title "Instructors & Compensation", Subtitle "Manage academic faculty, course assignments, and compensation agreements", Right Action: "View All Dues" button (links to `/manager/instructor-dues`).
  2. Summary Metric Row (3 Cards): Total Active Instructors (14), Active Cohort Assignments (16), Total Accrued Compensation (₪ 18,750).
  3. Filter Bar: Search by instructor name, Filter by Compensation Model (`Fixed`, `PercentageOfCollectedRevenue`, `PerSession`), Filter by Assignment Status (`Assigned`, `Unassigned`).
  4. Instructors Grid / Table: List of instructors with assigned courses and financial model badges.
  5. Modal: "Instructor Profile & Assignment Summary" dialog.

# DOMAIN DATA & MOCK ENTITIES:
- Sample Instructors Roster:
  - Instructor 1: ID: "INS-001", Name: "Eng. Omar Farooq", Email: "omar.dev@beacon.edu", Specialization: "Full-Stack Web & Cloud", Active Courses: 2 ("Full-Stack Web Dev - Cohort 4", "Node.js Backend Microservices"), Model: "Fixed (₪ 4,500 / cohort)", Accrued Dues: "₪ 4,500", Paid: "₪ 2,250", Outstanding: "₪ 2,250"
  - Instructor 2: ID: "INS-002", Name: "Leila Mansour", Email: "leila.design@beacon.edu", Specialization: "UI/UX & Product Design", Active Courses: 1 ("UI/UX Design Masterclass"), Model: "PercentageOfCollectedRevenue (25%)", Accrued Dues: "₪ 3,850", Paid: "₪ 0.00", Outstanding: "₪ 3,850"
  - Instructor 3: ID: "INS-003", Name: "Dr. Zaid Al-Hamad", Email: "zaid.data@beacon.edu", Specialization: "Python & Machine Learning", Active Courses: 1 ("Python for Data Analysis"), Model: "PerSession (₪ 150 / session)", Accrued Dues: "₪ 1,800" (12 completed sessions), Paid: "₪ 1,800", Outstanding: "₪ 0.00"

# DESIGN SYSTEM COMPONENTS:
- DataTable (Columns: Instructor Name, Email/Phone, Assigned Courses Pills, Compensation Model Badge, Accrued Dues, Outstanding Liability, Actions)
- CompensationModelBadge:
  - Fixed: Indigo pill
  - PercentageOfCollectedRevenue: Amber pill
  - PerSession: Blue pill
- MetricCard (Elevated white card with icon, number, and label)
- PrimaryButton, SecondaryOutlineButton

# CALLS TO ACTION (CTAs):
- Row Action: "View Assignment Details" -> Opens Instructor summary modal.
- Row Action 2: "Settle Payout" -> Direct link to `/manager/instructor-dues` with instructor pre-selected.
- Header CTA: "Go to Settlement Desk" -> Navigates to `/manager/instructor-dues`.

# FORM VALIDATION & BUSINESS RULES:
- Scoping: Instructors must belong to `CenterID: CTR-001`.
- Compensation Models are strictly constrained to:
  1. `Fixed`: Flat amount payable upon course activation or completion.
  2. `PercentageOfCollectedRevenue`: Percentage calculated strictly against *verified, collected payments* (not uncollected receivables).
  3. `PerSession`: Rate multiplied strictly by the count of *completed sessions with submitted attendance*.
- Read-only summary in this view; compensation model is configured during Course Setup (`SCR-M-03`).

# NAVIGATION & ROUTING RULES:
- Clicking instructor row opens their active course breakdown.
- Clicking course pill navigates directly to `/manager/courses/[id]`.

# COMPREHENSIVE STATES:
- Default State: Populated list of 14 instructors with active badges and financial metrics.
- Loading State: Skeleton loading blocks across the 3 metric cards and table rows.
- Empty State: "No instructors found. Staff members with the INSTRUCTOR role will appear here automatically."
- Error State: Red alert banner: "Could not retrieve instructor compensation summaries. [Retry]".

# RESPONSIVE BEHAVIOR:
- Mobile (<640px): Table converts to card format; each card displays instructor avatar, assigned courses, compensation model badge, and outstanding balance in large bold font.
- Desktop (1025px+): Comprehensive tabular view with scannable columns and right-aligned numeric data.

# ACCESSIBILITY (A11y) & WCAG 2.1 AA:
- Currency columns have `aria-label="New Israeli Shekels"`.
- Table columns sortable with keyboard (`Space`/`Enter`), with `aria-sort="ascending/descending"`.
````

---

### SCR-M-02: Courses Catalog & Lifecycle

````text
# SCREEN IDENTIFIER: SCR-M-02
# SCREEN TITLE: Courses Catalog & Lifecycle Management
# TARGET ROUTE: /manager/courses
# PRIMARY ACTOR: Center Manager (`MANAGER`)
# PURPOSE: Provide center managers with a master catalog of all course cohorts, filterable by lifecycle status (`Draft`, `Active`, `Completed`, `Archived`), and serve as the launchpad for creating and configuring new training programs.

# VISUAL & LAYOUT HIERARCHY:
- Layout: App Shell (Shared Top Header + Manager Sidebar with "Courses" active).
- Main Canvas:
  1. Page Header: Title "Courses & Cohorts", Subtitle "Manage curriculum offerings, cohort lifecycles, and instructor assignments", Right Action: "Create New Course" button (Primary).
  2. Lifecycle Filter Tabs: "All Courses (18)", "Active (12)", "Drafts (3)", "Completed (2)", "Archived (1)".
  3. Filter & Search Controls: Search by course title or code, Category filter, Instructor filter.
  4. Courses Grid / Table: Responsive course cards or table view (with view switcher toggle: Grid / Table).
  5. Pagination Controls: "Showing 1-12 of 18 courses", Previous / Next page buttons.

# DOMAIN DATA & MOCK ENTITIES:
- Course Entity Attributes: CourseID, Title, CourseCode, Category, Status, BasePrice, StartDate, EndDate, Capacity, EnrolledCount, AssignedInstructors.
- Sample Courses List:
  - Course 1: ID: "CRS-FSW-004", Code: "FSW-2026-C4", Title: "Full-Stack Web Development Bootcamp", Status: "Active", Dates: "Oct 15, 2026 - Jan 15, 2027", Capacity: 20, Enrolled: 20 (Full), BasePrice: "₪ 3,200", LeadInstructor: "Eng. Omar Farooq"
  - Course 2: ID: "CRS-UXD-002", Code: "UXD-2026-C2", Title: "UI/UX & Product Design Masterclass", Status: "Active", Dates: "Nov 01, 2026 - Jan 30, 2027", Capacity: 18, Enrolled: 12, BasePrice: "₪ 2,800", LeadInstructor: "Leila Mansour"
  - Course 3: ID: "CRS-DS-001", Code: "PYD-2026-C1", Title: "Python for Data Science & AI Foundations", Status: "Draft", Dates: "Dec 01, 2026 - Feb 28, 2027", Capacity: 15, Enrolled: 0, BasePrice: "₪ 3,500", LeadInstructor: "Unassigned"
  - Course 4: ID: "CRS-MKT-003", Code: "DMK-2026-C3", Title: "Digital Marketing & Performance Growth", Status: "Completed", Dates: "Jun 01, 2026 - Aug 30, 2026", Capacity: 25, Enrolled: 24, BasePrice: "₪ 2,000", LeadInstructor: "Kareem Naji"

# DESIGN SYSTEM COMPONENTS:
- ViewToggle (Grid vs Table view icons)
- StatusBadge (Draft: Slate, Active: Green, Completed: Blue, Archived: Red)
- CapacityProgressBar (e.g. 20/20 red if full, 12/18 green if available)
- PrimaryButton ("Create Course", Plus icon, Indigo-600)
- CourseCard (Thumbnail/Icon, Code, Title, Status Badge, Capacity Bar, Date range, Instructor avatar, Actions menu)

# CALLS TO ACTION (CTAs):
- Primary CTA: "Create New Course" -> Opens `/manager/courses` Create Course modal/stepper.
- Card CTA: "Manage Course" -> Navigates to Course Details `/manager/courses/[id]`.
- Row Action: "Archive Course" (Only for Draft or Completed courses; disabled for Active courses with enrolled students).

# FORM VALIDATION & BUSINESS RULES:
- Course Code must be unique within CenterID.
- Courses with status `Active` cannot be deleted or archived directly; must be marked `Completed` first.
- Capacity indicator shows full warning when `EnrolledCount >= Capacity`.

# NAVIGATION & ROUTING RULES:
- Clicking on a course title or card navigates to `/manager/courses/[id]`.
- Clicking "Create New Course" opens Course Creation Drawer on the current page.

# COMPREHENSIVE STATES:
- Default State: Grid of 12 active course cards with full capacity indicators and instructor badges.
- Loading State: 6 skeleton cards with animated shimmer placeholders.
- Empty State: "No courses found under this filter. [Create Course] or [Reset Filters]".
- Error State: Error banner: "Failed to load courses catalog. Please refresh your browser."

# RESPONSIVE BEHAVIOR:
- Mobile (<640px): 1-column card stack; filter tabs become a horizontal scrolling bar; search input takes full width.
- Tablet (641px-1024px): 2-column card grid.
- Desktop (1025px+): 3-column card grid or compact multi-column data table.

# ACCESSIBILITY (A11y) & WCAG 2.1 AA:
- Status badges use text and color (not color alone) to convey status.
- Progress bar includes `aria-valuenow="20"`, `aria-valuemin="0"`, `aria-valuemax="20"`, and `aria-label="Enrollment Capacity: 20 of 20 filled"`.
````

---

### SCR-M-03: Course Details, Sessions & Activation

````text
# SCREEN IDENTIFIER: SCR-M-03
# SCREEN TITLE: Course Details, Sessions & Activation Hub
# TARGET ROUTE: /manager/courses/[id]
# PRIMARY ACTOR: Center Manager (`MANAGER`)
# PURPOSE: Serve as the central operational management screen for an individual course cohort: editing course metadata, assigning multi-instructor teams with specific compensation models, scheduling lecture sessions, inspecting enrolled trainees, and enforcing the activation gate rule (requiring at least one assigned instructor).

# VISUAL & LAYOUT HIERARCHY:
- Layout: App Shell (Shared Top Header + Manager Sidebar).
- Breadcrumbs: "Courses > Full-Stack Web Development Bootcamp (FSW-2026-C4)"
- Top Course Banner:
  - Title, Course Code, Status Badge (`Draft` or `Active`), Base Price, Date Range, Capacity pill ("20 / 20 Enrolled").
  - Header Action Bar: "Activate Course" (Primary button, enabled only if >=1 instructor assigned), "Edit Details" (Outline), "Archive" (Danger Outline).
- Tabbed Navigation Bar:
  1. Overview & Settings (Active tab by default)
  2. Instructors & Compensation (Assign instructors, lead flag, contract terms)
  3. Lecture Sessions (Schedule dates, times, topics, assigned instructor)
  4. Enrolled Students (Roster, payment status, remaining balances)
  5. Materials & Tasks Summary (Read-only count of published items)
- Active Tab Body Container (White card, rounded-xl, p-6, border-slate-200).

# DOMAIN DATA & MOCK ENTITIES:
- Course Overview:
  - ID: "CRS-FSW-004", Title: "Full-Stack Web Development Bootcamp", Code: "FSW-2026-C4", Status: "Draft" (Ready for Activation)
  - Base Catalog Price: "₪ 3,200", Capacity: 20 Trainees, Total Sessions Planned: 24
  - Date Range: "Nov 01, 2026 to Jan 30, 2027", Schedule: "Mon / Wed / Sat, 18:00 - 21:00"
- Assigned Instructors:
  - Lead Instructor: Eng. Omar Farooq (Role: Lead, Model: Fixed, Fee: ₪ 4,500)
  - Assistant Instructor: Tamer Adel (Role: Assistant, Model: PerSession, Fee: ₪ 120 / session)
- Scheduled Sessions (First 3 of 24):
  - Session 1: Nov 01, 2026 | Topic: "Orientation & Modern Web Architecture" | Instructor: Eng. Omar Farooq
  - Session 2: Nov 03, 2026 | Topic: "Git Version Control & CI/CD Basics" | Instructor: Tamer Adel
  - Session 3: Nov 06, 2026 | Topic: "Advanced TypeScript & Design Patterns" | Instructor: Eng. Omar Farooq

# DESIGN SYSTEM COMPONENTS:
- BreadcrumbNav (Links to `/manager/courses` with chevron separators)
- TabGroup (Tabs: Overview, Instructors, Sessions, Enrollments, Materials)
- FormSection (Grouped inputs with title and descriptive helper text)
- InstructorAssignmentCard (Instructor name, Lead checkbox, Compensation model select dropdown, Fee input, Remove button)
- SessionScheduleTable (Session #, Date, Time, Topic, Assigned Instructor, Status)
- PrimaryButton ("Activate Course", CheckCircle icon, Indigo-600)
- ConfirmationModal (Modal: "Activate Course Cohort? This will enable public student enrollment.")

# CALLS TO ACTION (CTAs):
- Primary CTA: "Activate Course" -> Validates instructor count; sets status to `Active`; enables student enrollment.
- Tab CTA (Instructors): "Assign Instructor" -> Opens instructor assignment drawer.
- Tab CTA (Sessions): "Add Lecture Session" -> Opens session scheduler modal.
- Tab CTA (Overview): "Save Course Changes" -> Commits course edits to database.

# FORM VALIDATION & BUSINESS RULES:
- Hard Gate Rule: A course cannot be transitioned from `Draft` to `Active` unless at least ONE instructor is assigned. If count == 0, "Activate Course" button is disabled with tooltip: "Assign at least one instructor before activating".
- Date Validation: `StartDate` must precede `EndDate`.
- Capacity Validation: Minimum capacity is 1; capacity cannot be lowered below currently enrolled student count.
- Duplicate Instructor Check: The same instructor cannot be assigned twice to the same course.
- Compensation Rule: For `Fixed` model, fee must be > 0. For `Percentage` model, percentage must be between 1% and 100%. For `PerSession`, rate must be > 0.

# NAVIGATION & ROUTING RULES:
- "Back to Courses" returns to `/manager/courses`.
- Activating course updates status badge to `Active` and updates course status in-place.
- Clicking student name in Enrollments tab navigates to `/manager/students/[id]`.

# COMPREHENSIVE STATES:
- Default State (Draft): Top banner shows amber `Draft` badge; "Activate Course" button is enabled if instructors are assigned.
- Default State (Active): Top banner shows green `Active` badge; "Activate Course" button replaced with "Course Active" status pill.
- Loading State: Skeleton card headers, animated placeholder tabs and table lines.
- Validation Error: Red banner at top of form: "Activation Blocked: Course has no assigned instructors. Please assign a lead instructor in the Instructors tab."
- Success State: Green confirmation toast: "Course FSW-2026-C4 successfully activated. Student enrollments are now open."

# RESPONSIVE BEHAVIOR:
- Mobile (<640px): Tabs convert into a full-width select dropdown; session table converts to card list; action buttons stack vertically.
- Tablet (641px-1024px): Horizontal scrolling tab list; form inputs arrange in 2-column layout.
- Desktop (1025px+): Full horizontal tabs; 3-column overview grid; sticky header action bar.

# ACCESSIBILITY (A11y) & WCAG 2.1 AA:
- Tab navigation uses `role="tablist"`, `role="tab"`, `aria-selected="true/false"`, and `aria-controls="tabpanel-id"`.
- Keyboard arrow keys navigate between tabs.
- Activation button has descriptive `aria-describedby` pointing to validation helper text.
````


---

# SECTION 3: STUDENT INTAKE, ENROLLMENT & PAYMENT COLLECTION (SALES FLOW)

---

### SCR-S-01: Sales Operations Dashboard

````text
# SCREEN IDENTIFIER: SCR-S-01
# SCREEN TITLE: Sales Operations & Intake Dashboard
# TARGET ROUTE: /sales/dashboard
# PRIMARY ACTOR: Sales Specialist (`SALES`)
# PURPOSE: Provide sales representatives with an action-oriented intake and follow-up desk, focusing on new trainee inquiries, pending payment approval vouchers, upcoming course capacity thresholds, and overdue installment follow-up queues.

# VISUAL & LAYOUT HIERARCHY:
- Layout: App Shell (Shared Top Header + Sales-Filtered Sidebar).
- Top Bar: VELORA Logo, Center Badge "Beacon Training Center", Dashboard Link (active), Notifications (2 alerts), User Avatar "Sara Al-Khatib (Sales)".
- Sidebar Navigation: Dashboard (active), Students, Enrollments, Payments. (Note: Courses, Instructors, Instructor Dues, Staff, and Audit Log are strictly hidden per RBAC).
- Main Canvas:
  1. Welcome Banner: Greeting "Good morning, Sara", Subtitle "Sales Follow-up & Intake Command Center", Quick Action Bar (3 Buttons: "+ New Student Profile", "Enroll Student", "Record Payment").
  2. Intake Metric Cards (4 Cards): Today's Inquiries (8), Pending Payment Approvals (5 Vouchers / ₪ 4,200), Unpaid Active Enrollments (14 Trainees), Courses Nearing Full Capacity (3 Cohorts).
  3. Two-Column Operational Desk:
     - Left Column (55%): "Trainee Follow-up Queue" (Students with pending unpaid installments, follow-up date, phone shortcut, and payment recording CTA).
     - Right Column (45%): "Payment Voucher Approvals Status" (Status of recent offline payments submitted by Sales: Pending Approval, Approved, or Rejected with manager rationale).

# DOMAIN DATA & MOCK ENTITIES:
- Center Context: CenterID: "CTR-001", CenterName: "Beacon Training Center"
- Sample Follow-up Queue Items:
  - Lead 1: Student: "Yousef Mahmoud", Course: "Full-Stack Web Dev - Cohort 4", Remaining Balance: "₪ 1,600", Status: "PartiallyPaid", Due Date: "Overdue by 3 days", Phone: "+970-599-234567"
  - Lead 2: Student: "Hala Al-Qadi", Course: "UI/UX Design Masterclass", Remaining Balance: "₪ 2,800", Status: "Unpaid", Due Date: "Due Tomorrow", Phone: "+970-598-765432"
- Sample Vouchers in Review:
  - Voucher 1: REC-2026-089 | Student: "Rami Shaker" | Amount: "₪ 1,500" (Cash) | Status: "Pending Approval"
  - Voucher 2: REC-2026-088 | Student: "Noor Salem" | Amount: "₪ 2,800" (Bank Transfer) | Status: "Pending Approval (Excess: ₪ 200 Linked Refund)"
  - Voucher 3: REC-2026-085 | Student: "Fadi Odeh" | Amount: "₪ 1,000" (POS Card) | Status: "Rejected" (Reason: "Slip illegible, re-upload receipt")

# DESIGN SYSTEM COMPONENTS:
- QuickActionBar (Indigo primary button, 2 white outline buttons with contrasting borders)
- MetricCard (Elevated white card with icon container, metric value, status tag)
- FollowUpRowCard (Student name, course pill, balance amount in red, direct "Call" and "Record Payment" buttons)
- VoucherStatusBadge (Pending Approval: Amber, Approved: Green, Rejected: Red)

# CALLS TO ACTION (CTAs):
- Quick Action 1: "+ Register Student" -> Opens New Student Intake Drawer on `/sales/students`.
- Quick Action 2: "Enroll Student" -> Opens Enrollment Modal on `/sales/enrollments`.
- Row CTA: "Record Payment" -> Opens Payment Modal pre-filled with student and enrollment data.
- Header Notification Bell -> Opens notification flyout.

# FORM VALIDATION & BUSINESS RULES:
- Scoping: Sales sees only student profiles and enrollments within `CenterID: CTR-001`.
- RBAC Restriction: Sales CANNOT see course creation, staff lists, financial profit/loss, instructor dues, or private academic feedback.
- Payment Status: Sales cannot approve payments. Submissions enter `Pending Approval` state awaiting Center Manager review.

# NAVIGATION & ROUTING RULES:
- Clicking a student name navigates to `/sales/students?id=[studentId]`.
- Clicking "View All Payments" navigates to `/sales/payments`.

# COMPREHENSIVE STATES:
- Default State: Populated metrics, 4 follow-up items, 3 recent payment vouchers.
- Loading State: Animated skeleton shimmer across all cards and queue items.
- Empty State (Zero Follow-ups): "All student installments and follow-ups are up to date! Great job."
- Error State: Alert banner: "Could not synchronize sales queue with center database. [Retry]".

# RESPONSIVE BEHAVIOR:
- Mobile (<640px): Sidebar moves to off-canvas drawer (hamburger menu in header); 4 metric cards scroll horizontally; follow-up queue cards stack with full-width action buttons.
- Tablet (641px-1024px): 2-column metrics; follow-up desk stacks above payment status widget.
- Desktop (1025px+): 4-column metric grid; 55/45 split operational layout.

# ACCESSIBILITY (A11y) & WCAG 2.1 AA:
- Follow-up phone links use `<a href="tel:..." aria-label="Call Yousef Mahmoud at 0599234567">`.
- High contrast status badges exceed 4.5:1 ratio against background.
````

---

### SCR-S-02: Student Intake & Directory (Sales)

````text
# SCREEN IDENTIFIER: SCR-S-02
# SCREEN TITLE: Student Intake & Directory (Sales Desk)
# TARGET ROUTE: /sales/students
# PRIMARY ACTOR: Sales Specialist (`SALES`)
# PURPOSE: Enable front-line sales officers to rapidly search existing student records by name, email, or phone number, register new student profiles into the center, and immediately trigger course enrollment.

# VISUAL & LAYOUT HIERARCHY:
- Layout: App Shell (Shared Top Header + Sales Sidebar with "Students" active).
- Main Canvas:
  1. Page Header: Title "Student Directory & Intake", Subtitle "Search center student records or register new trainees", Right Action: "+ Register New Student" button (Primary Indigo).
  2. Search & Fast Filter Bar: Large Search Input (with magnifying glass icon and instant debounced lookup: "Search by student name, mobile phone, or email..."), Active Course Filter dropdown.
  3. Student Roster Table: Clean, dense list of registered students with enrollment status pills and balance tags.
  4. Slide-Over Drawer / Modal: "Register New Student Profile" form.

# DOMAIN DATA & MOCK ENTITIES:
- Center Scope: CenterID: "CTR-001"
- Sample Student Directory Records:
  - Student 1: ID: "STU-001", FullName: "Tariq Yousef Al-Sayed", Email: "tariq.student@gmail.com", Phone: "+970-599-887766", CivilID: "402918273", EnrolledCourses: 1 ("Full-Stack Web Dev"), TotalBalance: "₪ 1,200", Status: "Active"
  - Student 2: ID: "STU-002", FullName: "Dina Majed Al-Rantisi", Email: "dina.design@outlook.com", Phone: "+970-598-112233", CivilID: "401827364", EnrolledCourses: 1 ("UI/UX Design Masterclass"), TotalBalance: "₪ 0.00", Status: "Active"
  - Student 3: ID: "STU-003", FullName: "Khaled Waleed Barghouthi", Email: "khaled.wb@yahoo.com", Phone: "+970-597-445566", CivilID: "405617283", EnrolledCourses: 0, TotalBalance: "₪ 0.00", Status: "Prospect / Intake"

# DESIGN SYSTEM COMPONENTS:
- SearchBar (h-12, rounded-xl, border-slate-300, leading icon, clear button)
- DataTable (Columns: Student Avatar & Name, Contact Info, Civil ID, Active Enrollments, Total Outstanding, Actions)
- SlideOverDrawer (Width: 500px, Title: "Register New Student Profile", Subtitle: "Enter trainee personal and contact details")
- InputField, PhoneInput (with country code prefix +970), Textarea (Internal Sales Notes)
- PrimaryButton, SecondaryOutlineButton

# CALLS TO ACTION (CTAs):
- Primary CTA: "+ Register New Student" -> Opens student intake slide-over drawer.
- Row Action 1: "Enroll in Course" -> Opens `/sales/enrollments` modal with student pre-selected.
- Row Action 2: "View Profile" -> Opens student details card with enrollment history.
- Drawer CTA: "Save & Proceed to Enrollment" -> Persists profile and immediately opens Course Enrollment dialog.

# FORM VALIDATION & BUSINESS RULES:
- Full Name: Mandatory, minimum 3 characters.
- Mobile Phone: Mandatory, validated for valid format; must be unique within CenterID.
- Email: Mandatory, valid email syntax; must be unique within CenterID.
- Duplicate Detection: If phone or email already exists in center database, form blocks submission with error: "Student profile already exists with this phone/email. [Click here to view profile]".
- Civil ID: Optional for intake, but if provided, must be numeric (9 digits).

# NAVIGATION & ROUTING RULES:
- Saving new profile refreshes table and offers: "Enroll [Name] in a Course now? [Yes, Enroll] [Done]".
- Clicking "Enroll in Course" links directly to `/sales/enrollments?studentId=STU-001`.

# COMPREHENSIVE STATES:
- Default State: Table displays first 20 students, alphabetized, with search bar empty.
- Searching State: Search input shows spinner; results filter dynamically in <200ms.
- Empty State (No Search Results): "No student found matching '[query]'. Would you like to register them as a new student? [+ Register Student]".
- Form Error State: Inline red field borders with validation text: "This mobile phone is already registered to student Tariq Al-Sayed."
- Success State: Green toast notification: "Student profile for Dina Majed successfully created."

# RESPONSIVE BEHAVIOR:
- Mobile (<640px): Table transforms into responsive student contact cards with direct "Call" and "Enroll" action buttons. Drawer occupies 100% of viewport width.
- Desktop (1025px+): Wide table layout with sticky headers and right-aligned action buttons.

# ACCESSIBILITY (A11y) & WCAG 2.1 AA:
- Search input has `role="searchbox"` and explicit `aria-label="Search student directory"`.
- Drawer has `aria-modal="true"`, focus is trapped inside drawer while open.
````

---

### SCR-S-03: Course Enrollment, Discounts & Waitlist (Sales)

````text
# SCREEN IDENTIFIER: SCR-S-03
# SCREEN TITLE: Course Enrollment, Discounts & Waitlist Desk
# TARGET ROUTE: /sales/enrollments
# PRIMARY ACTOR: Sales Specialist (`SALES`)
# PURPOSE: Allow sales specialists to enroll registered students into active course cohorts, enforce capacity limits with automated FIFO waitlist placement, validate and apply authorized discounts, and initialize the student's financial ledger.

# VISUAL & LAYOUT HIERARCHY:
- Layout: App Shell (Shared Top Header + Sales Sidebar with "Enrollments" active).
- Main Canvas:
  1. Page Header: Title "Enrollments & Course Allocations", Subtitle "Enroll students into active cohorts or manage waitlists", Right Action: "+ New Enrollment" button (Primary).
  2. Tab Bar: "Active Enrollments (186)", "Course Waitlists (14 Trainees)".
  3. Filter Bar: Filter by Course, Filter by Payment Status (`Unpaid`, `PartiallyPaid`, `FullyPaid`), Search by Student Name.
  4. Active Enrollments Table: Student Name, Course Cohort, Base Price, Discount, Final Payable, Total Paid, Remaining Balance, Payment Status Badge.
  5. Modal Window: "Enroll Student in Course" (Full 4-step intake workflow).

# DOMAIN DATA & MOCK ENTITIES:
- Course Cohorts Available for Enrollment:
  - Cohort A: "Full-Stack Web Dev - Cohort 4" | Status: "Active" | Capacity: 20 | Enrolled: 20 (FULL - Waitlist Only) | Base Price: "₪ 3,200"
  - Cohort B: "UI/UX Design Masterclass" | Status: "Active" | Capacity: 18 | Enrolled: 12 (6 Seats Available) | Base Price: "₪ 2,800"
- Enrollment Modal Data Flow:
  - Selected Student: "Khaled Waleed Barghouthi" (ID: "STU-003")
  - Selected Course: "UI/UX Design Masterclass"
  - Base Catalog Price: "₪ 2,800"
  - Discount Type: "Fixed Amount" (Options: None, Fixed Amount, Percentage)
  - Discount Value: "₪ 300" (Reason: "Early Bird Registration")
  - Calculated Final Payable: "₪ 2,500" (₪ 2,800 - ₪ 300)
  - Initial Ledger State: TotalPaid = ₪ 0.00, RemainingBalance = ₪ 2,500, PaymentStatus = "Unpaid"

# DESIGN SYSTEM COMPONENTS:
- ModalDialog (Width: 640px, Title: "Enroll Student in Course", rounded-2xl, shadow-2xl)
- ComboboxSelect (Searchable student dropdown and searchable active course dropdown)
- PriceBreakdownCard (Summary box: Base Price, minus Discount, equals Final Payable in bold green)
- AlertBanner (Warning Amber: "Course at Full Capacity: Direct enrollment is closed. You can add this student to the FIFO Waitlist.")
- StatusBadge (Unpaid: Slate, PartiallyPaid: Amber, FullyPaid: Green, Waiting: Blue)
- PrimaryButton ("Confirm Enrollment"), SecondaryButton ("Cancel")

# CALLS TO ACTION (CTAs):
- Primary CTA (Modal): "Confirm & Initialize Enrollment" -> Validates rules, writes enrollment, initializes financial ledger, prompts to record initial payment.
- Waitlist CTA: "Add to Waitlist (Position #5)" -> Enrolls student on FIFO waitlist with timestamp, status='Waiting', 0 fees.
- Table Row CTA: "Record Payment" -> Directly opens payment modal (`SCR-S-04`).

# FORM VALIDATION & BUSINESS RULES:
- Course Status Gate: Enrollments are strictly permitted ONLY for courses with status `Active`. Draft, Completed, or Archived courses are disabled.
- Duplicate Enrollment Prevention: If the student already has an `Active` enrollment in the selected course, the submission is rejected immediately with HTTP 409 Conflict: "Student is already actively enrolled in this cohort."
- Capacity Hard-Lock & Waitlist: If `EnrolledCount >= Capacity`:
  - Direct enrollment button is disabled.
  - A prominent prompt appears: "Course is full (20/20). Would you like to add student to the Course Waitlist?"
  - Waitlist placement records student with status `Waiting`, FIFO timestamp, and zero fees charged.
- Discount Rule: `DiscountAmount <= BaseCatalogPrice`. If discount > base price, block with error: "Discount amount cannot exceed course base price (₪ 2,800)."

# NAVIGATION & ROUTING RULES:
- On successful enrollment: Dialog offers: "Enrollment confirmed! Record an initial payment now? [Record Payment] [Done]".
  - If "Record Payment" clicked -> Opens Payment Modal (`SCR-S-04`) with enrollment pre-selected.
  - If "Done" clicked -> Closes modal and updates table.

# COMPREHENSIVE STATES:
- Default State: Enrollments list showing latest enrollments with accurate balance calculations.
- Modal Open (Available Course): Shows seats remaining badge "6 seats left", price breakdown active.
- Modal Open (Full Course): Capacity bar turns red, direct enrollment disabled, "Add to Waitlist" button enabled.
- Error State (Duplicate): Banner inside modal: "Conflict: Khaled Barghouthi is already enrolled in this course."
- Success State: Toast: "Khaled Barghouthi successfully enrolled in UI/UX Design Masterclass. Ledger balance: ₪ 2,500."

# RESPONSIVE BEHAVIOR:
- Mobile (<640px): Modal displays full-screen with sticky bottom buttons; price breakdown card stays fixed above actions; table becomes card list with financial summary chips.
- Desktop (1025px+): 640px centered modal with 2-column input fields and clear financial receipt preview.

# ACCESSIBILITY (A11y) & WCAG 2.1 AA:
- Real-time calculation has `aria-live="polite"` announcing updated final payable when discount changes.
- Form inputs have associated `<label>` tags with required asterisks marked `aria-hidden="true"`.
````

---

### SCR-S-04: Offline Payment Entry & Overpayment Check (Sales)

````text
# SCREEN IDENTIFIER: SCR-S-04
# SCREEN TITLE: Record Offline Payment & Excess Detection
# TARGET ROUTE: /sales/payments
# PRIMARY ACTOR: Sales Specialist (`SALES`)
# PURPOSE: Enable sales staff to record manual, offline student tuition payments (Cash, Bank Transfer, POS Card, Cheque), enforce positive amount validation, automatically detect overpayments, auto-create linked refund requests for excess amounts, and submit payment vouchers to the Manager Approval Queue.

# VISUAL & LAYOUT HIERARCHY:
- Layout: App Shell (Shared Top Header + Sales Sidebar with "Payments" active).
- Main Canvas:
  1. Page Header: Title "Student Payment Vouchers", Subtitle "Record offline tuition payments and track voucher approval status", Right Action: "+ Record New Payment" button (Primary Indigo).
  2. Summary Metric Cards (3 Cards): Vouchers Submitted Today (4), Total Pending Manager Approval (₪ 4,200), Total Approved This Week (₪ 18,500).
  3. Filter Bar: Filter by Payment Method, Filter by Status (`Pending Approval`, `Completed`, `Rejected`), Search by Student Name or Receipt #.
  4. Payments Table: Receipt #, Date, Student Name, Course, Amount Paid, Method, Status Badge, Manager Notes, Receipt Slip.
  5. Centered Modal Window: "Record Offline Payment Voucher".

# DOMAIN DATA & MOCK ENTITIES:
- Center Context: CenterID: "CTR-001", Currency: "NIS" (₪)
- Record Payment Form Workflow:
  - Enrolled Student Lookup: "Tariq Yousef Al-Sayed" (ID: "STU-001")
  - Enrolled Course: "Full-Stack Web Dev - Cohort 4"
  - Current Ledger Context: Base: "₪ 3,200", Discount: "₪ 0.00", Total Payable: "₪ 3,200", Already Paid: "₪ 2,000", Current Remaining Balance: "₪ 1,200"
  - Payment Input Fields:
    - Amount Tendered: User inputs "₪ 1,500"
    - Payment Date: "2026-10-24"
    - Payment Method: Dropdown selection `Cash` (Options: `Cash`, `Bank Transfer`, `POS Card`, `Cheque`)
    - Reference / Slip #: "CSH-2026-1024-01"
    - Upload Receipt Slip: Optional attachment (image/PDF)
    - Sales Internal Notes: "Student paid remaining ₪ 1,200 plus ₪ 300 deposit for upcoming mobile workshop"
  - Overpayment Detection System:
    - Calculation: `ExcessAmount = Amount Tendered (1500) - Remaining Balance (1200) = ₪ 300.00`
    - System Alert Triggered: "Overpayment Detected! Tendered amount exceeds student's remaining balance by ₪ 300.00. An automatic linked Refund Request will be created upon manager approval."

# DESIGN SYSTEM COMPONENTS:
- ModalDialog (Width: 600px, Title: "Record Offline Tuition Payment", rounded-2xl, shadow-2xl)
- CurrencyInputField (Numeric input with bold `₪` prefix, large font, right aligned)
- SelectDropdown (Payment Methods with respective icons: Cash, CreditCard, Landmark, FileText)
- OverpaymentWarningCard (Amber-50 bg, Amber-700 text, Amber-300 border, AlertCircle icon, explains refund creation)
- StatusBadge (Pending Approval: Amber, Completed: Green, Rejected: Red)
- PrimaryButton ("Submit for Manager Approval"), SecondaryButton ("Cancel")

# CALLS TO ACTION (CTAs):
- Primary CTA (Modal): "Submit Voucher for Approval" -> Validates amount > 0, checks excess, submits voucher to Manager Approval Queue (`SCR-M-07`).
- Table Row CTA: "View Receipt / Voucher" -> Displays printable payment receipt preview.
- Table Row CTA (Rejected): "View Rejection Reason" -> Displays manager's audit rationale.

# FORM VALIDATION & BUSINESS RULES:
- Amount Validation: Mandatory, must be numeric, strictly greater than 0.00 (`Amount > 0.00`). If <= 0, block with error: "Payment amount must be greater than 0.00."
- Payment Method: Mandatory, restricted strictly to `Cash`, `Bank Transfer`, `POS Card`, or `Cheque`.
- Receipt Reference / Slip Number: Mandatory for tracking offline audit proof.
- Overpayment Logic (Non-Negotiable Business Rule):
  - If `Amount Tendered > Remaining Balance`:
    - System flags transaction as Overpayment.
    - Excess amount is calculated: `Excess = Amount - RemainingBalance`.
    - Auto-generates a linked `Refund Request` record with status `Pending Refund`.
    - The full voucher is queued for manager review. (No balance is updated until approved by Center Manager).
- Immutability: Payments cannot be deleted by Sales. If rejected by Manager, status remains `Rejected` in historical ledger for audit compliance.

# NAVIGATION & ROUTING RULES:
- On submission, modal closes, table refreshes showing the new voucher at the top with badge `Pending Approval`.
- Sales receives a confirmation banner: "Voucher #REC-2026-092 submitted. Awaiting manager approval."

# COMPREHENSIVE STATES:
- Default Modal State: Fields empty; ledger card displays student's current balance in blue.
- Overpayment Warning State: As user types "1500", the warning card immediately slides down showing: "₪ 300.00 excess detected. A refund voucher will be prepared for the student."
- Form Error State: Red border on amount: "Payment amount cannot be zero or negative."
- Success State: Toast: "Payment voucher for ₪ 1,500 submitted to manager approval queue."

# RESPONSIVE BEHAVIOR:
- Mobile (<640px): Modal is full-screen; payment method select uses large touch buttons (radio cards); table converts to stacked voucher cards.
- Desktop (1025px+): 600px centered modal; side-by-side ledger context and entry fields.

# ACCESSIBILITY (A11y) & WCAG 2.1 AA:
- Dynamic calculation of excess amount announced to screen readers via `aria-live="polite"`.
- Warning alert has `role="alert"` and clear semantic heading.
````

---

# SECTION 4: CENTER OVERSIGHT, FINANCIAL RECONCILIATION & PAYOUTS (MANAGER FINANCIAL FLOW)

---

### SCR-M-05: Center Students Master Directory

````text
# SCREEN IDENTIFIER: SCR-M-05
# SCREEN TITLE: Center Students Master Directory
# TARGET ROUTE: /manager/students
# PRIMARY ACTOR: Center Manager (`MANAGER`)
# PURPOSE: Provide center managers with a centralized master directory of all registered students, their complete enrollment histories, cumulative fees invoiced, total verified payments, outstanding balances, and administrative notes.

# VISUAL & LAYOUT HIERARCHY:
- Layout: App Shell (Shared Top Header + Manager Sidebar with "Students" active).
- Main Canvas:
  1. Page Header: Title "Students Directory", Subtitle "Master center registry, enrollment histories, and financial standing", Right Actions: "Export Student Roster" (Outline), "Add Student" (Primary).
  2. Metric Summary Bar (4 Cards): Total Registered Trainees (240), Actively Enrolled (186), Trainees with Overdue Balance (28), Total Center Receivables (₪ 46,300).
  3. Filter & Search Controls: Multi-field Search (Name, Email, Phone, Civil ID), Course Filter dropdown, Financial Standing Filter (`All`, `Fully Paid`, `Has Balance`, `No Enrollments`).
  4. Master Students Table: Comprehensive tabular view with sorting on all columns.
  5. Student Detailed Slide-Over Drawer: Shows full profile, active & past enrollments, payment receipt history, and academic task submission rate.

# DOMAIN DATA & MOCK ENTITIES:
- Sample Student Directory Records:
  - Trainee 1: ID: "STU-001", Name: "Tariq Yousef Al-Sayed", CivilID: "402918273", Phone: "+970-599-887766", ActiveCourses: 1 ("Full-Stack Web Dev"), Invoiced: "₪ 3,200", Paid: "₪ 2,000", Outstanding: "₪ 1,200" (Partially Paid), AttendanceRate: "92%"
  - Trainee 2: ID: "STU-002", Name: "Dina Majed Al-Rantisi", CivilID: "401827364", Phone: "+970-598-112233", ActiveCourses: 1 ("UI/UX Design"), Invoiced: "₪ 2,800", Paid: "₪ 2,800", Outstanding: "₪ 0.00" (Fully Paid), AttendanceRate: "100%"
  - Trainee 3: ID: "STU-004", Name: "Rami Shaker Al-Khatib", CivilID: "403928172", Phone: "+970-599-334455", ActiveCourses: 1 ("Python Data Science"), Invoiced: "₪ 3,500", Paid: "₪ 1,500", Outstanding: "₪ 2,000" (Overdue 14 days), AttendanceRate: "85%"

# DESIGN SYSTEM COMPONENTS:
- DataTable (Columns: Student Info, Civil ID, Contact, Active Cohorts, Invoiced, Paid, Outstanding Balance, Payment Status Badge, Actions)
- MetricCard (White elevated card with metric number and subtext)
- StatusBadge (Fully Paid: Green, Partially Paid: Amber, Unpaid: Red)
- SlideOverDrawer (Width: 540px, Tabs: Overview, Financial Ledger, Attendance & Academic)
- PrimaryButton, SecondaryOutlineButton

# CALLS TO ACTION (CTAs):
- Row Action: "View Student File" -> Opens comprehensive slide-over drawer with student details.
- Drawer CTA: "View Official Ledger" -> Navigates to student payments view.
- Header CTA: "Add Student" -> Opens student intake drawer.

# FORM VALIDATION & BUSINESS RULES:
- Scoping: Strictly isolated to current CenterID (`CTR-001`).
- Balance Integrity: `Outstanding Balance = Invoiced - Verified Paid`.
- Overdue Status: Automatically tagged as "Overdue" if remaining balance > 0 and enrollment date exceeds center payment terms (e.g. >14 days after cohort start).

# NAVIGATION & ROUTING RULES:
- Clicking student name opens student profile slide-over drawer.
- Clicking an enrolled course badge inside the drawer links to `/manager/courses/[id]`.

# COMPREHENSIVE STATES:
- Default State: Populated directory showing 20 students per page, sorted by most recently enrolled.
- Filtered State: Table shows subset with active filter badge "Outstanding Balance > 0 (28 records)".
- Empty State: "No student records match your query. [Clear Filters]".
- Loading State: Skeleton shimmer over table rows and metric cards.

# RESPONSIVE BEHAVIOR:
- Mobile (<640px): Table transforms into card list; financial metrics (Invoiced / Paid / Due) display in a compact horizontal strip on each card.
- Desktop (1025px+): Expanded multi-column table with full sorting and action menus.

# ACCESSIBILITY (A11y) & WCAG 2.1 AA:
- Currency values formatted with `<span>` containing `aria-label="New Israeli Shekels"`.
- Action buttons have descriptive accessible names (e.g., `aria-label="View profile for Tariq Yousef Al-Sayed"`).
````

---

### SCR-M-06: Center Enrollments & Waitlist Management

````text
# SCREEN IDENTIFIER: SCR-M-06
# SCREEN TITLE: Center Enrollments & Waitlist Management
# TARGET ROUTE: /manager/enrollments
# PRIMARY ACTOR: Center Manager (`MANAGER`)
# PURPOSE: Provide center management with executive control over all course enrollments, waitlist queues, authorized discount exceptions, and cohort capacity distributions across the center.

# VISUAL & LAYOUT HIERARCHY:
- Layout: App Shell (Shared Top Header + Manager Sidebar with "Enrollments" active).
- Main Canvas:
  1. Page Header: Title "Enrollment & Capacity Management", Subtitle "Monitor cohort capacities, waitlists, and approve discount allocations", Right Actions: "Enroll Student" (Primary).
  2. Tab Group: "Active Enrollments (186)", "Waitlist Queues (14)", "Cancelled / Withdrawn (3)".
  3. Cohort Capacity Overview Cards: Quick visual indicators of active cohorts approaching full capacity.
  4. Search & Filter Bar: Filter by Course Cohort, Filter by Discount Applied (`All`, `Standard Rate`, `Discounted`), Filter by Balance.
  5. Enrollments Table: Detailed view of enrollments with discount amounts, net payable, and actions.
  6. Waitlist Management Modal: "Promote Trainee from Waitlist" dialog.

# DOMAIN DATA & MOCK ENTITIES:
- Sample Enrollments:
  - Enrollment 1: ID: "ENR-2026-001", Student: "Tariq Yousef Al-Sayed", Course: "Full-Stack Web Dev (FSW-2026-C4)", BasePrice: "₪ 3,200", Discount: "₪ 0.00", FinalPayable: "₪ 3,200", TotalPaid: "₪ 2,000", Balance: "₪ 1,200", Status: "PartiallyPaid"
  - Enrollment 2: ID: "ENR-2026-002", Student: "Khaled Waleed Barghouthi", Course: "UI/UX Design (UXD-2026-C2)", BasePrice: "₪ 2,800", Discount: "₪ 300" (Early Bird), FinalPayable: "₪ 2,500", TotalPaid: "₪ 0.00", Balance: "₪ 2,500", Status: "Unpaid"
- Sample Waitlist Records (FIFO Priority):
  - Waitlist 1: Position #1 | Course: "Full-Stack Web Dev - Cohort 4" | Student: "Nasser Qabaha" | Added: "2026-10-18 14:20" | Status: "Waiting"
  - Waitlist 2: Position #2 | Course: "Full-Stack Web Dev - Cohort 4" | Student: "Amal Shaheen" | Added: "2026-10-19 09:15" | Status: "Waiting"

# DESIGN SYSTEM COMPONENTS:
- TabGroup (Underline style, count badges)
- DataTable (Columns: Student, Cohort, Base Price, Discount, Final Payable, Total Paid, Balance, Status, Actions)
- WaitlistQueueCard (FIFO position badge #1, trainee name, timestamp, "Promote to Active" CTA)
- ModalDialog ("Promote Student to Active Enrollment")
- StatusBadge (Active: Green, Waiting: Blue, Cancelled: Slate)

# CALLS TO ACTION (CTAs):
- Tab CTA (Waitlist): "Promote to Active Enrollment" -> Moves student from waitlist to active enrollment (verifying that capacity is available or expanded).
- Row Action: "Adjust Discount / Balance" -> Opens discount authorization modal (Manager only).
- Row Action: "Cancel Enrollment" -> Confirmation modal with cancellation audit notes.

# FORM VALIDATION & BUSINESS RULES:
- Waitlist FIFO Order: Students must be promoted strictly according to their waitlist timestamp (First In, First Out).
- Promotion Gate: A waitlisted student cannot be promoted to `Active` unless course capacity is expanded or an existing student withdraws (`EnrolledCount < Capacity`).
- Discount Cap: Manager can authorize custom discounts, but `Discount <= BasePrice` is strictly enforced.

# NAVIGATION & ROUTING RULES:
- Clicking student name navigates to student details in `/manager/students`.
- Clicking course name navigates to `/manager/courses/[id]`.

# COMPREHENSIVE STATES:
- Default State: Table populated with 186 active enrollments.
- Waitlist Tab State: Sorted list of waitlisted students with prominent FIFO queue badges.
- Empty Waitlist State: "No students currently on waitlist for any cohort."
- Error State: Error toast: "Cannot promote waitlisted student: Cohort is currently at maximum capacity (20/20)."

# RESPONSIVE BEHAVIOR:
- Mobile (<640px): Enrollments display as summary cards with colored financial progress bars; promotion buttons are full-width.
- Desktop (1025px+): Full table with direct sorting on balance and discount columns.

# ACCESSIBILITY (A11y) & WCAG 2.1 AA:
- Waitlist position uses semantic `<ol>` list with numbered badges.
- Modals include keyboard focus trap and announce promotion results via `aria-live`.
````

---

### SCR-M-07: Payment Approvals & Excess Refund Queue

````text
# SCREEN IDENTIFIER: SCR-M-07
# SCREEN TITLE: Administrative Payment Approvals & Excess Refund Queue
# TARGET ROUTE: /manager/payments
# PRIMARY ACTOR: Center Manager (`MANAGER`)
# PURPOSE: Serve as the critical financial gatekeeper where the Center Manager inspects offline payment vouchers submitted by Sales, approves or rejects vouchers with mandatory audit rationale, triggers atomic ACID ledger updates, and manages the execution of overpayment refund payouts.

# VISUAL & LAYOUT HIERARCHY:
- Layout: App Shell (Shared Top Header + Manager Sidebar with "Payments" active).
- Main Canvas:
  1. Page Header: Title "Financial Approvals & Cash Receipts", Subtitle "Inspect and approve offline payment vouchers and manage student excess refunds", Right Actions: "Payment Ledger Summary" (Outline).
  2. Tabbed Working Desk (3 Tabs):
     - Tab 1: "Pending Approval Queue (5)" [Prominent Amber Badge]
     - Tab 2: "Excess Refunds Queue (2)" [Prominent Red Badge]
     - Tab 3: "Approved Receipts History (142)"
  3. Action Queue Table: Full inspection view of submitted payment vouchers.
  4. Inspection & Decision Modal: "Inspect Payment Voucher & Take Action".
  5. Refund Execution Modal: "Record Refund Payout to Student".

# DOMAIN DATA & MOCK ENTITIES:
- Tab 1: Vouchers Awaiting Approval:
  - Voucher 1: ID: "PMT-2026-091", Receipt#: "REC-2026-089", Student: "Rami Shaker Al-Khatib", Course: "Python Data Science", Amount: "₪ 1,500.00", Method: "Cash", SubmittedBy: "Sara Al-Khatib (Sales)", SubmittedAt: "2026-10-24 10:15", RemainingBalanceBefore: "₪ 2,000.00", ExcessAmount: "₪ 0.00"
  - Voucher 2: ID: "PMT-2026-092", Receipt#: "REC-2026-090", Student: "Noor Salem", Course: "Full-Stack Web Dev", Amount: "₪ 1,400.00", Method: "Bank Transfer", Slip#: "TRX-BNK-99281", SubmittedBy: "Sara Al-Khatib (Sales)", SubmittedAt: "2026-10-24 11:30", RemainingBalanceBefore: "₪ 1,200.00", ExcessAmount: "₪ 200.00" (ALERT: Overpayment)
- Tab 2: Excess Refunds Queue:
  - Refund 1: ID: "REF-2026-004", LinkedPayment: "PMT-2026-092", Student: "Noor Salem", ExcessAmount: "₪ 200.00", Status: "Pending Refund", PayoutMethodsAllowed: "Cash / Bank Transfer", CreatedAt: "2026-10-24 11:30"
- Rejection Rationale Options: "Receipt slip illegible / missing", "Amount does not match bank deposit statement", "Incorrect course or student selected", "Duplicate voucher submitted", "Other (Custom rationale required)".

# DESIGN SYSTEM COMPONENTS:
- TabGroup (Tabs with badge counters: Pending [Amber], Refunds [Red], History [Slate])
- InspectionModal (Width: 680px, Two-column: Left = Voucher & Slip metadata, Right = Student Ledger context)
- OverpaymentWarningBox (Red-50 background, Red-700 text: "Overpayment Detected: Student's balance is ₪ 1,200. Tendered amount is ₪ 1,400. Approving this voucher will post ₪ 1,200 to course tuition and queue ₪ 200 for immediate refund.")
- PrimaryButton ("Approve Voucher", Check icon, Green-600)
- DangerOutlineButton ("Reject Voucher", X icon, Red-600)
- TextareaField ("Manager Rejection Rationale", required on rejection)

# CALLS TO ACTION (CTAs):
- Primary CTA (Modal): "Approve Payment Voucher" -> Executes atomic database transaction:
  1. Updates payment status to `Completed`.
  2. Increments enrollment `TotalPaid` by net course amount (`Amount - Excess`).
  3. Recalculates `RemainingBalance`.
  4. If `Excess > 0`, activates linked `Refund Request` in `Pending Refund`.
  5. Writes immutable `AuditLog` entry.
- Destructive CTA (Modal): "Reject Voucher" -> Requires mandatory rejection rationale, sets status to `Rejected`, balance remains unchanged, logs audit.
- Refund Execution CTA: "Confirm Refund Payout" -> Records refund payment method, receipt ref, sets refund status to `Refunded`.

# FORM VALIDATION & BUSINESS RULES:
- ACID Transaction: Approval must execute as a single atomic database transaction. If balance recalculation or audit logging fails, the transaction rolls back completely (zero partial state).
- Mandatory Rejection Rationale: The manager CANNOT reject a voucher without providing a clear written explanation. Reject button is disabled until rationale is entered (minimum 10 characters).
- Overpayment Auto-Split: When approving an overpayment:
  - Course enrollment balance can NEVER drop below 0.00.
  - Net amount applied to tuition = `RemainingBalance`.
  - Excess amount is strictly queued for refund payout. Excess funds are NEVER absorbed into center general revenue without an audit trail.

# NAVIGATION & ROUTING RULES:
- Actioning a voucher removes it from "Pending Approval" tab and moves it to "History" or "Refunds" tab.
- Success toast displays: "Voucher REC-2026-090 approved. ₪ 1,200 posted to tuition; ₪ 200 queued for refund."

# COMPREHENSIVE STATES:
- Default Queue State: 5 vouchers listed, highest urgency at the top.
- Inspection State (Standard): Shows voucher details, clean receipt slip preview, green "Approve" button.
- Inspection State (Overpayment): Prominent red alert banner detailing the ₪ 200 excess refund requirement.
- Reject Mode State: Rationale textarea expands with required indicator; "Approve" button disappears.
- Empty Queue State: Green checkmark illustration: "All offline payment vouchers have been reviewed and approved!"

# RESPONSIVE BEHAVIOR:
- Mobile (<640px): Inspection modal becomes full-screen sheet; receipt slip preview supports tap-to-zoom; decision buttons stick to screen bottom.
- Desktop (1025px+): 680px dialog with split view (ledger on left, slip preview on right).

# ACCESSIBILITY (A11y) & WCAG 2.1 AA:
- Rationale input has `aria-required="true"` and `aria-describedby="rationale-helper"`.
- Modal confirms to `role="dialog"` and `aria-labelledby="voucher-title"`.
````

---

### SCR-M-08: Instructor Dues & Settlement Payouts

````text
# SCREEN IDENTIFIER: SCR-M-08
# SCREEN TITLE: Instructor Dues & Settlement Payouts
# TARGET ROUTE: /manager/instructor-dues
# PRIMARY ACTOR: Center Manager (`MANAGER`)
# PURPOSE: Track center financial liabilities owed to instructors across varying compensation models (`Fixed`, `PercentageOfCollectedRevenue`, `PerSession`), inspect verified automated accruals, and record official settlement payout transactions.

# VISUAL & LAYOUT HIERARCHY:
- Layout: App Shell (Shared Top Header + Manager Sidebar with "Instructor Dues" active).
- Main Canvas:
  1. Page Header: Title "Instructor Dues & Settlements", Subtitle "Track compensation accruals, verify liability calculations, and settle payouts", Right Actions: "+ Record Settlement Payout" (Primary).
  2. Center Liabilities Summary (4 Metric Cards): Total Accrued Liabilities (₪ 18,750), Settled / Paid to Date (₪ 9,550), Outstanding Balance Due (₪ 9,200), Instructors Awaiting Settlement (6).
  3. Filter Bar: Filter by Compensation Model (`All`, `Fixed`, `Percentage`, `PerSession`), Filter by Settlement Status (`All`, `Pending`, `PartiallyPaid`, `Paid`, `Adjusted`), Search by Instructor.
  4. Instructor Dues Ledger Table: Instructor Name, Assigned Cohort, Compensation Model & Terms, Accrued Compensation, Total Paid, Balance Outstanding, Settlement Status Badge, Actions.
  5. Settlement Modal: "Record Instructor Payout" form.

# DOMAIN DATA & MOCK ENTITIES:
- Center Liabilities Roster:
  - Record 1: DueID: "DUE-2026-001", Instructor: "Eng. Omar Farooq", Course: "Full-Stack Web Dev (FSW-2026-C4)", Model: "Fixed", Terms: "₪ 4,500 flat fee", AccruedDues: "₪ 4,500.00", PaidToDate: "₪ 2,250.00", BalanceDue: "₪ 2,250.00", Status: "PartiallyPaid"
  - Record 2: DueID: "DUE-2026-002", Instructor: "Leila Mansour", Course: "UI/UX Design Masterclass", Model: "PercentageOfCollectedRevenue", Terms: "25% of verified collected revenue (Collected: ₪ 15,400)", AccruedDues: "₪ 3,850.00", PaidToDate: "₪ 0.00", BalanceDue: "₪ 3,850.00", Status: "Pending"
  - Record 3: DueID: "DUE-2026-003", Instructor: "Dr. Zaid Al-Hamad", Course: "Python Data Science", Model: "PerSession", Terms: "₪ 150 / session (12 completed sessions with attendance)", AccruedDues: "₪ 1,800.00", PaidToDate: "₪ 1,800.00", BalanceDue: "₪ 0.00", Status: "Paid"
- Record Settlement Payout Form:
  - Instructor: "Eng. Omar Farooq" (Pre-selected)
  - Course: "Full-Stack Web Dev (FSW-2026-C4)"
  - Outstanding Balance: "₪ 2,250.00"
  - Payout Amount Tendered: "₪ 2,250.00"
  - Payment Date: "2026-10-24"
  - Settlement Method: Dropdown `Bank Transfer` (Options: `Cash`, `Bank Transfer`, `Cheque`)
  - Bank Reference / Transaction ID: "BNK-PAY-88210"
  - Internal Accounting Notes: "Final settlement upon completion of Module 3"

# DESIGN SYSTEM COMPONENTS:
- MetricCard (White elevated card with liability number and status breakdown)
- CompensationModelBadge (Fixed: Indigo, Percentage: Amber, PerSession: Blue)
- SettlementStatusBadge (Pending: Amber, PartiallyPaid: Blue, Paid: Green, Adjusted: Slate)
- DataTable (Columns: Instructor, Course, Compensation Terms, Accrued, Paid, Balance, Status, Actions)
- ModalDialog (Width: 540px, Title: "Record Instructor Settlement Payout")
- PrimaryButton ("Record Payout"), SecondaryButton ("Cancel")

# CALLS TO ACTION (CTAs):
- Primary CTA (Modal): "Confirm Settlement Payout" -> Records payout transaction, updates InstructorDue record, logs immutable audit entry.
- Table Row CTA: "Settle Balance" -> Opens settlement modal pre-filled with instructor's outstanding balance.
- Table Row CTA: "View Accrual Audit" -> Opens breakdown dialog showing session logs or payment collection records justifying the accrued amount.

# FORM VALIDATION & BUSINESS RULES:
- Accrual Calculation Integrity:
  - `Fixed`: Full amount accrued upon course activation or completion per contract.
  - `PercentageOfCollectedRevenue`: Accrual is strictly calculated against *verified, approved payments* (`Payment.Status == 'Completed'`). Unapproved or pending payments are excluded.
  - `PerSession`: Accrual is strictly calculated as `SessionCount * SessionRate`, where session has completed status and submitted attendance.
- Payout Validation: Settlement payout amount must be strictly positive (`Amount > 0.00`) and cannot exceed outstanding balance due unless explicitly flagged as administrative adjustment.

# NAVIGATION & ROUTING RULES:
- Completing a settlement updates the dues ledger in-place and updates dashboard liabilities metric.
- Clicking instructor name links to `/manager/instructors`.

# COMPREHENSIVE STATES:
- Default State: Table showing all 16 cohort compensation agreements and current liability standing.
- Filtered State (Outstanding Only): Shows only instructors with `BalanceDue > 0.00`.
- Empty State: "No instructor liabilities found matching the selected filters."
- Success State: Toast banner: "Settlement payout of ₪ 2,250.00 for Eng. Omar Farooq recorded successfully."

# RESPONSIVE BEHAVIOR:
- Mobile (<640px): Table converts into liability cards; each card clearly displays the compensation model, terms formula, and a prominent "Settle Balance" button.
- Desktop (1025px+): Dense multi-column ledger view with right-aligned currency values.

# ACCESSIBILITY (A11y) & WCAG 2.1 AA:
- Formulas and calculations have descriptive `aria-label` explaining accrual basis (e.g. `aria-label="25 percent of 15,400 NIS collected revenue equals 3,850 NIS"`).
- Keyboard accessible modal with proper focus return.
````

---

### SCR-M-09: Center Operational & Financial Reports

````text
# SCREEN IDENTIFIER: SCR-M-09
# SCREEN TITLE: Center Operational & Financial Reports
# TARGET ROUTE: /manager/reports
# PRIMARY ACTOR: Center Manager (`MANAGER`)
# PURPOSE: Provide center managers with clear, high-level operational and financial summary reports covering student enrollment trends, fee collection rates, overdue receivables aging, and instructor compensation expenses.

# VISUAL & LAYOUT HIERARCHY:
- Layout: App Shell (Shared Top Header + Manager Sidebar with "Reports" active).
- Main Canvas:
  1. Page Header: Title "Operational & Financial Reports", Subtitle "Executive summary reports and center performance indicators", Right Actions: "Print / Save as PDF" (Secondary Outline).
  2. Date Range Filter: Preset buttons ("This Month", "Last 30 Days", "Current Quarter", "Year to Date").
  3. Report Category Cards (4 Core Summary Sections):
     - Section 1: Course Enrollment & Capacity Utilization Report
     - Section 2: Fee Collection & Cash Inflow Velocity Report
     - Section 3: Receivables Aging & Delinquency Report
     - Section 4: Instructor Compensation & Cost Distribution Report
  4. Tabular Summary Previews for each report section.

# DOMAIN DATA & MOCK ENTITIES:
- Center Info: ID: "CTR-001", Name: "Beacon Training Center", Period: "October 2026"
- Report 1: Course Capacity Utilization:
  - Total Available Seats: 220 seats across 12 active cohorts
  - Enrolled Seats: 186 seats (84.5% overall occupancy)
  - Full-Capacity Cohorts: 4 cohorts with active waitlists
- Report 2: Fee Inflow Velocity:
  - Total Billed / Invoiced: "₪ 184,500.00"
  - Total Collected & Verified: "₪ 138,200.00" (74.9%)
  - Pending Approval Vouchers: "₪ 4,200.00" (2.3%)
  - Uncollected Receivables: "₪ 42,100.00" (22.8%)
- Report 3: Overdue Aging Summary:
  - Current (Not yet due): "₪ 28,400.00"
  - 1-15 Days Overdue: "₪ 9,800.00" (12 students)
  - 16-30 Days Overdue: "₪ 5,900.00" (7 students)
  - >30 Days Overdue: "₪ 2,200.00" (2 students)
- Report 4: Instructor Compensation Expenses:
  - Total Accrued Liabilities: "₪ 18,750.00"
  - Total Settled Payouts: "₪ 9,550.00"
  - Outstanding Payables: "₪ 9,200.00"
  - Net Operating Margin Indicator: "₪ 119,450.00" (Collected Revenue - Instructor Payouts)

# DESIGN SYSTEM COMPONENTS:
- DateRangePicker (Preset pills + custom calendar selector)
- ReportCard (Container with section header, KPI badge, bar chart / progress visual, and data table)
- ProgressBar (Occupancy and collection visual meters)
- AgingTable (Columns: Aging Bucket, Number of Students, Total Amount, % of Total Receivables)
- SecondaryOutlineButton ("Print Report", Printer icon)

# CALLS TO ACTION (CTAs):
- Primary CTA: "Print / Save PDF" -> Triggers browser print stylesheet optimized for clean, paginated PDF output.
- Section CTA: "View Detailed Student List" -> Links to `/manager/students?filter=overdue`.
- Section CTA: "View Dues Breakdown" -> Links to `/manager/instructor-dues`.

# FORM VALIDATION & BUSINESS RULES:
- Scoping: All calculations strictly aggregate records belonging to `CenterID: CTR-001`.
- Accounting Rule: Billed = `Sum(FinalPayable)` for all active enrollments. Collected = `Sum(Amount)` for `Completed` payments only.
- Read-only reporting interface for MVP; no external third-party BI tool connections required.

# NAVIGATION & ROUTING RULES:
- Filter changes update report cards dynamically without full page reload.
- Quick links navigate to underlying operational screens (`SCR-M-05`, `SCR-M-06`, `SCR-M-08`).

# COMPREHENSIVE STATES:
- Default State: Populated reports for the current active month.
- Loading State: Skeleton shimmer loading over charts and table rows.
- Empty State (Zero Activity in Period): "No operational or financial records found for the selected date range."
- Error State: Alert banner: "Failed to generate report summaries. Please verify date parameters."

# RESPONSIVE BEHAVIOR:
- Mobile (<640px): Report sections stack vertically; tables display in compact 2-column key-value format; date range presets convert to dropdown.
- Desktop (1025px+): 2x2 grid of comprehensive analytical report cards.

# ACCESSIBILITY (A11y) & WCAG 2.1 AA:
- Print view applies `@media print` rules hiding sidebar and top navigation bars.
- Tables include complete headers (`<th>`), captions, and row scopes (`scope="row"`).
````

---

### SCR-M-11: Immutable Audit Trail

````text
# SCREEN IDENTIFIER: SCR-M-11
# SCREEN TITLE: Immutable Audit Trail
# TARGET ROUTE: /manager/audit
# PRIMARY ACTOR: Center Manager (`MANAGER`)
# PURPOSE: Provide center management and compliance officers with a tamper-proof, read-only system audit log capturing every administrative decision, course status change, payment voucher approval/rejection, refund execution, and instructor settlement with precise actor metadata and timestamps.

# VISUAL & LAYOUT HIERARCHY:
- Layout: App Shell (Shared Top Header + Manager Sidebar with "Audit Log" active).
- Main Canvas:
  1. Page Header: Title "System Audit Trail", Subtitle "Immutable historical log of all operational, financial, and administrative actions", Right Badge: "Compliance Mode: Append-Only".
  2. Filter & Query Toolbar: Date Range Picker, Action Type filter dropdown, Staff Member filter dropdown, Entity Search input (search by Student ID, Course Code, or Receipt #).
  3. Audit Events Table: Timestamp, Actor Name & Role, Action Category, Target Entity, Description & Diff, IP Address.
  4. Inspection Modal: "Audit Log Entry Inspection" (Displays complete JSON change delta: Before vs After).

# DOMAIN DATA & MOCK ENTITIES:
- Sample Audit Log Entries:
  - Event 1: ID: "AUD-9921", Timestamp: "2026-10-24 11:35:12", Actor: "Ahmad Qasim (MANAGER)", Action: "PAYMENT_APPROVE", Entity: "Payment: REC-2026-090", Description: "Approved offline payment of ₪ 1,400.00 for Noor Salem. Posted ₪ 1,200 to tuition; generated linked refund of ₪ 200 for excess.", IP: "192.168.1.45"
  - Event 2: ID: "AUD-9920", Timestamp: "2026-10-24 11:30:05", Actor: "Sara Al-Khatib (SALES)", Action: "PAYMENT_SUBMIT", Entity: "Payment: REC-2026-090", Description: "Submitted Bank Transfer voucher of ₪ 1,400.00 with ₪ 200 excess flagged.", IP: "192.168.1.52"
  - Event 3: ID: "AUD-9918", Timestamp: "2026-10-24 10:20:44", Actor: "Ahmad Qasim (MANAGER)", Action: "COURSE_ACTIVATE", Entity: "Course: FSW-2026-C4", Description: "Activated course cohort. Verified 2 assigned instructors (Lead: Omar Farooq).", IP: "192.168.1.45"
  - Event 4: ID: "AUD-9915", Timestamp: "2026-10-24 09:15:30", Actor: "Eng. Omar Farooq (INSTRUCTOR)", Action: "LATE_OVERRIDE_ENABLE", Entity: "Task: TSK-004", Description: "Enabled late submission override for student Tariq Al-Sayed.", IP: "192.168.1.88"
  - Event 5: ID: "AUD-9910", Timestamp: "2026-10-23 16:45:10", Actor: "Ahmad Qasim (MANAGER)", Action: "PAYMENT_REJECT", Entity: "Payment: REC-2026-085", Description: "Rejected POS voucher of ₪ 1,000. Rationale: 'Slip illegible, re-upload receipt'.", IP: "192.168.1.45"

# DESIGN SYSTEM COMPONENTS:
- ActionBadge:
  - PAYMENT_APPROVE / SETTLEMENT_PAYOUT: Green
  - PAYMENT_REJECT / COURSE_ARCHIVE: Red
  - PAYMENT_SUBMIT / ENROLLMENT_CREATE: Blue
  - LATE_OVERRIDE_ENABLE / COURSE_ACTIVATE: Amber
- DataTable (Columns: Timestamp, Actor, Action, Entity, Details, IP Address, Inspect Button)
- JsonDiffViewer (Side-by-side or colored diff showing old values vs new values)
- SlideOverDrawer / Modal ("Audit Event Details")

# CALLS TO ACTION (CTAs):
- Row Action: "Inspect Change Diff" -> Opens modal displaying full event details including JSON payload before/after state.
- Filter Action: "Apply Filters" -> Refreshes query results in real-time.
- Reset Action: "Reset Filters" -> Restores default 24-hour log view.

# FORM VALIDATION & BUSINESS RULES:
- Append-Only Immutability: Audit log records can NEVER be updated, edited, or deleted under any circumstances. There are NO edit or delete buttons on this screen.
- Scoping: Scoped strictly to `CenterID: CTR-001`.
- Complete Provenance: Every recorded event captures: Timestamp (ISO 8601 UTC), Actor UserID, Actor Role, Action Type, Target Entity ID, Old State, New State, and Client IP.

# NAVIGATION & ROUTING RULES:
- Clicking entity identifier (e.g. `REC-2026-090`) navigates to related view in `/manager/payments`.
- Fully self-contained inspection drawer.

# COMPREHENSIVE STATES:
- Default State: Displays latest 50 audit records in reverse chronological order.
- Filtered State: Displays matched events with active filter summary chips.
- Empty State: "No audit events match your search criteria. [Reset Filters]".
- Loading State: Shimmer loading lines across table rows.

# RESPONSIVE BEHAVIOR:
- Mobile (<640px): Table transforms into event cards; timestamp and action badge appear at top; description is expandable on tap.
- Desktop (1025px+): Comprehensive tabular layout with monospace font for IP addresses and timestamps.

# ACCESSIBILITY (A11y) & WCAG 2.1 AA:
- Timestamps include `<time datetime="2026-10-24T11:35:12Z">` with localized display text.
- JSON change view uses high-contrast diff formatting (Green for additions, Red for deletions) with accessible text tags `[Added]` and `[Removed]`.
````


---

# SECTION 5: ACADEMIC DELIVERY, CONTENT & EVALUATION (INSTRUCTOR FLOW)

---

### SCR-I-01: Instructor Academic Dashboard

````text
# SCREEN IDENTIFIER: SCR-I-01
# SCREEN TITLE: Instructor Academic Dashboard
# TARGET ROUTE: /instructor/dashboard
# PRIMARY ACTOR: Course Instructor (`INSTRUCTOR`)
# PURPOSE: Provide course instructors with an academic workspace highlighting their active assigned cohorts, upcoming lecture schedule, pending homework submissions awaiting grading, and quick shortcuts to attendance and materials.

# VISUAL & LAYOUT HIERARCHY:
- Layout: App Shell (Shared Top Header + Instructor-Filtered Sidebar).
- Top Bar: VELORA Logo, Center Badge "Beacon Training Center", Dashboard Link (active), Notifications (4 items), User Avatar "Eng. Omar Farooq (Instructor)".
- Sidebar Navigation: Dashboard (active), My Courses, Sessions & Attendance, Materials, Tasks & Submissions, Feedback, My Earnings. (Manager, Sales, and Staff links are strictly hidden).
- Main Canvas:
  1. Welcome Banner: Greeting "Welcome back, Eng. Omar", Subtitle "Academic Teaching & Evaluation Cockpit", Quick Action Bar: "Schedule Lecture", "Create Task", "Take Attendance".
  2. Academic Metric Cards (4 Cards): Active Assigned Cohorts (2), Total Active Students (38), Submissions Awaiting Review (9), Next Lecture Session (Today at 18:00).
  3. Two-Column Operational Layout:
     - Left Column (60%): "Upcoming Lecture Sessions & Attendance" (Next 3 scheduled lectures with direct "Take Attendance" button).
     - Right Column (40%): "Pending Task Submissions Queue" (Latest student submissions needing qualitative feedback with submission time and download link).

# DOMAIN DATA & MOCK ENTITIES:
- Instructor Identity: UserID: "USR-003", Name: "Eng. Omar Farooq", Role: "INSTRUCTOR"
- Assigned Active Courses:
  - Course 1: "Full-Stack Web Dev - Cohort 4" | Role: Lead Instructor | Enrolled: 20 Trainees | Progress: Session 8 of 24
  - Course 2: "Node.js Backend Microservices" | Role: Lead Instructor | Enrolled: 18 Trainees | Progress: Session 3 of 16
- Upcoming Sessions:
  - Session 1: Today, Oct 24 @ 18:00 - 21:00 | Course: "Full-Stack Web Dev" | Room: Lab 3 | Topic: "REST APIs & Express Middleware" | Action: "Take Attendance"
  - Session 2: Sat, Oct 26 @ 14:00 - 17:00 | Course: "Node.js Microservices" | Room: Lab 1 | Topic: "Docker Containerization"
- Pending Submissions Queue:
  - Submission 1: Task: "Build RESTful Auth API" | Student: "Tariq Yousef Al-Sayed" | Submitted: "2 hours ago (On-Time)" | File: `tariq_auth_api.zip`
  - Submission 2: Task: "Build RESTful Auth API" | Student: "Dina Majed Al-Rantisi" | Submitted: "5 hours ago (On-Time)" | File: `dina_auth_api.zip`
  - Submission 3: Task: "Database Schema Design" | Student: "Rami Shaker" | Submitted: "Yesterday (Late - Override Active)" | File: `rami_erd.pdf`

# DESIGN SYSTEM COMPONENTS:
- MetricCard (White elevated surface, indigo icon, metric number, status chip)
- SessionScheduleCard (Border-slate-200, time pill, course title, room badge, primary CTA button)
- SubmissionQueueItem (Student avatar, task title, timestamp, on-time green chip or late amber chip, "Review" link)
- PrimaryButton, SecondaryOutlineButton

# CALLS TO ACTION (CTAs):
- Quick Action: "Take Attendance" -> Navigates to `/instructor/attendance` for today's session.
- Quick Action: "Create Task" -> Opens New Task Modal on `/instructor/tasks`.
- Row CTA (Submissions): "Review & Grade" -> Opens submission feedback modal on `/instructor/tasks`.
- Session CTA: "Open Lecture Workspace" -> Navigates to `/instructor/courses/[id]`.

# FORM VALIDATION & BUSINESS RULES:
- Scoping: Instructor sees ONLY courses and students where they are formally assigned in `CourseInstructorAssignment`.
- Data Privacy: Instructor CANNOT see other instructors' courses, student financial balances, or center-level revenue metrics.
- Attendance Gate: Attendance can only be marked for active scheduled sessions.

# NAVIGATION & ROUTING RULES:
- Clicking a course card routes to `/instructor/courses/[id]`.
- Clicking "My Earnings" shortcut routes to `/instructor/earnings`.

# COMPREHENSIVE STATES:
- Default State: Populated schedule for today, 9 pending submissions in review queue.
- Loading State: Skeleton cards with pulsating placeholders across metrics and schedule.
- Empty Schedule State: "No lectures scheduled for today. [Schedule a Session]".
- Empty Queue State: Green icon: "All student submissions have been reviewed! Inbox zero."

# RESPONSIVE BEHAVIOR:
- Mobile (<640px): Sidebar moves to off-canvas drawer; metric cards scroll horizontally; schedule cards stack vertically with large touch action buttons.
- Desktop (1025px+): 60/40 grid layout with sticky upcoming session alerts.

# ACCESSIBILITY (A11y) & WCAG 2.1 AA:
- Upcoming session cards have semantic `aria-label="Upcoming lecture for Full-Stack Web Development, today at 18:00 in Lab 3"`.
- Action buttons have high-contrast text and clear focus rings.
````

---

### SCR-I-02: My Assigned Courses (Instructor)

````text
# SCREEN IDENTIFIER: SCR-I-02
# SCREEN TITLE: My Assigned Courses Catalog
# TARGET ROUTE: /instructor/courses
# PRIMARY ACTOR: Course Instructor (`INSTRUCTOR`)
# PURPOSE: Display a clear, focused catalog of training cohorts strictly assigned to the logged-in instructor, showing cohort progress, schedule, enrolled trainee counts, and direct entry into course workspaces.

# VISUAL & LAYOUT HIERARCHY:
- Layout: App Shell (Shared Top Header + Instructor Sidebar with "My Courses" active).
- Main Canvas:
  1. Page Header: Title "My Assigned Courses", Subtitle "Manage curriculum, sessions, materials, and student submissions for your cohorts", Right Indicator: "2 Active Cohorts".
  2. Filter & View Tabs: "Active Courses (2)", "Completed Cohorts (3)", "All Courses (5)".
  3. Course Grid: Responsive cards displaying each assigned course with visual progress bars.
  4. Quick Stats Bar on each card: Enrolled Trainees, Completed Sessions, Average Attendance Rate, Pending Submissions.

# DOMAIN DATA & MOCK ENTITIES:
- Assigned Course Records:
  - Course 1: ID: "CRS-FSW-004", Code: "FSW-2026-C4", Title: "Full-Stack Web Development Bootcamp", Schedule: "Mon / Wed / Sat, 18:00 - 21:00", Dates: "Oct 15, 2026 - Jan 15, 2027", EnrolledCount: 20, TotalSessions: 24, CompletedSessions: 8 (33% progress), RoleInCourse: "Lead Instructor", AverageAttendance: "91%", PendingTasks: 5
  - Course 2: ID: "CRS-ND-002", Code: "NOD-2026-C2", Title: "Node.js Backend & Cloud Microservices", Schedule: "Tue / Thu, 18:00 - 21:00", Dates: "Oct 20, 2026 - Dec 20, 2026", EnrolledCount: 18, TotalSessions: 16, CompletedSessions: 3 (19% progress), RoleInCourse: "Lead Instructor", AverageAttendance: "95%", PendingTasks: 4

# DESIGN SYSTEM COMPONENTS:
- CourseCard (Elevated white card, border-slate-200, hover:border-indigo-400 transition)
- ProgressBar (Indigo visual bar showing session completion % e.g. 8 of 24 sessions)
- StatBadge (Enrolled count: Slate, Attendance rate: Green, Pending submissions: Amber)
- PrimaryButton ("Open Workspace", ArrowRight icon)

# CALLS TO ACTION (CTAs):
- Primary Card CTA: "Enter Course Workspace" -> Navigates to `/instructor/courses/[id]`.
- Secondary Card CTA: "Take Attendance" -> Navigates to `/instructor/attendance?courseId=[id]`.
- Secondary Card CTA: "Upload Material" -> Navigates to `/instructor/materials?courseId=[id]`.

# FORM VALIDATION & BUSINESS RULES:
- Role & Assignment Scoping: Strictly restricted to courses where `CourseInstructorAssignment.InstructorID == CurrentUser.ID`. Any attempt to access an unassigned course returns 403 Forbidden.
- Read-only catalog view with navigation triggers.

# NAVIGATION & ROUTING RULES:
- Clicking card or "Enter Course Workspace" button opens `/instructor/courses/[id]`.
- Filter tabs toggle between Active and past Completed cohorts.

# COMPREHENSIVE STATES:
- Default State: Grid of 2 active course cards with live progress bars and pending review badges.
- Loading State: 2 skeleton card placeholders with animated shimmer.
- Empty State (No Assigned Courses): "You are not currently assigned to any active courses. Please contact your Center Manager."
- Completed Tab State: Lists past cohorts with final student completion statistics.

# RESPONSIVE BEHAVIOR:
- Mobile (<640px): 1-column card stack; progress bar spans 100% width; action buttons are stacked for easy tapping.
- Desktop (1025px+): 2-column wide grid layout with detailed stats badges.

# ACCESSIBILITY (A11y) & WCAG 2.1 AA:
- Progress bar includes `aria-valuenow="8"`, `aria-valuemin="0"`, `aria-valuemax="24"`, and `aria-label="Course completion: 8 of 24 sessions delivered"`.
- Entire card is keyboard focusable with visible focus ring.
````

---

### SCR-I-03: Course Workspace & Delivery Hub (Instructor)

````text
# SCREEN IDENTIFIER: SCR-I-03
# SCREEN TITLE: Course Workspace & Delivery Hub
# TARGET ROUTE: /instructor/courses/[id]
# PRIMARY ACTOR: Course Instructor (`INSTRUCTOR`)
# PURPOSE: Serve as the instructor's primary academic delivery workspace for an assigned cohort: managing lecture sessions, publishing course materials, issuing tasks, reviewing student submissions, tracking session attendance, and providing private student feedback.

# VISUAL & LAYOUT HIERARCHY:
- Layout: App Shell (Shared Top Header + Instructor Sidebar).
- Breadcrumbs: "My Courses > Full-Stack Web Development Bootcamp (FSW-2026-C4)"
- Top Course Banner:
  - Course Title, Code, Schedule, Enrolled Count (20), Progress (Session 8 of 24).
  - Quick Action Buttons: "+ Schedule Session", "+ Upload Material", "+ Create Task".
- Tabbed Navigation Bar:
  1. Overview & Syllabus Roadmap (Active by default)
  2. Sessions & Attendance (List of 24 sessions with status and attendance indicators)
  3. Course Materials (Downloadable slides, code repos, PDF guides)
  4. Tasks & Submissions (Homework assignments and grading queue)
  5. Student Roster & 1:1 Feedback (List of 20 enrolled trainees with private feedback links)
- Active Tab Body Container (White card, rounded-xl, p-6, border-slate-200).

# DOMAIN DATA & MOCK ENTITIES:
- Course Overview Context:
  - Title: "Full-Stack Web Development Bootcamp", Code: "FSW-2026-C4"
  - Dates: "Oct 15, 2026 - Jan 15, 2027", Time: "Mon / Wed / Sat, 18:00 - 21:00", Room: "Lab 3"
- Tab 1: Roadmap Summary:
  - Module 1: Modern JavaScript & TypeScript (Sessions 1-6) - Completed
  - Module 2: Node.js, Express & Database Architecture (Sessions 7-14) - In Progress
  - Module 3: React 19, Next.js & Tailwind CSS (Sessions 15-20) - Scheduled
  - Module 4: Cloud Deployment & Final Projects (Sessions 21-24) - Scheduled
- Enrolled Trainees Roster (Sample):
  - Trainee 1: Tariq Al-Sayed | Attendance: 92% (7/8) | Tasks: 3/3 Submitted | Feedback: 1 Note Added
  - Trainee 2: Dina Al-Rantisi | Attendance: 100% (8/8) | Tasks: 3/3 Submitted | Feedback: 2 Notes Added
  - Trainee 3: Rami Shaker | Attendance: 75% (6/8) | Tasks: 2/3 Submitted | Feedback: Needs Improvement Note

# DESIGN SYSTEM COMPONENTS:
- BreadcrumbNav
- TabGroup (Underline style, 5 tabs with notification badges)
- RoadmapTimeline (Step indicator showing completed, active, and upcoming modules)
- PrimaryButton, SecondaryOutlineButton
- TraineeRosterTable (Columns: Trainee, Attendance %, Submissions, Last Active, Private Feedback CTA)

# CALLS TO ACTION (CTAs):
- Quick Action: "+ Upload Material" -> Opens material upload drawer (`SCR-I-05`).
- Quick Action: "+ Create Task" -> Opens new task creation modal (`SCR-I-06`).
- Tab Action (Sessions): "Take Attendance for Session 8" -> Navigates to `SCR-I-04`.
- Row Action (Roster): "Give 1:1 Feedback" -> Opens private feedback modal (`SCR-I-07`).

# FORM VALIDATION & BUSINESS RULES:
- Scoping: Instructor must be actively assigned to this course.
- Tab isolation: Switching tabs loads tab data without full page refresh.
- Privacy Rule: Feedback notes given to students are private and 1:1. Trainees cannot see other trainees' feedback.

# NAVIGATION & ROUTING RULES:
- Breadcrumb "My Courses" returns to `/instructor/courses`.
- Action buttons open modals in-place on current workspace route.

# COMPREHENSIVE STATES:
- Default State: Overview tab displays active module roadmap, next lecture alert, and quick stats.
- Tab Switch State: Smooth transition between Sessions, Materials, Tasks, and Feedback tabs.
- Loading State: Skeleton shimmer over timeline and roster table.
- Error State: 403 Forbidden banner if unassigned instructor attempts direct URL access.

# RESPONSIVE BEHAVIOR:
- Mobile (<640px): Tab group becomes scrollable horizontal pill bar; roadmap collapses into vertical accordion; action buttons stack.
- Desktop (1025px+): Full horizontal tabs with sticky action toolbar.

# ACCESSIBILITY (A11y) & WCAG 2.1 AA:
- Tab bar conforms to WAI-ARIA tablist pattern with arrow-key keyboard navigation.
- Progress timeline elements have accessible screen reader descriptions.
````

---

### SCR-I-04: Sessions & Attendance Roster

````text
# SCREEN IDENTIFIER: SCR-I-04
# SCREEN TITLE: Sessions & Attendance Roster
# TARGET ROUTE: /instructor/attendance
# PRIMARY ACTOR: Course Instructor (`INSTRUCTOR`)
# PURPOSE: Enable instructors to schedule and manage lecture sessions, take interactive trainee attendance (`Present` / `Absent`), edit past attendance records, and automatically trigger per-session instructor compensation accrual upon submission.

# VISUAL & LAYOUT HIERARCHY:
- Layout: App Shell (Shared Top Header + Instructor Sidebar with "Sessions & Attendance" active).
- Main Canvas:
  1. Page Header: Title "Session Attendance Roster", Subtitle "Record and update lecture attendance for assigned cohorts", Right Action: "Save Attendance" (Primary Green button).
  2. Session Selector Toolbar: Course dropdown ("Full-Stack Web Dev - Cohort 4"), Session Selector dropdown ("Session 8: REST APIs & Express - Oct 24, 2026"), Quick Toggle: "Mark All Present" button.
  3. Session Attendance Summary Bar: Total Enrolled (20), Present (18), Absent (2), Attendance Rate (90%).
  4. Trainee Attendance Roster: Clean, touch-optimized list/table of enrolled students with interactive `Present` / `Absent` radio toggle pills and optional attendance notes.
  5. Sticky Bottom Action Bar: "Save Attendance Records" button, "Discard Changes" button.

# DOMAIN DATA & MOCK ENTITIES:
- Course Context: "Full-Stack Web Dev - Cohort 4" (ID: "CRS-FSW-004")
- Active Session: Session #8 | Date: "2026-10-24" | Time: "18:00 - 21:00" | Topic: "REST APIs & Express Middleware" | Status: "In Progress"
- Trainee Roster (Sample of 20):
  - Trainee 1: ID: "STU-001", Name: "Tariq Yousef Al-Sayed", Status: `Present` (Selected)
  - Trainee 2: ID: "STU-002", Name: "Dina Majed Al-Rantisi", Status: `Present` (Selected)
  - Trainee 3: ID: "STU-004", Name: "Rami Shaker Al-Khatib", Status: `Absent` (Selected, Note: "Excused - Family emergency")
  - Trainee 4: ID: "STU-005", Name: "Noor Salem", Status: `Present` (Selected)
- Instructor Compensation Trigger Context:
  - If Instructor's model for this course is `PerSession`:
    - Rate: "₪ 150 / session"
    - Saving attendance for completed Session 8 will automatically trigger a system accrual of +₪ 150 to the instructor's `InstructorDue` ledger!

# DESIGN SYSTEM COMPONENTS:
- SessionSelectorDropdown (Displays session number, date, and topic)
- AttendanceToggleGroup:
  - `Present`: Green pill (Selected: bg-green-600 text-white; Unselected: bg-white text-green-700 border-green-300)
  - `Absent`: Red pill (Selected: bg-red-600 text-white; Unselected: bg-white text-red-700 border-red-300)
- BulkActionButton ("Mark All Present", CheckSquare icon, Slate-100 button)
- StickyBottomBar (bg-white border-t border-slate-200 p-4 shadow-lg flex justify-between items-center)
- PrimaryButton ("Save Attendance Records", Save icon, Green-600)

# CALLS TO ACTION (CTAs):
- Primary CTA: "Save Attendance Records" -> Submits roster to backend, enforces unique constraint, updates student attendance rates, triggers per-session compensation accrual if applicable, logs audit.
- Bulk CTA: "Mark All Present" -> Automatically toggles all 20 trainees to `Present` state for rapid submission.
- Row Action: "Add Note" -> Expands text input for excused absence explanation.

# FORM VALIDATION & BUSINESS RULES:
- Mandatory Status: Every enrolled trainee on the roster must be marked as either `Present` or `Absent`. Null states are not allowed upon submission.
- Unique Attendance Constraint: Enforces composite uniqueness on `(SessionID, EnrollmentID)`. Re-submitting an existing session performs an atomic `upsert` (update) with audit logging.
- Per-Session Accrual Rule (Critical Domain Requirement):
  - If the logged-in instructor is contracted under the `PerSession` model, the successful completion and saving of session attendance automatically increments the instructor's accrued dues by the session rate.
  - The system checks if this session was already counted to prevent duplicate payout accruals.

# NAVIGATION & ROUTING RULES:
- Saving attendance shows success toast: "Attendance for Session 8 saved successfully (18 Present, 2 Absent)."
- User remains on the page, with attendance marked "Saved & Locked (Click Edit to modify)".

# COMPREHENSIVE STATES:
- Default State: Roster loaded with empty selections (or previous selections if session was already taken).
- Unsaved Changes State: Sticky bottom bar appears with yellow indicator: "Unsaved attendance modifications detected."
- Saving State: "Save Attendance" button displays spinner and "Submitting Roster...".
- Success State: Toast: "Attendance recorded. Student attendance percentages updated."

# RESPONSIVE BEHAVIOR:
- Mobile (<640px): Table converts into full-width card rows; each card displays student photo/avatar, student name, and large touch-friendly `[ Present ]` and `[ Absent ]` buttons side-by-side (>=48px touch target).
- Desktop (1025px+): Compact table with inline radio pills and notes input.

# ACCESSIBILITY (A11y) & WCAG 2.1 AA:
- Radio toggle group uses proper `<fieldset>` and `<legend>` for each student.
- Arrow keys switch between `Present` and `Absent` for each student row.
- "Mark All Present" has confirmation prompt announced via `aria-live`.
````

---

### SCR-I-05: Course Materials Library (Instructor)

````text
# SCREEN IDENTIFIER: SCR-I-05
# SCREEN TITLE: Course Materials Library (Instructor Upload)
# TARGET ROUTE: /instructor/materials
# PRIMARY ACTOR: Course Instructor (`INSTRUCTOR`)
# PURPOSE: Allow instructors to upload, organize, and publish lecture slides, reference readings, source code repositories, and homework resources for enrolled students, enforcing strict file size and type validations.

# VISUAL & LAYOUT HIERARCHY:
- Layout: App Shell (Shared Top Header + Instructor Sidebar with "Materials" active).
- Main Canvas:
  1. Page Header: Title "Course Materials Library", Subtitle "Publish and organize learning resources for enrolled trainees", Right Action: "+ Upload New Material" button (Primary Indigo).
  2. Cohort Selector & Folder Tabs: Course dropdown ("Full-Stack Web Dev - Cohort 4"), Session Folders ("All Materials", "Module 1: JS/TS", "Module 2: Node.js", "Module 3: React").
  3. Materials Table / Grid: File Name & Icon, Linked Session, File Type & Size, Upload Date, Download Count, Actions Menu.
  4. Drag & Drop Upload Drawer / Modal: "Upload Course Resource" dialog.

# DOMAIN DATA & MOCK ENTITIES:
- Course Context: "Full-Stack Web Dev - Cohort 4" (ID: "CRS-FSW-004")
- Existing Published Materials:
  - File 1: `Lecture08_Express_Middleware.pdf` | Type: PDF | Size: "4.2 MB" | Linked Session: Session 8 | Uploaded: "Today at 14:00" | Downloads: 14
  - File 2: `Auth_API_Starter_Code.zip` | Type: ZIP Archive | Size: "12.8 MB" | Linked Session: Session 8 | Uploaded: "Today at 14:15" | Downloads: 18
  - File 3: `Database_ERD_Guide.pdf` | Type: PDF | Size: "2.1 MB" | Linked Session: Session 7 | Uploaded: "Oct 21, 2026" | Downloads: 20
  - File 4: `TypeScript_CheatSheet.docx` | Type: DOCX | Size: "1.4 MB" | Linked Session: Session 3 | Uploaded: "Oct 18, 2026" | Downloads: 19

# DESIGN SYSTEM COMPONENTS:
- FileTypeIcon (PDF: Red, ZIP: Amber, DOCX/PPTX: Blue, Code: Slate)
- DragAndDropZone (Dashed indigo border, UploadCloud icon, "Drag & drop files here or browse", helper text: "Max file size: 25MB")
- SlideOverDrawer (Width: 500px, Title: "Upload Course Material")
- InputField ("Resource Title"), SelectDropdown ("Link to Lecture Session"), Textarea ("Description / Instructions")
- PrimaryButton ("Publish Material"), DangerGhostButton ("Delete Resource")

# CALLS TO ACTION (CTAs):
- Primary CTA: "+ Upload New Material" -> Opens upload slide-over drawer.
- Drawer CTA: "Publish Resource" -> Validates file size (<=25MB) and type, uploads to secure storage, adds record to database, notifies enrolled students.
- Row Action: "Download File" -> Downloads authenticated file copy.
- Row Action: "Delete" -> Confirmation modal: "Remove this material? Enrolled students will no longer be able to download it."

# FORM VALIDATION & BUSINESS RULES:
- File Size Hard Limit: Maximum file size is strictly 25 MB (`<= 25MB`). Uploading a file > 25MB triggers immediate client-side block: "File exceeds maximum allowable size of 25MB."
- File Type Whitelist: Permitted extensions strictly restricted to:
  `.pdf`, `.doc`, `.docx`, `.ppt`, `.pptx`, `.xls`, `.xlsx`, `.jpg`, `.jpeg`, `.png`, `.zip`.
  Executing files (`.exe`, `.bat`, `.sh`, `.js`) are strictly rejected.
- Scoping & Storage Protection: Materials uploaded here are strictly accessible ONLY to students with an `Active` enrollment in this specific course. Unenrolled users receive 403 Forbidden.

# NAVIGATION & ROUTING RULES:
- Uploading closes drawer and appends the file to the active session list.
- Course dropdown switches the file directory between instructor's assigned courses.

# COMPREHENSIVE STATES:
- Default State: Display of 4 published files with type icons and download counters.
- Drag-Over State: Drop zone highlights with solid indigo background and "Release to upload file".
- Uploading State: Progress bar showing "Uploading Auth_API_Starter_Code.zip (65%)...".
- Error State: Red alert banner: "Upload Failed: File 'large_dataset.zip' is 34.2MB, which exceeds the 25MB limit."
- Success State: Green toast: "Material 'Lecture08_Express_Middleware.pdf' published successfully."

# RESPONSIVE BEHAVIOR:
- Mobile (<640px): Drop zone adapts to simple "Choose File" button; file table converts to compact list with large download touch icons.
- Desktop (1025px+): Wide table with file metadata, download metrics, and right-aligned action menus.

# ACCESSIBILITY (A11y) & WCAG 2.1 AA:
- File upload input has explicit `aria-describedby` pointing to allowable formats and size constraints.
- File type icons include `aria-hidden="true"`, with file format announced in text (e.g. `PDF document, 4.2 MB`).
````

---

### SCR-I-06: Tasks, Deadlines & Submissions Review

````text
# SCREEN IDENTIFIER: SCR-I-06
# SCREEN TITLE: Tasks, Deadlines & Submissions Review
# TARGET ROUTE: /instructor/tasks
# PRIMARY ACTOR: Course Instructor (`INSTRUCTOR`)
# PURPOSE: Allow instructors to create homework assignments with strict deadlines and attachments, inspect trainee submissions, download student files, provide private qualitative grading feedback, and toggle late submission overrides for individual students.

# VISUAL & LAYOUT HIERARCHY:
- Layout: App Shell (Shared Top Header + Instructor Sidebar with "Tasks & Submissions" active).
- Main Canvas:
  1. Page Header: Title "Tasks & Submissions Review", Subtitle "Issue homework assignments and evaluate trainee submissions", Right Action: "+ Create New Task" button (Primary Indigo).
  2. Active Task Selector Tabs: Horizontal pills showing active tasks in this cohort ("Task 1: Git Workflow", "Task 2: Build RESTful API [Active - 9 Pending]", "Task 3: Database Schema").
  3. Task Overview Header Card: Task Title, Deadline, Total Submissions (18/20), Pending Grading (9), Attached Instructions link.
  4. Submissions Evaluation Table: Student Name & Avatar, Submission Timestamp, Deadline Compliance Badge (`On-Time` / `Late` / `Missing`), Submitted File Link, Grade / Status, Actions.
  5. Centered Review Modal: "Evaluate Trainee Submission" (Side-by-side file review, qualitative feedback textarea, and Late Override toggle).

# DOMAIN DATA & MOCK ENTITIES:
- Course Context: "Full-Stack Web Dev - Cohort 4"
- Active Task Context:
  - Task ID: "TSK-002", Title: "Build RESTful Authentication API", DueDate: "2026-10-23 23:59 (Passed Yesterday)"
  - Instructions: "Implement JWT authentication with register, login, and refresh token endpoints using Express and PostgreSQL."
- Submissions Roster (Sample):
  - Trainee 1: Tariq Yousef Al-Sayed | SubmittedAt: "2026-10-23 21:14 (On-Time)" | File: `tariq_auth_api.zip` (3.4 MB) | Status: "Pending Review"
  - Trainee 2: Dina Majed Al-Rantisi | SubmittedAt: "2026-10-23 22:45 (On-Time)" | File: `dina_auth_api.zip` (4.1 MB) | Status: "Reviewed" (Feedback: "Excellent modular structure! Clean JWT verification.")
  - Trainee 3: Rami Shaker Al-Khatib | SubmittedAt: "2026-10-24 10:30 (Late - Allowed by Override)" | File: `rami_auth.zip` (2.8 MB) | Status: "Pending Review" | LateOverrideActive: true
  - Trainee 4: Fadi Odeh | Status: "Missing" | Deadline: "Expired" | LateOverrideActive: false

# DESIGN SYSTEM COMPONENTS:
- TaskSelectorTabs (Pill buttons with count badges)
- StatusBadge:
  - On-Time: Green
  - Late (Override): Amber
  - Missing / Expired: Red
  - Reviewed: Blue
- DataTable (Columns: Trainee, Submitted At, Compliance, File Attachment, Review Status, Action)
- ModalDialog (Width: 640px, Title: "Evaluate Submission: Tariq Yousef Al-Sayed")
- TextareaField ("Qualitative Instructor Feedback", rows: 4, placeholder: "Enter constructive feedback on code quality, architecture, and improvements...")
- SwitchToggle ("Allow Late Submission Override for this student")
- PrimaryButton ("Submit Feedback & Complete Review"), SecondaryButton ("Cancel")

# CALLS TO ACTION (CTAs):
- Primary CTA: "+ Create New Task" -> Opens task creation drawer (Title, Description, Due Date, Attachment).
- Row Action: "Review Submission" -> Opens evaluation modal with student file and feedback form.
- Modal CTA: "Save Evaluation" -> Commits qualitative feedback, marks submission as `Reviewed`, sends notification to student.
- Modal Toggle: "Allow Late Override" -> Toggles override flag allowing student to upload past deadline.

# FORM VALIDATION & BUSINESS RULES:
- Strict Deadline Enforcement:
  - When deadline passes, student upload portal is automatically locked for that task.
  - An instructor has the sole authority to toggle `LateOverrideAllowed = true` for an individual student.
  - When override is active, the student can submit, and the submission is clearly watermarked as `Submitted Late (Override Authorized)`.
- Qualitative Feedback Validation: Feedback text cannot be empty when completing review (minimum 5 characters).
- Privacy: All task feedback is strictly 1:1 and private between instructor and student.

# NAVIGATION & ROUTING RULES:
- Saving evaluation updates status badge to `Reviewed` with green checkmark in table.
- Trainee receives instant notification in their portal (`SCR-ST-05`).

# COMPREHENSIVE STATES:
- Default State: Submissions table loaded with 9 pending reviews highlighted at top.
- Modal Evaluation State: Displays student's uploaded file name, submission timestamp, and empty feedback box.
- Late Override Toggled State: Switch toggles amber with confirmation text: "Late submission window opened for Tariq Al-Sayed."
- Success State: Toast: "Evaluation and feedback for Tariq Al-Sayed saved successfully."

# RESPONSIVE BEHAVIOR:
- Mobile (<640px): Task tabs scroll horizontally; table converts to submission cards; evaluation modal takes full screen.
- Desktop (1025px+): 640px modal with side-by-side submission timestamp details and feedback editor.

# ACCESSIBILITY (A11y) & WCAG 2.1 AA:
- Late override toggle has `role="switch"` and `aria-checked="true/false"`.
- Feedback textarea has associated label and character count helper text.
````

---

### SCR-I-07: 1:1 Private Student Feedback (Instructor)

````text
# SCREEN IDENTIFIER: SCR-I-07
# SCREEN TITLE: 1:1 Private Student Feedback Desk
# TARGET ROUTE: /instructor/feedback
# PRIMARY ACTOR: Course Instructor (`INSTRUCTOR`)
# PURPOSE: Provide instructors with a dedicated confidential desk to deliver 1:1 qualitative feedback, behavioral evaluations, and academic recommendations to individual students, with strict privacy shielding from other trainees and sales staff.

# VISUAL & LAYOUT HIERARCHY:
- Layout: App Shell (Shared Top Header + Instructor Sidebar with "Feedback" active).
- Main Canvas:
  1. Page Header: Title "1:1 Student Feedback Desk", Subtitle "Provide confidential evaluations, recommendations, and mentorship notes", Right Indicator: "Confidentiality: 1:1 Private".
  2. Cohort & Student Selector Bar: Course dropdown ("Full-Stack Web Dev - Cohort 4"), Student dropdown with search ("Tariq Yousef Al-Sayed").
  3. Student Academic Context Snapshot Card: Attendance Rate (92%), Tasks Completed (3/3), Active Cohort Schedule.
  4. Two-Column Workspace:
     - Left Column (50%): "Historical Feedback Archive" (Chronological feed of previous feedback notes given to this student by the instructor).
     - Right Column (50%): "Compose New Feedback Note" (Feedback category, note text area, character counter, send button).

# DOMAIN DATA & MOCK ENTITIES:
- Selected Student: ID: "STU-001", Name: "Tariq Yousef Al-Sayed", Course: "Full-Stack Web Dev - Cohort 4"
- Feedback Archive for Tariq:
  - Note 1 (Oct 18, 2026): Category: "Academic Progress" | Instructor: "Eng. Omar Farooq" | Text: "Tariq demonstrates outstanding comprehension of TypeScript generics and async patterns. Advised him to focus more on SQL indexing strategies in Module 2."
  - Note 2 (Oct 10, 2026): Category: "Attendance & Engagement" | Instructor: "Eng. Omar Farooq" | Text: "Consistently punctual and actively contributes during live coding sessions. Great initiative in peer discussions."
- Compose New Feedback Note Form:
  - Category Selection: Dropdown (`Academic Performance`, `Attendance & Engagement`, `Technical Recommendations`, `General Mentorship`)
  - Feedback Body: Textarea (Max 2,000 characters)
  - Character Count Indicator: "0 / 2,000 characters"

# DESIGN SYSTEM COMPONENTS:
- StudentSnapshotCard (Avatar, Name, Course, Attendance %, Tasks Completed badge)
- FeedbackHistoryFeed (Timeline format, date badge, category tag, note body, instructor signature)
- TextareaField (h-48, rounded-xl, border-slate-300, focus:border-indigo-500)
- SelectDropdown (Category with semantic icons)
- PrimaryButton ("Submit Private Feedback", Send icon, Indigo-600)

# CALLS TO ACTION (CTAs):
- Primary CTA: "Submit Private Feedback" -> Validates non-empty content, encrypts/saves note, sends private notification to student (`SCR-ST-07`), updates timeline.
- Selector CTA: "Change Student" -> Switches context and loads selected student's history.

# FORM VALIDATION & BUSINESS RULES:
- Strict Non-Empty Rule: Feedback note cannot be empty or whitespace (minimum 10 characters, maximum 2,000 characters).
- Absolute Privacy & Access Boundary:
  - Feedback is strictly visible ONLY to: (1) The composing instructor, (2) The recipient student, and (3) The Center Manager for administrative oversight.
  - Other students in the cohort CANNOT see this feedback under any circumstances.
  - Sales Specialists CANNOT see academic feedback.

# NAVIGATION & ROUTING RULES:
- On submission, form clears, and the new note appears at the top of the history feed with a "Just now" timestamp.
- Success toast: "Private feedback delivered to Tariq Yousef Al-Sayed."

# COMPREHENSIVE STATES:
- Default State: Tariq Al-Sayed selected with 2 historical notes in timeline and empty composer.
- Empty History State (New Student): Timeline shows: "No previous feedback notes recorded for this student. Compose the first note on the right."
- Validation Error: Red text below textarea: "Feedback note is too short. Please provide at least 10 characters."
- Success State: Toast: "Feedback note dispatched to student portal."

# RESPONSIVE BEHAVIOR:
- Mobile (<640px): 2-column layout stacks vertically (Compose form on top, history feed below); student selector uses full width.
- Desktop (1025px+): 50/50 side-by-side layout for effortless review of previous notes while composing new ones.

# ACCESSIBILITY (A11y) & WCAG 2.1 AA:
- Textarea includes `aria-describedby="char-count"`, character count announces dynamically.
- History feed uses semantic `<article>` containers with `<time>` tags.
````

---

### SCR-I-08: My Earnings & Accruals (Instructor)

````text
# SCREEN IDENTIFIER: SCR-I-08
# SCREEN TITLE: My Earnings & Compensation Accruals
# TARGET ROUTE: /instructor/earnings
# PRIMARY ACTOR: Course Instructor (`INSTRUCTOR`)
# PURPOSE: Provide instructors with a transparent, read-only personal financial dashboard showing accrued compensation broken down by course and contract model (`Fixed`, `Percentage`, `PerSession`), total received settlement payouts, and pending payable balances.

# VISUAL & LAYOUT HIERARCHY:
- Layout: App Shell (Shared Top Header + Instructor Sidebar with "My Earnings" active).
- Main Canvas:
  1. Page Header: Title "My Earnings & Compensation", Subtitle "Track accrued compensation, contract terms, and received payouts", Right Indicator: "Currency: NIS (₪)".
  2. Personal Financial Summary Cards (3 Cards): Total Accrued Compensation (₪ 6,750), Total Settled & Received (₪ 4,500), Outstanding Balance Payable (₪ 2,250).
  3. Course-by-Course Accrual Breakdown Table: Course Title, Contract Model, Terms & Formula, Accrued Amount, Received Payouts, Outstanding Balance, Status Badge.
  4. Historical Settlement Payouts Log: List of official payouts issued by center management with transaction receipt numbers and payment dates.

# DOMAIN DATA & MOCK ENTITIES:
- Instructor Scope: Scoped strictly to `CurrentUser.ID == USR-003` (Eng. Omar Farooq)
- Personal Financial Metrics:
  - Total Accrued: "₪ 6,750.00"
  - Total Settled / Paid: "₪ 4,500.00"
  - Outstanding Payable: "₪ 2,250.00"
- Course Accruals Breakdown:
  - Course 1: "Full-Stack Web Dev - Cohort 4" | Model: `Fixed` | Terms: "₪ 4,500.00 flat cohort fee" | Accrued: "₪ 4,500.00" | Received: "₪ 2,250.00" | Balance: "₪ 2,250.00" | Status: "PartiallyPaid"
  - Course 2: "Node.js Backend Microservices" | Model: `PerSession` | Terms: "₪ 150.00 / session (15 completed sessions with attendance)" | Accrued: "₪ 2,250.00" | Received: "₪ 2,250.00" | Balance: "₪ 0.00" | Status: "Paid"
- Historical Payouts Ledger:
  - Payout 1: Date: "2026-10-15" | Amount: "₪ 2,250.00" | Method: "Bank Transfer" | Ref#: "BNK-PAY-88210" | Description: "Full settlement for Node.js completed sessions"
  - Payout 2: Date: "2026-10-01" | Amount: "₪ 2,250.00" | Method: "Cash Receipt" | Ref#: "CSH-PAY-1002" | Description: "50% advance for Full-Stack Web Dev cohort activation"

# DESIGN SYSTEM COMPONENTS:
- MetricCard (White elevated card with bold green or indigo monetary values)
- CompensationModelBadge (Fixed: Indigo, Percentage: Amber, PerSession: Blue)
- SettlementStatusBadge (Paid: Green, PartiallyPaid: Amber, Pending: Blue)
- DataTable (Columns: Course, Model, Terms Formula, Accrued, Received, Balance, Status)
- PayoutReceiptItem (Date, Receipt#, Method badge, Amount in bold green, description)

# CALLS TO ACTION (CTAs):
- Row Action: "View Accrual Audit" -> Opens modal detailing session logs or collection records that generated the accrued amount.
- Header Action: "Download Earnings Statement" -> Generates printable PDF summary of instructor's compensation.

# FORM VALIDATION & BUSINESS RULES:
- Read-Only Security Boundary: Instructors cannot edit or create payout records; this screen is strictly read-only.
- Strict Personal Scoping: Instructors can NEVER see other instructors' earnings, center-level profit margins, or student tuition records.
- Model Formulas:
  - `Fixed`: Shows full contractual fee.
  - `PerSession`: Shows exact formula `CompletedSessionsCount * RatePerSession`.
  - `Percentage`: Shows `VerifiedCollectedRevenue * RatePercentage`.

# NAVIGATION & ROUTING RULES:
- Clicking a course title navigates to `/instructor/courses/[id]`.
- All interactions remain within the personal earnings scope.

# COMPREHENSIVE STATES:
- Default State: Populated financial metrics, 2 course agreements, 2 historical payouts.
- Loading State: Skeleton shimmer loading across metric cards and table rows.
- Empty State (New Instructor): "No earnings or compensation agreements recorded yet. Assigned cohorts will appear here."

# RESPONSIVE BEHAVIOR:
- Mobile (<640px): Summary metrics stack; table converts to compensation cards with bold remaining balance; payout history displays as a clean timeline.
- Desktop (1025px+): Comprehensive multi-column table with right-aligned numeric data.

# ACCESSIBILITY (A11y) & WCAG 2.1 AA:
- Monetary values formatted with `<data value="2250.00">` and clear currency labels (`NIS`).
- Status pills maintain high contrast (exceeding WCAG 2.1 AA requirements).
````


---

# SECTION 6: TRAINEE PORTAL, LEARNING & BALANCE TRACKING (STUDENT FLOW)

---

### SCR-ST-01: Student Portal Dashboard

````text
# SCREEN IDENTIFIER: SCR-ST-01
# SCREEN TITLE: Student Portal Command Center
# TARGET ROUTE: /student/dashboard
# PRIMARY ACTOR: Enrolled Student (`STUDENT`)
# PURPOSE: Provide enrolled trainees with a clear personal learning portal, displaying active cohort progress, upcoming lecture schedule, pending homework deadlines, personal attendance percentage, and tuition balance standing.

# VISUAL & LAYOUT HIERARCHY:
- Layout: App Shell (Shared Top Header + Student-Filtered Sidebar).
- Top Bar: VELORA Logo, Center Badge "Beacon Training Center", Dashboard Link (active), Notifications (2 new), User Avatar "Tariq Yousef Al-Sayed (Student)".
- Sidebar Navigation: Dashboard (active), My Courses, Materials, Tasks, Attendance, Feedback, Payments & Balance. (All administrative and teaching tools are hidden).
- Main Canvas:
  1. Welcome Banner: Greeting "Welcome back, Tariq!", Subtitle "Your active learning journey and academic schedule", Next Lecture Pill ("Next: Today @ 18:00 in Lab 3").
  2. Trainee Metric Summary (4 Cards): Active Enrolled Courses (1), Overall Attendance Rate (92%), Tasks Submitted (3/3), Tuition Balance Due (₪ 1,200).
  3. Two-Column Learning Hub:
     - Left Column (60%): "Upcoming Lecture Sessions" (Next 2 lectures with topic, date, room, and syllabus link).
     - Right Column (40%): "Active Assignments & Deadlines" (Tasks due soon with live countdown timer and "Submit Assignment" button).
  4. Quick Materials Strip: Latest 3 lecture slides published by instructors with direct download buttons.

# DOMAIN DATA & MOCK ENTITIES:
- Student Identity: UserID: "USR-STU-001", Name: "Tariq Yousef Al-Sayed", Role: "STUDENT"
- Active Enrolled Course:
  - Course: "Full-Stack Web Development Bootcamp (FSW-2026-C4)"
  - Instructor: "Eng. Omar Farooq" | Progress: Session 8 of 24 (33%)
- Upcoming Lectures:
  - Session 8: Today, Oct 24 @ 18:00 - 21:00 | Topic: "REST APIs & Express Middleware" | Location: "Lab 3"
  - Session 9: Mon, Oct 27 @ 18:00 - 21:00 | Topic: "PostgreSQL & Database Migrations" | Location: "Lab 3"
- Active Tasks & Deadlines:
  - Task 2: "Build RESTful Authentication API" | Status: "Submitted (Under Review)" | Due: "Oct 23 (Closed)"
  - Task 3: "Database Schema Design" | Status: "Pending Submission" | Due: "Oct 28 at 23:59 (4 days remaining)"
- Tuition Ledger Context:
  - Course Fee: "₪ 3,200" | Paid: "₪ 2,000" | Remaining Balance: "₪ 1,200" | Status: "Partially Paid"

# DESIGN SYSTEM COMPONENTS:
- TraineeMetricCard (White elevated card, bold metrics, colored progress chips)
- NextLectureBanner (Gradient-free indigo card, clock icon, calendar icon, map-pin icon)
- DeadlineCard (Border-amber-200, clock icon, task title, countdown badge "4 days left", CTA button)
- MaterialDownloadPill (File icon, title, file size, download icon)
- PrimaryButton ("Submit Task", Upload icon, Indigo-600)

# CALLS TO ACTION (CTAs):
- Primary CTA: "Submit Assignment" -> Navigates to `/student/tasks?taskId=TSK-003`.
- Card CTA: "View Course Page" -> Navigates to `/student/courses/CRS-FSW-004`.
- Balance CTA: "View Tuition Receipts" -> Navigates to `/student/payments`.

# FORM VALIDATION & BUSINESS RULES:
- Scoping: Trainee sees ONLY data belonging to their own active enrollments (`Enrollment.StudentID == CurrentUser.ID`).
- Privacy: Trainees have ZERO visibility into other students' attendance, tasks, grades, feedback, or financial payments.
- Read-only overview with action triggers.

# NAVIGATION & ROUTING RULES:
- Clicking a course card routes to `/student/courses/[id]`.
- Clicking "Tasks" routes to `/student/tasks`.

# COMPREHENSIVE STATES:
- Default State: Populated dashboard with 1 active course, next lecture today, 1 task due in 4 days.
- Loading State: Skeleton cards with smooth animated shimmer across all widgets.
- Empty State (No Active Courses): "You are not currently enrolled in any active courses. Please contact center sales."
- Zero Balance State: Tuition card shows green checkmark: "Tuition fully settled (₪ 0.00 balance)!".

# RESPONSIVE BEHAVIOR:
- Mobile (<640px): Sidebar moves to off-canvas drawer; metric cards scroll horizontally; lecture cards and deadlines stack in single column with full-width buttons.
- Desktop (1025px+): 60/40 two-column layout with fixed next-lecture alert bar.

# ACCESSIBILITY (A11y) & WCAG 2.1 AA:
- Next lecture card uses semantic `<section aria-labelledby="next-lecture-heading">`.
- Deadline countdown includes accessible screen reader description.
````

---

### SCR-ST-02: My Enrolled Courses (Student)

````text
# SCREEN IDENTIFIER: SCR-ST-02
# SCREEN TITLE: My Enrolled Courses
# TARGET ROUTE: /student/courses
# PRIMARY ACTOR: Enrolled Student (`STUDENT`)
# PURPOSE: Display a dedicated catalog of training programs the student is actively enrolled in or has completed, showing cohort progress, schedule, instructor details, and direct access to course learning workspaces.

# VISUAL & LAYOUT HIERARCHY:
- Layout: App Shell (Shared Top Header + Student Sidebar with "My Courses" active).
- Main Canvas:
  1. Page Header: Title "My Enrolled Courses", Subtitle "Access curriculum, materials, assignments, and attendance for your cohorts", Right Indicator: "1 Active Enrollment".
  2. Tab Bar: "Active Courses (1)", "Completed Courses (0)".
  3. Enrolled Courses Grid: Clean, high-contrast cards representing enrolled cohorts with visual progress bars.
  4. Quick Summary Chips on each card: Instructor Name, Schedule, Attendance %, Tasks Completed, Course Workspace Link.

# DOMAIN DATA & MOCK ENTITIES:
- Enrolled Course Records:
  - Course 1: ID: "CRS-FSW-004", Code: "FSW-2026-C4", Title: "Full-Stack Web Development Bootcamp", Dates: "Oct 15, 2026 - Jan 15, 2027", Schedule: "Mon / Wed / Sat, 18:00 - 21:00", Room: "Lab 3", LeadInstructor: "Eng. Omar Farooq", CompletedSessions: 8, TotalSessions: 24 (33% Complete), AttendanceRate: "92% (7/8 Sessions Attended)", TasksSubmitted: "2 of 3 Completed", Status: "Active"

# DESIGN SYSTEM COMPONENTS:
- CourseEnrollmentCard (Elevated white surface, border-slate-200, hover:border-indigo-400 transition)
- ProgressBar (Indigo visual bar with text: "33% Complete (Session 8 of 24)")
- StatChip (Attendance: Green, Tasks: Blue, Schedule: Slate)
- PrimaryButton ("Go to Course Workspace", ArrowRight icon, Indigo-600)

# CALLS TO ACTION (CTAs):
- Primary CTA: "Enter Course Workspace" -> Navigates to `/student/courses/CRS-FSW-004`.
- Quick CTA: "Download Materials" -> Navigates directly to Materials tab.
- Quick CTA: "View Tasks" -> Navigates directly to Tasks tab.

# FORM VALIDATION & BUSINESS RULES:
- Scoping: Displays strictly courses where `Enrollment.StudentID == CurrentUser.ID` and `Enrollment.Status IN ('Active', 'Completed')`.
- Students cannot view or access courses they have not enrolled in.

# NAVIGATION & ROUTING RULES:
- Clicking card or "Enter Course Workspace" button opens `/student/courses/[id]`.
- Tabs filter between active cohorts and past completed programs.

# COMPREHENSIVE STATES:
- Default State: 1 active course card with 33% progress bar and 92% attendance rate.
- Loading State: Skeleton card with shimmer animation.
- Empty State: "You have no active course enrollments at this time. Contact your center advisor to enroll."
- Completed State: Displays certificates or final completion status when cohort ends.

# RESPONSIVE BEHAVIOR:
- Mobile (<640px): 1-column card layout; progress bar takes 100% width; buttons are stacked with touch-friendly dimensions.
- Desktop (1025px+): Wide card layout with clear horizontal separation between schedule and progress indicators.

# ACCESSIBILITY (A11y) & WCAG 2.1 AA:
- Progress bar includes `role="progressbar"`, `aria-valuenow="33"`, and `aria-label="Course completion: 33%"`.
- Card container is fully keyboard navigable.
````

---

### SCR-ST-03: Course Learning Hub (Student)

````text
# SCREEN IDENTIFIER: SCR-ST-03
# SCREEN TITLE: Course Learning Hub (Student Workspace)
# TARGET ROUTE: /student/courses/[id]
# PRIMARY ACTOR: Enrolled Student (`STUDENT`)
# PURPOSE: Serve as the student's unified academic learning center for an enrolled course, providing tabbed navigation across lecture materials, homework tasks, session attendance history, and private instructor feedback.

# VISUAL & LAYOUT HIERARCHY:
- Layout: App Shell (Shared Top Header + Student Sidebar).
- Breadcrumbs: "My Courses > Full-Stack Web Development Bootcamp (FSW-2026-C4)"
- Top Course Banner:
  - Course Title, Code, Lead Instructor "Eng. Omar Farooq", Schedule "Mon/Wed/Sat 18:00 - 21:00", Room "Lab 3".
  - Quick Progress Indicator: "Session 8 of 24 | Attendance: 92%".
- Tabbed Navigation Bar:
  1. Materials & Resources (Downloadable slides, code archives, readings)
  2. Tasks & Homework (Assignments list, submission status, upload buttons)
  3. My Attendance (Lecture-by-lecture attendance record)
  4. Instructor Feedback (Private qualitative feedback from instructors)
- Active Tab Body Container (White card, rounded-xl, p-6, border-slate-200).

# DOMAIN DATA & MOCK ENTITIES:
- Course Context:
  - ID: "CRS-FSW-004", Code: "FSW-2026-C4", Title: "Full-Stack Web Development Bootcamp"
  - Instructor: "Eng. Omar Farooq" (Lead Instructor)
- Tab 1: Materials (4 Available Files):
  - `Lecture08_Express_Middleware.pdf` (4.2 MB) | Uploaded: Today
  - `Auth_API_Starter_Code.zip` (12.8 MB) | Uploaded: Today
- Tab 2: Tasks (3 Tasks):
  - Task 1: "Git Workflow & Branching" | Status: "Submitted & Reviewed (Grade: Excellent)"
  - Task 2: "Build RESTful Auth API" | Status: "Submitted (Under Review)"
  - Task 3: "Database Schema Design" | Status: "Pending Submission" | Due: Oct 28
- Tab 3: Attendance:
  - 7 Sessions Present, 1 Session Absent (Excused) | Overall Rate: 92%
- Tab 4: Private Feedback:
  - 2 Notes from Eng. Omar Farooq regarding technical progress and code modularity.

# DESIGN SYSTEM COMPONENTS:
- BreadcrumbNav
- TabGroup (Underline style, 4 tabs with active indigo highlight and count badges)
- CourseBannerCard (Border-slate-200, instructor avatar, schedule tags)
- StatusBadge (Submitted: Blue, Reviewed: Green, Pending: Amber, Absent: Red)
- PrimaryButton, SecondaryOutlineButton

# CALLS TO ACTION (CTAs):
- Tab CTA (Materials): "Download All Materials (.zip)" -> Downloads authenticated course bundle.
- Tab CTA (Tasks): "Submit Assignment" -> Opens submission modal (`SCR-ST-05`).
- Breadcrumb Action: "Back to My Courses" -> Returns to `/student/courses`.

# FORM VALIDATION & BUSINESS RULES:
- Enrollment Verification: Student must have an active enrollment in `CRS-FSW-004`. Unenrolled students are rejected with 403 Forbidden.
- Tab Isolation: Tabs switch instantly without page reload.
- Privacy: All data displayed is strictly personalized to this student.

# NAVIGATION & ROUTING RULES:
- Clicking tab changes active view within `/student/courses/[id]`.
- Deep links (e.g. `/student/courses/[id]?tab=tasks`) open the specified tab directly.

# COMPREHENSIVE STATES:
- Default State: Materials tab open by default showing today's lecture resources.
- Tab Transition: Clean smooth transition between Materials, Tasks, Attendance, and Feedback.
- Loading State: Skeleton card headers and animated placeholder lines.
- Error State: 403 Forbidden alert if student attempts to access a course they are not enrolled in.

# RESPONSIVE BEHAVIOR:
- Mobile (<640px): Tab list becomes horizontally scrollable; banner text stacks; file items use full card width.
- Desktop (1025px+): Wide banner with horizontal tab navigation and fixed action buttons.

# ACCESSIBILITY (A11y) & WCAG 2.1 AA:
- WAI-ARIA compliant tablist with `aria-controls` and `aria-selected` attributes.
- Arrow keys navigate between tabs.
````

---

### SCR-ST-04: Course Materials & Downloads (Student)

````text
# SCREEN IDENTIFIER: SCR-ST-04
# SCREEN TITLE: Course Materials & Downloads
# TARGET ROUTE: /student/materials
# PRIMARY ACTOR: Enrolled Student (`STUDENT`)
# PURPOSE: Provide enrolled students with an organized directory of all published lecture slides, reference readings, code repositories, and syllabus files across their enrolled courses, with secure authenticated downloads.

# VISUAL & LAYOUT HIERARCHY:
- Layout: App Shell (Shared Top Header + Student Sidebar with "Materials" active).
- Main Canvas:
  1. Page Header: Title "Course Materials & Resources", Subtitle "Download lecture presentations, reference guides, and source code", Right Indicator: "6 Available Files".
  2. Cohort Selector / Filter: Course dropdown ("Full-Stack Web Dev - Cohort 4"), Search by file name input.
  3. Grouped Materials List: Files organized chronologically by Lecture Session (e.g. "Session 8: REST APIs", "Session 7: Relational Databases", "Session 3: TypeScript").
  4. File Item Card / Row: File Type Icon, Title, Associated Session, File Size, Upload Date, "Download" Button (Secondary Outline with download icon).

# DOMAIN DATA & MOCK ENTITIES:
- Course Context: "Full-Stack Web Dev - Cohort 4"
- Available Downloadable Materials:
  - Session 8:
    - File 1: `Lecture08_Express_Middleware.pdf` | Type: PDF | Size: "4.2 MB" | Uploaded: "Oct 24, 2026"
    - File 2: `Auth_API_Starter_Code.zip` | Type: ZIP | Size: "12.8 MB" | Uploaded: "Oct 24, 2026"
  - Session 7:
    - File 3: `Database_ERD_Guide.pdf` | Type: PDF | Size: "2.1 MB" | Uploaded: "Oct 21, 2026"
  - Session 3:
    - File 4: `TypeScript_CheatSheet.docx` | Type: DOCX | Size: "1.4 MB" | Uploaded: "Oct 18, 2026"
  - General Resources:
    - File 5: `Course_Syllabus_and_Grading_Policy.pdf` | Type: PDF | Size: "850 KB" | Uploaded: "Oct 15, 2026"

# DESIGN SYSTEM COMPONENTS:
- FileTypeIcon (PDF: Red, ZIP: Amber, DOCX: Blue, General: Slate)
- SearchBar (Instant file name lookup)
- SessionGroupHeader (Sticky module/session divider)
- DownloadButton (White surface, border-slate-300, text-slate-700, DownloadCloud icon, hover:bg-slate-50)
- AuthenticatedDownloadLink (Secure, signed token URL generator)

# CALLS TO ACTION (CTAs):
- Primary CTA: "Download File" -> Generates secure time-limited signed URL and triggers browser file download.
- Search Action: "Search Files" -> Filters materials in real-time as user types.

# FORM VALIDATION & BUSINESS RULES:
- Scoping & Authorization Gate: Students can ONLY view and download materials for courses where they have an `Active` enrollment.
- Backend Storage Security: Download URLs are short-lived signed tokens; direct unauthorized storage access is blocked.
- Read-only interface.

# NAVIGATION & ROUTING RULES:
- Clicking "Download" triggers file download without navigating away.
- Course selector dropdown switches between enrolled cohorts.

# COMPREHENSIVE STATES:
- Default State: Display of 5 files grouped under session headings.
- Downloading State: Button shows small spinner and "Downloading...".
- Empty State: "No materials published for this cohort yet. Check back after your next lecture session."
- Search Empty State: "No files found matching '[query]'. [Clear Search]".

# RESPONSIVE BEHAVIOR:
- Mobile (<640px): File rows display as compact cards; download button is a prominent full-width touch element with file size clearly labeled.
- Desktop (1025px+): Structured list with file metadata and right-aligned download buttons.

# ACCESSIBILITY (A11y) & WCAG 2.1 AA:
- Download links include descriptive text (e.g. `aria-label="Download Lecture08 Express Middleware, PDF format, 4.2 megabytes"`).
- Keyboard accessible: Enter/Space triggers download.
````

---

### SCR-ST-05: Tasks & Homework Submissions (Student)

````text
# SCREEN IDENTIFIER: SCR-ST-05
# SCREEN TITLE: Tasks & Homework Submissions Portal
# TARGET ROUTE: /student/tasks
# PRIMARY ACTOR: Enrolled Student (`STUDENT`)
# PURPOSE: Enable students to view assigned homework tasks, inspect assignment guidelines and attachments, upload solution files before deadlines, replace submitted files on-time, view deadline locks, utilize late overrides when authorized, and review private instructor evaluations.

# VISUAL & LAYOUT HIERARCHY:
- Layout: App Shell (Shared Top Header + Student Sidebar with "Tasks" active).
- Main Canvas:
  1. Page Header: Title "My Tasks & Assignments", Subtitle "Submit homework, track deadlines, and review instructor evaluations", Right Filter: Course selector.
  2. Task Status Filter Tabs: "All Tasks (3)", "Pending Submission (1)", "Under Review (1)", "Evaluated (1)".
  3. Tasks List / Cards: Task Title, Course, Due Date, Compliance Status Badge (`Pending`, `Submitted On-Time`, `Submitted Late`, `Past Deadline - Locked`, `Reviewed`), Action CTA.
  4. Submission Modal Window: "Submit Assignment Solution" (File drag & drop, student comments textarea, submit button).

# DOMAIN DATA & MOCK ENTITIES:
- Course Context: "Full-Stack Web Dev - Cohort 4"
- Sample Tasks Roster:
  - Task 1: ID: "TSK-001", Title: "Git Workflow & Team Branching", DueDate: "Oct 18, 2026", Status: "Evaluated", Submission: `tariq_git_workflow.zip`, InstructorFeedback: "Flawless pull request workflow and commit history. 10/10."
  - Task 2: ID: "TSK-002", Title: "Build RESTful Authentication API", DueDate: "Oct 23, 2026 (Passed Yesterday)", Status: "Submitted On-Time", SubmittedAt: "Oct 23, 21:14", File: `tariq_auth_api.zip` (3.4 MB), StatusNote: "Awaiting Instructor Review"
  - Task 3: ID: "TSK-003", Title: "Database Schema Design & ERD", DueDate: "Oct 28, 2026 @ 23:59", Status: "Pending Submission", TimeRemaining: "4 days remaining", Instructions: "Design a relational schema for a multi-tenant logistics portal. Submit DBML or PDF ERD diagram.", Attachment: `schema_spec.pdf`
  - Task 4 (Simulated Past Deadline): ID: "TSK-004", Title: "TypeScript Advanced Generics", DueDate: "Oct 12, 2026", Status: "Past Deadline - Locked", LateOverrideActive: false

# DESIGN SYSTEM COMPONENTS:
- TaskCard (Elevated white surface, deadline clock icon, status badge, action CTA)
- ComplianceBadge:
  - Pending: Amber
  - Submitted On-Time: Green
  - Submitted Late (Override): Amber
  - Locked / Expired: Red
  - Evaluated: Blue
- ModalDialog (Width: 560px, Title: "Submit Assignment: Database Schema Design")
- DragAndDropZone (Dashed border, UploadCloud icon, "Upload solution file (Max: 25MB)")
- TextareaField ("Notes for Instructor (Optional)")
- PrimaryButton ("Submit Assignment", Indigo-600)

# CALLS TO ACTION (CTAs):
- Card CTA (Pending): "Upload Submission" -> Opens submission modal.
- Card CTA (On-Time Submitted): "Replace Submission" -> Allows student to upload an updated file before deadline.
- Card CTA (Evaluated): "View Evaluation" -> Opens dialog with instructor's qualitative feedback.
- Modal CTA: "Confirm & Upload Solution" -> Validates file size (<=25MB) and type, commits submission.

# FORM VALIDATION & BUSINESS RULES:
- Strict Deadline Lock & Replacement Rules:
  - Before Deadline: Student can submit and can freely replace/overwrite their submitted file.
  - Past Deadline (Standard): Submission portal is automatically locked. Upload button is disabled with text: "Deadline has passed. Submissions are closed."
  - Past Deadline (With Override): If instructor enabled `LateOverrideAllowed == true`, the upload button is re-enabled with an amber badge: "Late Submission Authorized by Instructor".
- File Size & Type Validation:
  - Max size: Strictly 25 MB (`<= 25MB`).
  - Allowed extensions: `.pdf`, `.doc`, `.docx`, `.zip`, `.png`, `.jpg`, `.jpeg`.
  - Executable files are blocked.

# NAVIGATION & ROUTING RULES:
- Submitting updates the task card status to `Submitted On-Time` with timestamp.
- Success toast: "Task solution for [Title] submitted successfully."

# COMPREHENSIVE STATES:
- Default State: 3 tasks displayed with appropriate deadline chips.
- Modal Open State: File drop zone active, shows 4 days countdown.
- Locked State (Expired Task): "Submissions closed. Contact instructor for a late override."
- Evaluated State: Card displays instructor's feedback note in an indented quote card.

# RESPONSIVE BEHAVIOR:
- Mobile (<640px): Task cards stack; modal is full-width; file upload provides camera/files selector.
- Desktop (1025px+): Structured list with clear deadline columns and side-by-side status badges.

# ACCESSIBILITY (A11y) & WCAG 2.1 AA:
- Drag and drop input includes native keyboard `<input type="file">` alternative.
- Countdown timer uses `aria-live="off"` to prevent annoying screen reader announcements.
````

---

### SCR-ST-06: Personal Attendance Record (Student)

````text
# SCREEN IDENTIFIER: SCR-ST-06
# SCREEN TITLE: Personal Attendance Record
# TARGET ROUTE: /student/attendance
# PRIMARY ACTOR: Enrolled Student (`STUDENT`)
# PURPOSE: Provide enrolled students with complete transparency into their lecture-by-lecture attendance history, showing overall attendance percentage, attended vs absent sessions, and verified instructor absence notes.

# VISUAL & LAYOUT HIERARCHY:
- Layout: App Shell (Shared Top Header + Student Sidebar with "Attendance" active).
- Main Canvas:
  1. Page Header: Title "My Attendance Record", Subtitle "Verify your lecture attendance history and compliance percentage", Right Indicator: "Course: Full-Stack Web Dev".
  2. Attendance Performance Summary (3 Metric Cards): Overall Attendance Rate (92%), Total Sessions Held (8 Sessions), Attended Sessions (7 Present / 1 Absent).
  3. Visual Compliance Meter: Circular progress ring or bold horizontal bar showing 92% attendance against center minimum threshold (80%).
  4. Lecture-by-Lecture Attendance Log: Chronological table listing Session #, Date, Time, Topic, Attendance Status Badge (`Present` / `Absent`), and Excused Notes.

# DOMAIN DATA & MOCK ENTITIES:
- Course Context: "Full-Stack Web Dev - Cohort 4"
- Personal Attendance Metrics:
  - Overall Rate: "92%" (Threshold for graduation certificate: 80%)
  - Attended: "7 Sessions Present"
  - Missed: "1 Session Absent (Excused)"
- Chronological Session History:
  - Session 8: Oct 24, 2026 | Topic: "REST APIs & Express Middleware" | Status: `Present` | Time: "18:00 - 21:00"
  - Session 7: Oct 21, 2026 | Topic: "PostgreSQL Relational Schema Design" | Status: `Present`
  - Session 6: Oct 18, 2026 | Topic: "Advanced Asynchronous JavaScript" | Status: `Absent` | Note: "Excused - Medical Notice Provided"
  - Session 5: Oct 15, 2026 | Topic: "TypeScript Generics & Interfaces" | Status: `Present`
  - Session 4: Oct 12, 2026 | Topic: "Node.js Core Modules & Event Loop" | Status: `Present`
  - Session 3: Oct 09, 2026 | Topic: "Git Feature Branching & CI Basics" | Status: `Present`
  - Session 2: Oct 06, 2026 | Topic: "Modern ES6+ Syntax & Tooling" | Status: `Present`
  - Session 1: Oct 03, 2026 | Topic: "Course Orientation & Setup" | Status: `Present`

# DESIGN SYSTEM COMPONENTS:
- MetricCard (White elevated card with attendance percentage in bold green)
- ComplianceMeter (Visual bar showing current % vs 80% minimum center policy threshold)
- StatusBadge:
  - Present: Green pill (Checkmark icon)
  - Absent: Red pill (X icon)
  - Excused Absent: Amber pill (Info icon)
- DataTable (Columns: Session #, Date, Topic, Status, Instructor Note)

# CALLS TO ACTION (CTAs):
- Action: "Download Attendance Report" -> Generates printable PDF summary of student attendance.
- Action: "Course Selector" -> Switches between enrolled courses.

# FORM VALIDATION & BUSINESS RULES:
- Scoping: Strictly scoped to `CurrentUser.ID` attendance records.
- Read-Only Guarantee: Students cannot edit attendance records under any circumstances.
- Threshold Policy: If attendance drops below 80%, an amber warning banner appears: "Warning: Your attendance has dropped below the 80% center completion requirement. Please consult your instructor."

# NAVIGATION & ROUTING RULES:
- Course dropdown filters session logs if student is enrolled in multiple courses.

# COMPREHENSIVE STATES:
- Default State: Populated list of 8 sessions with 92% rate and 1 excused absence note.
- Low Attendance Warning State: If rate < 80%, top banner displays amber compliance warning.
- Empty State (New Cohort): "No lecture sessions have been conducted yet. Attendance will appear after your first lecture."

# RESPONSIVE BEHAVIOR:
- Mobile (<640px): Summary cards stack; session table converts to card timeline with colored left borders (Green for Present, Red for Absent).
- Desktop (1025px+): Tabular layout with clear column alignment and bold status badges.

# ACCESSIBILITY (A11y) & WCAG 2.1 AA:
- Status badges use icons and text labels (never color alone).
- Table has explicit column headers with `scope="col"`.
````

---

### SCR-ST-07: Private Instructor Feedback View (Student)

````text
# SCREEN IDENTIFIER: SCR-ST-07
# SCREEN TITLE: Private Instructor Feedback View
# TARGET ROUTE: /student/feedback
# PRIMARY ACTOR: Enrolled Student (`STUDENT`)
# PURPOSE: Provide students with a private, confidential feed of 1:1 constructive evaluations, academic recommendations, and mentorship feedback submitted by their course instructors.

# VISUAL & LAYOUT HIERARCHY:
- Layout: App Shell (Shared Top Header + Student Sidebar with "Feedback" active).
- Main Canvas:
  1. Page Header: Title "Instructor Feedback & Mentorship", Subtitle "Confidential 1:1 evaluations and recommendations from your instructors", Right Badge: "Privacy: Visible Only to You".
  2. Cohort Filter: Course dropdown ("Full-Stack Web Dev - Cohort 4").
  3. Privacy Guarantee Banner: Blue-50 container explaining that this feedback is strictly confidential and never shared with other students or sales staff.
  4. Chronological Feedback Feed: Stream of feedback cards, each featuring the instructor's avatar, date, category tag, constructive feedback text, and verified signature.

# DOMAIN DATA & MOCK ENTITIES:
- Student Scope: `CurrentUser.ID == USR-STU-001` (Tariq Yousef Al-Sayed)
- Confidential Feedback Items:
  - Feedback 1: Date: "Oct 18, 2026 (6 days ago)" | Instructor: "Eng. Omar Farooq" | Course: "Full-Stack Web Dev - Cohort 4" | Category: "Academic Performance" | Note Text: "Tariq demonstrates outstanding comprehension of TypeScript generics and async patterns. Advised him to focus more on SQL indexing strategies in Module 2."
  - Feedback 2: Date: "Oct 10, 2026 (2 weeks ago)" | Instructor: "Eng. Omar Farooq" | Course: "Full-Stack Web Dev - Cohort 4" | Category: "Attendance & Engagement" | Note Text: "Consistently punctual and actively contributes during live coding sessions. Great initiative in peer discussions."

# DESIGN SYSTEM COMPONENTS:
- FeedbackCard (White surface, border-slate-200, rounded-xl, p-6, shadow-sm)
- InstructorSignatureBadge (Avatar, Instructor Name, Role "Lead Instructor", Verified Badge)
- CategoryTag (Academic: Indigo, Behavioral: Blue, Mentorship: Slate)
- PrivacyAlertBox (Blue-50 bg, Blue-800 text, ShieldCheck icon)

# CALLS TO ACTION (CTAs):
- Action: "Filter by Course" -> Drops down list of enrolled courses.
- Action: "Acknowledge Note" -> Optional confirmation button indicating student has read the advice.

# FORM VALIDATION & BUSINESS RULES:
- Strict 1:1 Privacy:
  - This feed displays ONLY feedback where `RecipientStudentID == CurrentUser.ID`.
  - Trainees CANNOT see any other student's feedback notes.
  - Read-only interface for students.

# NAVIGATION & ROUTING RULES:
- Notifications linking to new feedback navigate directly to this screen.

# COMPREHENSIVE STATES:
- Default State: Display of 2 private feedback notes in reverse chronological order.
- Loading State: Skeleton cards with shimmer animation.
- Empty State: "No feedback notes recorded yet. Your instructor's qualitative evaluations will appear here as the cohort progresses."

# RESPONSIVE BEHAVIOR:
- Mobile (<640px): Feedback cards use full screen width; avatar and metadata stack above feedback body.
- Desktop (1025px+): Centered reading canvas (max-w-4xl) with comfortable line height and generous padding.

# ACCESSIBILITY (A11y) & WCAG 2.1 AA:
- Each feedback entry uses semantic `<article aria-labelledby="feedback-date">`.
- High contrast typography (Slate-900 on White) meets WCAG AAA for body text.
````

---

### SCR-ST-08: Student Tuition Ledger & Receipts

````text
# SCREEN IDENTIFIER: SCR-ST-08
# SCREEN TITLE: Student Tuition Ledger & Receipts
# TARGET ROUTE: /student/payments
# PRIMARY ACTOR: Enrolled Student (`STUDENT`)
# PURPOSE: Provide enrolled trainees with complete financial transparency into their course tuition fees, applied discount grants, total verified payments, outstanding balance due, and official receipt vouchers issued by center management.

# VISUAL & LAYOUT HIERARCHY:
- Layout: App Shell (Shared Top Header + Student Sidebar with "Payments & Balance" active).
- Main Canvas:
  1. Page Header: Title "Tuition Ledger & Receipts", Subtitle "Verify your course fees, payment receipts, and outstanding tuition balance", Right Badge: "Currency: NIS (₪)".
  2. Financial Ledger Card (Comprehensive Summary Banner):
     - Base Catalog Tuition: "₪ 3,200.00"
     - Applied Discount: "- ₪ 0.00"
     - Net Final Payable: "₪ 3,200.00"
     - Verified Payments Paid: "₪ 2,000.00"
     - Remaining Balance Due: "₪ 1,200.00" (Bold Amber)
     - Overall Payment Status Badge: `Partially Paid`
  3. Offline Payment Instructions Notice: Informational card explaining how to make installment payments at the center front-desk or via bank deposit.
  4. Historical Approved Receipts Table: Receipt #, Payment Date, Amount Paid, Payment Method, Reference / Slip #, Manager Approval Status, Action ("Download Receipt Voucher").

# DOMAIN DATA & MOCK ENTITIES:
- Student Scope: `CurrentUser.ID == USR-STU-001` (Tariq Yousef Al-Sayed)
- Enrollment Ledger Breakdown:
  - Course: "Full-Stack Web Development Bootcamp (FSW-2026-C4)"
  - Base Catalog Price: "₪ 3,200.00"
  - Applied Discount: "₪ 0.00"
  - Total Payable: "₪ 3,200.00"
  - Total Paid: "₪ 2,000.00"
  - Remaining Balance: "₪ 1,200.00"
  - Status: "PartiallyPaid"
- Historical Approved Receipts:
  - Receipt 1: Receipt#: "REC-2026-042" | Date: "2026-10-15" | Amount: "₪ 1,000.00" | Method: "Cash" | Status: "Completed (Approved by Manager)" | Slip: "CSH-1015-01"
  - Receipt 2: Receipt#: "REC-2026-018" | Date: "2026-10-01" | Amount: "₪ 1,000.00" | Method: "POS Card Terminal" | Status: "Completed (Approved by Manager)" | Slip: "POS-9921-TX"
- Center Offline Payment Deposit Instructions:
  - Cash / POS: "Visit Center Front-Desk (Sun-Thu, 09:00 - 18:00)"
  - Bank Deposit: "Bank of Palestine | Account: 1234567 | IBAN: PS00PALS00000000001234567"
  - Note: "Always request an official stamped receipt from center sales upon payment."

# DESIGN SYSTEM COMPONENTS:
- LedgerSummaryCard (Multi-stat banner: Base Price, Discount, Total Payable, Total Paid, Remaining Due)
- PaymentStatusBadge (Fully Paid: Green, Partially Paid: Amber, Unpaid: Red)
- DataTable (Columns: Receipt #, Date, Amount Paid, Method, Approval Status, Action)
- PaymentInstructionsCard (Border-blue-200, bg-blue-50/50, Bank icon, clear deposit steps)
- SecondaryOutlineButton ("Download Receipt PDF", DownloadCloud icon)

# CALLS TO ACTION (CTAs):
- Row Action: "Download Official Receipt Voucher" -> Generates printable PDF receipt with center stamp watermark and receipt reference.
- Header Action: "Print Complete Statement" -> Prints full tuition ledger history.

# FORM VALIDATION & BUSINESS RULES:
- Scoping: Scoped strictly to `CurrentUser.ID` financial ledger. Students have zero visibility into other trainees' financial records.
- Completed Payments Only: Only payments approved by Center Manager (`Status == 'Completed'`) appear on this ledger and reduce the student's remaining balance. Vouchers pending approval or rejected vouchers do not affect student balance.
- Read-only interface for trainees (all payments are collected offline at center desk).

# NAVIGATION & ROUTING RULES:
- Course selector dropdown toggles ledger between enrolled courses if student has multiple active enrollments.

# COMPREHENSIVE STATES:
- Default State: Ledger shows ₪ 1,200 remaining balance and 2 approved receipts totaling ₪ 2,000.
- Fully Paid State: Remaining balance displays "₪ 0.00" with a green `Fully Paid` badge and "Tuition Settled" message.
- Loading State: Skeleton shimmer loading across the ledger card and receipts table.
- Empty State: "No payment receipts found for this course cohort."

# RESPONSIVE BEHAVIOR:
- Mobile (<640px): Ledger card breaks into stacked horizontal metrics; receipts table converts into card vouchers with bold amounts and direct download buttons.
- Desktop (1025px+): Wide banner with 5-part financial calculation strip and structured receipt table.

# ACCESSIBILITY (A11y) & WCAG 2.1 AA:
- Currency numbers formatted with clear semantic tags and `aria-label="1,200 New Israeli Shekels outstanding"`.
- Table headers include `scope="col"`.
````

---

# SECTION 7: GLOBAL ACCOUNT & COMMUNICATION HUB (SHARED FLOW)

---

### SCR-SHARED-01: Notifications Center

````text
# SCREEN IDENTIFIER: SCR-SHARED-01
# SCREEN TITLE: Notifications Center
# TARGET ROUTE: /notifications
# PRIMARY ACTOR: All Roles (`MANAGER`, `SALES`, `INSTRUCTOR`, `STUDENT`)
# PURPOSE: Provide a centralized notification center accessible to all authenticated users from the global header, presenting role-scoped operational, academic, and financial alerts with read/unread indicators and direct deep links.

# VISUAL & LAYOUT HIERARCHY:
- Layout: App Shell (Shared Top Header with active bell icon + Shared Role-Filtered Sidebar).
- Main Canvas:
  1. Page Header: Title "Notification Center", Subtitle "Stay updated on tasks, session schedules, payment approvals, and center alerts", Right Actions: "Mark All as Read" (Secondary Outline).
  2. Filter Tabs: "All Notifications (12)", "Unread Only (4)", "Academic Alerts", "Financial & Payments".
  3. Notifications Stream: Clean chronological list of notification items with semantic category icons, timestamp, bold message, unread blue dot indicator, and action link.

# DOMAIN DATA & MOCK ENTITIES:
- Role-Scoped Notification Examples:
  - If Manager:
    - Item 1 (Unread): "Payment Approval Required: Sara Al-Khatib submitted voucher REC-2026-090 (₪ 1,400.00 with ₪ 200 excess) for review." -> Link: `/manager/payments`
    - Item 2 (Unread): "Course Capacity Alert: Full-Stack Web Dev has reached 20/20 capacity. 4 students waiting." -> Link: `/manager/enrollments`
  - If Sales:
    - Item 1 (Unread): "Payment Voucher Approved: Manager approved voucher REC-2026-089 (₪ 1,500.00) for Rami Shaker." -> Link: `/sales/payments`
    - Item 2: "Payment Rejected: Voucher REC-2026-085 was rejected. Rationale: 'Slip illegible'." -> Link: `/sales/payments`
  - If Instructor:
    - Item 1 (Unread): "New Submission: Tariq Yousef Al-Sayed submitted Task 2: Build RESTful Auth API." -> Link: `/instructor/tasks`
    - Item 2: "Session Reminder: Lecture Session 8 scheduled for today at 18:00 in Lab 3." -> Link: `/instructor/attendance`
  - If Student:
    - Item 1 (Unread): "New Material Published: Eng. Omar Farooq uploaded 'Lecture08_Express_Middleware.pdf'." -> Link: `/student/materials`
    - Item 2 (Unread): "Feedback Received: New 1:1 evaluation note added by Eng. Omar Farooq." -> Link: `/student/feedback`
    - Item 3: "Payment Approved: Your offline tuition receipt REC-2026-042 for ₪ 1,000.00 was approved." -> Link: `/student/payments`

# DESIGN SYSTEM COMPONENTS:
- NotificationItemCard (Unread: bg-indigo-50/40 border-indigo-200; Read: bg-white border-slate-200)
- CategoryIcon (Financial: Amber Landmark, Academic: Indigo BookOpen, System: Slate Bell, Task: Blue CheckCircle)
- UnreadIndicatorDot (Solid blue-600 circle, 8px)
- FilterTabs (Underline style with unread badge counters)
- SecondaryOutlineButton ("Mark All as Read", CheckCheck icon)

# CALLS TO ACTION (CTAs):
- Primary Action: Click Notification Item -> Marks item as read, decrements top header unread counter, navigates directly to target screen.
- Header CTA: "Mark All as Read" -> Updates all unread notifications to read state, clears unread badges.
- Tab Filter: "Unread Only" -> Filters list to unread items.

# FORM VALIDATION & BUSINESS RULES:
- Role & Tenant Scoping: Notifications are strictly filtered by current `UserID`, `Role`, and `CenterID`.
- No cross-tenant notification leakage.
- Automatic counter sync: Reading an item immediately updates the global header notification badge count.

# NAVIGATION & ROUTING RULES:
- Clicking a notification deep-links directly to the relevant record or workspace.

# COMPREHENSIVE STATES:
- Default State: Populated list of notifications, 4 unread items with blue highlight at top.
- Filtered State (Unread Only): Displays only the 4 unread alerts.
- Empty State (Zero Notifications): Bell illustration: "You're all caught up! No notifications to display."
- Loading State: Skeleton rows with pulsing placeholders.

# RESPONSIVE BEHAVIOR:
- Mobile (<640px): Notification items fill 100% width; tap area spans entire card; filter tabs scroll horizontally.
- Desktop (1025px+): Constrained reading width (max-w-4xl) with right-aligned action buttons and timestamps.

# ACCESSIBILITY (A11y) & WCAG 2.1 AA:
- Unread items have `aria-label="Unread notification: [Message]"`.
- Global unread badge has `role="status"` and `aria-live="polite"`.
````

---

### SCR-SHARED-02: User Profile Management

````text
# SCREEN IDENTIFIER: SCR-SHARED-02
# SCREEN TITLE: User Profile Management
# TARGET ROUTE: /profile
# PRIMARY ACTOR: All Roles (`MANAGER`, `SALES`, `INSTRUCTOR`, `STUDENT`)
# PURPOSE: Enable authenticated users across all roles to view and update their personal contact information (Full Name, Phone Number, Profile Avatar placeholder) and update their account password.

# VISUAL & LAYOUT HIERARCHY:
- Layout: App Shell (Shared Top Header + Role-Filtered Sidebar with User Menu trigger).
- Main Canvas:
  1. Page Header: Title "My Profile & Account", Subtitle "Manage your personal contact information and login credentials", Right Indicator: Role Badge (`MANAGER` / `SALES` / `INSTRUCTOR` / `STUDENT`).
  2. Profile Overview Card: User Avatar, Full Name, Work Email (read-only), Assigned Center ("Beacon Training Center"), System Role.
  3. Two-Section Form Layout:
     - Section 1: "Personal Information" (Full Name, Phone Number, Bio / Notes).
     - Section 2: "Security & Password" (Current Password, New Password, Confirm New Password).
  4. Bottom Action Bar: "Save Profile Changes" (Primary Indigo), "Discard Changes" (Ghost).

# DOMAIN DATA & MOCK ENTITIES:
- Sample Profile Context (Manager):
  - User ID: "USR-001" | Role: "MANAGER" | Center: "Beacon Training Center (CTR-001)"
  - Full Name: "Ahmad Qasim"
  - Work Email: "ahmad.manager@beacon.edu" (Read-only, managed by center admin)
  - Mobile Phone: "+970-599-112233"
  - Member Since: "January 10, 2026"
- Password Change Fields:
  - Current Password: "••••••••••••"
  - New Password: "••••••••••••"
  - Confirm Password: "••••••••••••"

# DESIGN SYSTEM COMPONENTS:
- AvatarUploadWidget (Circular avatar container with Camera icon overlay)
- FormSectionCard (White elevated card, rounded-xl, p-6, border-slate-200)
- InputField (Label, icon, helper text)
- RoleBadge (Manager: Indigo, Sales: Amber, Instructor: Blue, Student: Green)
- PrimaryButton ("Save Changes", Save icon, Indigo-600)
- InlineAlert (Success: Green banner; Error: Red banner)

# CALLS TO ACTION (CTAs):
- Primary CTA: "Save Changes" -> Validates phone number and name, commits updates to database, displays success toast.
- Password CTA: "Update Password" -> Validates current password match, checks new password strength (min 8 chars), updates hash.

# FORM VALIDATION & BUSINESS RULES:
- Full Name: Mandatory, minimum 3 characters.
- Phone Number: Mandatory, valid format.
- Email: Read-only in profile screen to prevent tenant mismatch or orphaned auth records.
- Password Rules:
  - If changing password, `Current Password` is strictly required.
  - `New Password` must be at least 8 characters.
  - `Confirm Password` must match `New Password` exactly.

# NAVIGATION & ROUTING RULES:
- Access via Top Header User Menu -> "Profile".
- Saving profile updates user's display name in top header dynamically.

# COMPREHENSIVE STATES:
- Default State: Populated fields with user's current data.
- Saving State: "Save Changes" button disabled with spinner.
- Error State (Password Mismatch): Red field border on Confirm Password: "New passwords do not match."
- Success State: Green toast banner: "Profile details updated successfully."

# RESPONSIVE BEHAVIOR:
- Mobile (<640px): Profile overview stacks vertically; inputs take 100% width; buttons stick to bottom.
- Desktop (1025px+): 2-column form grid; max-w-4xl centered container.

# ACCESSIBILITY (A11y) & WCAG 2.1 AA:
- Password inputs include explicit labels and show/hide toggles.
- Form fields have matching `for` and `id` associations.
````

---

### SCR-SHARED-03: Account & Display Settings

````text
# SCREEN IDENTIFIER: SCR-SHARED-03
# SCREEN TITLE: Account & Display Settings
# TARGET ROUTE: /settings
# PRIMARY ACTOR: All Roles (`MANAGER`, `SALES`, `INSTRUCTOR`, `STUDENT`)
# PURPOSE: Provide users with basic MVP account preferences, including display theme confirmation, notification digest preferences, and regional date/time format options.

# VISUAL & LAYOUT HIERARCHY:
- Layout: App Shell (Shared Top Header + Role-Filtered Sidebar).
- Main Canvas:
  1. Page Header: Title "Account Settings", Subtitle "Customize your application experience and notification preferences", Right Action: "Save Preferences" (Primary Indigo).
  2. Settings Sections (3 Cards):
     - Section 1: "Interface & Appearance" (Theme selection: Clean Light Enterprise default; Language: English LTR default).
     - Section 2: "Notification Preferences" (Toggle operational email alerts, toggle task deadline reminders, toggle payment status digests).
     - Section 3: "Regional & Localization" (Currency indicator: NIS ₪; Timezone: Jerusalem/Gaza UTC+2; Date Format: DD/MM/YYYY).
  3. Sticky Save Footer: "Save Settings" button.

# DOMAIN DATA & MOCK ENTITIES:
- Preferences Defaults:
  - Interface Theme: "Light Enterprise (Default)"
  - Language: "English (LTR)"
  - Notification Toggles:
    - Email Alerts for Urgent Tasks: Checked (True)
    - Session Reminders (2 hours before lecture): Checked (True)
    - Weekly Summary Digest: Unchecked (False)
  - Regional Settings:
    - Default Center Currency: "NIS (₪)" (Locked by center configuration)
    - Timezone: "Jerusalem / Gaza (UTC+02:00)"
    - Date Display Format: "DD/MM/YYYY (e.g. 24/10/2026)"

# DESIGN SYSTEM COMPONENTS:
- FormSectionCard (White surface, border-slate-200, rounded-xl, p-6)
- SwitchToggle (Label, description, active indigo state)
- RadioCardGroup (Appearance theme selection cards with small visual preview)
- SelectDropdown (Date format options)
- PrimaryButton ("Save Preferences", Indigo-600)

# CALLS TO ACTION (CTAs):
- Primary CTA: "Save Preferences" -> Persists user preferences, displays success toast.
- Reset CTA: "Restore Defaults" -> Resets toggles to center default configuration.

# FORM VALIDATION & BUSINESS RULES:
- Scoping: Preferences are scoped to the authenticated user's profile (`UserPreference`).
- Currency is strictly read-only (determined by center configuration `CTR-001`).
- Language is locked to English for MVP per PRD specifications.

# NAVIGATION & ROUTING RULES:
- Accessible via Top Header User Menu -> "Settings".
- Saving updates local storage and user preferences immediately.

# COMPREHENSIVE STATES:
- Default State: Display of configured toggles and light theme selection.
- Saving State: Button displays spinner and "Saving Preferences...".
- Success State: Toast: "Account preferences saved successfully."

# RESPONSIVE BEHAVIOR:
- Mobile (<640px): Settings cards stack; toggle switches provide large touch targets (>=48px); save button sticks to screen bottom.
- Desktop (1025px+): Clean centered layout (max-w-4xl).

# ACCESSIBILITY (A11y) & WCAG 2.1 AA:
- Toggles use `<button role="switch" aria-checked="true">` with descriptive labels.
- Radio card groups use `<div role="radiogroup">`.
````
