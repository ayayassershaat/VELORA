# Publish task and submit deliverable

**Use case:** UC-TSK-01 / UC-TSK-02 — Publish homework task and submit deliverable.

**Participants:** Assigned Course Instructor or Center Manager, Enrolled Student (actors), Application, Data Store.

**Preconditions:** Actor has course access; student is actively enrolled; task belongs to the center/course.

**Main scenario:** Authorized staff publishes task details and deadline. Student reads task and submits an allowed file. Application validates enrollment, deadline, file size (maximum 25 MB), and allowed format, then stores submission metadata and protected file reference.

**Alternative scenarios:** Before the deadline, student may replace the submission file. After deadline, only an explicit instructor/manager override permits submission and it is marked Late.

**Error scenarios:** Unauthorized actor/student, task not found, deadline passed without override, invalid file type/size, upload failure, or concurrent replacement. Existing submission should remain intact when replacement fails.

**Mermaid code:**

```mermaid
sequenceDiagram
    actor Instructor as Assigned Instructor or Center Manager
    actor Student as Enrolled Student
    participant App as Application
    participant Store as Data Store
    Instructor->>App: Submit task details and deadline
    App->>App: Authorize course access and validate task fields
    App->>Store: Save task in center/course scope
    Store-->>App: Task saved
    App-->>Instructor: Confirm task publication
    Student->>App: Open task and submit deliverable
    App->>App: Verify enrollment, deadline, file size and format
    alt Student is not enrolled or task inaccessible
        App-->>Student: Deny access
    else Deadline passed and no override
        App-->>Student: Reject submission; deadline has passed
    else Invalid file
        App-->>Student: Reject file with size or format error
    else Valid submission before deadline
        App->>Store: Save submission metadata and protected file reference
        Store-->>App: Submission saved
        App-->>Student: Confirm submission
    else Deadline passed with prior authorized override
        App->>Store: Save submission marked Late
        Store-->>App: Late submission saved
        App-->>Student: Confirm late submission
    end
    opt Student replaces file before deadline
        Student->>App: Request submission replacement
        App->>App: Recheck enrollment, deadline and file constraints
        App->>Store: Replace current file reference and submission metadata
        Store-->>App: Replacement saved or conflict
        App-->>Student: Confirm replacement or preserve prior submission on failure
    end
```

**الشرح بالعربية:** ينشر المدرب أو المدير المهمة وموعدها. يتحقق التطبيق من صلاحية الطالب واشتراكه والموعد وحجم الملف وصيغته قبل حفظ بيانات التسليم ومرجع الملف المحمي. يسمح باستبدال الملف قبل الموعد. بعد الموعد يرفض التسليم ما لم يوجد استثناء مصرح به مسبقاً، وعندها يسجل بحالة متأخر.

**Open questions:** لا تتوفر ERD/DBML. توثيق تدفق المستخدم يذكر رفع الاستثناء لكل طالب، بينما مخطط الاستخدام يصوغ السماح كامتداد عام؛ يجب تثبيت نطاق الاستثناء. لم توضح المستندات سياسة تعارض عمليتي استبدال متزامنتين.
