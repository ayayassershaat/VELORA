
# Design-Level UML Class Diagram Specification (مواصفات ومخطط الأصناف البرمجية)
## VELORA (فيلورا) — Training Center Management Platform

- **Document Version:** 1.0.0  
- **Document Type:** Design-Level UML Class Diagram & Object-Oriented Architecture Specification  
- **Target Release:** Confirmed MVP (Minimum Viable Product)  
- **Primary Sources of Truth:** [PRD.md](file:///D:/docs/PRD.md), [domain-model.md](file:///D:/docs/diagrams/domain-model.md), [ERD.md](file:///D:/docs/docs/ERD.md), [database.dbml](file:///D:/docs/docs/database.dbml), and [sequence-diagrams.md](file:///D:/docs/docs/diagrams/sequence-diagrams.md)  
- **Companion Raw Mermaid Source:** [class-diagram.mmd](file:///D:/docs/diagrams/class-diagram.mmd)  
- **Status:** Approved Baseline  

---

## 1. Executive Summary & Design Methodology

### 1.1 Objective & Architectural Scope
This document specifies the formal **Design-Level UML Class Diagram** for the core domain layer of the **VELORA Training Center Management Platform**. While conceptual domain models focus on high-level business concepts and Entity-Relationship Diagrams (ERDs) define relational database schemas, this design-level class diagram defines the **object-oriented software architecture** that governs the business logic, entity encapsulation, operational behavior, and structural invariants of the application.

### 1.2 Object-Oriented Modeling Principles
In strict compliance with UML 2.5 Superstructure standards and Domain-Driven Design (DDD) tactical design patterns:
1. **Rich Domain Entities vs. Anemic Models:** Classes encapsulate both state (private attributes with explicit types) and verified business operations (public methods), eliminating anemic models while avoiding framework-specific coupling (such as Laravel Eloquent or Next.js state hooks).
2. **Composition vs. Inheritance:** Unnecessary and brittle inheritance hierarchies (e.g., subclassing `User` into `Student` or `Instructor`) are avoided. Instead, behavioral and data specializations are modeled via **1-to-1 Composition (`*--`)** with dedicated profile entities (`StudentProfile`, `InstructorProfile`), guaranteeing that identity and authentication remain decoupled from academic profiles.
3. **Composition (`*--`) vs. Aggregation (`o--`) Semantics:**
   - **Composition (`*--`):** Applied where a child entity has an existential dependency on the parent aggregate root (e.g., `Course` strictly owns `CourseSession`, `CourseInstructor`, and `Task`; `Enrollment` strictly owns `Payment` vouchers; `CourseInstructor` owns `InstructorDue`). If the root is removed or archived, the composed parts share that fate.
   - **Aggregation (`o--` / Association `-->`):** Applied where entities have independent lifecycles but are linked operationally (e.g., `CourseInstructor` references `InstructorProfile`; `Enrollment` references `StudentProfile`).
4. **Behavioral Grounding in Documented Sequences:** Every operation modeled on a domain class is directly justified by documented interactions in [sequence-diagrams.md](file:///D:/docs/docs/diagrams/sequence-diagrams.md), [user-flow.md](file:///D:/docs/docs/user-flow.md), and [state-diagrams.md](file:///D:/docs/diagrams/state-diagrams.md).

---

## 2. Mermaid Class Diagram

The following design-level class diagram contains all 19 domain classes, their attributes with precise design types, operational methods, and verified relationship cardinalities. The standalone `.mmd` source is available in [class-diagram.mmd](file:///D:/docs/diagrams/class-diagram.mmd).

```mermaid
classDiagram
    direction TB

    %% ========================================================
    %% VELORA PLATFORM - DESIGN-LEVEL UML CLASS DIAGRAM (MVP)
    %% Domain Layer Architecture & Object-Oriented Specification
    %% ========================================================

    %% --------------------------------------------------------
    %% 1. IDENTITY & TENANCY MANAGEMENT CONTEXT
    %% --------------------------------------------------------
    class Center {
        -UUID id
        -String name
        -String code
        -CenterStatus status
        -DateTime createdAt
        -DateTime updatedAt
        +activate() void
        +suspend() void
        +isOperational() Boolean
    }

    class User {
        -UUID id
        -UUID centerId
        -String fullName
        -String email
        -String passwordHash
        -UserRole role
        -AccountStatus status
        -DateTime createdAt
        -DateTime updatedAt
        +verifyPassword(String plainPassword) Boolean
        +updatePassword(String newHash) void
        +suspend() void
        +reactivate() void
        +archive() void
        +hasRole(UserRole requiredRole) Boolean
    }

    class StudentProfile {
        -UUID id
        -UUID userId
        -UUID centerId
        -String phone
        -String civilId
        -String notes
        -DateTime createdAt
        -DateTime updatedAt
        +updateContact(String phone, String civilId, String notes) void
    }

    class InstructorProfile {
        -UUID id
        -UUID userId
        -UUID centerId
        -String phone
        -String specialization
        -String bio
        -DateTime createdAt
        -DateTime updatedAt
        +updateQualifications(String specialization, String bio, String phone) void
    }

    %% --------------------------------------------------------
    %% 2. ACADEMIC & COURSE MANAGEMENT CONTEXT
    %% --------------------------------------------------------
    class Course {
        -UUID id
        -UUID centerId
        -String title
        -String code
        -String description
        -Decimal basePrice
        -Date startDate
        -Date endDate
        -Integer capacity
        -CourseStatus status
        -DateTime createdAt
        -DateTime updatedAt
        +publish() void
        +conclude() void
        +archive() void
        +isCapacityReached(Integer activeEnrolledCount) Boolean
        +assignInstructor(InstructorProfile instructor, CompensationModel model, Decimal value, Boolean isLead) CourseInstructor
        +scheduleSession(Date sessionDate, Time startTime, Time endTime, String topic, InstructorProfile instructor) CourseSession
        +canAcceptEnrollments() Boolean
    }

    class CourseInstructor {
        -UUID id
        -UUID courseId
        -UUID instructorId
        -CompensationModel compensationModel
        -Decimal modelValue
        -Boolean isLead
        -DateTime createdAt
        -DateTime updatedAt
        +updateTerms(CompensationModel model, Decimal value, Boolean isLead) void
        +setLeadStatus(Boolean isLead) void
    }

    class CourseSession {
        -UUID id
        -UUID courseId
        -UUID instructorId
        -Date sessionDate
        -Time startTime
        -Time endTime
        -String topic
        -Integer sessionOrder
        -SessionStatus status
        -DateTime createdAt
        -DateTime updatedAt
        +complete() void
        +cancel(String reason) void
        +reassignInstructor(InstructorProfile newInstructor) void
        +isDelivered() Boolean
    }

    class CourseWaitlist {
        -UUID id
        -UUID courseId
        -UUID studentId
        -WaitlistStatus status
        -DateTime requestedAt
        -DateTime notifiedAt
        -String notes
        -DateTime createdAt
        -DateTime updatedAt
        +notifyVacancy() void
        +confirmEnrollment() void
        +cancel(String reason) void
        +hasExpired(Integer gracePeriodHours) Boolean
    }

    class CourseMaterial {
        -UUID id
        -UUID courseId
        -UUID sessionId
        -UUID uploaderId
        -String title
        -String description
        -String filePath
        -String fileName
        -Long fileSizeBytes
        -String mimeType
        -DateTime createdAt
        -DateTime updatedAt
        +validateFileRestrictions(Long maxSizeBytes, List~String~ allowedMimes) Boolean
        +isLinkedToSession() Boolean
    }

    %% --------------------------------------------------------
    %% 3. STUDENT ENROLLMENT & FINANCIAL COLLECTION CONTEXT
    %% --------------------------------------------------------
    class Enrollment {
        -UUID id
        -UUID centerId
        -UUID courseId
        -UUID studentId
        -Date enrollmentDate
        -Decimal basePrice
        -Decimal discountAmount
        -Decimal finalPayable
        -Decimal totalPaid
        -Decimal remainingBalance
        -PaymentStatus paymentStatus
        -EnrollmentStatus status
        -DateTime createdAt
        -DateTime updatedAt
        +applyDiscount(Decimal discount) void
        +calculateBalances() void
        +creditApprovedPayment(Decimal amount) Decimal
        +withdraw() void
        +complete() void
        +isActive() Boolean
        +isFullyPaid() Boolean
    }

    class Payment {
        -UUID id
        -UUID centerId
        -UUID enrollmentId
        -Decimal amount
        -Decimal appliedAmount
        -Decimal excessAmount
        -Date paymentDate
        -PaymentMethod paymentMethod
        -String receiptReference
        -String notes
        -PaymentVoucherStatus status
        -UUID createdBy
        -UUID approvedBy
        -DateTime approvedAt
        -String rejectionReason
        -DateTime createdAt
        -DateTime updatedAt
        +approve(User approver, Decimal remainingBalance) void
        +reject(String reason, User approver) void
        +adjust(Decimal newAmount, String rationale, User manager) void
        +hasExcessAmount() Boolean
        +isPosted() Boolean
    }

    class RefundRequest {
        -UUID id
        -UUID centerId
        -UUID paymentId
        -UUID enrollmentId
        -Decimal amount
        -RefundStatus status
        -PaymentMethod payoutMethod
        -String payoutReference
        -Date payoutDate
        -UUID executedBy
        -String failureReason
        -String notes
        -DateTime createdAt
        -DateTime updatedAt
        +confirmPayout(PaymentMethod method, String reference, User officer) void
        +markFailed(String reason) void
        +requeue() void
        +isSettled() Boolean
    }

    %% --------------------------------------------------------
    %% 4. TEACHING & ACCOUNTABILITY CONTEXT
    %% --------------------------------------------------------
    class Task {
        -UUID id
        -UUID courseId
        -UUID sessionId
        -UUID creatorId
        -String title
        -String instructions
        -String attachmentPath
        -DateTime dueDatetime
        -Boolean allowLateSubmission
        -DateTime createdAt
        -DateTime updatedAt
        +isPastDeadline() Boolean
        +canAcceptSubmission() Boolean
        +setLateOverride(Boolean allowed) void
    }

    class Submission {
        -UUID id
        -UUID taskId
        -UUID enrollmentId
        -String filePath
        -String fileName
        -Long fileSizeBytes
        -DateTime submittedAt
        -SubmissionStatus status
        -String notes
        -DateTime createdAt
        -DateTime updatedAt
        +replaceFile(String filePath, String fileName, Long sizeBytes) void
        +evaluateCompliance(DateTime deadline, Boolean lateOverride) void
        +isLate() Boolean
    }

    class Attendance {
        -UUID id
        -UUID sessionId
        -UUID enrollmentId
        -AttendanceStatus status
        -UUID recordedBy
        -String remarks
        -DateTime createdAt
        -DateTime updatedAt
        +updateStatus(AttendanceStatus status, String remarks, User editor) void
        +isPresent() Boolean
    }

    class Feedback {
        -UUID id
        -UUID courseId
        -UUID enrollmentId
        -UUID instructorId
        -UUID sessionId
        -UUID taskId
        -String content
        -DateTime createdAt
        -DateTime updatedAt
        +canBeViewedBy(User viewer) Boolean
    }

    %% --------------------------------------------------------
    %% 5. INSTRUCTOR SETTLEMENT CONTEXT
    %% --------------------------------------------------------
    class InstructorDue {
        -UUID id
        -UUID centerId
        -UUID courseInstructorId
        -Decimal accruedAmount
        -Decimal paidAmount
        -Decimal outstandingDue
        -InstructorDueStatus status
        -DateTime lastCalculatedAt
        -DateTime createdAt
        -DateTime updatedAt
        +accrueFixed(Decimal fixedSum) void
        +accruePercentage(Decimal totalVerifiedCash, Decimal percentage) void
        +accruePerSession(Integer completedSessions, Decimal sessionRate) void
        +recordPayout(Decimal amount, String reference, PaymentMethod method, User manager) InstructorPayout
        +recalculateOutstanding() void
        +isFullySettled() Boolean
    }

    class InstructorPayout {
        -UUID id
        -UUID centerId
        -UUID instructorDueId
        -Decimal amount
        -Date payoutDate
        -PaymentMethod payoutMethod
        -String referenceNumber
        -String notes
        -UUID disbursedBy
        -DateTime createdAt
        -DateTime updatedAt
        +validateDisbursement() Boolean
    }

    %% --------------------------------------------------------
    %% 6. AUDIT & GOVERNANCE CONTEXT
    %% --------------------------------------------------------
    class AuditLog {
        -UUID id
        -UUID centerId
        -UUID userId
        -String action
        -String entityType
        -UUID entityId
        -String oldValues
        -String newValues
        -String ipAddress
        -String userAgent
        -DateTime createdAt
        +logAction(User actor, String action, String entityType, UUID entityId, String oldJson, String newJson, String ip) AuditLog
    }

    %% --------------------------------------------------------
    %% DOMAIN VALUE OBJECTS & ENUMERATIONS
    %% --------------------------------------------------------
    class UserRole {
        <<enumeration>>
        Manager
        Sales
        Instructor
        Student
    }

    class CourseStatus {
        <<enumeration>>
        Draft
        Active
        Completed
        Archived
    }

    class CompensationModel {
        <<enumeration>>
        FixedAmount
        PercentageOfCollectedRevenue
        PerSession
    }

    class EnrollmentStatus {
        <<enumeration>>
        Active
        Completed
        Withdrawn
    }

    class PaymentStatus {
        <<enumeration>>
        Unpaid
        PartiallyPaid
        FullyPaid
    }

    class PaymentVoucherStatus {
        <<enumeration>>
        Pending_Approval
        Completed
        Rejected
        Adjusted
    }

    class RefundStatus {
        <<enumeration>>
        Pending_Refund
        Refunded
        Refund_Failed
    }

    class InstructorDueStatus {
        <<enumeration>>
        Pending
        PartiallyPaid
        Paid
        Adjusted
    }

    %% ========================================================
    %% RELATIONSHIPS, CARDINALITIES & COMPOSITION SEMANTICS
    %% ========================================================

    %% Tenancy & User Specializations
    Center "1" *-- "0..*" User : hosts and scopes
    Center "1" *-- "0..*" Course : catalogs
    Center "1" *-- "0..*" AuditLog : chronicles
    User "1" *-- "0..1" StudentProfile : specializes as (1:1)
    User "1" *-- "0..1" InstructorProfile : specializes as (1:1)

    %% Academic & Course Hierarchy
    Course "1" *-- "1..*" CourseInstructor : contracts
    CourseInstructor "0..*" --> "1" InstructorProfile : assigns
    Course "1" *-- "0..*" CourseSession : schedules
    CourseSession "0..*" --> "0..1" InstructorProfile : conducted by
    Course "1" *-- "0..*" CourseWaitlist : queues
    CourseWaitlist "0..*" --> "1" StudentProfile : queues student
    Course "1" *-- "0..*" Enrollment : admits
    Enrollment "0..*" --> "1" StudentProfile : enrolls
    Course "1" *-- "0..*" CourseMaterial : publishes
    CourseMaterial "0..*" --> "0..1" CourseSession : anchors optionally
    Course "1" *-- "0..*" Task : issues
    Task "0..*" --> "0..1" CourseSession : contexts optionally

    %% Student Financial & Academic Operations
    Enrollment "1" *-- "0..*" Payment : pays installments
    Payment "1" --> "0..1" RefundRequest : triggers excess
    RefundRequest "0..*" --> "1" Enrollment : reimburses
    Task "1" *-- "0..*" Submission : collects
    Enrollment "1" *-- "0..*" Submission : delivers
    CourseSession "1" *-- "0..*" Attendance : records presence
    Attendance "0..*" --> "1" Enrollment : verifies presence
    Course "1" *-- "0..*" Feedback : anchors
    Feedback "0..*" --> "1" Enrollment : mentors
    Feedback "0..*" --> "1" InstructorProfile : authored by

    %% Instructor Compensation
    CourseInstructor "1" *-- "1" InstructorDue : accrues terms (1:1)
    InstructorDue "1" *-- "0..*" InstructorPayout : settles via
```

---

## 3. Class Catalog, Responsibilities & Operational Method Signatures

### 3.1 Module 1: Tenancy & Identity Management Context

#### Class: `Center` (المركز التدريبي / المستأجر السيادي)
- **Architectural Role:** Multi-Tenant Sovereign Root & Bounded Boundary.
- **Responsibilities:**
  - Establishes the authoritative tenancy boundary for all child entities (`centerId`).
  - Governs operational subscription state (`Active` vs. `Suspended`).
- **Attributes:**
  - `-UUID id`: Globally unique tenant identifier.
  - `-String name`: Commercial legal trade name.
  - `-String code`: Unique human-readable alphanumeric identifier (`VEL-MAIN`).
  - `-CenterStatus status`: Operational state of the center.
  - `-DateTime createdAt`, `-DateTime updatedAt`: Auditing timestamps.
- **Operations:**
  - `+activate(): void` — Sets tenant status to `Active`.
  - `+suspend(): void` — Suspends all center operational access.
  - `+isOperational(): Boolean` — Returns `true` only if status is `Active`.

#### Class: `User` (هوية المستخدم المعتمد)
- **Architectural Role:** Authenticated Identity Root.
- **Responsibilities:**
  - Encapsulates authentication credentials and tenant association.
  - Enforces role-based access authorization (`Manager`, `Sales`, `Instructor`, `Student`).
  - Manages account access lifecycle (`Active`, `Suspended`, `Archived`).
- **Attributes:**
  - `-UUID id`: Unique identity identifier.
  - `-UUID centerId`: Scoped tenant key.
  - `-String fullName`: Legal user name.
  - `-String email`: Tenant-unique login email.
  - `-String passwordHash`: Secure hashed credential (Argon2id/bcrypt).
  - `-UserRole role`: Assigned operational role.
  - `-AccountStatus status`: Account lifecycle state.
- **Operations:**
  - `+verifyPassword(String plainPassword): Boolean` — Validates password hash against input.
  - `+updatePassword(String newHash): void` — Updates security credential with new hash.
  - `+suspend(): void` — Deactivates account, invalidating sessions (`EC-AUTH-01`).
  - `+reactivate(): void` — Re-enables suspended account.
  - `+archive(): void` — Soft-archives user upon permanent departure (`FR-AUD-04`).
  - `+hasRole(UserRole requiredRole): Boolean` — Checks authorization against requested role.

#### Class: `StudentProfile` (الملف التعريفي للمتدرب)
- **Architectural Role:** Trainee Specialized Profile (1:1 Composition with `User`).
- **Responsibilities:**
  - Maintains student demographic, contact, and administrative metadata.
  - Acts as the operational anchor for course enrollments and waitlist requests.
- **Attributes:**
  - `-UUID id`: Profile identifier.
  - `-UUID userId`: 1:1 foreign reference to `User`.
  - `-UUID centerId`: Tenant isolation anchor.
  - `-String phone`: Contact mobile telephone number.
  - `-String civilId`: National Civil ID / Passport number.
  - `-String notes`: Administrative counseling notes.
- **Operations:**
  - `+updateContact(String phone, String civilId, String notes): void` — Updates contact info.

#### Class: `InstructorProfile` (الملف التعريفي للمدرب)
- **Architectural Role:** Academic Trainer Specialized Profile (1:1 Composition with `User`).
- **Responsibilities:**
  - Maintains instructional domain expertise, specialization, and biography.
  - Acts as the anchor for course teaching assignments and personal compensation dues.
- **Attributes:**
  - `-UUID id`: Profile identifier.
  - `-UUID userId`: 1:1 foreign reference to `User`.
  - `-UUID centerId`: Tenant isolation anchor.
  - `-String phone`: Contact telephone number.
  - `-String specialization`: Academic instructional domain.
  - `-String bio`: Curriculum vitae summary.
- **Operations:**
  - `+updateQualifications(String specialization, String bio, String phone): void` — Updates trainer profile.

---

### 3.2 Module 2: Academic Program & Course Management Context

#### Class: `Course` (الدورة التدريبية)
- **Architectural Role:** Course Cohort Aggregate Root.
- **Responsibilities:**
  - Manages course cohort metadata, catalog pricing, and calendar duration.
  - Enforces seat capacity constraints (`BR-CRS-CAPACITY-HARDLOCK`).
  - Controls cohort lifecycle (`Draft` → `Active` → `Completed` → `Archived`).
  - Oversees creation of sessions, instructor assignments, and tasks.
- **Attributes:**
  - `-UUID id`: Course identifier.
  - `-UUID centerId`: Scoped tenant key.
  - `-String title`: Course cohort title.
  - `-String code`: Center-unique course code.
  - `-String description`: Course syllabus description.
  - `-Decimal basePrice`: Catalog base fee (`>= 0.00`).
  - `-Date startDate`, `-Date endDate`: Cohort calendar timeline.
  - `-Integer capacity`: Maximum allowable seats (nullable = open).
  - `-CourseStatus status`: Lifecycle status.
- **Operations:**
  - `+publish(): void` — Transitions status from `Draft` to `Active` after verifying that at least one instructor with compensation terms is assigned (`BR-CRS-03`).
  - `+conclude(): void` — Transitions status to `Completed` upon reaching `endDate` or concluding all sessions.
  - `+archive(): void` — Locks course cohort permanently as read-only (`FR-CRS-05`).
  - `+isCapacityReached(Integer activeEnrolledCount): Boolean` — Returns `true` if `capacity != null` and `activeEnrolledCount >= capacity`.
  - `+assignInstructor(InstructorProfile instructor, CompensationModel model, Decimal value, Boolean isLead): CourseInstructor` — Contracts trainer.
  - `+scheduleSession(Date sessionDate, Time startTime, Time endTime, String topic, InstructorProfile instructor): CourseSession` — Adds lecture.
  - `+canAcceptEnrollments(): Boolean` — Verifies status is `Active`.

#### Class: `CourseInstructor` (تكليف المدرب بالدورة)
- **Architectural Role:** Contractual Junction Entity (Composed within `Course`).
- **Responsibilities:**
  - Couples an instructor to a specific course cohort with binding financial terms.
  - Designates Lead Instructor operational privileges.
  - Hosts the 1:1 running `InstructorDue` liability ledger.
- **Attributes:**
  - `-UUID id`: Contract identifier.
  - `-UUID courseId`, `-UUID instructorId`: Junction foreign keys.
  - `-CompensationModel compensationModel`: `FixedAmount`, `PercentageOfCollectedRevenue`, or `PerSession`.
  - `-Decimal modelValue`: Agreed rate or percentage.
  - `-Boolean isLead`: Flag indicating primary course coordinator.
- **Operations:**
  - `+updateTerms(CompensationModel model, Decimal value, Boolean isLead): void` — Updates agreement terms.
  - `+setLeadStatus(Boolean isLead): void` — Toggles lead instructor status.

#### Class: `CourseSession` (الجلسة التدريبية / المحاضرة)
- **Architectural Role:** Scheduled Instructional Unit (Composed within `Course`).
- **Responsibilities:**
  - Tracks date, timetable, topic, and conducting instructor for each lecture.
  - Acts as the operational parent for binary student attendance sheets.
  - Triggers `PerSession` instructor compensation accrual upon verified delivery (`OQ-04`).
- **Attributes:**
  - `-UUID id`: Session identifier.
  - `-UUID courseId`, `-UUID instructorId`: Parent references.
  - `-Date sessionDate`: Calendar date of lecture.
  - `-Time startTime`, `-Time endTime`: Scheduled timeframe (`endTime > startTime`).
  - `-String topic`: Pedagogical lecture title.
  - `-Integer sessionOrder`: Chronological sequence index.
  - `-SessionStatus status`: `Scheduled`, `Completed`, or `Cancelled`.
- **Operations:**
  - `+complete(): void` — Sets status to `Completed` upon verified attendance submission.
  - `+cancel(String reason): void` — Cancels session; excludes from per-session instructor accruals (`OQ-04`).
  - `+reassignInstructor(InstructorProfile newInstructor): void` — Reassigns conducting trainer.
  - `+isDelivered(): Boolean` — Returns `true` if status is `Completed`.

#### Class: `CourseWaitlist` (طلب قائمة الانتظار)
- **Architectural Role:** FIFO Admission Queue Entity (Composed within `Course`).
- **Responsibilities:**
  - Manages chronological queue entries for filled cohorts (`BR-CRS-WAITLIST-FIFO`).
  - Enforces zero-fee holding until official admission (`OQ-06`).
  - Manages seat reservation notification windows.
- **Attributes:**
  - `-UUID id`: Queue ticket identifier.
  - `-UUID courseId`, `-UUID studentId`: Target references.
  - `-WaitlistStatus status`: `Waiting`, `Notified`, `Enrolled`, `Cancelled`.
  - `-DateTime requestedAt`: Chronological timestamp for strict FIFO sorting.
  - `-DateTime notifiedAt`: Timestamp when vacancy reservation was offered.
  - `-String notes`: Trainee intake remarks.
- **Operations:**
  - `+notifyVacancy(): void` — Transitions status to `Notified` and records notification timestamp.
  - `+confirmEnrollment(): void` — Transitions status to `Enrolled` upon candidate confirmation.
  - `+cancel(String reason): void` — Cancels ticket upon rejection or reservation expiration.
  - `+hasExpired(Integer gracePeriodHours): Boolean` — Evaluates if response grace period has elapsed.

#### Class: `CourseMaterial` (المادة والمورد التعليمي)
- **Architectural Role:** Course Resource Entity (Composed within `Course`).
- **Responsibilities:**
  - Stores metadata and protected filesystem paths for educational assets (PDF, DOCX, ZIP).
  - Enforces file upload boundaries (up to 25 MB; strict MIME filtering).
  - Optionally links to a specific lecture session for curricular context.
- **Attributes:**
  - `-UUID id`: Material identifier.
  - `-UUID courseId`, `-UUID sessionId`, `-UUID uploaderId`: Relational anchors.
  - `-String title`, `-String description`: Display metadata.
  - `-String filePath`, `-String fileName`: File storage pointer.
  - `-Long fileSizeBytes`: File size in bytes (`<= 26,214,400`).
  - `-String mimeType`: Verified MIME type.
- **Operations:**
  - `+validateFileRestrictions(Long maxSizeBytes, List~String~ allowedMimes): Boolean` — Enforces upload security gates (`FR-MAT-02/04`).
  - `+isLinkedToSession(): Boolean` — Returns `true` if `sessionId != null`.

---

### 3.3 Module 3: Student Enrollment & Financial Collection Context

#### Class: `Enrollment` (التحاق المتدرب بالدورة)
- **Architectural Role:** Student Cohort Contract Aggregate Root.
- **Responsibilities:**
  - Establishes the formal commitment binding an active student to an active course.
  - Computes and maintains the running financial balance:
    $$\text{FinalPayable} = \text{BasePrice} - \text{DiscountAmount}$$
    $$\text{RemainingBalance} = \text{FinalPayable} - \text{TotalPaid}$$
  - Controls access authorization to sessions, materials, tasks, and attendance.
  - Owns all child installment payment vouchers (`Payment`).
- **Attributes:**
  - `-UUID id`: Enrollment identifier.
  - `-UUID centerId`, `-UUID courseId`, `-UUID studentId`: Scoped references.
  - `-Date enrollmentDate`: Admission date.
  - `-Decimal basePrice`, `-Decimal discountAmount`, `-Decimal finalPayable`: Pricing schedule.
  - `-Decimal totalPaid`, `-Decimal remainingBalance`: Running balance ledger.
  - `-PaymentStatus paymentStatus`: `Unpaid`, `PartiallyPaid`, or `FullyPaid`.
  - `-EnrollmentStatus status`: `Active`, `Completed`, or `Withdrawn`.
- **Operations:**
  - `+applyDiscount(Decimal discount): void` — Validates `discount <= basePrice` and re-evaluates `finalPayable`.
  - `+calculateBalances(): void` — Recalculates remaining balance and updates `paymentStatus` atomically.
  - `+creditApprovedPayment(Decimal amount): Decimal` — Credits an approved voucher; returns any excess overpayment amount.
  - `+withdraw(): void` — Sets status to `Withdrawn`, decrements active course count, and preserves history (`BR-STU-03`).
  - `+complete(): void` — Transitions status to `Completed` upon cohort conclusion.
  - `+isActive(): Boolean` — Returns `true` if status is `Active`.
  - `+isFullyPaid(): Boolean` — Returns `true` if `remainingBalance == 0.00`.

#### Class: `Payment` (سند قبض القسط المالي)
- **Architectural Role:** Payment Installment Voucher (Composed within `Enrollment`).
- **Responsibilities:**
  - Captures offline money collection details (`Cash`, `BankTransfer`, `POS_Card`, `Cheque`).
  - Implements mandatory 2-phase administrative approval workflow (`BR-FIN-APPROVAL`, `OQ-02`).
  - Splits payment into applied fee balance and excess overpayment amounts.
  - Enforces ledger immutability (hard deletion prohibited; corrections via adjustments).
- **Attributes:**
  - `-UUID id`: Payment voucher identifier.
  - `-UUID centerId`, `-UUID enrollmentId`: Relational anchors.
  - `-Decimal amount`: Total collected payment (`> 0.00`).
  - `-Decimal appliedAmount`: Portioned credit applied directly to tuition.
  - `-Decimal excessAmount`: Overpayment portion exceeding remaining balance.
  - `-Date paymentDate`: Date money was collected offline.
  - `-PaymentMethod paymentMethod`: Mode of payment.
  - `-String receiptReference`: External receipt number or bank transaction ID.
  - `-String notes`: Internal cashier remarks.
  - `-PaymentVoucherStatus status`: `Pending_Approval`, `Completed`, `Rejected`, `Adjusted`.
  - `-UUID createdBy`, `-UUID approvedBy`: Auditing actor references.
  - `-DateTime approvedAt`: Administrative review timestamp.
  - `-String rejectionReason`: Mandatory explanation if rejected.
- **Operations:**
  - `+approve(User approver, Decimal remainingBalance): void` — Verifies funds, computes `appliedAmount = min(amount, remainingBalance)` and `excessAmount = max(0, amount - remainingBalance)`, transitions status to `Completed`, and logs approver.
  - `+reject(String reason, User approver): void` — Rejects voucher with documented reason; leaves student balance unchanged.
  - `+adjust(Decimal newAmount, String rationale, User manager): void` — Reverses or corrects posted payment with audit justification (`FR-FIN-05`).
  - `+hasExcessAmount(): Boolean` — Returns `true` if `excessAmount > 0.00`.
  - `+isPosted(): Boolean` — Returns `true` if status is `Completed`.

#### Class: `RefundRequest` (طلب استرداد الفائض المالي)
- **Architectural Role:** Mandatory Excess Reimbursement Claim.
- **Responsibilities:**
  - Created automatically when an approved payment voucher contains an excess amount (`BR-FIN-OVERPAY-REFUND`, `OQ-05`).
  - Prohibits converting excess money into credit balances for future cohorts.
  - Tracks physical or banking payout disbursement execution (`BR-FIN-REFUND-VERIFY`).
- **Attributes:**
  - `-UUID id`: Claim identifier.
  - `-UUID centerId`, `-UUID paymentId`, `-UUID enrollmentId`: Relational anchors.
  - `-Decimal amount`: Excess amount to be refunded (`> 0.00`).
  - `-RefundStatus status`: `Pending_Refund`, `Refunded`, or `Refund_Failed`.
  - `-PaymentMethod payoutMethod`: `Cash` or `BankTransfer`.
  - `-String payoutReference`: Disbursement wire reference or petty-cash receipt.
  - `-Date payoutDate`: Execution date.
  - `-UUID executedBy`: Finance staff reference.
  - `-String failureReason`: Failure explanation if disbursement fails.
- **Operations:**
  - `+confirmPayout(PaymentMethod method, String reference, User officer): void` — Transitions status to `Refunded` upon verified physical/bank payout execution (`BR-FIN-REFUND-VERIFY`).
  - `+markFailed(String reason): void` — Transitions status to `Refund_Failed` and documents failure cause.
  - `+requeue(): void` — Re-queues failed claim for alternative payout processing.
  - `+isSettled(): Boolean` — Returns `true` if status is `Refunded`.

---

### 3.4 Module 4: Teaching, Tasks & Academic Accountability Context

#### Class: `Task` (المهمة / الواجب التدريبي)
- **Architectural Role:** Academic Assessment Entity (Composed within `Course`).
- **Responsibilities:**
  - Defines homework instructions, attachments, and optional submission deadlines.
  - Controls default deadline cut-offs (`FR-TSK-05`).
  - Governs late submission exception toggling (`OQ-03`).
- **Attributes:**
  - `-UUID id`: Task identifier.
  - `-UUID courseId`, `-UUID sessionId`, `-UUID creatorId`: Relational anchors.
  - `-String title`, `-String instructions`: Assignment prompt.
  - `-String attachmentPath`: Optional reference file.
  - `-DateTime dueDatetime`: Submission deadline.
  - `-Boolean allowLateSubmission`: Override toggle for late submissions.
- **Operations:**
  - `+isPastDeadline(): Boolean` — Returns `true` if `dueDatetime != null` and current time exceeds it.
  - `+canAcceptSubmission(): Boolean` — Evaluates if submission is allowed (either before deadline or override enabled).
  - `+setLateOverride(Boolean allowed): void` — Toggles late submission permission for trainees (`FR-TSK-06`).

#### Class: `Submission` (تسليم المتدرب للواجب)
- **Architectural Role:** Student Deliverable Entity (Composed within `Enrollment`).
- **Responsibilities:**
  - Encapsulates student deliverable upload and time-stamped proof of delivery.
  - Manages compliance tagging (`On_Time` vs. `Late`).
  - Allows file replacement prior to deadline cutoff (`FR-TSK-04`).
- **Attributes:**
  - `-UUID id`: Submission identifier.
  - `-UUID taskId`, `-UUID enrollmentId`: Relational anchors.
  - `-String filePath`, `-String fileName`: File storage pointer.
  - `-Long fileSizeBytes`: File size in bytes (`<= 25 MB`).
  - `-DateTime submittedAt`: Upload timestamp.
  - `-SubmissionStatus status`: `On_Time` or `Late`.
  - `-String notes`: Optional student submission remarks.
- **Operations:**
  - `+replaceFile(String filePath, String fileName, Long sizeBytes): void` — Overwrites file prior to deadline.
  - `+evaluateCompliance(DateTime deadline, Boolean lateOverride): void` — Sets status to `On_Time` or `Late`.
  - `+isLate(): Boolean` — Returns `true` if status is `Late`.

#### Class: `Attendance` (سجل حضور الجلسة)
- **Architectural Role:** Session Participation Record (Composed within `CourseSession`).
- **Responsibilities:**
  - Encapsulates verified binary attendance (`Present` or `Absent`) for an enrolled student during a session.
  - Enforces single record uniqueness per `(sessionId, enrollmentId)`.
- **Attributes:**
  - `-UUID id`: Attendance record identifier.
  - `-UUID sessionId`, `-UUID enrollmentId`: Composite unique anchors.
  - `-AttendanceStatus status`: `Present` or `Absent`.
  - `-UUID recordedBy`: User reference who took attendance.
  - `-String remarks`: Optional attendance remarks.
- **Operations:**
  - `+updateStatus(AttendanceStatus status, String remarks, User editor): void` — Updates presence state.
  - `+isPresent(): Boolean` — Returns `true` if status is `Present`.

#### Class: `Feedback` (التقييم والملاحظات الفردية)
- **Architectural Role:** Private Pedagogical Mentoring Entity (Composed within `Enrollment`).
- **Responsibilities:**
  - Encapsulates 1-to-1 private written evaluations authored by instructors for trainees.
  - Enforces privacy boundaries (strictly inaccessible to peers and sales staff).
- **Attributes:**
  - `-UUID id`: Feedback identifier.
  - `-UUID courseId`, `-UUID enrollmentId`, `-UUID instructorId`: Relational anchors.
  - `-UUID sessionId`, `-UUID taskId`: Optional pedagogical links.
  - `-String content`: Written evaluation text (up to 2,000 chars).
- **Operations:**
  - `+canBeViewedBy(User viewer): Boolean` — Verifies user is the recipient student, the authoring instructor, or a manager (`FR-FDB-04`).

---

### 3.5 Module 5: Instructor Settlement Context

#### Class: `InstructorDue` (مستحقات المدرب المالية)
- **Architectural Role:** Instructor Liability Ledger (1:1 Composition with `CourseInstructor`).
- **Responsibilities:**
  - Calculates accrued compensation dynamically based on configured contract models:
    1. `FixedAmount`: Lump-sum upon activation.
    2. `PercentageOfCollectedRevenue`: Percentage of verified completed student cash payments (`BR-INS-03`).
    3. `PerSession`: Completed sessions with verified attendance sheets (`OQ-04`).
  - Tracks running settlement liability balance:
    $$\text{OutstandingDue} = \text{AccruedAmount} - \text{PaidAmount}$$
- **Attributes:**
  - `-UUID id`: Due ledger identifier.
  - `-UUID centerId`, `-UUID courseInstructorId`: Scoped anchors.
  - `-Decimal accruedAmount`: Total calculated earnings (`>= 0.00`).
  - `-Decimal paidAmount`: Running sum of disbursed payouts.
  - `-Decimal outstandingDue`: Net liability remaining (`>= 0.00`).
  - `-InstructorDueStatus status`: `Pending`, `PartiallyPaid`, `Paid`, or `Adjusted`.
  - `-DateTime lastCalculatedAt`: Timestamp of last automated formula execution.
- **Operations:**
  - `+accrueFixed(Decimal fixedSum): void` — Sets accrued amount to agreed lump sum.
  - `+accruePercentage(Decimal totalVerifiedCash, Decimal percentage): void` — Computes accrued dues from collected revenue.
  - `+accruePerSession(Integer completedSessions, Decimal sessionRate): void` — Computes accrued dues from delivered lectures.
  - `+recordPayout(Decimal amount, String reference, PaymentMethod method, User manager): InstructorPayout` — Disburses settlement payout.
  - `+recalculateOutstanding(): void` — Recalculates balance and updates status.
  - `+isFullySettled(): Boolean` — Returns `true` if `outstandingDue == 0.00`.

#### Class: `InstructorPayout` (سند صرف مستحقات المدرب)
- **Architectural Role:** Disbursement Voucher (Composed within `InstructorDue`).
- **Responsibilities:**
  - Records the physical or bank wire transfer settling instructor earnings.
  - Retains immutable payout receipts for financial audit reports.
- **Attributes:**
  - `-UUID id`: Payout voucher identifier.
  - `-UUID centerId`, `-UUID instructorDueId`: Relational anchors.
  - `-Decimal amount`: Disbursed currency value (`> 0.00`).
  - `-Date payoutDate`: Disbursement date.
  - `-PaymentMethod payoutMethod`: Mode of disbursement.
  - `-String referenceNumber`: Transaction or check reference.
  - `-String notes`: Administrative payout remarks.
  - `-UUID disbursedBy`: Manager user reference.
- **Operations:**
  - `+validateDisbursement(): Boolean` — Verifies payout amount does not exceed outstanding liability.

---

### 3.6 Module 6: Audit & System Governance Context

#### Class: `AuditLog` (سجل التدقيق العملياتي والمالي)
- **Architectural Role:** Append-Only System Audit Chronicle.
- **Responsibilities:**
  - Chronicling critical entity lifecycle modifications, payment approvals, and permission events.
  - Ensuring regulatory compliance with non-destructive, immutable audit records.
- **Attributes:**
  - `-UUID id`: Audit entry identifier.
  - `-UUID centerId`, `-UUID userId`: Actor anchors.
  - `-String action`: Executed business operation (e.g., `PAYMENT_APPROVED`, `COURSE_ARCHIVED`).
  - `-String entityType`, `-UUID entityId`: Target entity reference.
  - `-String oldValues`, `-String newValues`: JSON delta snapshots.
  - `-String ipAddress`, `-String userAgent`: Client telemetry metadata.
- **Operations:**
  - `+logAction(User actor, String action, String entityType, UUID entityId, String oldJson, String newJson, String ip): AuditLog` — Factory method persisting immutable audit entry.

---

## 4. Comprehensive Class Relationships & Multiplicity Analysis

The following matrix documents the precise relationship type, multiplicity, navigation direction, and business rationale for all connections in the class diagram:

| Source Class | Target Class | UML Relationship Type | Multiplicity | Navigation | Architectural Justification & Business Rules |
| :--- | :--- | :---: | :---: | :---: | :--- |
| **`Center`** | **`User`** | Composition (`*--`) | `1` to `0..*` | Uni | All users are hosted within and partitioned by the training center (`BR-TEN-01`). |
| **`Center`** | **`Course`** | Composition (`*--`) | `1` to `0..*` | Uni | All educational course cohorts are cataloged and offered by the center (`BR-CRS-01`). |
| **`Center`** | **`AuditLog`** | Composition (`*--`) | `1` to `0..*` | Uni | The center owns an immutable, centralized audit chronicle (`FR-AUD-01`). |
| **`User`** | **`StudentProfile`** | Composition (`*--`) | `1` to `0..1` | Bi | 1:1 specialization profile for trainees; decoupled from authentication credentials. |
| **`User`** | **`InstructorProfile`**| Composition (`*--`) | `1` to `0..1` | Bi | 1:1 specialization profile for academic trainers; decoupled from authentication credentials. |
| **`Course`** | **`CourseInstructor`**| Composition (`*--`) | `1` to `1..*` | Bi | A course contracts at least one instructor before activation (`BR-CRS-03`). |
| **`CourseInstructor`**| **`InstructorProfile`**| Association (`-->`) | `0..*` to `1` | Uni | Links course assignment to the physical trainer profile (`FR-INS-01`). |
| **`Course`** | **`CourseSession`** | Composition (`*--`) | `1` to `0..*` | Bi | Lecture sessions have an existential dependency on their course cohort (`FR-CRS-04`). |
| **`CourseSession`** | **`InstructorProfile`**| Association (`-->`) | `0..*` to `0..1` | Uni | A session is optionally assigned to a specific contracted trainer. |
| **`Course`** | **`CourseWaitlist`** | Composition (`*--`) | `1` to `0..*` | Bi | Filled courses maintain an ordered FIFO waitlist queue (`BR-CRS-WAITLIST-FIFO`). |
| **`CourseWaitlist`** | **`StudentProfile`** | Association (`-->`) | `0..*` to `1` | Uni | Waitlist entries queue specific registered student profiles. |
| **`Course`** | **`Enrollment`** | Composition (`*--`) | `1` to `0..*` | Bi | Course admits multiple student enrollment contracts up to capacity (`FR-STU-02`). |
| **`Enrollment`** | **`StudentProfile`** | Association (`-->`) | `0..*` to `1` | Uni | Binds a trainee's physical profile to the course admission contract. |
| **`Course`** | **`CourseMaterial`** | Composition (`*--`) | `1` to `0..*` | Bi | Educational resources are published within and owned by the course (`FR-MAT-01`). |
| **`CourseMaterial`** | **`CourseSession`** | Association (`-->`) | `0..*` to `0..1` | Uni | A published resource can optionally anchor to a specific lecture session. |
| **`Course`** | **`Task`** | Composition (`*--`) | `1` to `0..*` | Bi | Homework assignments belong strictly to the course cohort (`FR-TSK-01`). |
| **`Task`** | **`CourseSession`** | Association (`-->`) | `0..*` to `0..1` | Uni | A task can optionally contextualize a specific lecture topic. |
| **`Enrollment`** | **`Payment`** | Composition (`*--`) | `1` to `0..*` | Bi | Installment vouchers belong to and settle a specific enrollment ledger (`FR-FIN-03`). |
| **`Payment`** | **`RefundRequest`** | Association (`-->`) | `1` to `0..1` | Uni | Excess payment amount triggers an automatic linked refund request (`OQ-05`). |
| **`RefundRequest`** | **`Enrollment`** | Association (`-->`) | `0..*` to `1` | Uni | Excess funds reimburse tuition back to the originating enrollment. |
| **`Task`** | **`Submission`** | Composition (`*--`) | `1` to `0..*` | Bi | A task collects multiple student deliverable submissions (`FR-TSK-03`). |
| **`Enrollment`** | **`Submission`** | Composition (`*--`) | `1` to `0..*` | Bi | An enrollment delivers at most one active submission per task (`BR-TSK-03`). |
| **`CourseSession`** | **`Attendance`** | Composition (`*--`) | `1` to `0..*` | Bi | A session holds exactly one attendance sheet of student entries (`FR-ATT-04`). |
| **`Attendance`** | **`Enrollment`** | Association (`-->`) | `0..*` to `1` | Uni | Each attendance row verifies the presence of an active enrollment. |
| **`Course`** | **`Feedback`** | Composition (`*--`) | `1` to `0..*` | Bi | Qualitative guidance is anchored within the course cohort (`FR-FDB-01`). |
| **`Feedback`** | **`Enrollment`** | Association (`-->`) | `0..*` to `1` | Uni | Feedback delivers private mentoring to an individual trainee. |
| **`Feedback`** | **`InstructorProfile`**| Association (`-->`) | `0..*` to `1` | Uni | Feedback is authored by an assigned instructor. |
| **`CourseInstructor`**| **`InstructorDue`** | Composition (`*--`) | `1` to `1` | Bi | Each course assignment agreement owns exactly one running dues ledger (`FR-INSFIN-01`). |
| **`InstructorDue`** | **`InstructorPayout`** | Composition (`*--`) | `1` to `0..*` | Bi | Settlement disbursements belong to an instructor due liability ledger (`FR-INSFIN-03`). |

---

## 5. Arabic Technical & Architectural Explanation (الشرح المعماري لنظام الأصناف)

يقدم هذا المخطط الهيكلي للأصناف البرمجية (`UML Class Diagram`) الصياغة التصميمية الدقيقة لطبقة النطاق (`Domain Layer`) في منظومة **VELORA (فيلورا)**، حيث يترجم متطلبات العمل إلى كائنات برمجية مسؤولة تجمع بين البيانات المجردة والعمليات السلوكية الموثقة.

### 5.1 فلسفة التصميم الكائني وتجنب الوراثة المفرطة
1. **استخدام التركيب بدلاً من الوراثة (`Composition over Inheritance`):**
   - في بعض التصاميم التقليدية، يتم اشتقاق أصناف مثل `Student` و `Instructor` و `Manager` وراثياً من صنف أساسي `User`. ولكن في منظومة VELORA، تم اعتماد نمط **التركيب بنسبة 1:1 (`1:1 Composition`)** عبر فصلي `StudentProfile` و `InstructorProfile`.
   - **المبرر المعماري:** فصل حساب الدخول والمصادقة الأمنية (`User`) عن الملف التعريفي الأكاديمي والمهني (`Profile`). هذا الفصل يمنع تضخم الكائنات ويسهل التعامل مع المستخدمين الذين قد تتغير أدوارهم، ويطابق تماماً بنية قاعدة البيانات ونموذج النطاق.
2. **علاقات التملك والتركيب الصارم (`Composition Semantics`):**
   - تم استخدام علاقة التركيب القوي (`*--`) حيثما كان الكيان التابع لا يملك أي معنى أو وجود مستقل عن الكيان الحاوي:
     - **الدورة التدريبية (`Course`):** تملك جلساتها (`CourseSession`)، وموادها التعليمية (`CourseMaterial`)، ومهامها (`Task`)، وتكليفات مدربيها (`CourseInstructor`).
     - **عقد الالتحاق (`Enrollment`):** يملك سندات الدفع (`Payment`)، وتسليمات الواجبات (`Submission`)، والملاحظات التوجيهية (`Feedback`).
     - **تكليف المدرب (`CourseInstructor`):** يملك سجلاً مالياً مستقلاً لمستحقاته (`InstructorDue`) والذي يملك بدوره دفعات الصرف (`InstructorPayout`).

### 5.2 السلوكيات والعمليات الذاتية للكيانات (Rich Domain Operations)
بدلاً من استخدام كائنات عقيمة تقتصر على دوال الجلب والتعيين (`Getters/Setters`)، يحتوي كل صنف على دوال تنفيذية تعبر عن منطق العمل:
- **`Course.publish()`:** لا تغير الحالة فحسب، بل تتحقق برمجياً من تعيين مدرب واحد على الأقل وتحديد نموذج تعويضه المالي قبل إتاحة الدورة للتسجيل (`BR-CRS-03`).
- **`Enrollment.creditApprovedPayment()`:** تخصم القسط المعتمد من الرصيد المتبقي ذرياً، وإذا وجد فائض تعيده ليتم توجيهه تلقائياً إلى طلب استرداد (`RefundRequest`).
- **`Payment.approve()`:** تُحسب وتفرز تلقائياً بين المبلغ المطبق على الرسوم والمبلغ الزائد، وتمنع ترحيل أي أموال غير معتمدة إلى رصيد المتدرب (`OQ-02`).
- **`Task.canAcceptSubmission()`:** تفحص الموعد النهائي آلياً وتغلق التسليم فور انقضائه ما لم يفعل المدرب خيار الاستثناء اليدوي للتسليم المتأخر (`OQ-03`).
- **`InstructorDue.accruePerSession()`:** تحتسب المستحقات حصراً بناءً على الجلسات المنجزة فعلياً والمثبت حضورها عبر سجلات `Attendance` وتستبعد الجلسات الملغاة (`OQ-04`).

---

## 6. Assumptions, Open Questions & Design Decisions

### 6.1 Confirmed Design Decisions (القرارات التصميمية المحسومة)
1. **عزل المستأجر متعدد المراكز (Multi-Tenancy Scoping):**  
   كل كيان يحتوي على `centerId` كخاصية خاصة إلزامية لضمان العزل التام للبيانات على مستوى الخادم (`BR-TEN-01`).
2. **سندات الدفع واعتمادها على مرحلتين (Two-Phase Payment Approval - OQ-02):**  
   صنف `Payment` يمثل السند، ويبقى بحالة `Pending_Approval` حتى تستدعى الدالة `approve()` بواسطة المدير أو المحاسب، والتي تقوم بتحديث رصيد كائن `Enrollment` في معاملة ذرية.
3. **معالجة الفائض المالي الإلزامية (Mandatory Refund Processing - OQ-05):**  
   يرتبط صنف `Payment` بصنف `RefundRequest` بعلاقة `1 : 0..1`، بحيث ينشأ كائن الاسترداد تلقائياً عند وجود أي فائض مالي ويُمنع تحويله لرصيد دائن.
4. **السعة وقوائم الانتظار (Capacity & Waitlists - OQ-06):**  
   يفحص صنف `Course` السعة القصوى عبر الدالة `isCapacityReached()`؛ وعند اكتمالها يُنشأ كائن `CourseWaitlist` لترتيب المتقدمين زمنياً (FIFO) دون فرض أي رسوم مالية.

### 6.2 Minor Operational Questions for Backend Implementation
1. **تمثيل المحاسب (Accountant Role vs. Manager):**  
   في مخطط حالات الاستخدام ومصفوفة PRD يظهر `Center Manager` كمسؤول عن الاعتماد المالي، بينما تذكر وثيقة `user-flow.md` إمكانية اعتماد السندات بواسطة `Accountant`.  
   *القرار التصميمي المعتمد:* تم ربط عمليات الاعتماد `Payment.approve()` بمعرف المستخدم العام `User approver` والتحقق من امتلاكه صلاحية إدارية/مالية على مستوى السياسات البرمجية (`Policy/Gate`).
2. **استرجاع السندات المعدلة (Audit Adjustments):**  
   تم تصميم عملية `Payment.adjust()` لتتطلب إدخال سبب التعديل ومسؤول العملية، مع تسجيل كائن `AuditLog` تلقائياً لمنع أي حذف مادي للعمليات المحاسبية.

---
*End of Design-Level UML Class Diagram Specification.*
