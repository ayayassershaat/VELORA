# Execute excess refund

**Use case:** UC-REF-01 — Execute excess refund payout.

**Participants:** Center Manager (actor), Application, Data Store.

**Preconditions:** Approved payment has an associated Pending Refund request for an actual excess; payout is made offline.

**Main scenario:** Manager reviews the request, executes cash or bank refund, records amount/date/method/reference, confirms execution, and application marks Refunded and writes an immutable audit record.

**Alternative scenarios:** Refund payout may be cash or bank transfer as allowed by the flow.

**Error scenarios:** Request not pending, amount/reference invalid, payout not confirmed, or write failure. Failed payout is recorded with reason and remains actionable/requeued; do not mark refunded before confirmation.

**Mermaid code:**

```mermaid
sequenceDiagram
    actor Manager as Center Manager
    participant App as Application
    participant Store as Data Store
    Manager->>App: Open pending refund request
    App->>App: Authorize manager
    App->>Store: Read refund and linked payment in center scope
    Store-->>App: Refund state and excess amount
    alt Request unavailable or not pending
        App-->>Manager: Reject action; show current request state
    else Pending refund
        App-->>Manager: Display amount due and student details
        Manager->>App: Record offline payout details and execution result
        App->>App: Validate payout amount, date, method and reference
        alt Payout not confirmed or failed
            App->>Store: Record failure reason and retain actionable refund status
            Store-->>App: Failure recorded
            App-->>Manager: Confirm failure recorded for follow-up
        else Payout confirmed
            App->>Store: Mark Refunded and append immutable audit entry
            Store-->>App: Refund and audit saved
            App-->>Manager: Confirm refund completion and voucher
        end
    end
```

**الشرح بالعربية:** يراجع المدير طلب الاسترداد المرتبط بفائض معتمد. يسجل بيانات الصرف النقدي أو التحويل ونتيجة التنفيذ. لا يغير التطبيق الحالة إلى مسترد إلا بعد تأكيد الصرف؛ وإذا فشل، يوثق السبب ويبقي الطلب قابلاً للمتابعة.

**Open questions:** لا تتوفر ERD/DBML لتأكيد حالات الاسترداد الدقيقة أو العلاقة بين المبلغ الزائد والسند. يلزم توضيح صلاحية المحاسب في تنفيذ الصرف؛ المصدر المسند في حالة الاستخدام هو المدير.
