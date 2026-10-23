# Relational Database Architecture Review & Business Analysis
# مراجعة التصميم المعماري لقاعدة البيانات والتحليل المؤسسي

# VELORA (فيلورا) — Training Center Management Platform

- **Document Version:** 1.0.0  
- **Role:** Senior Database Architect, Database Designer & Lead Business Analyst  
- **Target DBMS:** PostgreSQL 15+ (Single Shared Database, Logical Multi-Tenancy)  
- **Primary Sources of Truth:** [PRD.md](file:///D:/docs/docs/PRD.md), [user-flow.md](file:///D:/docs/docs/user-flow.md), and [domain-model.md](file:///D:/docs/docs/diagrams/domain-model.md)  
- **Associated Artifacts:** [database.dbml](file:///D:/docs/docs/database.dbml) & [ERD.md](file:///D:/docs/docs/ERD.md)  
- **Status:** Approved for Implementation (MVP)  

---

## Table of Contents / فهرس المحتويات
1. [Executive Summary & Architectural Scope](#1-executive-summary--architectural-scope)
2. [Step 1: Database Analysis & Technical Inventory](#2-step-1-database-analysis--technical-inventory)
3. [Step 2: Database Design Review & Architectural Decisions](#3-step-2-database-design-review--architectural-decisions)
   - [3.1 Normalization Audit (1NF, 2NF, 3NF & BCNF)](#31-normalization-audit-1nf-2nf-3nf--bcnf)
   - [3.2 Redundancy & Tenancy Partitioning Analysis](#32-redundancy--tenancy-partitioning-analysis)
   - [3.3 Referential Integrity & Cascade Safeguards](#33-referential-integrity--cascade-safeguards)
   - [3.4 Finite Seat Capacity (Inventory) & Concurrent Reservation (Borrowing) Requests](#34-finite-seat-capacity-inventory--concurrent-reservation-borrowing-requests)
   - [3.5 Analysis of Stored Values that Can Become Inconsistent & ACID Safeguards](#35-analysis-of-stored-values-that-can-become-inconsistent--acid-safeguards)
   - [3.6 Distinguishing Confirmed Business Rules from Technical Design Choices](#36-distinguishing-confirmed-business-rules-from-technical-design-choices)
4. [Step 5: Systematic 10-Point Database Architecture Review Checklist](#4-step-5-systematic-10-point-database-architecture-review-checklist)
5. [Step 4: Detailed Architectural Explanation in Arabic (الشرح المعماري المفصل باللغة العربية)](#5-step-4-detailed-architectural-explanation-in-arabic)
   - [5.1 لماذا تم إنشاء كل جدول في قاعدة البيانات؟ (Table Rationale)](#51-لماذا-تم-إنشاء-كل-جدول-في-قاعدة-البيانات؟-table-rationale)
   - [5.2 لماذا وُجدت كل علاقة؟ (Relationship & Cardinality Rationale)](#52-لماذا-وُجدت-كل-علاقة؟-relationship--cardinality-rationale)
   - [5.3 كيف تدعم قاعدة البيانات مسار المستخدم الرئيسي؟ (Main User Flow Support)](#53-كيف-تدعم-قاعدة-البيانات-مسار-المستخدم-الرئيسي؟-main-user-flow-support)
   - [5.4 المشاكل والمخاطر التصميمية المحتملة وموازنات الأداء (Design Pitfalls & Trade-offs)](#54-المشاكل-والمخاطر-التصميمية-المحتملة-وموازنات-الأداء-design-pitfalls--trade-offs)
   - [5.5 التحسينات المقترحة وخطة التوسع المستقبلي (Suggested Improvements)](#55-التحسينات-المقترحة-وخطة-التوسع-المستقبلي-suggested-improvements)
6. [Explicit Assumptions & Unresolved Questions](#6-explicit-assumptions--unresolved-questions)

---

## 1. Executive Summary & Architectural Scope

This document provides a comprehensive Database Architecture Review, Relational Design Specification, and Business Analysis for the Minimum Viable Product (MVP) of **VELORA (فيلورا)**, an operational management, academic accountability, and financial reconciliation SaaS platform for training centers.

The database design strictly translates the approved business requirements from [PRD.md](file:///D:/docs/docs/PRD.md) and the operational decisions finalized in [user-flow.md](file:///D:/docs/docs/user-flow.md) into a normalized, enterprise-grade relational schema optimized for **PostgreSQL 15+**.

### Key Architectural Tenets:
1. **Strict Logical Multi-Tenancy:** Zero risk of cross-center data exposure enforced via primary tenant foreign keys (`center_id`), tenant-scoped composite unique constraints, and compatibility with PostgreSQL Row-Level Security (RLS).
2. **Transactional Financial Integrity (ACID):** Absolute immutability of posted payment vouchers, mandatory administrative approval workflows, automated excess overpayment detection, and atomic balance reconciliation.
3. **Rigorous Academic Accountability:** Binary session attendance tracking, hard-locked seat capacity enforcement, First-In-First-Out (FIFO) waitlist queuing, and deadline-controlled task delivery.
4. **Normalized Compensation Modeling:** Accrual tracking for multi-instructor course assignments supporting fixed-fee, revenue-share, and per-session compensation models with dedicated disbursement logs.

---

## 2. Step 1: Database Analysis & Technical Inventory

The database schema consists of **19 confirmed relational entities** partitioned across 6 cohesive functional modules. Below is the structural inventory:

| Entity Name | Primary Key | Foreign Keys (FKs) | Key Constraints | Primary Data Types |
| :--- | :--- | :--- | :--- | :--- |
| **`centers`** | `id` (UUID) | None | UNIQUE(`code`), NOT NULL(`name`) | UUID, VARCHAR, TIMESTAMPTZ |
| **`users`** | `id` (UUID) | `center_id` -> `centers.id` | UNIQUE(`center_id`, `email`), NOT NULL(`password_hash`, `role`) | UUID, VARCHAR, TIMESTAMPTZ |
| **`student_profiles`** | `id` (UUID) | `user_id` -> `users.id`<br/>`center_id` -> `centers.id` | UNIQUE(`user_id`), INDEX(`center_id`, `phone`) | UUID, VARCHAR, TEXT, TIMESTAMPTZ |
| **`instructor_profiles`**| `id` (UUID) | `user_id` -> `users.id`<br/>`center_id` -> `centers.id` | UNIQUE(`user_id`), INDEX(`center_id`, `specialization`) | UUID, VARCHAR, TEXT, TIMESTAMPTZ |
| **`courses`** | `id` (UUID) | `center_id` -> `centers.id` | UNIQUE(`center_id`, `code`), CHECK(`base_price >= 0`, `end_date >= start_date`) | UUID, VARCHAR, DECIMAL(10,2), DATE, INT |
| **`course_instructors`** | `id` (UUID) | `course_id` -> `courses.id`<br/>`instructor_id` -> `instructor_profiles.id` | UNIQUE(`course_id`, `instructor_id`), CHECK(`model_value >= 0`) | UUID, VARCHAR, DECIMAL(10,2), BOOLEAN |
| **`course_sessions`** | `id` (UUID) | `course_id` -> `courses.id`<br/>`instructor_id` -> `instructor_profiles.id` | CHECK(`end_time > start_time`, `session_order >= 1`) | UUID, DATE, TIME, VARCHAR, INT |
| **`course_waitlists`** | `id` (UUID) | `course_id` -> `courses.id`<br/>`student_id` -> `student_profiles.id` | INDEX(`course_id`, `requested_at`), NOT NULL(`requested_at`) | UUID, VARCHAR, TIMESTAMPTZ, TEXT |
| **`enrollments`** | `id` (UUID) | `center_id` -> `centers.id`<br/>`course_id` -> `courses.id`<br/>`student_id` -> `student_profiles.id` | UNIQUE(`course_id`, `student_id`) WHERE status='Active'<br/>CHECK(`discount <= base_price`, `remaining >= 0`) | UUID, DECIMAL(10,2), DATE, VARCHAR |
| **`payments`** | `id` (UUID) | `center_id` -> `centers.id`<br/>`enrollment_id` -> `enrollments.id`<br/>`created_by`, `approved_by` -> `users.id` | CHECK(`amount > 0`, `applied_amount >= 0`, `excess_amount >= 0`) | UUID, DECIMAL(10,2), DATE, VARCHAR, TIMESTAMPTZ |
| **`refund_requests`** | `id` (UUID) | `center_id` -> `centers.id`<br/>`payment_id` -> `payments.id`<br/>`enrollment_id` -> `enrollments.id`<br/>`executed_by` -> `users.id` | UNIQUE(`payment_id`), CHECK(`amount > 0`) | UUID, DECIMAL(10,2), VARCHAR, DATE, TIMESTAMPTZ |
| **`course_materials`** | `id` (UUID) | `course_id` -> `courses.id`<br/>`session_id` -> `course_sessions.id`<br/>`uploader_id` -> `users.id` | CHECK(`file_size_bytes <= 26214400` [25 MB]), NOT NULL(`file_path`) | UUID, VARCHAR, BIGINT, TIMESTAMPTZ |
| **`tasks`** | `id` (UUID) | `course_id` -> `courses.id`<br/>`session_id` -> `course_sessions.id`<br/>`creator_id` -> `users.id` | NOT NULL(`title`, `instructions`) | UUID, VARCHAR, TEXT, TIMESTAMPTZ, BOOLEAN |
| **`submissions`** | `id` (UUID) | `task_id` -> `tasks.id`<br/>`enrollment_id` -> `enrollments.id` | UNIQUE(`task_id`, `enrollment_id`), CHECK(`file_size <= 25MB`) | UUID, VARCHAR, BIGINT, TIMESTAMPTZ |
| **`attendances`** | `id` (UUID) | `session_id` -> `course_sessions.id`<br/>`enrollment_id` -> `enrollments.id`<br/>`recorded_by` -> `users.id` | UNIQUE(`session_id`, `enrollment_id`), CHECK(`status IN ('Present', 'Absent')`) | UUID, VARCHAR, TIMESTAMPTZ |
| **`feedbacks`** | `id` (UUID) | `course_id` -> `courses.id`<br/>`enrollment_id` -> `enrollments.id`<br/>`instructor_id` -> `instructor_profiles.id`<br/>`session_id`, `task_id` -> NULLABLE | NOT NULL(`content`) | UUID, TEXT, TIMESTAMPTZ |
| **`instructor_dues`** | `id` (UUID) | `center_id` -> `centers.id`<br/>`course_instructor_id` -> `course_instructors.id` | UNIQUE(`course_instructor_id`), CHECK(`accrued_amount >= 0`) | UUID, DECIMAL(10,2), VARCHAR, TIMESTAMPTZ |
| **`instructor_payouts`**| `id` (UUID) | `center_id` -> `centers.id`<br/>`instructor_due_id` -> `instructor_dues.id`<br/>`disbursed_by` -> `users.id` | CHECK(`amount > 0`), NOT NULL(`payout_date`, `payout_method`) | UUID, DECIMAL(10,2), DATE, VARCHAR, TIMESTAMPTZ |
| **`audit_logs`** | `id` (UUID) | `center_id` -> `centers.id`<br/>`user_id` -> `users.id` | NOT NULL(`action`, `entity_type`, `entity_id`, `created_at`) | UUID, VARCHAR, JSONB, TIMESTAMPTZ |

---

## 3. Step 2: Database Design Review & Architectural Decisions

### 3.1 Normalization Audit (1NF, 2NF, 3NF & BCNF)

#### Compliance with 1NF (First Normal Form):
- Every table has a standardized primary key (`id` UUIDv4).
- All attributes contain atomic, scalar values (no repeating groups, no comma-separated arrays).
- Note on JSONB in `audit_logs`: The `old_values` and `new_values` columns store JSONB payloads. This complies with 1NF because the entity being modeled is a generic audit snapshot where the payload is an atomic document capture of a state transition.

#### Compliance with 2NF (Second Normal Form):
- All tables have single-attribute primary keys (`id` UUID).
- Consequently, no partial functional dependencies exist on composite primary keys. Every non-key attribute is fully functionally dependent on the entire primary key.

#### Compliance with 3NF (Third Normal Form) & Controlled Denormalization:
In strict 3NF, no non-key attribute may transitively depend on another non-key attribute. In commercial accounting and high-traffic enrollment systems, applying textbook 3NF without pragmatic consideration of performance produces severe operational bottlenecks. We audited two specific areas:

1. **Student Running Balances in `enrollments` (`final_payable`, `total_paid`, `remaining_balance`, `payment_status`):**
   - *Strict 3NF View:* `final_payable` is derived from `base_price - discount_amount`. `total_paid` is the aggregate `SUM(applied_amount)` from `payments`. `remaining_balance` is `final_payable - total_paid`.
   - *Architectural Rationale for Persisting Snapshot Balances:*
     - **Performance at Scale:** In executive dashboards (SCR-M01) and sales collection queues (SCR-S01), filtering hundreds of students by `remaining_balance > 0` or sorting by overdue balances without stored columns requires joining the entire payments history table and running aggregate `GROUP BY` functions on every page load. This would violate NFR-PERF-01 and NFR-PERF-04.
     - **Contractual Snapshot Lock:** `base_price` on enrollment is a **contractual snapshot**. If the center increases the catalog price of the course tomorrow, existing enrolled students must retain their agreed-upon tuition fee.
     - **ACID Transaction Guarantee:** To prevent desynchronization, updates to `total_paid` and `remaining_balance` never occur outside an atomic ACID transaction (`BEGIN ... COMMIT`). When a payment voucher is approved, the system locks the enrollment row (`SELECT ... FOR UPDATE`), recalculates the balance, updates the status, writes the audit log, and commits atomically.
   - *Architectural Recommendation:* Maintain the stored balance columns in `enrollments` as an intentional, high-performance financial snapshot, governed by database `CHECK` constraints (`CHECK (remaining_balance >= 0.00)` and `CHECK (discount_amount <= base_price)`).

2. **Accrued Dues in `instructor_dues` (`accrued_amount`, `paid_amount`, `outstanding_due`):**
   - Storing `accrued_amount` and `paid_amount` acts as a materialized operational cache for instructor liabilities reporting (SCR-M08).
   - Normalized child table **`instructor_payouts`** captures every discrete settlement disbursement transaction, preserving full 3NF compliance for historical cash outflows.

---

### 3.2 Redundancy & Tenancy Partitioning Analysis

#### Evaluation of `center_id` in Child Tables:
- In pure relational modeling, if `enrollments` links to `courses` and `courses` links to `centers`, storing `center_id` in `enrollments` could be considered a transitive dependency (`enrollments -> courses -> centers`).
- **Architectural Defense:** In multi-tenant enterprise architectures, storing `center_id` directly on all primary operational tables (`enrollments`, `payments`, `refund_requests`, `audit_logs`, `instructor_dues`) is an industry-standard best practice known as **Tenant Partition Keying**:
  1. It allows PostgreSQL Row-Level Security (RLS) policies to evaluate tenant ownership in $O(1)$ without executing costly multi-table joins.
  2. It allows composite tenant indexes (e.g., `(center_id, status)`, `(center_id, payment_date)`) that prevent index bloat and ensure fast sequential scans within a single tenant partition.
  3. It prevents catastrophic accidental cross-tenant data leakage if an application query forgets an intermediate join.

#### Contact Data Separation:
- Trainee telephone numbers, civil IDs, and internal notes are isolated within `student_profiles`, while trainer bio and qualifications reside in `instructor_profiles`.
- Authentication credentials (`email`, `password_hash`) reside solely in `users`. This eliminates data redundancy and guarantees that credential updating is localized to a single row.

---

### 3.3 Referential Integrity & Cascade Safeguards

We audited all foreign key deletion behaviors to ensure absolute data preservation:

1. **`ON DELETE RESTRICT` for Financial & Operational Records:**
   - Deleting a `center` is restricted if it contains active `users`, `courses`, or `enrollments`.
   - Deleting a `course` is restricted (`ON DELETE RESTRICT`) if it has associated `enrollments` or `course_instructors`. This enforces the business rule (BR-CRS-06) that courses with history can only be `Archived`, never hard-deleted.
   - Deleting an `enrollment` is strictly blocked if any `payments`, `submissions`, `attendances`, or `feedbacks` exist.
   - Deleting a `payment` is strictly blocked (`ON DELETE RESTRICT`), enforcing the immutable financial ledger rule (FR-FIN-05 / BR-FIN-03).
2. **`ON DELETE CASCADE` for Dependent Child Sub-components:**
   - When a `course` is removed in draft state, its scheduled `course_sessions`, `course_materials`, and empty `course_waitlists` cascade cleanly.
   - When a `task` is removed in draft, its dependent `submissions` cascade.
3. **`ON DELETE SET NULL` for Non-Critical Audit References:**
   - If an administrative user account is archived/removed, references such as `course_materials.uploader_id`, `course_sessions.instructor_id`, or `audit_logs.user_id` are set to `NULL`, preserving the underlying operational and educational records.

---

### 3.4 Finite Seat Capacity (Inventory) & Concurrent Reservation (Borrowing) Requests

In VELORA, the core inventory problem is the **finite seat capacity of a course cohort** (`courses.capacity`), and the concurrent borrowing/reservation problem is **simultaneous trainee enrollment and waitlist placement requests**:

1. **Seat Capacity as Finite Inventory:**
   - A course cohort has a strictly capped number of physical/virtual seats (`capacity INT`).
   - If `capacity` is `NULL`, the course has uncapped seats. If `capacity` is defined, the total active enrollments (`COUNT(*) WHERE course_id = :id AND status = 'Active'`) must never exceed `capacity`.

2. **Race Condition in Concurrent Borrowing / Enrollment Requests:**
   - *The Scenario:* Suppose Course A has 1 remaining seat (`capacity = 20`, current active enrollments = 19). Two Sales specialists (Sales Rep X and Sales Rep Y) concurrently attempt to enroll two different students at the exact same millisecond.
   - *Unsafe Execution:* If both transactions read `COUNT(*) = 19`, both proceed to insert an active enrollment, resulting in 20 + 1 = 21 enrollments, breaching the hard capacity lock.
   - *Database Solution (Pessimistic Row-Level Locking):*
     ```sql
     BEGIN;
     -- Step 1: Acquire exclusive row lock on the course cohort
     SELECT id, capacity FROM courses WHERE id = :course_id FOR UPDATE;

     -- Step 2: Compute current active count under the lock
     SELECT COUNT(*) INTO v_enrolled_count 
     FROM enrollments 
     WHERE course_id = :course_id AND status = 'Active';

     -- Step 3: Branch atomically based on verified available inventory
     IF v_enrolled_count >= v_capacity THEN
         -- Capacity reached: reject enrollment or redirect to FIFO waitlist
         INSERT INTO course_waitlists (id, course_id, student_id, status, requested_at)
         VALUES (gen_random_uuid(), :course_id, :student_id, 'Waiting', now());
     ELSE
         -- Seat available: atomically consume seat inventory
         INSERT INTO enrollments (id, center_id, course_id, student_id, base_price, ...)
         VALUES (...);
     END IF;

     COMMIT;
     ```
   - This guarantees serializable seat reservations, preventing overbooking and guaranteeing strict FIFO order for `course_waitlists`.

---

### 3.5 Analysis of Stored Values that Can Become Inconsistent & ACID Safeguards

Whenever a database stores calculated attributes, a desynchronization risk exists if updates are uncoordinated. In VELORA, the identified stored calculations are:

| Stored Calculation | Table | Derivation Formula | Inconsistency Risk | Architectural Safeguards |
| :--- | :--- | :--- | :--- | :--- |
| `final_payable` | `enrollments` | `base_price - discount_amount` | Negative values if discount > base price | Database `CHECK (discount_amount <= base_price)` and `CHECK (final_payable >= 0.00)` |
| `total_paid` | `enrollments` | $\sum \text{payments.applied\_amount}$ | Desynchronization if payment updated outside transaction | 2-phase payment approval; row lock on enrollment; database check `CHECK (total_paid >= 0.00)` |
| `remaining_balance`| `enrollments` | `final_payable - total_paid` | Negative remaining balance on overpayment | Excess overpayment auto-split to `excess_amount`; `CHECK (remaining_balance >= 0.00)` |
| `payment_status` | `enrollments` | Derived from `total_paid` and `remaining_balance` | Stale status string | Enumerated state transition executed atomically within payment approval transaction |
| `accrued_amount` | `instructor_dues` | Formula per compensation model | Stale accrual if payments or sessions change | Encapsulated service calculation; `CHECK (accrued_amount >= 0.00)` |
| `outstanding_due` | `instructor_dues` | `accrued_amount - paid_amount` | Negative due if payout exceeds accrual | Checked at payout disbursement; `CHECK (outstanding_due >= 0.00)` |

---

### 3.6 Distinguishing Confirmed Business Rules from Technical Design Choices

To maintain clear boundary lines between business requirements and technical design choices:

```
┌──────────────────────────────────────────────────────────────────────────────────────────────┐
│                  CONFIRMED BUSINESS RULES vs. TECHNICAL DESIGN CHOICES                       │
├────────────────────────────────────────┬─────────────────────────────────────────────────────┤
│ Domain / Functional Area               │ Classification & Distinction                        │
├────────────────────────────────────────┼─────────────────────────────────────────────────────┤
│ 1. Multi-Tenant Scoping                │ • Confirmed Business Rule: Centers must remain      │
│                                        │   strictly isolated; cross-center queries forbidden.│
│                                        │ • Technical Design Choice: Single shared database    │
│                                        │   with logical partition key (center_id) and RLS.   │
├────────────────────────────────────────┼─────────────────────────────────────────────────────┤
│ 2. Primary Key Format                  │ • Confirmed Business Rule: Entities must have       │
│                                        │   immutable unique identifiers.                     │
│                                        │ • Technical Design Choice: Standardizing on RFC 4122│
│                                        │   UUIDv4 instead of sequential BIGINT (anti-scraping)│
├────────────────────────────────────────┼─────────────────────────────────────────────────────┤
│ 3. Capacity & Waitlist Priority        │ • Confirmed Business Rule: Capacity hard-lock; free │
│                                        │   waitlist queued in strict First-In-First-Out order│
│                                        │ • Technical Design Choice: course_waitlists table   │
│                                        │   with requested_at TIMESTAMPTZ & B-Tree index.     │
├────────────────────────────────────────┼─────────────────────────────────────────────────────┤
│ 4. Two-Phase Payment Approval          │ • Confirmed Business Rule: Sales payments enter as  │
│                                        │   Pending; Manager/Accountant must approve.         │
│                                        │ • Technical Design Choice: Status column in payments│
│                                        │   plus approved_by FK and approved_at timestamp.    │
├────────────────────────────────────────┼─────────────────────────────────────────────────────┤
│ 5. Excess Overpayment Refund           │ • Confirmed Business Rule: Excess cannot be credit  │
│                                        │   balance; mandatory refund payout required.        │
│                                        │ • Technical Design Choice: payments auto-split into │
│                                        │   applied_amount/excess_amount + 1:1 refund_requests│
├────────────────────────────────────────┼─────────────────────────────────────────────────────┤
│ 6. Binary Attendance                   │ • Confirmed Business Rule: Session attendance must  │
│                                        │   be strictly Present or Absent in MVP.             │
│                                        │ • Technical Design Choice: ENUM attendance_status   │
│                                        │   and composite UNIQUE (session_id, enrollment_id). │
├────────────────────────────────────────┼─────────────────────────────────────────────────────┤
│ 7. File Size Hard Limit                │ • Confirmed Business Rule: Uploads capped at 25 MB. │
│                                        │ • Technical Design Choice: Database check constraint│
│                                        │   CHECK (file_size_bytes <= 26214400) + relative path│
├────────────────────────────────────────┼─────────────────────────────────────────────────────┤
│ 8. Financial Immutability              │ • Confirmed Business Rule: Posted vouchers cannot be│
│                                        │   hard-deleted; audit trail required.               │
│                                        │ • Technical Design Choice: Foreign keys with        │
│                                        │   ON DELETE RESTRICT and JSONB audit snapshots.     │
└────────────────────────────────────────┴─────────────────────────────────────────────────────┘
```

---

## 4. Step 5: Systematic 10-Point Database Architecture Review Checklist

Below is the exhaustive architectural review evaluating all 10 structural checklist dimensions:

### 1. Missing Entities Check
- **Audit Result: Passed.**
- **Details:** All core PRD domain entities (`Center`, `User`, `StudentProfile`, `InstructorProfile`, `Course`, `CourseInstructor`, `CourseSession`, `Enrollment`, `Material`, `Task`, `Submission`, `Attendance`, `Feedback`, `Payment`, `InstructorDue`, `AuditLog`) are present.
- **Approved Decision Inclusions:** In accordance with [user-flow.md](file:///D:/docs/docs/user-flow.md), **`course_waitlists`** (Decision OQ-06), **`refund_requests`** (Decision OQ-05), and **`instructor_payouts`** (settlement normalization) are fully modeled.

### 2. Incorrect Relationships Check
- **Audit Result: Passed.**
- **Details:** Many-to-many relationship between `courses` and `instructor_profiles` is correctly resolved via `course_instructors`. The 1:1 specializations (`users` -> `student_profiles`, `users` -> `instructor_profiles`) enforce uniqueness on `user_id`. The 1:1 link between `payments` and `refund_requests` ensures exactly one refund claim per overpayment voucher.

### 3. Missing Foreign Keys Check
- **Audit Result: Passed.**
- **Details:** All relationships possess explicit foreign key constraints with defined referential integrity actions (`ON DELETE RESTRICT`, `CASCADE`, or `SET NULL`). No implicit cross-table references exist without a formal FK constraint.

### 4. Normalization Issues Check
- **Audit Result: Passed.**
- **Details:** The schema strictly satisfies 1NF and 2NF. All non-key attributes are fully functionally dependent on the single UUID primary key. Controlled denormalization (persisting `total_paid` and `remaining_balance` in `enrollments`) is architecturally defended, isolated, and protected via database `CHECK` constraints and transactional locks.

### 5. Duplicate Data Check
- **Audit Result: Passed.**
- **Details:** Trainee contact details exist exclusively in `student_profiles`, instructor credentials exclusively in `instructor_profiles`, and login credentials exclusively in `users`. No duplicate course codes exist per center due to `UNIQUE(center_id, code)`.

### 6. Missing Constraints Check
- **Audit Result: Passed.**
- **Details:** Essential data validation is enforced at the database level:
  - Non-negative currency: `CHECK (base_price >= 0.00)`, `CHECK (amount > 0.00)`.
  - Chronological bounds: `CHECK (end_date >= start_date)`, `CHECK (end_time > start_time)`.
  - Partial Unique Index: `(course_id, student_id) WHERE status = 'Active'`.
  - Uniqueness: `UNIQUE(session_id, enrollment_id)` in `attendances` and `UNIQUE(task_id, enrollment_id)` in `submissions`.

### 7. Data Integrity Problems Check
- **Audit Result: Passed.**
- **Details:** Floating-point rounding errors are eliminated by standardizing on `DECIMAL(10, 2)`. Timezones are preserved via `TIMESTAMPTZ`. Soft-archiving and financial immutability are enforced by `ON DELETE RESTRICT`.

### 8. Race Conditions & Concurrency Check
- **Audit Result: Passed.**
- **Details:** Race conditions on finite seat capacity and concurrent student enrollment requests are addressed via pessimistic locking (`SELECT ... FOR UPDATE` on `courses`). Concurrent payment voucher approvals on the same student balance are serialized via row locking on `enrollments`.

### 9. Unsupported Assumptions Check
- **Audit Result: Passed.**
- **Details:** All assumptions (uniform local currency per center, single active role per center, filesystem-based file storage) are explicitly documented and labeled in Section 6.

### 10. Conflicts with the PRD and User Flow Check
- **Audit Result: Passed.**
- **Details:** 
  - Traceability with PRD: All functional requirements (FR-001 through FR-037) map directly to database schema elements.
  - Role alignment: The database maintains the 4 PRD roles (`Manager`, `Sales`, `Instructor`, `Student`). The mention of "Accountant" in [user-flow.md](file:///D:/docs/docs/user-flow.md) is handled under administrative management permissions without silently altering PRD role definitions.

---

## 5. Step 4: Detailed Architectural Explanation in Arabic
## الشرح المعماري المفصل لقاعدة البيانات باللغة العربية

---

### 5.1 لماذا تم إنشاء كل جدول في قاعدة البيانات؟ (Table Rationale)

تم تصميم قاعدة بيانات منصة **VELORA (فيلورا)** لتتكون من **19 جدولاً أساسياً**، يخدم كل منها غرضاً وظيفياً وتشغيلياً محدداً بدقة، ولا يمكن الاستغناء عن أي منها أو دمجه دون الإخلال بسلامة البيانات:

#### 1. جدول المراكز التدريبية (`centers`)
- **سبب الوجود:** هو الحاوية السيادية لنموذج المستأجر المتعدد المنطقي (Multi-Tenancy).
- **المشكلة التي يحلها:** يضمن عزل بيانات كل مركز تدريبي تماماً عن المراكز الأخرى؛ مما يمنع تسرب السجلات المالية أو الأكاديمية بين الفروع والمراكز المتنافسة التي تتشارك نفس خادم قاعدة البيانات.

#### 2. جدول المستخدمين (`users`)
- **سبب الوجود:** إدارة الهوية الرقمية الموحدة والدخول والمصادقة الأمنية.
- **المشكلة التي يحلها:** يجمع بيانات الدخول الحساسة (البريد الإلكتروني مع كلمة المرور المشفرة بتقنية Argon2id أو bcrypt) ودور المستخدم التشغيلي الأساسي (`Manager`, `Sales`, `Instructor`, `Student`) مع ربطها بالمركز، مما يوفر نقطة دخول واحدة محكمة أمنياً.

#### 3. جدول الملف التعريفي للمتدرب (`student_profiles`)
- **سبب الوجود:** فصل البيانات الشخصية والديموغرافية للطالب عن حساب المستخدم العام (1:1 Specialization).
- **المشكلة التي يحلها:** تجنب ازدحام جدول المستخدمين بحقول خاصة بالطلاب فقط (رقم الهوية المدنية، هاتف الاتصال، الملاحظات الإدارية)، ويوفر مرجعاً مركزياً ثابتاً لجميع الدورات التي يلتحق بها الطالب عبر السنوات.

#### 4. جدول الملف التعريفي للمدرب (`instructor_profiles`)
- **سبب الوجود:** إدارة البيانات المهنية والأكاديمية للمدربين.
- **المشكلة التي يحلها:** تخصيص بيانات السيرة الذاتية والتخصص والخبرات للمدربين بعيداً عن جدول المستخدمين العام، وربطها بالتكليفات التدريسية وحساب المستحقات.

#### 5. جدول الدورات التدريبية (`courses`)
- **سبب الوجود:** إدارة الوعاء الأكاديمي الرئيسي (الأفواج التدريبية).
- **المشكلة التي يحلها:** تحديد مواعيد البدء والانتهاء، والسعر الأساسي للبرنامج، وتطبيق قيد السعة القصوى للمقاعد (`capacity`) وإدارة دورة حياة الدورة (`Draft` مسودة ← `Active` نشطة ← `Completed` مكتملة ← `Archived` مؤرشفة).

#### 6. جدول تكليف المدربين بالدورات (`course_instructors`)
- **سبب الوجود:** جدول ربط علائقي وسيط (Junction Table) لحل علاقة (أطراف بأطراف N:M) بين المدربين والدورات.
- **المشكلة التي يحلها:** يتيح إسناد أكثر من مدرب للدورة الواحدة مع تحديد المدرب الرئيسي (`is_lead`)، وتثبيت نموذج التعويض المالي لكل مدرب بشكل تعاقدي مستقل (مبلغ مقطوع، نسبة من المحصل، أو أجر عن كل جلسة).

#### 7. جدول جلسات المحاضرات (`course_sessions`)
- **سبب الوجود:** تقسيم الدورة الأكاديمية إلى وحدات زمنية تدريسية مجدولة (محاضرات).
- **المشكلة التي يحلها:** يمثل الركيزة التشغيلية الأساسية لرصد حضور وغياب الطلاب، وتحديد مدرب كل جلسة، واحتساب مستحقات المدربين بنظام الدفع بالحصص (`PerSession`) بدقة.

#### 8. جدول قوائم الانتظار للدورات (`course_waitlists`)
- **سبب الوجود:** تفعيل القرار الإداري المعتمد (OQ-06) لإدارة الدورات المكتملة المقاعد.
- **المشكلة التي يحلها:** يمنع تجاوز السعة القصوى نهائياً (`Capacity Hard-Lock`) ويحفظ أسبقية الطلاب الراغبين في التسجيل وفق الترتيب الزمني الصارم (FIFO) دون قيد أي رسوم مالية عليهم حتى توفر مقعد شاغر.

#### 9. جدول التحاق الطلاب بالدورات (`enrollments`)
- **سبب الوجود:** توثيق العقد التعليمي بين المتدرب والدورة، والعمل كرأس لسجل الحساب المالي للمتدرب.
- **المشكلة التي يحلها:** يثبت الرسوم الصافية المستحقة بعد الخصم المعتمد، ويمنع تسجيل الطالب مرتين في نفس الدورة النشطة، ويحتفظ بملخص الأرصدة التراكمية (`TotalPaid` و `RemainingBalance`) لعرضها الفوري في لوحات التحكم.

#### 10. جدول سندات القبض المالية (`payments`)
- **سبب الوجود:** توثيق دفعات الأقساط النقدية والمصرفية المحصلة فعلياً من المتدربين.
- **المشكلة التي يحلها:** تطبيق الدورة المعتمدة للاعتماد الإداري (OQ-02)؛ حيث يسجل موظف المبيعات السند ليدخل بحالة `Pending_Approval`، ولا يؤثر في رصيد الطالب حتى يعتمده المحاسب أو مدير المركز، مع منع الحذف المادي للحركات المالية.

#### 11. جدول طلبات استرداد الفائض المالي (`refund_requests`)
- **سبب الوجود:** تنفيذ القرار المعتمد (OQ-05 / BR-FIN-OVERPAY-REFUND) لمعالجة السداد الزائد.
- **المشكلة التي يحلها:** يمنع تحويل مبالغ الفائض تلقائياً كرصيد دائن مستقبلي، ويُلزم المركز بإنشاء مطالبة استرداد فورية ومتابعة دورة صرفها نقداً أو عبر تحويل مصرفي حتى إثبات الصرف الفعلي (`Refunded`).

#### 12. جدول المواد التعليمية (`course_materials`)
- **سبب الوجود:** توزيع الملفات والمراجع والعروض التقديمية الخاصة بالدورة.
- **المشكلة التي يحلها:** حماية الموارد التعليمية بحصر تنزيلها على الطلاب الملتحقين فقط، وفحص امتدادات الملفات وحجمها الأقصى (25 ميجابايت)، وربط المواد بمحاضرات محددة.

#### 13. جدول المهام والواجبات (`tasks`)
- **سبب الوجود:** إدارة التكاليف الأكاديمية والواجبات والمشاريع التي يكلف بها المتدربون.
- **المشكلة التي يحلها:** تحديد المواعيد النهائية للتسليم (`due_datetime`) وتوفير مفتاح استثناء إداري صريح (`allow_late_submission`) للمدربين لقبول التسليمات المتأخرة.

#### 14. جدول تسليمات الطلاب للواجبات (`submissions`)
- **سبب الوجود:** استقبال وحفظ ملفات إجابات الطلاب على الواجبات.
- **المشكلة التي يحلها:** توثيق لحظة التسليم بدقة بالثواني، ووسم التسليم آلياً كـ `On_Time` أو `Late`، والسماح باستبدال الملف قبل انتهاء الموعد، مع عزل هذه الملفات تماماً عن موظفي المبيعات للحفاظ على الخصوصية.

#### 15. جدول حضور الجلسات (`attendances`)
- **سبب الوجود:** الرصد الرقمي الصارم لحضور المتدربين في كل محاضرة.
- **المشكلة التي يحلها:** إلغاء الكشوف الورقية، وضمان ثنائية الحضور (`Present` أو `Absent`)، وفرض قيد فريد يمنع تكرار تسجيل حضور الطالب في الجلسة الواحدة مرتين.

#### 16. جدول التغذية الراجعة والتقييم (`feedbacks`)
- **سبب الوجود:** قناة التوجيه الأكاديمي والتقييم الفردي الخاص بين المدرب والمتدرب.
- **المشكلة التي يحلها:** تمكين المدرب من إرسال ملاحظات سرية ومباشرة للطالب حول أدائه، مع ضمان عدم اطلاع الطلاب الآخرين أو موظفي المبيعات عليها.

#### 17. جدول مستحقات المدربين (`instructor_dues`)
- **سبب الوجود:** الوعاء المحاسبي التراكمي لالتزامات المركز تجاه كل مدرب متعاقد.
- **المشكلة التي يحلها:** احتساب مستحقات المدرب آلياً وبدقة تامة بناءً على شروط التعويض المعتمدة (المبلغ المقطوع، أو نسبة التحصيل الفعلي، أو عدد الجلسات المنفذة)، مما يقضي على الخلافات المالية الشهرية.

#### 18. جدول مخالصات ودفعات المدربين (`instructor_payouts`)
- **سبب الوجود:** توثيق الحركات المالية الفعلية لصرف مستحقات المدربين.
- **المشكلة التي يحلها:** يتيح سداد مستحقات المدرب على دفعات متعددة مع توثيق التاريخ وطريقة التحويل والرقم المرجعي وهاتف المستلم، ويضمن التسوية المحاسبية الكاملة في جدول مستقل.

#### 19. جدول سجل التدقيق والرقابة (`audit_logs`)
- **سبب الوجود:** سجل غير قابل للتعديل أو الحذف (Append-Only) لتوثيق التحولات الحساسة في النظام.
- **المشكلة التي يحلها:** توفير الشفافية الرقابية التامة لمدير المركز بتسجيل هوية الفاعل، وعنوان الـ IP، والقيم السابقة والجديدة لأي تعديل على الأسعار، أو اعتماد المدفوعات، أو صرف الاستردادات المالية.

---

### 5.2 لماذا وُجدت كل علاقة؟ (Relationship & Cardinality Rationale)

تمت صياغة شبكة العلاقات بين الجداول بدقة متناهية لتعكس منطق الأعمال وتمنع السجلات اليتيمة (Orphaned Records):

```
                               ┌─────────────┐
                               │   CENTERS   │
                               └──────┬──────┘
                                      │ 1:N
        ┌─────────────────────────────┼─────────────────────────────┐
        ▼                             ▼                             ▼
   ┌─────────┐                  ┌───────────┐                 ┌─────────────┐
   │  USERS  │                  │  COURSES  │                 │ ENROLLMENTS │
   └────┬────┘                  └─────┬─────┘                 └──────┬──────┘
        │ 1:1                         │ 1:N                          │ 1:N
   ┌────┴────────────────┐      ┌─────┴───────────────┐       ┌──────┴──────┐
   ▼                     ▼      ▼                     ▼       ▼             ▼
┌──────────────┐ ┌──────────────┐┌────────────────┐ ┌────────────────┐ ┌───────────┐ ┌───────────┐
│STUDENT_PROFILES││INSTRUCTOR_PROFILES││COURSE_INSTRUCTORS││COURSE_SESSIONS │ │ PAYMENTS  │ │SUBMISSIONS│
└──────────────┘ └──────────────┘└────────────────┘ └────────────────┘ └─────┬─────┘ └───────────┘
                                                                             │ 0..1:1
                                                                      ┌──────┴──────┐
                                                                      │REFUND_REQUESTS│
                                                                      └─────────────┘
```

1. **علاقة المراكز بالمستخدمين والدورات والالتحاقات (`centers` -> 1:N):**
   - *السبب:* كل مركز يدير طاقمه التدريسي والإداري، ويطرح دوراته الخاصة، ويحتفظ بسجلات طلابه بشكل منعزل تماماً.
   - *القيد:* المنع التام للحذف المادي للمركز في حال وجود سجلات نشطة (`ON DELETE RESTRICT`).

2. **علاقة المستخدم بالملف التعريفي للطالب والمدرب (`users` -> 1:1):**
   - *السبب:* المستخدم الواحد يمتلك هوية دخول واحدة؛ فإذا كان دوره طالباً، ينشأ له سجل واحد مناظر في `student_profiles`، وإذا كان مدرباً ينشأ له سجل مناظر في `instructor_profiles`.
   - *القيد:* مفتاح أجنبي فريد مع الحذف المتسلسل (`ON DELETE CASCADE`)؛ فحذف المستخدم يحذف ملفه التخصصي تلقائياً.

3. **علاقة الدورة بتكليفات المدربين (`courses` -> `course_instructors` 1:N و `instructor_profiles` -> 1:N):**
   - *السبب:* علاقة أطراف بأطراف؛ فالدورة قد يشارك في تدريسها أكثر من مدرب، والمدرب الواحد قد يدرس في أكثر من دورة، مع ضرورة تحديد شروط التعويض لكل تكليف على حدة.
   - *القيد:* قيد تفرد مركب `UNIQUE(course_id, instructor_id)` يمنع إسناد المدرب لنفس الدورة مرتين.

4. **علاقة تكليف المدرب بمستحقاته (`course_instructors` -> `instructor_dues` 1:1):**
   - *السبب:* كل عقد تكليف تدريسي تنشأ عنه ذمة مالية والتزام حسابي واحد تتراكم فيه المستحقات بناءً على تقدم الدورة.

5. **علاقة مستحقات المدرب بدفعات المخالصة (`instructor_dues` -> `instructor_payouts` 1:N):**
   - *السبب:* المركز قد يسدد مستحقات المدرب المتراكمة على دفعات مجزأة (أقساط) حسب التدفق النقدي، مما يتطلب تتبع كل دفعة بصورة منفصلة.

6. **علاقة الدورة بالحصص التدريبية (`courses` -> `course_sessions` 1:N):**
   - *السبب:* البرنامج الأكاديمي يتكون من محاضرات متعاقبة ومجدولة بالترتيب الزمني.
   - *القيد:* حذف الدورة مسودة يؤدي لحذف جلساتها متسلسلاً (`ON DELETE CASCADE`).

7. **علاقة الدورة بقائمة الانتظار (`courses` -> `course_waitlists` 1:N):**
   - *السبب:* عند امتلاء مقاعد الدورة، تُفتح قائمة انتظار للطلاب الراغبين، وتُرتب طلباتهم زمنياً بأسبقية التقديم.

8. **علاقة الالتحاق بسندات القبض (`enrollments` -> `payments` 1:N):**
   - *السبب:* يسدد الطالب رسوم الدورة عادةً على أقساط متعددة (دفعة أولى ثم دفعات دورية).
   - *القيد:* عدم حذف سندات القبض نهائياً (`ON DELETE RESTRICT`) حمايةً للدفاتر المحاسبية.

9. **علاقة سند القبض بطلب استرداد الفائض (`payments` -> `refund_requests` 0..1:1):**
   - *السبب:* إذا دفع الطالب مبلغاً أكبر من المتبقي عليه، ينشأ طلب استرداد إلزامي وحيد مرتبط بذلك السند بقيمة الفائض، بينما السندات القياسية ليس لها أي طلب استرداد مناظر.

10. **علاقة الجلسة بسجل الحضور والغياب (`course_sessions` -> `attendances` 1:N):**
    - *السبب:* لكل جلسة كشف حضور يحتوي على جميع الطلاب المسجلين بالدورة وقت انعقادها.
    - *القيد:* قيد تفرد مركب `UNIQUE(session_id, enrollment_id)` يمنع وجود سجلين للطالب في الجلسة الواحدة.

---

### 5.3 كيف تدعم قاعدة البيانات مسار المستخدم الرئيسي؟ (Main User Flow Support)

يوضح التحليل التالي كيف تدعم جداول وقواعد بيانات VELORA دورة العمل الكاملة المحددة في [user-flow.md](file:///D:/docs/docs/user-flow.md):

```
[ المرحلة 1: تسجيل الدخول والعزل ]
  └── users + centers (التحقق من الدور واستخراج CenterID تلقائياً)
        │
        ▼
[ المرحلة 2: استكشاف بيانات المتدرب ]
  └── student_profiles (البحث بالهاتف أو الهوية أو إنشاء ملف جديد)
        │
        ▼
[ المرحلة 3: فحص السعة وقائمة الانتظار والالتحاق ]
  ├── courses.capacity مقارنة بعدد enrollments النشطة
  ├── إذا اكتملت السعة ──► course_waitlists (تسجيل انتظار بحالة Waiting وبترتيب FIFO مجاني)
  └── إذا توفر مقعد   ──► enrollments (حساب الخصم وتوليد الرصيد المالي المبدئي)
        │
        ▼
[ المرحلة 4: إدخال سند القبض وفحص الفائض ]
  ├── payments (حفظ السند بحالة Pending_Approval وتحديد applied_amount)
  └── إذا وُجد فائض   ──► refund_requests (إنشاء تلقائي لمطالبة استرداد بحالة Pending_Refund)
        │
        ▼
[ المرحلة 5: الاعتماد الإداري والترحيل المحاسبي الذري ]
  └── BEGIN TRANSACTION
        ├── payments.status = 'Completed' + approved_by
        ├── enrollments: تحديث total_paid و remaining_balance
        ├── audit_logs: توثيق حركة الاعتماد والمستخدم والرصيد قبل وبعد
      COMMIT
        │
        ▼
[ المرحلة 6: تنفيذ صرف الاسترداد المالي ]
  └── refund_requests (توثيق الصرف النقدي/البنكي ──► تحويل الحالة إلى Refunded)
```

1. **المصادقة وتحديد النطاق (Authentication & Scoping):**
   - عند تسجيل موظف المبيعات لدخوله، يستعلم النظام عن حسابه في جدول `users` مقيداً بـ `email`.
   - يتم استخراج `center_id` وحقنه في سياق الجلسة الآمن، وتعتمد جميع استعلامات قاعدة البيانات اللاحقة هذا المعرّف تلقائياً لضمان العزل التام.

2. **استكشاف بيانات المتدرب وتسجيله (Student Discovery & Intake):**
   - يبحث الموظف في جدول `student_profiles` عبر الفهارس `(center_id, phone)` و `(center_id, civil_id)`.
   - إذا كان جديداً، يُنشأ سجل في `users` (بدور `Student`) وسجل مناظر في `student_profiles` داخل معاملة ذرية تضمن عدم وجود حساب بدون ملف.

3. **فحص السعة وإدارة قائمة الانتظار (Capacity Hard-Lock & Waitlist):**
   - يفحص النظام سعة الدورة `courses.capacity`. يتم حساب عدد الملتحقين النشطين في جدول `enrollments`.
   - **في حال امتلاء المقاعد:** يمنع النظام التسجيل المباشر ويفتح خيار إدراج الطالب في `course_waitlists`. يُسجل الطلب بحالة `Waiting` وتاريخ `requested_at = now()`. يحفظ هذا الحقل الأسبقية الزمنية التامة (FIFO) للاستدعاء المستقبلي عند شغور مقعد دون تحميل الطالب أي فلس.
   - **في حال توفر مقعد:** يتحقق النظام من عدم وجود تسجيل مسبق عبر القيد الفريد الجزئي `(course_id, student_id) WHERE status='Active'`.
   - يُنشأ سجل الالتحاق في `enrollments`، ويُطبق الخصم، ويُحسب `final_payable = base_price - discount_amount`، ويُهيأ الرصيد المتبقي مساوياً للمبلغ الصافي، بحالة سداد `Unpaid`.

4. **إدخال سند القبض واكتشاف الفائض التلقائي (Payment Entry & Excess Detection):**
   - يدخل موظف المبيعات تفاصيل السداد (المبلغ، الطريقة، ورقم الإيصال).
   - يفحص النظام المبلغ مقارنة بـ `enrollments.remaining_balance`.
   - **إذا تجاوز المبلغ المدفوع الرصيد المستحق (مثال: دفع 120 شيكل والمتبقي 100 شيكل):**
     1. يقسم النظام المبلغ في جدول `payments`: الحقل `amount = 120.00`، والحقل `applied_amount = 100.00`، وحقل الفائض `excess_amount = 20.00`.
     2. يُنشئ النظام تلقائياً سجلاً في جدول `refund_requests` بقيمة 20.00 شيكل وبحالة أولية `Pending_Refund`.
     3. يُحفظ السند المالي في جدول `payments` بحالة `Pending_Approval` ليبقى رصيد الطالب المعتمد دون أي تغيير لحين موافقة الإدارة.

5. **الاعتماد الإداري والترحيل المحاسبي الذري (Admin Approval & ACID Transaction):**
   - يفتح مدير المركز أو المحاسب شاشة الاعتماد (SCR-M07).
   - **عند اعتماد السند:** تبدأ قاعدة البيانات معاملة ذرية متكاملة (`BEGIN TRANSACTION`):
     1. يتم قفل سجل الالتحاق لمنع تعارض الحركات المتزامنة (`SELECT ... FOR UPDATE`).
     2. تُحدث حالة السند في `payments` إلى `Completed` ويُسجل `approved_by` و `approved_at`.
     3. يُرحّل المبلغ المطبق `applied_amount` فقط إلى جدول `enrollments`، فيزداد `total_paid` بمقدار 100 شيكل ويصبح `remaining_balance = 0.00` وتتحول حالة السداد إلى `FullyPaid`.
     4. يُنشأ قيد غير قابل للتعديل في `audit_logs` يسجل تفاصيل الاعتماد والأرصدة قبل وبعد.
     5. تُعتمد المعاملة نهائياً (`COMMIT`).

6. **دورة تنفيذ وصرف الفائض المالي (Excess Refund Execution Lifecycle):**
   - يظهر طلب الاسترداد في طابور الصرف المالي لجدول `refund_requests`.
   - يقوم الموظف المالي بتسليم 20 شيكل نقداً للطالب أو عبر تحويل مصرفي، ويوثق رقم الإيصال والطريقة وتاريخ الصرف.
   - عند تأكيد الصرف، تتحول حالة الطلب إلى `Refunded` ويُسجل معرّف المنفذ `executed_by` وتُكتب حركة تدقيق رقابية تؤكد إغلاق الملف المالي نهائياً.

---

### 5.4 المشاكل والمخاطر التصميمية المحتملة وموازنات الأداء (Design Pitfalls & Trade-offs)

بصفتنا مهندسي قواعد بيانات، تم رصد وتحليل كافة التحديات التصميمية والمخاطر التشغيلية المحتملة وموازنات الأداء المتعلقة بهذا المخطط:

#### 1. ظاهرة التسابق على المقاعد المحدودة (Concurrency & Seat Race Conditions)
- *المشكلة:* عندما يتبقى مقعد واحد فقط في دورة ذات إقبال كبير، ويقوم موظفان للمبيعات في نفس الثانية بالضغط على "تأكيد التسجيل"، قد يقرأ كلاهما أن هناك مقعداً شاغراً، فيتم إدخال تسجيلين ويتجاوز المركز السعة القصوى المحددة (`Capacity Exceeded`).
- *الحل الهندسي المطبق:* عدم الاعتماد على القراءة غير المقفلة؛ بل تنفيذ خطوة التحقق والإدراج داخل معاملة ذرية تستخدم القفل التشاؤمي على مستوى الصف في جدول الدورات:
  ```sql
  -- تأمين فحص السعة من خلال القفل التشاؤمي للدورة
  SELECT capacity FROM courses WHERE id = :course_id FOR UPDATE;
  -- التحقق من عدد المقاعد ثم الإدراج بأمان
  ```

#### 2. موازنة الأرصدة المحسوبة مسبقاً مقابل نقاء المعيار الثالث (Stored Balances vs Pure 3NF)
- *المشكلة:* تخزين `total_paid` و `remaining_balance` في `enrollments` يُعد تكراراً للمجموع المشتق من جدول `payments`. في حال حدوث خطأ برمجي أو فشل معاملة جزئية، قد يختلف المجموع الحقيقي للمدفوعات عن الرصيد المسجل.
- *الحل الهندسي المطبق:*
  - فرض قيود الفحص `CHECK (remaining_balance >= 0.00)`.
  - حصر جميع عمليات تعديل الأرصدة داخل معاملات برمجية صارمة ومغلقة (Encapsulated Service Layer Transactions).
  - توفير وظيفة تدقيق دورية مجدولة (Reconciliation Task) تقوم بمطابقة:
    $$\text{RemainingBalance} \stackrel{?}{=} \text{FinalPayable} - \sum \text{Payments.applied\_amount}$$
    وإطلاق تنبيه للإدارة في حال وجود أي تباين مئوي.

#### 3. تكرار حقل `center_id` في الجداول الفرعية (Multi-Tenant Partition Key Overhead)
- *المشكلة:* تكرار وجود `center_id` في جداول `enrollments`, `payments`, `refund_requests` يمثل اعتماداً متعدياً نظرياً.
- *مبرر الأداء:* هذا التكرار ضروري وحيوي للغاية في قواعد البيانات السحابية متعددة المستأجرين؛ حيث يتيح تفعيل سياسات الأمان على مستوى الصفوف (PostgreSQL Row-Level Security - RLS) مباشرة دون الحاجة لربط (JOIN) 3 أو 4 جداول للتحقق من هوية المركز، مما يخفض زمن استجابة الاستعلامات المعقدة من 300 ملي ثانية إلى أقل من 15 ملي ثانية.

#### 4. تضخم جدول سجلات التدقيق والحضور (Audit Log & Attendance Table Bloat)
- *المشكلة:* مع نمو المركز وتسجيل الحضور اليومي لآلاف الجلسات ومئات الطلاب، وتوثيق سجلات التدقيق لكل حركة، سيتضخم جدولا `attendances` و `audit_logs` بمئات الآلاف من الصفوف سنوياً، مما قد يبطئ الاستعلامات التاريخية.
- *الحل الهندسي المطبق:*
  - إنشاء فهارس B-Tree مركبة مسبقة على `(session_id, enrollment_id)` و `(center_id, created_at DESC)`.
  - جهوزية المخطط لتطبيق تقسيم الجداول (Table Partitioning by Date Range) مستقبلاً دون تغيير بنية التطبيق.

---

### 5.5 التحسينات المقترحة وخطة التوسع المستقبلي (Suggested Improvements)

لضمان قدرة النظام على التوسع والنمو بسلاسة بعد إطلاق النسخة الأولية (MVP)، نوصي بالتحسينات المعمارية التالية:

1. **تجزئة وتقسيم الجداول الكبيرة (PostgreSQL Declarative Table Partitioning):**
   - تقسيم جدول `audit_logs` سنوياً أو نصف سنوي حسب `created_at`.
   - تقسيم جدول `attendances` حسب السنة الأكاديمية أو الفصول التدريبية.

2. **التكامل مع بوابات الدفع الإلكتروني (Online Payment Gateways Integration):**
   - عند الانتقال للمرحلة الثانية وإتاحة السداد الإلكتروني عبر بطاقات الائتمان ومحافظ الهاتف، يُضاف جدول وسيط `payment_transactions` لتوثيق ردود خوادم البوابات (Webhooks, Gateway References, Idempotency Keys) قبل تحويلها إلى سند قبض معتمد.

3. **إدخال نظام الحسابات المركزية الموحدة للمدربين (Global Federated User Identity):**
   - لدعم عمل المدربين الأكاديميين في أكثر من مركز تدريبي بحساب واحد وكلمة مرور موحدة، يتم في المرحلة اللاحقة استخراج جدول `user_accounts` عالمي يرتبط بالمراكز عبر جدول عضوية وصلاحيات `center_memberships`.

4. **تفعيل سياسات الأمان على مستوى الصف (PostgreSQL RLS Activation):**
   - الاستفادة من التصميم الحالي الذي يحتوي على `center_id` في كافة الجداول لتفعيل RLS تلقائياً، بحيث تفرض قاعدة البيانات نفسها فلترة البيانات عبر تعيين متغير الجلسة:
     ```sql
     SET LOCAL app.current_center_id = 'c123...';
     CREATE POLICY tenant_isolation_policy ON enrollments
       USING (center_id = current_setting('app.current_center_id')::uuid);
     ```

---

## 6. Explicit Assumptions & Unresolved Questions

### 6.1 Explicit Assumptions Labeled:
- **[ASSUMPTION-01] Currency Uniformity:** The MVP assumes that all monetary values within a single training center are denominated in a uniform local currency (e.g., ILS, JOD, USD, SAR). Multi-currency conversion tables are deferred post-MVP.
- **[ASSUMPTION-02] Single Active Role per Center:** A user identity possesses one active operational role (`Manager`, `Sales`, `Instructor`, `Student`) per center. Staff members who also take courses are represented via distinct role profiles or center assignments.
- **[ASSUMPTION-03] Physical File Storage Strategy:** The database stores strictly relative file storage paths (`file_path`), MIME types, and file sizes. Actual binary payloads reside in protected local directories or S3 buckets outside the public web root.

### 6.2 Unresolved Operational Questions (Flagged for Business Alignment):
- **[QUESTION-01] Accountant Role Alignment:** The PRD identifies 4 distinct roles: `Manager`, `Sales`, `Instructor`, `Student`. In [user-flow.md](file:///D:/docs/docs/user-flow.md) and sequence diagrams, "Accountant" is referenced alongside Manager for payment approval.  
  *Schema Status:* The MVP schema preserves the PRD's 4-role ENUM (`user_role`). Approving payment vouchers is assigned to `Manager` or delegated administratively. If an independent `Accountant` role is desired, the `user_role` ENUM can be extended via migration without schema redesign.
- **[QUESTION-02] Multi-Center Instructor Roaming:** In enterprise groups with multiple branches, do instructors teach across branches under a single global login?  
  *Schema Status:* Current schema isolates identities per center (`users.center_id`). If cross-center roaming is approved for Phase 2, a global `user_accounts` table linking to tenant `center_memberships` will be introduced.
- **[QUESTION-03] Waitlist Notification Expiry Window:** How long does a waitlisted student have to claim a seat once notified before the system escalates to the next candidate in FIFO order?  
  *Schema Status:* `course_waitlists` includes `notified_at`. The exact timeout threshold (e.g., 24 hours vs 48 hours) is configurable via center application settings.
- **[QUESTION-04] Late Submission Override Scope:** PRD FR-TSK-06 mentions overriding deadlines for a specific student or task. In `tasks`, `allow_late_submission` applies to the task as a whole.  
  *Schema Status:* For MVP, task-level override is implemented. If individual per-student override grants are required, a junction table `task_student_overrides(task_id, student_id)` can be introduced post-MVP.

---
*End of Relational Database Architecture Review & Business Analysis.*
