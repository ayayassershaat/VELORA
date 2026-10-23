# Create course and assign instructor

**Use case:** UC-CRS-01 / UC-CRS-03 — Create course cohort and assign instructor/terms.

**Participants:** Center Manager (actor), Application, Data Store.

**Preconditions:** Manager is signed in and authorized; center scope comes from the session.

**Main scenario:** Manager submits course details and instructor/compensation terms; application validates fields, dates, tenant ownership, and compensation model; it persists the course and assignment.

**Alternative scenarios:** Multiple instructors may be assigned with distinct compensation terms. Session scheduling is a separate course operation.

**Error scenarios:** Unauthorized role, invalid details/terms, instructor outside the center, duplicate assignment, or write conflict. A failed operation must not leave a partial course/assignment.

**Mermaid code:**

```mermaid
sequenceDiagram
    actor Manager as Center Manager
    participant App as Application
    participant Store as Data Store
    Manager->>App: Submit course details and instructor terms
    App->>App: Authorize manager and validate details and terms
    App->>Store: Check instructor belongs to manager's center
    Store-->>App: Instructor ownership and assignment state
    alt Unauthorized or invalid request
        App-->>Manager: Reject with access or validation error
    else Duplicate instructor assignment
        App-->>Manager: Reject duplicate assignment
    else Valid request
        App->>Store: Save course and assignment in center scope
        Store-->>App: Course and assignment saved
        App-->>Manager: Confirm course setup
    else Concurrent duplicate or write conflict
        Store-->>App: Uniqueness or write conflict
        App-->>Manager: Report conflict; retain consistent data
    end
```

**الشرح بالعربية:** يرسل المدير بيانات الدورة وشروط أجر المدرب. يتحقق التطبيق من الصلاحيات وصحة البيانات وانتماء المدرب للمركز، ثم يحفظ الدورة وإسناد المدرب. يمنع الإسناد المكرر، ويحافظ على سلامة البيانات عند تعارض الحفظ.

**Open questions:** لا تتوفر ERD/DBML لتأكيد ذرية حفظ الدورة والإسناد أو أسماء الحقول. تفاصيل التحقق لكل حقل غير محددة بالكامل في PRD.
