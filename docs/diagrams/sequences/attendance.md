# Record attendance

**Use case:** UC-ATT-01 — Record session attendance sheet.

**Participants:** Assigned Course Instructor or Center Manager (actor), Application, Data Store.

**Preconditions:** Actor is signed in and authorized for the center and course; session exists and belongs to that center.

**Main scenario:** Actor loads the session roster, marks each enrolled student Present or Absent, and submits the sheet. Application validates roster membership and statuses, then inserts or updates one attendance row per session/student.

**Alternative scenarios:** Resubmission updates existing attendance entries rather than creating duplicates.

**Error scenarios:** Unauthorized or cross-center access, nonexistent session, student not enrolled, invalid attendance status, or write failure. A unique session/student constraint protects concurrent submissions.

**Mermaid code:**

```mermaid
sequenceDiagram
    actor Staff as Assigned Instructor or Center Manager
    participant App as Application
    participant Store as Data Store
    Staff->>App: Open session attendance
    App->>App: Authorize actor and derive center scope
    App->>Store: Read session and enrolled roster
    Store-->>App: Session and roster or not found
    alt Session unavailable or access denied
        App-->>Staff: Return not found or forbidden response
    else Session available
        App-->>Staff: Display enrolled roster
        Staff->>App: Submit Present or Absent for each roster member
        App->>App: Validate session, enrollment membership and statuses
        alt Invalid roster member or status
            App-->>Staff: Reject sheet with validation details
        else Valid sheet
            App->>Store: Upsert attendance by session and student
            Store-->>App: Attendance rows saved or uniqueness conflict
            alt Saved
                App-->>Staff: Confirm attendance recorded
            else Concurrent conflict
                App-->>Staff: Refresh sheet and retry with current records
            end
        end
    end
```

**الشرح بالعربية:** يعرض التطبيق قائمة المسجلين في الجلسة بعد التحقق من صلاحية المدرب أو المدير. يرسل المستخدم حالات الحضور، ويتأكد التطبيق من أن كل طالب ضمن القائمة وأن الحالة صحيحة. تحفظ السجلات بالتحديث أو الإدراج وفق مفتاح الجلسة والطالب لمنع التكرار عند الإرسال المتزامن.

**Open questions:** لا تتوفر ERD/DBML لتأكيد مفتاح التفرد الدقيق. يلزم توضيح ما إذا كان يمكن تعديل الحضور بعد إغلاق الجلسة ومن يملك ذلك.
