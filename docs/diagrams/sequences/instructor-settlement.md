# Review instructor dues and settle payout

**Use case:** UC-INSFIN-01 / UC-INSFIN-02 — Review liabilities and disburse instructor settlement.

**Participants:** Center Manager (actor), Application, Data Store.

**Preconditions:** Manager is authorized; instructor and dues belong to the manager's center. Instructor compensation terms exist.

**Main scenario:** Manager requests liabilities; application reads applicable dues and compensation terms; manager records a payout against eligible outstanding dues; application validates amount and records settlement and audit data.

**Alternative scenarios:** Due calculation follows configured model: fixed amount, percentage of collected revenue, or per completed session. Per-session dues accrue only for sessions actually completed and officially documented with attendance. Partial settlement leaves remainder outstanding.

**Error scenarios:** Unauthorized/cross-center request, no eligible due, invalid/over-remaining payout, or concurrent payout. Recheck current due balance at write time to prevent overpayment.

**Mermaid code:**

```mermaid
sequenceDiagram
    actor Manager as Center Manager
    participant App as Application
    participant Store as Data Store
    Manager->>App: Request instructor liabilities
    App->>App: Authorize manager and derive center scope
    App->>Store: Read instructor terms, completed sessions, collected revenue and dues
    Store-->>App: Scoped dues and calculation inputs
    App->>App: Calculate eligible liability by configured compensation model
    App-->>Manager: Display liabilities and payout status
    Manager->>App: Submit settlement amount and payout reference
    App->>App: Validate positive amount and eligible outstanding balance
    alt No eligible due or invalid amount
        App-->>Manager: Reject settlement with reason
    else Valid settlement
        App->>Store: Re-read due balance and save payout atomically
        Store-->>App: Payout saved or concurrent balance conflict
        alt Payout saved
            App-->>Manager: Confirm settlement and remaining due
        else Concurrent payout conflict
            App-->>Manager: Refresh dues before retrying
        end
    end
```

**الشرح بالعربية:** يعرض التطبيق مستحقات المدرب للمدير وفق شروط التعويض المسجلة. تُحسب أجرة الجلسة فقط للجلسات المنفذة والمثبتة بالحضور. عند تسجيل دفعة، يتحقق التطبيق من المبلغ والمستحق القائم ويعيد قراءته عند الحفظ لمنع صرف مبلغ يتجاوز الرصيد بسبب عمليات متزامنة.

**Open questions:** لا تتوفر ERD/DBML. لم تحدد الوثائق قواعد توزيع مستحقات النسبة المئوية عند تعدد المدربين أو تفاصيل تعديل/تسوية المستحقات المتنازع عليها.
