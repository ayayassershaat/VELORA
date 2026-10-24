# Screen Inventory - VELORA MVP

**Document Version:** 1.0.0  
**Status:** Approved for MVP Implementation  
**Date:** October 2026  
**References:** [PRD.md](../PRD.md), [UI_UX_REQUIREMENTS.md](UI_UX_REQUIREMENTS.md), [UX_BLUEPRINT.md](UX_BLUEPRINT.md)

---

## Table of Contents

1. [Overview](#1-overview)
2. [Authentication](#2-authentication)
3. [Shared](#3-shared)
4. [Manager Screens](#4-manager-screens)
5. [Sales Screens](#5-sales-screens)
6. [Instructor Screens](#6-instructor-screens)
7. [Student Screens](#7-student-screens)
8. [Summary](#8-summary)

---

## 1. Overview

This inventory specifies every MVP screen with ID, purpose, roles, route, components, data, actions, navigation, validation, states, accessibility, and responsive behavior. All items are strictly within MVP scope.

**Priority:** `CORE` | `SUPPORTING`  
**Layout:** Uses shared Header/Sidebar per UI requirements

---

## 2. Authentication

| Field | Details |
| :--- | :--- |
| **Screen ID** | `SCR-AUTH-01` |
| **Screen Name** | Login |
| **Priority** | CORE |
| **Purpose** | Authenticate user with email/password, establish session, route by role |
| **Roles** | All (unauthenticated) |
| **Route** | `/login` |
| **Layout** | Auth layout (no sidebar/header for app nav) |
| **Components** | Logo, Email input, Password input, Login button, Error message area |
| **Data** | Auth credentials (email, password); session returns user(role, centerId) |
| **Actions** | Submit login, handle errors, redirect by role |
| **Navigation** | On success → role dashboard. No app nav shown. |
| **Validation** | Required email/password; reject inactive/suspended; rate-limit on failures |
| **States** | Default, Loading, Error, Rate-limited |
| **Accessibility** | Labels, focus states, aria-live for errors |
| **Responsive** | Centered card on mobile/desktop |

---

## 3. Shared

| Field | Details |
| :--- | :--- |
| **Screen ID** | `SCR-SHARED-01` |
| **Screen Name** | Notifications |
| **Priority** | SUPPORTING |
| **Purpose** | View role-relevant notifications (tasks, materials, payments, approvals) |
| **Roles** | MANAGER, SALES, INSTRUCTOR, STUDENT |
| **Route** | `/notifications` (accessible via header) |
| **Layout** | App layout with Header/Sidebar |
| **Components** | Notification list, timestamps, read/unread indicator |
| **Data** | Notifications filtered by role/center/user |
| **Actions** | Mark read, navigate to related item |
| **Navigation** | From header bell → this screen |
| **Validation** | N/A |
| **States** | Loading, Empty, List |
| **Accessibility** | List semantics, keyboard navigation |
| **Responsive** | Full width on mobile, constrained on desktop |

| Field | Details |
| :--- | :--- |
| **Screen ID** | `SCR-SHARED-02` |
| **Screen Name** | Profile |
| **Priority** | SUPPORTING |
| **Purpose** | View/update basic profile info |
| **Roles** | MANAGER, SALES, INSTRUCTOR, STUDENT |
| **Route** | `/profile` |
| **Layout** | App layout |
| **Components** | Profile form, save button |
| **Data** | User profile (name/email/contact) |
| **Actions** | Update profile, change password (if exposed) |
| **Navigation** | Header user menu → Profile |
| **Validation** | Required fields, email format |
| **States** | View/Edit, Loading, Success/Error |
| **Accessibility** | Form labels, error associations |
| **Responsive** | Stacked on mobile, form layout on desktop |

| Field | Details |
| :--- | :--- |
| **Screen ID** | `SCR-SHARED-03` |
| **Screen Name** | Settings |
| **Priority** | SUPPORTING |
| **Purpose** | Basic account settings |
| **Roles** | MANAGER, SALES, INSTRUCTOR, STUDENT |
| **Route** | `/settings` |
| **Layout** | App layout |
| **Components** | Settings sections |
| **Data** | User preferences (minimal for MVP) |
| **Actions** | Save settings |
| **Navigation** | Header user menu → Settings |
| **Validation** | As applicable |
| **States** | Default, Saving, Success |
| **Accessibility** | Section headings, controls labeled |
| **Responsive** | Responsive layout |

---

## 4. Manager Screens

| Field | Details |
| :--- | :--- |
| **Screen ID** | `SCR-M-01` |
| **Screen Name** | Manager Dashboard |
| **Priority** | CORE |
| **Purpose** | Executive overview of center operations/finances |
| **Roles** | MANAGER |
| **Route** | `/manager/dashboard` |
| **Layout** | App layout (Header + Sidebar) |
| **Components** | Summary cards (courses/students/instructors), Financial summary (invoiced/collected/receivables/liabilities), Alerts (overdue balances/courses starting/unassigned) |
| **Data** | Aggregated metrics scoped to center |
| **Actions** | Navigate to detailed sections |
| **Navigation** | Default post-login; header Dashboard |
| **Validation** | N/A |
| **States** | Loading, Data, Empty |
| **Accessibility** | Card headings, landmarks |
| **Responsive** | Cards stack on mobile, grid on desktop |

| Field | Details |
| :--- | :--- |
| **Screen ID** | `SCR-M-02` |
| **Screen Name** | Courses List |
| **Priority** | CORE |
| **Purpose** | List/manage courses by lifecycle status |
| **Roles** | MANAGER |
| **Route** | `/manager/courses` |
| **Layout** | App layout |
| **Components** | Table/list with filters (status), Create Course button, Actions (view/edit/assign/archive) |
| **Data** | Courses (id, name, code, status, dates, capacity, enrolled) |
| **Actions** | Create, filter, view details, assign instructors, archive |
| **Navigation** | Sidebar → Courses |
| **Validation** | N/A |
| **States** | Loading, Empty, List, Error |
| **Accessibility** | Table headers, action labels |
| **Responsive** | Cards on mobile, table on desktop |

| Field | Details |
| :--- | :--- |
| **Screen ID** | `SCR-M-03` |
| **Screen Name** | Course Details |
| **Priority** | CORE |
| **Purpose** | Manage course details, instructors, sessions, enrollments |
| **Roles** | MANAGER |
| **Route** | `/manager/courses/[id]` |
| **Layout** | App layout |
| **Components** | Tabs (Overview, Instructors, Sessions, Enrollments, Materials/Tasks summary), action buttons |
| **Data** | Course, assignments, sessions, enrollments |
| **Actions** | Edit course, assign instructor, schedule session, activate/archive |
| **Navigation** | From Courses list |
| **Validation** | Dates/price rules; >=1 instructor for activation |
| **States** | Loading, View, Edit |
| **Accessibility** | Tabs accessible, forms labeled |
| **Responsive** | Stacked sections on mobile |

| Field | Details |
| :--- | :--- |
| **Screen ID** | `SCR-M-04` |
| **Screen Name** | Instructors List |
| **Priority** | CORE |
| **Purpose** | Manage instructors and compensation overview |
| **Roles** | MANAGER |
| **Route** | `/manager/instructors` |
| **Layout** | App layout |
| **Components** | Instructors table, assignments summary |
| **Data** | Instructors, course assignments, compensation models |
| **Actions** | View details, navigate to dues |
| **Navigation** | Sidebar → Instructors |
| **Validation** | N/A |
| **States** | Loading, List, Empty |
| **Accessibility** | Table semantics |
| **Responsive** | Cards/table responsive |

| Field | Details |
| :--- | :--- |
| **Screen ID** | `SCR-M-05` |
| **Screen Name** | Students List |
| **Priority** | CORE |
| **Purpose** | Center student directory |
| **Roles** | MANAGER |
| **Route** | `/manager/students` |
| **Layout** | App layout |
| **Components** | Search/filter, students table |
| **Data** | Students, enrollments summary |
| **Actions** | View student profile/enrollments/payments |
| **Navigation** | Sidebar → Students |
| **Validation** | N/A |
| **States** | Loading, List, Empty |
| **Accessibility** | Search labeled, table headers |
| **Responsive** | Responsive |

| Field | Details |
| :--- | :--- |
| **Screen ID** | `SCR-M-06` |
| **Screen Name** | Enrollments |
| **Priority** | CORE |
| **Purpose** | Manage enrollments/discounts/waitlist |
| **Roles** | MANAGER |
| **Route** | `/manager/enrollments` |
| **Layout** | App layout |
| **Components** | Enrollments table, filters, actions |
| **Data** | Enrollments with course/student/status/balances |
| **Actions** | Enroll (if needed), apply discount, manage waitlist |
| **Navigation** | Sidebar → Enrollments |
| **Validation** | Discount <= base price; no duplicates |
| **States** | Loading, List |
| **Accessibility** | Filters labeled |
| **Responsive** | Responsive |

| Field | Details |
| :--- | :--- |
| **Screen ID** | `SCR-M-07` |
| **Screen Name** | Payments (Manager) |
| **Priority** | CORE |
| **Purpose** | Review pending approvals, manage payments/refunds |
| **Roles** | MANAGER |
| **Route** | `/manager/payments` |
| **Layout** | App layout |
| **Components** | Pending approvals queue, All payments, Refunds section |
| **Data** | Payments (status/method/amount/ref), linked refunds |
| **Actions** | Approve/Reject (with reason), view details, track refunds |
| **Navigation** | Sidebar → Payments |
| **Validation** | Reason required on reject; audit logged |
| **States** | Loading, Queue, Details, Action states |
| **Accessibility** | Action buttons labeled, confirmations |
| **Responsive** | Stacked on mobile |

| Field | Details |
| :--- | :--- |
| **Screen ID** | `SCR-M-08` |
| **Screen Name** | Instructor Dues |
| **Priority** | CORE |
| **Purpose** | View liabilities and settle payouts |
| **Roles** | MANAGER |
| **Route** | `/manager/instructor-dues` |
| **Layout** | App layout |
| **Components** | Liabilities summary, instructor dues table, settlement form |
| **Data** | InstructorDue (accrued/paid/outstanding/status), assignments, payouts |
| **Actions** | Record settlement payout, view history |
| **Navigation** | Sidebar → Instructor Dues |
| **Validation** | Positive amounts, required fields |
| **States** | Loading, List, Settlement modal |
| **Accessibility** | Forms labeled, tables accessible |
| **Responsive** | Responsive |

| Field | Details |
| :--- | :--- |
| **Screen ID** | `SCR-M-09` |
| **Screen Name** | Reports |
| **Priority** | SUPPORTING |
| **Purpose** | Essential operational/financial summaries |
| **Roles** | MANAGER |
| **Route** | `/manager/reports` |
| **Layout** | App layout |
| **Components** | Report cards/sections (students, courses, payments, dues) |
| **Data** | Aggregated reports scoped to center |
| **Actions** | View details |
| **Navigation** | Sidebar → Reports |
| **Validation** | N/A |
| **States** | Loading, Report view |
| **Accessibility** | Headings, tables |
| **Responsive** | Responsive |

| Field | Details |
| :--- | :--- |
| **Screen ID** | `SCR-M-10` |
| **Screen Name** | Staff & Users |
| **Priority** | CORE |
| **Purpose** | Manage staff accounts (Manager/Sales/Instructor) |
| **Roles** | MANAGER |
| **Route** | `/manager/staff` |
| **Layout** | App layout |
| **Components** | Staff table, add/edit user form |
| **Data** | Users with roles scoped to center |
| **Actions** | Create/update staff accounts, assign roles |
| **Navigation** | Sidebar → Staff & Users |
| **Validation** | Email unique, role required, status valid |
| **States** | Loading, List, Form |
| **Accessibility** | Forms labeled |
| **Responsive** | Responsive |

| Field | Details |
| :--- | :--- |
| **Screen ID** | `SCR-M-11` |
| **Screen Name** | Audit Log |
| **Priority** | CORE |
| **Purpose** | View immutable audit trail of key actions/transactions |
| **Roles** | MANAGER |
| **Route** | `/manager/audit` |
| **Layout** | App layout |
| **Components** | Audit table with filters (date/action/user) |
| **Data** | AuditLog entries (immutable) |
| **Actions** | Filter/search |
| **Navigation** | Sidebar → Audit Log |
| **Validation** | N/A |
| **States** | Loading, List, Empty |
| **Accessibility** | Table with timestamps |
| **Responsive** | Responsive |

---

## 5. Sales Screens

| Field | Details |
| :--- | :--- |
| **Screen ID** | `SCR-S-01` |
| **Screen Name** | Sales Dashboard |
| **Priority** | CORE |
| **Purpose** | Focus on intake, follow-up, payments pending |
| **Roles** | SALES |
| **Route** | `/sales/dashboard` |
| **Layout** | App layout |
| **Components** | Quick actions, follow-up queue, payments pending approval status |
| **Data** | Students needing follow-up, enrollments, payments pending |
| **Actions** | Navigate to register/enroll/payments |
| **Navigation** | Default post-login; header Dashboard |
| **Validation** | N/A |
| **States** | Loading, Data |
| **Accessibility** | Cards/links labeled |
| **Responsive** | Responsive |

| Field | Details |
| :--- | :--- |
| **Screen ID** | `SCR-S-02` |
| **Screen Name** | Students (Sales) |
| **Priority** | CORE |
| **Purpose** | Search/register students |
| **Roles** | SALES |
| **Route** | `/sales/students` |
| **Layout** | App layout |
| **Components** | Search bar, Add Student button, Students table |
| **Data** | Students directory (scoped to center) |
| **Actions** | Search, add student, view, enroll |
| **Navigation** | Sidebar → Students |
| **Validation** | Unique email/phone within center |
| **States** | Loading, List, Form modal |
| **Accessibility** | Forms labeled, search accessible |
| **Responsive** | Responsive |

| Field | Details |
| :--- | :--- |
| **Screen ID** | `SCR-S-03` |
| **Screen Name** | Enrollments (Sales) |
| **Priority** | CORE |
| **Purpose** | Enroll students, apply discounts, manage waitlist |
| **Roles** | SALES |
| **Route** | `/sales/enrollments` |
| **Layout** | App layout |
| **Components** | Enroll form/modal, enrollments list, waitlist section |
| **Data** | Active courses, students, enrollments, waitlist |
| **Actions** | Enroll, apply discount (validate), join/handle waitlist |
| **Navigation** | Sidebar → Enrollments |
| **Validation** | No duplicate active enrollment; discount <= base price; capacity checks |
| **States** | Loading, Form, List, Waitlist |
| **Accessibility** | Modal accessible, validation messages |
| **Responsive** | Responsive |

| Field | Details |
| :--- | :--- |
| **Screen ID** | `SCR-S-04` |
| **Screen Name** | Payments (Sales) |
| **Priority** | CORE |
| **Purpose** | Record offline payments (pending approval) |
| **Roles** | SALES |
| **Route** | `/sales/payments` |
| **Layout** | App layout |
| **Components** | Record Payment form/modal, payments list with status |
| **Data** | Enrollments with balances, payments (Pending Approval/Completed/Rejected) |
| **Actions** | Record payment, view status |
| **Navigation** | Sidebar → Payments |
| **Validation** | Amount > 0; detect overpayment; reference/method required |
| **States** | Loading, Form, List |
| **Accessibility** | Forms labeled, status indicators |
| **Responsive** | Responsive |

---

## 6. Instructor Screens

| Field | Details |
| :--- | :--- |
| **Screen ID** | `SCR-I-01` |
| **Screen Name** | Instructor Dashboard |
| **Priority** | CORE |
| **Purpose** | Overview of assigned courses, sessions, tasks to review |
| **Roles** | INSTRUCTOR |
| **Route** | `/instructor/dashboard` |
| **Layout** | App layout |
| **Components** | Assigned courses cards, upcoming sessions, tasks pending review, quick links |
| **Data** | Courses assigned, sessions, submissions pending |
| **Actions** | Navigate to course/workspace |
| **Navigation** | Default post-login; header Dashboard |
| **Validation** | N/A |
| **States** | Loading, Data |
| **Accessibility** | Cards/links labeled |
| **Responsive** | Responsive |

| Field | Details |
| :--- | :--- |
| **Screen ID** | `SCR-I-02` |
| **Screen Name** | My Courses |
| **Priority** | CORE |
| **Purpose** | List assigned courses only |
| **Roles** | INSTRUCTOR |
| **Route** | `/instructor/courses` |
| **Layout** | App layout |
| **Components** | Course cards/list |
| **Data** | Assigned courses |
| **Actions** | Open course workspace |
| **Navigation** | Sidebar → My Courses |
| **Validation** | N/A |
| **States** | Loading, List, Empty |
| **Accessibility** | Links labeled |
| **Responsive** | Responsive |

| Field | Details |
| :--- | :--- |
| **Screen ID** | `SCR-I-03` |
| **Screen Name** | Course Workspace (Instructor) |
| **Priority** | CORE |
| **Purpose** | Manage course: sessions/attendance, materials, tasks, feedback |
| **Roles** | INSTRUCTOR |
| **Route** | `/instructor/courses/[id]` |
| **Layout** | App layout |
| **Components** | Tabs (Overview, Sessions/Attendance, Materials, Tasks/Submissions, Feedback) |
| **Data** | Course, sessions, roster, materials, tasks, submissions |
| **Actions** | Schedule session, take attendance, upload material, create task, review submissions, give feedback, toggle late override |
| **Navigation** | From My Courses |
| **Validation** | File size/type, deadlines, non-empty feedback, unique attendance |
| **States** | Loading, Tabbed view, Forms, Modals |
| **Accessibility** | Tabs, forms, file inputs labeled |
| **Responsive** | Responsive |

| Field | Details |
| :--- | :--- |
| **Screen ID** | `SCR-I-04` |
| **Screen Name** | Sessions & Attendance |
| **Priority** | CORE |
| **Purpose** | Take/edit session attendance |
| **Roles** | INSTRUCTOR |
| **Route** | `/instructor/attendance` (or via course workspace) |
| **Layout** | App layout |
| **Components** | Session selector, attendance roster (Present/Absent), save button |
| **Data** | Sessions, enrollments, attendance records |
| **Actions** | Mark attendance, save (upsert), trigger per-session accrual if applicable |
| **Navigation** | Sidebar → Sessions & Attendance or workspace tab |
| **Validation** | Unique per session/enrollment |
| **States** | Loading, Roster, Saving |
| **Accessibility** | Radio/controls labeled, table accessible |
| **Responsive** | Cards on mobile, table on desktop |

| Field | Details |
| :--- | :--- |
| **Screen ID** | `SCR-I-05` |
| **Screen Name** | Materials (Instructor) |
| **Priority** | CORE |
| **Purpose** | Upload/manage course materials |
| **Roles** | INSTRUCTOR |
| **Route** | `/instructor/materials` |
| **Layout** | App layout |
| **Components** | Upload form, materials list per course |
| **Data** | Materials with course/session linkage |
| **Actions** | Upload (validate size/type), view list |
| **Navigation** | Sidebar → Materials |
| **Validation** | <=25MB, allowed types (PDF/DOC/DOCX/PPT/PPTX/XLS/XLSX/JPG/JPEG/PNG/ZIP) |
| **States** | Loading, Upload, List |
| **Accessibility** | File input labeled |
| **Responsive** | Responsive |

| Field | Details |
| :--- | :--- |
| **Screen ID** | `SCR-I-06` |
| **Screen Name** | Tasks & Submissions |
| **Priority** | CORE |
| **Purpose** | Create tasks, review submissions, provide feedback/override |
| **Roles** | INSTRUCTOR |
| **Route** | `/instructor/tasks` |
| **Layout** | App layout |
| **Components** | Tasks list, create task form, submissions queue, submission review modal |
| **Data** | Tasks, submissions (status/timestamp), students |
| **Actions** | Create task, review submission, download file, give feedback, allow late override |
| **Navigation** | Sidebar → Tasks & Submissions |
| **Validation** | Deadline logic, override authorization |
| **States** | Loading, List, Review, Modal |
| **Accessibility** | Forms/modals accessible |
| **Responsive** | Responsive |

| Field | Details |
| :--- | :--- |
| **Screen ID** | `SCR-I-07` |
| **Screen Name** | Feedback (Instructor) |
| **Priority** | CORE |
| **Purpose** | Add private 1:1 feedback |
| **Roles** | INSTRUCTOR |
| **Route** | `/instructor/feedback` |
| **Layout** | App layout |
| **Components** | Student selector, feedback form (max 2000 chars) |
| **Data** | Students in assigned courses, feedback history |
| **Actions** | Submit feedback (private) |
| **Navigation** | Sidebar → Feedback |
| **Validation** | Non-empty feedback |
| **States** | Loading, Form, Success |
| **Accessibility** | Textarea labeled, char limit indicated |
| **Responsive** | Responsive |

| Field | Details |
| :--- | :--- |
| **Screen ID** | `SCR-I-08` |
| **Screen Name** | My Earnings |
| **Priority** | CORE |
| **Purpose** | View own accrued dues and payouts |
| **Roles** | INSTRUCTOR |
| **Route** | `/instructor/earnings` |
| **Layout** | App layout |
| **Components** | Earnings summary, breakdown by course, payout history |
| **Data** | InstructorDue records for instructor only, payouts |
| **Actions** | View details |
| **Navigation** | Sidebar → My Earnings |
| **Validation** | N/A (read-only) |
| **States** | Loading, Data, Empty |
| **Accessibility** | Tables/summary labeled |
| **Responsive** | Responsive |

---

## 7. Student Screens

| Field | Details |
| :--- | :--- |
| **Screen ID** | `SCR-ST-01` |
| **Screen Name** | Student Dashboard |
| **Priority** | CORE |
| **Purpose** | Overview of courses, tasks, attendance, balance |
| **Roles** | STUDENT |
| **Route** | `/student/dashboard` |
| **Layout** | App layout |
| **Components** | Enrolled courses, upcoming tasks/deadlines, attendance summary, balance card |
| **Data** | Enrollments, tasks, attendance, payments/balance |
| **Actions** | Navigate to course/tasks/payments |
| **Navigation** | Default post-login; header Dashboard |
| **Validation** | N/A |
| **States** | Loading, Data |
| **Accessibility** | Cards/links labeled |
| **Responsive** | Responsive |

| Field | Details |
| :--- | :--- |
| **Screen ID** | `SCR-ST-02` |
| **Screen Name** | My Courses (Student) |
| **Priority** | CORE |
| **Purpose** | List enrolled courses |
| **Roles** | STUDENT |
| **Route** | `/student/courses` |
| **Layout** | App layout |
| **Components** | Course cards/list |
| **Data** | Enrollments (Active/Completed) |
| **Actions** | Open course page |
| **Navigation** | Sidebar → My Courses |
| **Validation** | N/A |
| **States** | Loading, List, Empty |
| **Accessibility** | Links labeled |
| **Responsive** | Responsive |

| Field | Details |
| :--- | :--- |
| **Screen ID** | `SCR-ST-03` |
| **Screen Name** | Course Page (Student) |
| **Priority** | CORE |
| **Purpose** | Access course content: materials, tasks, attendance, feedback |
| **Roles** | STUDENT |
| **Route** | `/student/courses/[id]` |
| **Layout** | App layout |
| **Components** | Tabs (Materials, Tasks, Attendance, Feedback) |
| **Data** | Materials, tasks, submissions, attendance, feedback |
| **Actions** | Download material, submit/replace task, view attendance, view feedback |
| **Navigation** | From My Courses |
| **Validation** | File size/type, deadline enforcement |
| **States** | Loading, Tabbed, Forms |
| **Accessibility** | Tabs, links, forms labeled |
| **Responsive** | Responsive |

| Field | Details |
| :--- | :--- |
| **Screen ID** | `SCR-ST-04` |
| **Screen Name** | Materials (Student) |
| **Priority** | CORE |
| **Purpose** | Download materials for enrolled courses |
| **Roles** | STUDENT |
| **Route** | `/student/materials` |
| **Layout** | App layout |
| **Components** | Materials list grouped by course |
| **Data** | Materials for enrolled courses only |
| **Actions** | Download (protected) |
| **Navigation** | Sidebar → Materials |
| **Validation** | Access gated to enrolled courses |
| **States** | Loading, List, Empty |
| **Accessibility** | Download links labeled |
| **Responsive** | Responsive |

| Field | Details |
| :--- | :--- |
| **Screen ID** | `SCR-ST-05` |
| **Screen Name** | Tasks (Student) |
| **Priority** | CORE |
| **Purpose** | View tasks, submit/replace before deadline |
| **Roles** | STUDENT |
| **Route** | `/student/tasks` |
| **Layout** | App layout |
| **Components** | Tasks list, task detail/submission modal/form |
| **Data** | Tasks, submissions (status/timestamp) |
| **Actions** | Upload file, replace before deadline, view status/feedback if attached |
| **Navigation** | Sidebar → Tasks |
| **Validation** | File size/type, deadline/block unless override |
| **States** | Loading, List, Submission, Deadline states |
| **Accessibility** | Forms labeled, status indicators |
| **Responsive** | Responsive |

| Field | Details |
| :--- | :--- |
| **Screen ID** | `SCR-ST-06` |
| **Screen Name** | Attendance (Student) |
| **Priority** | CORE |
| **Purpose** | View personal attendance history |
| **Roles** | STUDENT |
| **Route** | `/student/attendance` |
| **Layout** | App layout |
| **Components** | Attendance summary (%), sessions list (Present/Absent) |
| **Data** | Attendance records for student |
| **Actions** | View details |
| **Navigation** | Sidebar → Attendance |
| **Validation** | N/A (read-only) |
| **States** | Loading, Data, Empty |
| **Accessibility** | Summary labeled, list accessible |
| **Responsive** | Responsive |

| Field | Details |
| :--- | :--- |
| **Screen ID** | `SCR-ST-07` |
| **Screen Name** | Feedback (Student) |
| **Priority** | CORE |
| **Purpose** | View private feedback from instructors |
| **Roles** | STUDENT |
| **Route** | `/student/feedback` |
| **Layout** | App layout |
| **Components** | Feedback list (course/instructor/date) |
| **Data** | Private feedback for student only |
| **Actions** | View details |
| **Navigation** | Sidebar → Feedback |
| **Validation** | N/A (private, scoped) |
| **States** | Loading, List, Empty |
| **Accessibility** | List accessible, privacy noted |
| **Responsive** | Responsive |

| Field | Details |
| :--- | :--- |
| **Screen ID** | `SCR-ST-08` |
| **Screen Name** | Payments & Balance (Student) |
| **Priority** | CORE |
| **Purpose** | View fee ledger, payments, balance |
| **Roles** | STUDENT |
| **Route** | `/student/payments` |
| **Layout** | App layout |
| **Components** | Balance summary (Base/Discount/Payable/Paid/Remaining/Status), payments receipts list |
| **Data** | Enrollment ledger, verified payments (Completed) |
| **Actions** | View receipt details |
| **Navigation** | Sidebar → Payments & Balance |
| **Validation** | N/A (read-only) |
| **States** | Loading, Data, Empty |
| **Accessibility** | Summary tables/lists labeled |
| **Responsive** | Responsive |

---

## 8. Summary

- **Total MVP Screens:** 32 (Auth:1, Shared:3, Manager:11, Sales:4, Instructor:8, Student:8)
- **Core Priority:** Majority are CORE; only Notifications/Profile/Settings/Reports are SUPPORTING but required for MVP usability per UI requirements
- **Navigation:** Shared header (Dashboard, Notifications, User Menu) + shared filtered sidebar per role
- **Key Constraints Enforced:** Tenant isolation, RBAC, approval workflow for payments, deadline enforcement with overrides, private feedback, instructor dues scoped to owner, attendance uniqueness
- **Responsive:** Mobile-first with breakpoints 360px+, sidebar collapsible/off-canvas on mobile
- **Accessibility:** Semantic structure, labels, focus states, validation messages
- **All screens align with PRD MVP scope and UI_UX_REQUIREMENTS**