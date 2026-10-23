# VELORA MVP Sequence Diagrams

Source of truth: `docs/PRD.md`, cross-checked against `docs/user-flow.md` and `docs/diagrams/use-case.md`. `docs/ERD.md` and `docs/database.dbml` were not found; data steps use PRD entities and logical participants. Components shown are logical, not claims about deployed architecture.

## Major MVP use cases

- [Sign in and tenant validation](sequences/sign-in.md) — UC-AUTH-01
- [Create course and assign instructor](sequences/course-setup.md) — UC-CRS-01/03
- [Register student and enroll](sequences/student-enrollment.md) — UC-STU-01, UC-ENR-01
- [Record attendance](sequences/attendance.md) — UC-ATT-01
- [Publish task and submit deliverable](sequences/task-submission.md) — UC-TSK-01/02
- [Record and approve offline payment](sequences/payment-approval.md) — UC-PAY-01/02
- [Execute excess refund](sequences/refund.md) — UC-REF-01
- [Review instructor dues and settle payout](sequences/instructor-settlement.md) — UC-INSFIN-01/02

