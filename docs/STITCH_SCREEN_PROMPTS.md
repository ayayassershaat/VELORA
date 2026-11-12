# VELORA — Google Stitch Screen Prompts (MVP)

**Status:** Approved source set: PRD, `SCREEN_INVENTORY.md`, and `DESIGN_SYSTEM.md`  
**Scope:** Exactly the 35 screens in the approved inventory, including its three supporting shared screens.  
**Use:** Each `text` block is standalone and individually copyable. Generate one screen per prompt. Generate UI only; do not generate frontend code.

## Shared design and product rules

Every prompt below inherits these rules; keep them intact in every generated screen:

- Product: VELORA (فيلورا), training-center operations platform. Arabic is the product language and the default interface is native Arabic RTL. Keep identifiers, emails, file names, codes, and currency values legible in their natural direction within RTL layouts. Do not translate entity IDs or invent English-first UI.
- Apply the approved Design System: enterprise clarity and high information density; no decorative clutter. Canvas `#F8FAFC`, white surfaces `#FFFFFF`, subdued surface `#F1F5F9`, subtle border `#E2E8F0`, primary text `#0F172A`, secondary `#334155`, muted `#64748B`; brand indigo `#4F46E5` (hover `#4338CA`); success `#16A34A` / `#DCFCE7`, warning `#D97706` / `#FEF3C7`, danger `#DC2626` / `#FEE2E2`, informational `#2563EB` / `#DBEAFE`.
- Use the approved Arabic-capable sans-serif stack, type scale, 4px spacing baseline, 8px control and 12px card radii, and restrained elevation. Use consistent line icons; mirror directional icons for RTL, but do not mirror logos, numbers, or media.
- Authenticated screens use the fixed 64px white top bar, VELORA logo, active center badge, global search, notification bell, and user menu. Use a 256px right-hand sidebar on desktop with links filtered to the actor's role; unauthorized links are hidden, never disabled. Main canvas scrolls independently. Keep the sign-in screen outside this app shell.
- Keep tenant data scoped to the signed-in center. Never expose another center's data. Preserve role privacy: Sales cannot see instructor dues or private academic feedback; feedback is private 1:1; students only see their own records; instructors only see assigned courses and their enrolled students.
- Financial actions represent verified offline transactions only. Payment entry creates `Pending Approval`; manager approval/rejection is separate. Show payable, paid, and remaining balances clearly. Display overpayment as a warning and its linked refund request; never imply online checkout. Posted financial records are not hard-deleted.
- Use the inventory's route, purpose, role, content, data, components, actions and validation. Do not add MVP features, fields, links, metrics, or controls unsupported by the sources. Use plausible Arabic sample labels and data; clearly mark all examples as mock data.
- Every screen design must show its active/populated, loading, empty, error/validation, and success feedback states. Loading uses content-shaped skeletons, not a blocking full-screen spinner. Empty states include a concise explanation and only an in-scope next action. Field errors appear beside fields; operation errors use clear alerts/toasts. Do not invent success for an action the actor cannot perform.
- Responsive behavior: 360–639px mobile uses a hamburger/right-side navigation drawer, one-column content, stacked data cards instead of wide tables, full-width dialogs/drawers, and thumb-reachable sticky actions when the screen has a primary action. 640–1023px tablet uses collapsible navigation, two-column metric grids, and contained horizontal table scrolling. 1024–1439px desktop shows full right sidebar and data columns. At 1440px+, center content within 1600px max width.
- Accessibility: WCAG 2.1 AA; 4.5:1 text contrast and 3:1 controls/status; minimum 44×44px touch targets; visible keyboard focus; logical RTL keyboard order; associated labels and help/error text; semantic headings/tables; announce changing results and confirmations with appropriate live regions; never rely on color alone. Dialogs trap focus and close with Escape when safe.
- Use NIS only where the source center configuration specifies it. Dates/timezone follow center configuration. Do not fabricate calculations; use the given sample values consistently or display neutral placeholders.

## User-flow order

Authentication → manager foundation and course setup → sales intake, enrollment and payment entry → manager enrollment/financial control → instructor delivery → student learning and tuition → shared account and notifications.

---

## 1. Authentication and entry

### SCR-AUTH-01 — Login & Session Authentication

```text
Create one responsive VELORA sign-in screen. ID: SCR-AUTH-01. Title: تسجيل الدخول. Route: /login. Actor: unauthenticated user for any center role. Purpose: validate email and password, establish the session, and route the user to the dashboard permitted for their role.

Hierarchy and content: standalone centered sign-in card, VELORA mark/wordmark, center identity when provided, concise page title, email and password fields (password visibility toggle), remember-me option only if present in the approved inventory, primary sign-in action, and a reserved inline feedback area. Do not add password recovery or registration features.
Data: email, password, remember-me if applicable; no prefilled credentials. Components: labeled inputs, accessible password toggle, checkbox, primary button, inline alert.
CTA and validation: Sign in; email required and valid, password required and at least 8 characters. On success route by role to its authorized dashboard. Invalid credentials and suspended account use the approved explanatory error; do not reveal whether an email exists.
Navigation: entry from /login; success routes to /manager/dashboard, /sales/dashboard, /instructor/dashboard, or /student/dashboard according to role.
States: empty/default; button-level loading and duplicate-submit prevention; invalid credentials or suspended-account alert; success/redirect confirmation. Include a network/server retry error.
Responsive and accessibility: follow shared rules; focus email on entry, submit by Enter, label every field, and announce errors accessibly. Preserve RTL while keeping email text LTR.
```

## 2. Manager foundation and course setup

### SCR-M-01 — Manager Executive Dashboard

```text
Create one responsive screen. ID: SCR-M-01. Title: لوحة تحكم المدير التنفيذية. Route: /manager/dashboard. Actor: MANAGER. Purpose: action-oriented overview of center operations and urgent approvals.

Hierarchy: app shell with manager-only sidebar; page title and date/context; four KPI cards; urgent task/approval panel; today's sessions list; links to relevant operational records. Content/data: active course count, active student count, cash collected this month, pending approvals, today's sessions, near-capacity courses or urgent approval count only where available. Keep metrics traceable and center-scoped.
Components: KPI cards, compact session table/list, status badges, task panel, links/buttons to course catalog, enrollments and payment approvals. No invented download/export action.
CTA/navigation: open course catalog or a specific course; review full payment approval queue; review enrollments. Quick approval only if explicitly supported by the source flow and show a confirmation step. Sidebar links remain manager-only.
Validation: manager authorization and active-center scope. States: populated; skeletons matching KPI/list geometry; no urgent items message; load failure with retry; successful navigation/action feedback.
Responsive: metrics 4/2/1 columns by desktop/tablet/mobile; session rows become cards. Accessibility: KPI labels and values read together; semantic list/table and non-color status cues. Follow all shared rules.
```

### SCR-M-10 — Staff & Users Management

```text
Create one screen. ID: SCR-M-10. Title: إدارة موظفي المركز والصلاحيات. Route: /manager/staff. Actor: MANAGER. Purpose: manage center staff accounts and their permitted roles/status.

Hierarchy/content: page heading and Add Staff action; searchable staff table with name, email, phone, role, and Active/Suspended status; add/edit staff side drawer. Data: StaffUser fields from inventory; supported staff roles SALES and MANAGER. Components: table/cards, role/status badges, labeled form, role selector, status control, drawer.
CTA/navigation: add or edit staff; suspend/reactivate account if supported. Remain on staff directory after save. No invitations, payroll, or role types beyond the approved set.
Validation: required identity fields; valid email/phone; unique email; role required. Confirm suspension. States: populated; skeleton rows; no staff with Add Staff CTA; duplicate/invalid field and server errors; saved confirmation.
Responsive: desktop table, mobile staff cards and full-screen drawer with sticky save actions. Accessibility: switch state announced, drawer focus trap/Escape, field errors associated. Follow shared rules.
```

### SCR-M-04 — Instructors Directory & Compensation

```text
Create one screen. ID: SCR-M-04. Title: سجل المحاضرين وعقود الاستحقاق. Route: /manager/instructors. Actor: MANAGER. Purpose: maintain instructor profiles and approved compensation terms.

Hierarchy/content: heading and Add Instructor action; directory table/cards showing name, phone, specialization, assigned courses if available, compensation model and contract value. Drawer/modal for add/edit profile and compensation. Data: Instructor; models Fixed, Percentage, PerSession and their configured values. Keep compensation information manager-only.
Components: searchable directory, model/status badges, profile/contract form, course assignment entry only where supported by inventory. CTA: add/edit instructor or contract; link to manager dues ledger. Navigation: /manager/instructor-dues.
Validation: required profile fields; compensation value positive; percentage cannot exceed 100%; preserve saved terms. States: populated, skeleton, no instructors with Add CTA, field/server error, save success.
Responsive: table to profile cards; form becomes full-width drawer/dialog on mobile. Accessibility: model/value labels explicit; keyboard-operable controls and focus management. Follow shared rules.
```

### SCR-M-02 — Courses Catalog & Lifecycle

```text
Create one screen. ID: SCR-M-02. Title: فهرس الدورات التدريبية. Route: /manager/courses. Actor: MANAGER. Purpose: browse center courses and start a course draft.

Hierarchy/content: heading and Create Draft Course action; filter/search controls supported by inventory; course table or responsive cards with title, code, base price, capacity, enrolled count, dates and lifecycle status. Show capacity as enrolled/capacity with accessible progress. Components: filters, table/cards, status badges, progress indicator, create-draft dialog/drawer.
CTA/navigation: create a draft or open course workspace at /manager/courses/[id]. No unrelated catalog, marketing, or course purchase actions.
Validation: base price >= 0; capacity >= 1; preserve draft status. States: populated, skeleton, empty catalog with create-draft CTA, validation/server error, draft-created success.
Responsive: three/two/one card columns or table-to-cards; filters wrap. Accessibility: progress has value/max text; table headers and status labels. Follow shared rules.
```

### SCR-M-03 — Course Details, Sessions & Activation

```text
Create one screen. ID: SCR-M-03. Title: مساحة عمل الدورة وتفعيلها. Route: /manager/courses/[id]. Actor: MANAGER. Purpose: manage a course's setup and operational details in one course-scoped workspace.

Hierarchy/content: course identity/status and overview; six tabs from inventory: overview, instructors, sessions, students, materials, tasks; activate action with readiness status. Data: course details, assigned instructors/contracts as visible to manager, scheduled sessions, enrolled students, published material/task summaries. Components: tab bar, summary cards, session list, enrollment list, assignment/scheduling forms/dialogs.
CTA/navigation: edit course details; assign instructor; schedule session; open relevant attendance/workspace; activate course. Validation: activation blocked until at least one instructor and one session are set; show exact missing requirement. Do not activate silently.
States: populated; skeleton; empty tab with only relevant in-scope action; access/not-found or save errors; saved/activated confirmation. Responsive tabs scroll or become compact; lists stack on mobile. Accessibility: tab keyboard behavior, clear disabled explanation for unmet activation prerequisites. Follow shared rules.
```

## 3. Sales intake, enrollment and payment entry

### SCR-S-01 — Sales Operations Dashboard

```text
Create one screen. ID: SCR-S-01. Title: لوحة عمليات المبيعات والتحصيل. Route: /sales/dashboard. Actor: SALES. Purpose: prioritize student follow-up, recent registration work, and collection tasks.

Hierarchy/content: role-filtered app shell; heading; compact summary of overdue installment accounts and recent registrations; prominent student search; actionable follow-up list. Data is restricted to student/enrollment/payment information needed by Sales. Never show instructor dues or academic feedback.
Components: search field, summary cards, overdue/recent list, status badges, links to student directory and payment entry. CTA/navigation: find a student (/sales/students), open enrollment (/sales/enrollments/new), record offline payment (/sales/payments/new) when a student is selected.
Validation: role and center scope. States: populated; skeleton; no overdue items with calm message; data-load error/retry; clear success feedback after supported action.
Responsive: single-column task cards and touch-sized quick actions. Accessibility: descriptive phone/contact action names if displayed, semantic data and status. Follow shared rules.
```

### SCR-S-02 — Student Intake & Directory (Sales)

```text
Create one screen. ID: SCR-S-02. Title: دليل الطلاب واستكشافهم. Route: /sales/students. Actor: SALES (manager access follows manager routes). Purpose: find existing students and begin intake without duplicate profiles.

Hierarchy/content: page heading; real-time multi-field search; student directory; Register New Student action; rows/cards show full name, phone, email, national ID where configured, course count and outstanding balance. Data: StudentProfile only; no feedback or instructor compensation.
Components: debounced search, results table/cards, payment-status indicator, new-student form entry, link/action to enrollment flow. CTA/navigation: open student record or start enrollment at /sales/enrollments/new. Empty search offers register-new-student path.
Validation: prevent duplicate phone/email; validate required profile fields. States: results; skeleton; no matches with new student option; validation/server error; profile created/search refreshed confirmation.
Responsive: mobile contact cards show name, phone and balance; filters stack. Accessibility: announce result count via polite live region; label search and controls. Follow shared rules.
```

### SCR-S-03 — Course Enrollment, Discounts & Waitlist

```text
Create one screen. ID: SCR-S-03. Title: معالج إلحاق الطالب بالدورة. Route: /sales/enrollments/new. Actor: SALES and MANAGER. Purpose: select a student/course, calculate the enrollment price, and enroll or place on waitlist according to capacity.

Hierarchy/content: three-step flow (student, course, financial terms); visible step progress; course capacity and waitlist status; sticky live financial summary. Data: StudentID, CourseID, BasePrice, DiscountAmount/Type, FinalPayable, CapacityStatus. Components: student selector, course selector, discount controls, capacity indicator, summary, drawer with sticky actions.
CTA/navigation: continue/back; confirm active enrollment or FIFO waitlist placement; on completion proceed to offline payment entry only if the approved flow offers it, otherwise return with confirmation. Do not add automatic online payment.
Validation: discount cannot exceed base price; block duplicate active enrollment with clear conflict; full course permits waitlist only. States: each step; skeleton options; no eligible courses/students; duplicate/capacity/field/server errors; enrollment or waitlist success.
Responsive: full-screen drawer on mobile; sticky bottom step actions. Accessibility: announce step and recalculated total; focus management; labels for capacity and price. Follow shared rules.
```

### SCR-S-04 — Offline Payment Entry & Overpayment Check

```text
Create one screen. ID: SCR-S-04. Title: قيد سند قبض يدوي. Route: /sales/payments/new. Actor: SALES and MANAGER. Purpose: record a verified offline receipt and send it for manager review.

Hierarchy/content: selected student/enrollment and balance breakdown (payable, paid, remaining); payment form; computed overpayment warning; confirmation footer. Data: student/course, amount, method (cash, POS card, bank transfer, cheque), receipt reference, remaining balance, excess/refund request if any. Components: currency amount input, method selector, receipt reference field, balance summary, warning banner, confirmation dialog.
CTA/navigation: submit receipt as Pending Approval. Overpayment warning states excess and linked refund request behavior; do not claim refund paid. Return to student/payment context after success.
Validation: amount > 0; receipt reference and method required; decimal input. Warn and require confirmation when amount exceeds balance; explain excess. States: blank/selected enrollment, loading, no eligible enrollment, invalid amount/reference, overpayment warning, submission failure, pending-approval success.
Responsive: single-column numeric-input form and sticky submit. Accessibility: decimal mobile keypad; announce calculated balance/excess and approval status. Follow shared rules.
```

## 4. Manager enrollment and financial control

### SCR-M-05 — Center Students Master Directory

```text
Create one screen. ID: SCR-M-05. Title: سجل طلاب المركز. Route: /manager/students. Actor: MANAGER. Purpose: review center-wide student profiles, enrollments and financial standing.

Hierarchy/content: heading, student search/filters including payment status; comprehensive table/cards with student identity, contact, course/enrollment summary, payable/paid/remaining totals and status. Data remains within active center. Components: filters, sortable directory, status badges, links to student/course/payment detail.
CTA/navigation: open student financial record or associated course/enrollment. Do not add unsupported bulk exports or deletion.
Validation: filters and route access. States: populated, loading skeleton, no students or no filter matches, search/load error, successful navigation feedback.
Responsive: table becomes student cards with key balance values, filters collapse. Accessibility: currency labels and numeric alignment remain understandable in RTL; semantic table/card headings. Follow shared rules.
```

### SCR-M-06 — Center Enrollments & Waitlist Management

```text
Create one screen. ID: SCR-M-06. Title: إدارة المقاعد وقوائم الانتظار. Route: /manager/enrollments. Actor: MANAGER. Purpose: monitor course seats, enrollment status and FIFO waitlists.

Hierarchy/content: course selector/filter; capacity summary; active enrollment and waitlist sections. Data: student, course, enrollment state, seat capacity, waitlist position/order and financial summary where permitted. Components: capacity progress, enrollment table/cards, waitlist order badges, promotion confirmation dialog.
CTA/navigation: inspect course/student; promote next waitlisted student only when capacity is available. Do not reorder FIFO or bypass full capacity without an explicitly supported capacity update.
Validation: promotion requires available seat and next FIFO position; block duplicate or over-capacity placement. States: populated, skeleton, no waitlist with all seats assigned message, conflict/error, promotion success.
Responsive: lists become cards; promotion actions remain thumb reachable. Accessibility: announce queue position and capacity; confirmation describes effect. Follow shared rules.
```

### SCR-M-07 — Payment Approvals & Excess Refund Queue

```text
Create one screen. ID: SCR-M-07. Title: طابور اعتماد السندات وطلبات الاسترداد. Route: /manager/payments/approvals. Actor: MANAGER. Purpose: independently review pending offline receipts and linked excess refund requests.

Hierarchy/content: pending receipt queue with student, course, amount, method, reference, submitting staff, date, balance impact and linked excess/refund details. Separate clear review and decision area. Components: queue table/cards, detail panel, approve and reject controls, rejection-reason dialog, refund status indicator.
CTA/navigation: approve pending receipt (ledger update); reject with required reason; review/record refund status only per supported workflow. Approval/rejection is manager-only and distinct from Sales submission. No hard delete.
Validation: rejection reason minimum 5 characters; prevent duplicate decisions; show overpayment explicitly. States: populated; skeleton; queue empty; conflict/server error; approved/rejected confirmation and updated queue.
Responsive: review cards and full-screen decision dialog on mobile. Accessibility: distinct icon/text as well as color; confirmation for consequential actions; live status announcement. Follow shared rules.
```

### SCR-M-08 — Instructor Dues & Settlement Payouts

```text
Create one screen. ID: SCR-M-08. Title: مستحقات المحاضرين والتسويات. Route: /manager/instructor-dues. Actor: MANAGER. Purpose: review calculated instructor liabilities and record offline payout settlements.

Hierarchy/content: liability summary; dues ledger grouped/filterable by instructor/course; accrued, paid and outstanding amounts; dated payout history. Data reflects contract model and earned dues; PerSession accrual follows completed sessions with attendance. Components: financial KPI cards, ledger table/cards, payout drawer/dialog, receipt/reference input.
CTA/navigation: record payout (cash/bank transfer per source); open instructor/course detail. No sales visibility and no online disbursement.
Validation: payout > 0 and does not exceed outstanding due; require supported method/reference fields. States: populated, skeleton, no accrued dues, invalid/excess payout or load error, payout saved with recalculated balance.
Responsive: horizontally contained ledger on tablet; mobile cards with instructor name and amounts. Accessibility: explicit currency labels, aligned figures, review confirmation. Follow shared rules.
```

### SCR-M-09 — Center Operational & Financial Reports

```text
Create one screen. ID: SCR-M-09. Title: التقارير التشغيلية والمالية. Route: /manager/reports. Actor: MANAGER. Purpose: summarize center invoicing, collections, receivables and instructor liabilities for a selected period.

Hierarchy/content: title and date range controls; summary cards for TotalInvoiced, CashCollected, OutstandingReceivables, InstructorLiabilities; detailed supported report breakdowns by course/period. Components: date inputs/presets only if defined in source, summary cards, accessible chart/table with textual values, detail links.
CTA/navigation: adjust date range; navigate from a metric to its source ledger. Printing/export only if the approved inventory explicitly lists it; do not invent integrations.
Validation: start date <= end date; preserve center timezone/currency. States: populated; aggregation skeletons; no transactions for period; invalid date/load error; refreshed report confirmation.
Responsive: charts simplify to labeled summaries; tables become cards. Accessibility: never use color-only chart keys; provide values in text; print-friendly only if in scope. Follow shared rules.
```

### SCR-M-11 — Immutable Audit Trail

```text
Create one screen. ID: SCR-M-11. Title: سجل التدقيق الرقابي. Route: /manager/audit. Actor: MANAGER. Purpose: inspect immutable historical center actions and compare recorded changes.

Hierarchy/content: read-only audit heading; filters for date, user, entity/action; table/timeline of timestamp, user, IP, action, entity, prior/new values; detail diff viewer. Components: filter controls, audit table/cards, read-only detail dialog. Data: AuditLog records only.
CTA/navigation: filter/search and inspect a record. No edit, delete, restore, or mutation actions. Keep records tenant-scoped.
Validation: valid date range and permitted filters. States: populated; skeleton; no records/filter matches; load error/retry; filter applied feedback.
Responsive: concise mobile audit cards, with details opened in a full-screen view. Accessibility: chronological headings and clear before/after labels; keyboard-accessible filters. Follow shared rules.
```

## 5. Instructor delivery

### SCR-I-01 — Instructor Academic Dashboard

```text
Create one screen. ID: SCR-I-01. Title: لوحة المحاضر. Route: /instructor/dashboard. Actor: INSTRUCTOR. Purpose: surface today's assigned session, attendance action, pending task reviews and the instructor's own dues summary.

Hierarchy/content: next/today session card with course/time/location; prominent attendance action; pending submissions count/list; personal accrued/outstanding dues summary. Data is limited to assigned courses and the instructor's own earnings. Components: session card, review queue link, KPI/summary card, upcoming sessions list.
CTA/navigation: take attendance for assigned session; review submissions; open earnings; open assigned course. Sidebar only shows instructor permissions.
Validation: assigned-course scope. States: populated; skeleton; no session today and/or no submissions; load/authorization error; successful navigation/action feedback.
Responsive: one-hand mobile layout, prominent >=48px attendance action. Accessibility: session details in logical order and status text. Follow shared rules.
```

### SCR-I-02 — My Assigned Courses

```text
Create one screen. ID: SCR-I-02. Title: دوراتي المسندة. Route: /instructor/courses. Actor: INSTRUCTOR. Purpose: list only courses assigned to the signed-in instructor.

Hierarchy/content: heading and course list/cards with course name/code, schedule, lifecycle status, session progress and enrolled student count where available. Components: course cards or table, status/progress badges, open-workspace action.
CTA/navigation: open /instructor/courses/[id]. Do not show unassigned center courses or manager-only contract editing.
Validation: authorization against assignment. States: populated; skeleton; no assignments with contact-center-admin guidance; load/access error; course-open confirmation.
Responsive: desktop grid, mobile single column. Accessibility: keyboard focus on cards/actions and progress text. Follow shared rules.
```

### SCR-I-03 — Course Workspace & Delivery Hub

```text
Create one screen. ID: SCR-I-03. Title: مساحة عمل المحاضر للدورة. Route: /instructor/courses/[id]. Actor: INSTRUCTOR assigned to this course. Purpose: course-scoped launch point for delivery operations.

Hierarchy/content: course identity and next session summary; four tabs: sessions/attendance, materials, tasks, private feedback; compact enrolled-student/content summary. Components: accessible tab bar, summary/status cards, tab panels and relevant action shortcuts. Data limited to this assigned course.
CTA/navigation: open attendance roster, material library, tasks/submissions, or feedback tools using course-specific routes. Preserve course context across tabs.
Validation: block unauthorized/unassigned course with access error; no cross-course data. States: populated; skeleton; empty tab with relevant action only; not found/access/server error; saved action confirmation.
Responsive: tabs horizontally scroll or compact on mobile; summaries stack. Accessibility: keyboard-operable tabs, selected state announced. Follow shared rules.
```

### SCR-I-04 — Sessions & Attendance Roster

```text
Create one screen. ID: SCR-I-04. Title: كشف الحضور والغياب. Route: /instructor/courses/[id]/attendance. Actor: assigned INSTRUCTOR (manager access per permissions). Purpose: record Present/Absent attendance for one scheduled session and support attendance-based session completion/accrual.

Hierarchy/content: course/session selector or session header; date/time and session status; enrolled student roster with one Present/Absent control per student; save/complete action. Data: session, enrollment/student identity, attendance status. Components: roster table/cards, accessible segmented/radio controls, summary count, confirmation action.
CTA/navigation: save attendance; return to course workspace. Prevent duplicate records; editing existing attendance updates the existing record. Do not mark session complete without the supported attendance submission flow.
Validation: each student has one status; session belongs to assigned course. States: roster populated; skeleton; no enrolled students/session; validation/network error; saved attendance confirmation and completion status.
Responsive: roster becomes stacked student cards with large controls. Accessibility: controls labeled with student name and state; status not conveyed by color alone; announce saved result. Follow shared rules.
```

### SCR-I-05 — Course Materials Library (Instructor)

```text
Create one screen. ID: SCR-I-05. Title: مكتبة المواد العلمية. Route: /instructor/courses/[id]/materials. Actor: assigned INSTRUCTOR. Purpose: publish and manage course resources for enrolled students.

Hierarchy/content: course breadcrumb/title; upload area; material list with filename, type, size, upload date and author; available download/access details only if tracked. Components: file picker/drop zone, file rows/cards, upload progress, remove action only when permitted by source.
CTA/navigation: upload material; return to course workspace. Files available to course members only through authorized access.
Validation: approved formats (PDF, Office documents, images, ZIP) and 25MB limit; show unsupported-format/oversize error. States: populated; skeleton; no materials with upload action; upload/protection failure; upload success with file details.
Responsive: mobile file picker and list cards. Accessibility: file type/size exposed to assistive tech; announce upload progress/completion. Follow shared rules.
```

### SCR-I-06 — Tasks, Deadlines & Submissions Review

```text
Create one screen. ID: SCR-I-06. Title: التكليفات وتسليمات الطلاب. Route: /instructor/courses/[id]/tasks. Actor: assigned INSTRUCTOR. Purpose: create course tasks, review student submissions, and apply the supported late-submission policy.

Hierarchy/content: task list with title, deadline, submission counts/status; create/edit task form; selected task submission queue with student, submitted time, file and status. Components: task cards/table, deadline field, submission table/cards, protected file download, late-submission override control only as inventory permits.
CTA/navigation: create task; inspect/download submission; enable late submission when authorized. Stay in course context. No auto-grading or quiz engine.
Validation: required title/instructions/deadline; file limit 25MB; late acceptance clearly marked. States: populated; skeleton; no tasks/submissions; invalid task/file/access error; task saved and review/override success feedback.
Responsive: task cards and submission cards; forms full-screen on mobile. Accessibility: deadline/status text, labeled override, secure download name/type and live save feedback. Follow shared rules.
```

### SCR-I-07 — 1:1 Private Student Feedback

```text
Create one screen. ID: SCR-I-07. Title: التغذية الراجعة الفردية الخاصة. Route: /instructor/courses/[id]/feedback. Actor: assigned INSTRUCTOR. Purpose: send an academic feedback note privately to one student in the course.

Hierarchy/content: course context; student selector/list; selected student's feedback history; compose note form with related task/session context when supported. Components: student selector, private-history cards, labeled text area, send/save action. Clearly indicate private 1:1 visibility.
CTA/navigation: submit note; return to course workspace or related task. Do not expose notes to other students or Sales. No public comments/chat.
Validation: student must be enrolled in assigned course; note required; preserve privacy. States: history present; skeleton; no feedback yet; no eligible students; validation/server error; note saved confirmation.
Responsive: single-column note composer and history cards. Accessibility: privacy indicator read by screen readers, labels and announced confirmation. Follow shared rules.
```

### SCR-I-08 — My Earnings & Accruals

```text
Create one screen. ID: SCR-I-08. Title: مستحقاتي المالية. Route: /instructor/earnings. Actor: INSTRUCTOR. Purpose: transparently show the instructor's own accrued dues and payout history.

Hierarchy/content: summary of accrued, paid and outstanding amounts; per-course ledger; payout entries and statuses. Data only for signed-in instructor, calculated from assigned compensation terms; PerSession accrual is tied to completed sessions with submitted attendance. Components: summary cards, course ledger table/cards, dated payout list, Pending/PartiallyPaid/Paid status badges.
CTA/navigation: inspect course or payout detail where available; this is a read view, do not add payout initiation or contract editing.
Validation: authenticated instructor ownership. States: populated; skeleton; no accrued dues/payouts; data-load error; refreshed confirmation. Responsive: course cards on mobile. Accessibility: explicit currency and status labels. Follow shared rules.
```

## 6. Student learning and tuition

### SCR-ST-01 — Student Learning Dashboard

```text
Create one screen. ID: SCR-ST-01. Title: بوابة الطالب. Route: /student/dashboard. Actor: STUDENT. Purpose: provide a mobile-first view of enrolled courses and the student's next academic and payment actions.

Hierarchy/content: enrolled active course cards; upcoming task deadlines; recent published lecture materials; personal fee balance overview. Data restricted to this student. Components: course cards, deadline list, recent materials list, payable/paid/remaining summary.
CTA/navigation: open course hub, task, material or personal payment ledger. No peer data or public feedback.
Validation: active enrollment and account access. States: populated; skeleton; no courses with clear enrollment status; load/access error; confirmation after supported action.
Responsive: one-column cards optimized for 360px and thumb use. Accessibility: deadlines include dates and urgency text, balance labels explicit. Follow shared rules.
```

### SCR-ST-02 — My Enrolled Courses

```text
Create one screen. ID: SCR-ST-02. Title: دوراتي المسجل بها. Route: /student/courses. Actor: STUDENT. Purpose: list the student's active and completed course enrollments.

Hierarchy/content: heading; active/completed grouping or filter; course cards with name/code, dates, instructor where available and enrollment status. Components: course cards, status badges, open-course action.
CTA/navigation: open /student/courses/[id]. No marketplace or self-enrollment purchase flow.
Validation: show only own enrolled courses. States: populated; skeleton; no enrollments; load/access error; course-open feedback.
Responsive: single-column cards on mobile. Accessibility: status text and focusable card action. Follow shared rules.
```

### SCR-ST-03 — Course Learning Hub

```text
Create one screen. ID: SCR-ST-03. Title: مساحة تعلم الدورة. Route: /student/courses/[id]. Actor: enrolled STUDENT. Purpose: course-scoped hub for learning materials, tasks, attendance and instructor feedback.

Hierarchy/content: course identity/progress context; tabs for materials, tasks, attendance and private feedback; next deadline/session summary where available. Data only from this student's course enrollment. Components: accessible tabs, summaries, task/deadline status, action links.
CTA/navigation: open /materials, /tasks, /attendance or /feedback under same course. Prevent cross-course navigation/data leakage.
Validation: active enrollment required; unauthorized access has clear error. States: populated; skeleton; empty tab with explanation; access/server error; confirmation for supported actions.
Responsive: compact horizontally scrollable tabs and stacked summaries. Accessibility: tab semantics and selected state, meaningful due-date/status labels. Follow shared rules.
```

### SCR-ST-04 — Course Materials & Downloads (Student)

```text
Create one screen. ID: SCR-ST-04. Title: مواد الدورة ومحاضراتها. Route: /student/courses/[id]/materials. Actor: enrolled STUDENT. Purpose: browse and securely download published materials for this course.

Hierarchy/content: course breadcrumb; materials grouped by session/date when available; each item shows title/file name, type, size, upload date and instructor. Components: material list/cards and secure download actions.
CTA/navigation: download selected file; return to course hub. Only published course materials are visible.
Validation: active course enrollment and download authorization checked at request time. States: populated; skeleton; no materials message; access/file/server error; download-started/success feedback.
Responsive: vertically stacked, touch-friendly file cards. Accessibility: file type/size in accessible name; download status announced. Follow shared rules.
```

### SCR-ST-05 — Tasks & Homework Submissions

```text
Create one screen. ID: SCR-ST-05. Title: التكليفات وتسليم الحلول. Route: /student/courses/[id]/tasks. Actor: enrolled STUDENT. Purpose: review assignment instructions/deadlines and upload or replace the student's own solution when allowed.

Hierarchy/content: task list/detail with title, instructions, due date/countdown, late policy and this student's submission/status. Components: task cards, file picker/drop zone, upload progress, submitted-file summary, status badges Pending/Submitted On-Time/Submitted Late/Closed.
CTA/navigation: submit or replace file before deadline; view own submitted file. Do not expose other students' submissions or add grading tools.
Validation: 25MB file maximum and supported file types; reject after deadline unless instructor enabled late submissions; record submitted time and late status. States: active, skeleton, no tasks, upload/format/size/deadline errors, upload progress, successful submission confirmation.
Responsive: file picker works with mobile file manager; sticky submit action. Accessibility: announce progress/result and deadline closure; labels for uploaded file. Follow shared rules.
```

### SCR-ST-06 — Personal Attendance Record

```text
Create one screen. ID: SCR-ST-06. Title: سجل حضوري. Route: /student/courses/[id]/attendance. Actor: enrolled STUDENT. Purpose: show this student's attendance by session and cumulative percentage.

Hierarchy/content: attendance percentage summary; dated session history; Present/Absent status and totals. Read-only data from instructor attendance records. Components: accessible percentage indicator, session table/cards, status icon plus text. Show the source-defined 75% warning threshold if applicable.
CTA/navigation: return to course hub. No attendance editing.
Validation: own enrollment only. States: populated; skeleton; no recorded sessions; load error; updated record feedback if data refreshes.
Responsive: session rows become cards on mobile. Accessibility: color is not the sole status cue; percentage has text equivalent; clear dates. Follow shared rules.
```

### SCR-ST-07 — Private Instructor Feedback View

```text
Create one screen. ID: SCR-ST-07. Title: ملاحظات المحاضر الخاصة. Route: /student/courses/[id]/feedback. Actor: enrolled STUDENT. Purpose: read private instructor feedback addressed only to this student.

Hierarchy/content: privacy-labeled page; dated feedback cards with instructor name, note and associated task/session context when present. Data strictly limited to the authenticated student's feedback. Components: private feedback cards and contextual links to the student's task where supported.
CTA/navigation: read feedback; open associated own task if available; return to course hub. No reply/chat or peer visibility.
Validation: course enrollment and ownership. States: populated; skeleton; no feedback yet; access/load error; refreshed feedback indication.
Responsive: stacked cards. Accessibility: privacy notice is explicit text, heading structure and dates are screen-reader friendly. Follow shared rules.
```

### SCR-ST-08 — Student Tuition Ledger & Receipts

```text
Create one screen. ID: SCR-ST-08. Title: سجل الرسوم والإيصالات. Route: /student/payments. Actor: STUDENT. Purpose: show the student's enrollment charges, verified offline payments and remaining balances.

Hierarchy/content: per-course financial summary with Base Price, Discount, Final Payable, Total Paid, Remaining Balance; receipt list with reference, date, offline method, amount and approval status. Components: balance cards, ledger table/cards, receipt detail/download only if supported by the source.
CTA/navigation: inspect own receipt/payment details. No online checkout, payment gateway, or payment recording by student.
Validation: show only student's own enrollments and verified/visible payment records; distinguish pending approval from approved payment. States: populated; skeleton; no payment records; load error; refreshed ledger confirmation.
Responsive: financial summary stacks; receipts become cards. Accessibility: amounts labeled with currency and status; numbers remain legible in RTL. Follow shared rules.
```

## 7. Shared authenticated screens

### SCR-SHARED-01 — Notifications Center

```text
Create one screen. ID: SCR-SHARED-01. Title: مركز الإشعارات. Route: /notifications. Actor: any authenticated role. Purpose: review role-appropriate operational notifications.

Hierarchy/content: page heading; unread/read filter if supported; chronological notification list with type, message, timestamp and relevant record link. Show only notifications authorized for this role and center. Components: notification cards/list, unread indicator, in-scope mark-read control if supported by source.
CTA/navigation: open linked authorized record; mark read only if supported. No chat, social feed or notification preference controls on this screen.
Validation: linked destinations enforce normal access control. States: populated; skeleton; no notifications; load/permission error; read/open success feedback.
Responsive: stacked notification cards. Accessibility: unread state includes text/ARIA; time is readable; updates announced. Follow shared rules.
```

### SCR-SHARED-02 — User Profile Management

```text
Create one screen. ID: SCR-SHARED-02. Title: الملف الشخصي. Route: /profile. Actor: authenticated MANAGER, SALES, INSTRUCTOR or STUDENT. Purpose: view and update the user's own permitted profile details.

Hierarchy/content: profile identity and role; editable name/phone and avatar only if supported by approved source; email shown read-only if identity management requires it; separate password-change section only if included in approved requirements. Components: labeled profile form, save action, field-level feedback.
CTA/navigation: save own profile; return through user menu. Never permit editing another user's role or tenant.
Validation: required name (minimum 3 characters where specified), valid phone; if password update exists, require current password, new password >=8 characters, and matching confirmation. Do not add profile fields unsupported by the source.
States: populated; skeleton; missing profile fallback; field/server error; save success. Responsive: stacked mobile form and reachable save action. Accessibility: labels, password visibility controls if present, associated errors. Follow shared rules.
```

### SCR-SHARED-03 — Account & Display Settings

```text
Create one screen. ID: SCR-SHARED-03. Title: إعدادات الحساب والعرض. Route: /settings. Actor: authenticated roles. Purpose: manage only the account/display and notification preferences explicitly approved for MVP.

Hierarchy/content: grouped settings sections, using only settings fields present in the approved PRD/inventory/design requirements. Preserve Arabic RTL as the default; do not offer unsupported language changes, themes, security features, or notification categories. Display center-configured currency/timezone/date format as read-only if shown.
Components: accessible switches/selects only for approved editable preferences; save/reset actions only when supported. Data belongs to the authenticated user or center configuration as applicable.
CTA/navigation: save supported preferences; restore defaults only if the source defines it. No invented settings.
Validation: validate supported selections; keep center-owned values read-only. States: loaded settings; skeleton; defaults/unset state; save/validation/server error; saved confirmation.
Responsive: sections stack, touch targets >=44px, save action reachable. Accessibility: switches expose role/state and labels; grouped controls have headings; announce save result. Follow shared rules.
```
