# Record and approve offline payment

**Use case:** UC-PAY-01 / UC-PAY-02 — Record and approve/reject offline payment voucher.

**Participants:** Sales Specialist (records), Center Manager (reviews), Application, Data Store.

**Preconditions:** Sales and manager are authorized in the same center; enrollment exists. Payment channels are offline; no payment gateway is involved.

**Main scenario:** Sales enters positive payment amount, date, method and receipt reference. Application validates enrollment and remaining balance; creates Pending Approval voucher and, for excess, linked Pending Refund request. Manager approves or rejects. Approval atomically posts immutable payment, applies only the amount due, updates balance/status and audit log.

**Alternative scenarios:** Rejection requires an audit rationale and leaves the student balance unchanged. No excess means no refund request. Partial payment leaves PartiallyPaid; exact settlement yields FullyPaid.

**Error scenarios:** Invalid amount or enrollment, duplicate receipt/conflict, unauthorized reviewer, concurrent approvals, or transaction failure. Rollback must leave payment and balance unchanged. Posted payments cannot be deleted.

**Mermaid code:**

```mermaid
sequenceDiagram
    actor Sales as Sales Specialist
    actor Manager as Center Manager or Accountant
    participant App as Application
    participant Store as Data Store
    Sales->>App: Submit offline payment voucher details
    App->>App: Authorize sales role and validate positive amount and method
    App->>Store: Read enrollment and current balance in center scope
    Store-->>App: Enrollment, balance and payment state
    alt Enrollment invalid or amount invalid
        App-->>Sales: Reject voucher with validation error
    else Valid payment entry
        App->>App: Compare amount with remaining balance
        App->>Store: Save Pending Approval voucher
        opt Payment exceeds remaining balance
            App->>Store: Create linked Pending Refund for excess only
            Store-->>App: Refund request saved
        end
        Store-->>App: Voucher saved
        App-->>Sales: Confirm submission; balance remains unchanged pending approval
        Manager->>App: Review pending voucher and decide
        App->>App: Authorize manager/accountant review
        alt Reviewer lacks permission
            App-->>Manager: Deny review
        else Manager rejects with rationale
            App->>Store: Mark voucher Rejected and record rationale
            Store-->>App: Rejection recorded; balance unchanged
            App-->>Manager: Confirm rejection
        else Manager approves
            App->>Store: Begin atomic posting of voucher, balance and audit record
            Store-->>App: Re-read current balance and approval state
            App->>App: Revalidate state; apply amount up to payable balance
            alt State conflict or transaction failure
                App->>Store: Roll back posting transaction
                Store-->>App: No partial financial changes
                App-->>Manager: Report conflict or safe retryable failure
            else Posting succeeds
                App->>Store: Mark Completed; update balance/status and immutable audit log
                Store-->>App: Transaction committed
                App-->>Manager: Confirm approval and official receipt
            end
        end
    end
```

**الشرح بالعربية:** يسجل موظف المبيعات سنداً غير إلكتروني بمبلغ موجب، ويبقى الرصيد دون تغيير حتى الاعتماد. ينشأ طلب استرداد مرتبط تلقائياً عند وجود فائض، ويقتصر المبلغ المطبق على الرسوم المستحقة. عند اعتماد المدير، يعيد التطبيق فحص الحالة والرصيد وينفذ ترحيل السند وتحديث الرصيد وسجل التدقيق في معاملة ذرية. الرفض يتطلب سبباً ولا يغير الرصيد.

**Open questions:** لا تتوفر ERD/DBML لتأكيد تمثيل المبلغ الإجمالي والفائض في السند. تعارض اعتماد سندين على الرصيد نفسه يحسم بإعادة القراءة والتحقق الذري؛ يلزم تحديد سلوك عرض رسالة التعارض للمستخدم. مصفوفة الصلاحيات في PRD ومخطط حالات الاستخدام لا يعرضان المحاسب كفاعل مستقل، بينما قرار OQ-02 في تدفق المستخدم يجيز اعتماده؛ يلزم توحيد تعريف الدور.

