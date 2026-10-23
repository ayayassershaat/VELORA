# Register student and enroll

**Use case:** UC-STU-01 / UC-ENR-01 — Register student profile and enroll in course.

**Participants:** Sales Specialist or Center Manager (actor), Application, Data Store.

**Preconditions:** Staff member is signed in and authorized; lookups are scoped to the authenticated center.

**Main scenario:** Find an existing student or create a profile; select an active course; validate no active duplicate and available capacity; calculate discount/payable; save enrollment and opening balance.

**Alternative scenarios:** Existing profile is reused. No discount means base price applies. At capacity, direct enrollment is blocked and staff may request free FIFO waitlist placement.

**Error scenarios:** Duplicate active enrollment (409), inactive course, invalid discount, duplicate contact data, or seat race. Capacity must be rechecked when saving to prevent oversubscription.

**Mermaid code:**

```mermaid
sequenceDiagram
    actor Staff as Sales Specialist or Center Manager
    participant App as Application
    participant Store as Data Store
    Staff->>App: Search student and choose course
    App->>App: Authorize staff and validate request
    App->>Store: Find student and active course in center scope
    Store-->>App: Student profile and course state
    opt Student profile does not exist
        Staff->>App: Submit student profile
        App->>App: Validate required fields and contact uniqueness
        App->>Store: Save profile in center scope
        Store-->>App: Student profile saved
    end
    App->>Store: Check active enrollment and seat capacity
    Store-->>App: Enrollment and capacity state
    alt Course is inactive
        App-->>Staff: Reject; enrollments require an active course
    else Active duplicate enrollment exists
        App-->>Staff: Reject duplicate enrollment
    else Course is full
        App-->>Staff: Explain full capacity and offer free waitlist
        opt Staff requests waitlist placement
            Staff->>App: Request waitlist placement
            App->>Store: Save Waiting entry with FIFO timestamp and no fee
            Store-->>App: Waitlist entry saved
            App-->>Staff: Confirm waiting status
        end
    else Course has capacity
        opt Discount requested
            Staff->>App: Submit fixed or percentage discount
            App->>App: Validate discount does not exceed base price
        end
        App->>App: Calculate payable and opening balance
        App->>Store: Save enrollment and balance; reserve seat
        Store-->>App: Saved or capacity/uniqueness conflict
        alt Enrollment saved
            App-->>Staff: Confirm enrollment and balance
        else Concurrent enrollment conflict
            App-->>Staff: Reject conflict and refresh availability
        end
    end
```

**الشرح بالعربية:** يبحث الموظف عن ملف الطالب أو ينشئه بعد التحقق من بياناته. يفحص التطبيق نشاط الدورة والتسجيل السابق والسعة، ثم يحسب الخصم والرصيد الابتدائي. عند اكتمال المقاعد يمنع التسجيل المباشر ويتيح قائمة انتظار مجانية بترتيب FIFO. يعاد فحص السعة عند الحفظ لمنع تجاوزها بسبب طلبات متزامنة.

**Open questions:** لا تتوفر ERD/DBML. لم يحدد PRD تفاصيل تحرير المقعد عند الانسحاب أو ترقية المنتظرين تلقائياً.
