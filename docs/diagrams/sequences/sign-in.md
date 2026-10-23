# Sign in and tenant validation

**Use case:** UC-AUTH-01 / UC-AUTH-01a — Sign in to center portal.

**Participants:** Authenticated User (actor), Application, Data Store.

**Preconditions:** User has a center-specific account; no authenticated session exists.

**Main scenario:** User submits credentials; application verifies account and password, derives tenant context server-side, records sign-in, and returns a role-tailored workspace.

**Alternative scenarios:** A valid active account is directed to its role-specific dashboard.

**Error scenarios:** Invalid credentials, inactive/suspended account, rate limit, or store failure denies sign-in without establishing a session. Tenant scope is never accepted from user input.

**Mermaid code:**

```mermaid
sequenceDiagram
    actor User
    participant App as Application
    participant Store as Data Store
    User->>App: Submit email and password
    App->>Store: Find account by credential identity
    Store-->>App: Account record, password hash, status, center and role
    App->>App: Verify password, active status and rate limit
    alt Invalid credentials
        App-->>User: Reject with generic credential error
    else Account inactive or suspended
        App-->>User: Reject with account status message
    else Valid active account
        App->>App: Derive center scope from authenticated account
        App->>Store: Record successful login event
        Store-->>App: Login event recorded
        App-->>User: Establish session and show role-tailored dashboard
    else Authentication store failure
        App-->>User: Return safe error; do not establish session
    end
```

**الشرح بالعربية:** يدخل المستخدم بياناته، ويتحقق التطبيق من كلمة المرور وحالة الحساب. يستمد التطبيق نطاق المركز من الحساب الموثق ولا يثق بمعرّف مركز يرسله المستخدم. عند النجاح يسجل حدث الدخول ويفتح لوحة الدور المناسبة؛ وعند الفشل لا ينشئ جلسة.

**Open questions:** لا تتوفر ERD/DBML لتأكيد حقول الهوية أو سجل الدخول. قرار الحساب المنفصل لكل مركز موثق في مخطط حالات الاستخدام.
