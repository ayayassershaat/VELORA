# Google Stitch Master Prompt - VELORA (فيلورا)

Copy and paste this entire prompt as-is into Google Stitch. 

```text
# ROLE
You are acting as an expert Google Stitch UI/UX Engineer, specialized in building clean, data-dense, role-based SaaS web applications for administrative workflows. 

Your sole focus is to design and build the MVP of VELORA exactly as specified. Do not add features, pages, or ideas outside of what is provided. 

# PROJECT CONTEXT
Project Name: VELORA (فيلورا)
Type: Multi-Tenant SaaS Web Application for Training Center Management
Target: Web Application (Responsive) 
Primary Language: English (No RTL required for MVP)
Tech Alignment: Next.js (React), TypeScript, Tailwind CSS
Status: Approved for MVP Implementation

Goal: Consolidate course management, student tracking, attendance, tasks, offline payments, and instructor compensation into a single, role-scoped platform to replace spreadsheets and fragmented communication.

# SOURCE OF TRUTH
You MUST strictly follow these documents for this task:
- docs/PRD.md (Product Requirements, MVP Scope, Roles, Features, Constraints)
- docs/UI_UX_REQUIREMENTS.md (Shared Navigation, Header/Sidebar Rules, States, Responsiveness, Accessibility)
- docs/UX_BLUEPRINT.md (User Journeys, Flows, Sitemap, Navigation Rules)
- docs/SCREEN_INVENTORY.md (Exact MVP Screens, Data, Actions, Validation, States)

Do not infer or invent new features. If there is any ambiguity, default to the most minimal MVP interpretation based off of these documents.

# CORE CONSTRAINTS
- MVP ONLY. Strictly adhere to the "Must Have" scope from the PRD. Ignore all "Should Have", "Nice to Have", and Non-Goals.
- NO native mobile apps. Web only, fully responsive.
- NO live payment gateway, NO live video/conferencing, NO chat/messaging, NO quizzes/exams, NO certificates, NO AI features.
- OFFLINE payments only. All payments are manual (Cash, Bank Transfer, POS Card, Cheque) and require Manager approval.
- Multi-tenant with strict CenterID scoping. Never trust client supplied tenant IDs.
- RBAC is enforced. UI must hide unauthorized items, server enforcement is implied.
- Immutable financial transactions. Payments cannot be hard deleted, only approved/rejected/adjusted with audit context.
- Private data. Instructor feedback is strictly 1:1. Sales cannot see instructor dues or private feedback. Students only see their own data.
- Instructor dues must follow their compensation model (Fixed, PercentageOfCollectedRevenue, PerSession) and only accrue under the correct conditions (e.g PerSession requires completed sessions with attendance).

# USERS & ROLES
There are exactly 4 roles:
1. MANAGER (Center Manager) - Full authority within center scope
2. SALES (Sales Specialist) - Intake, enrollment, recording payments (pending approval)
3. INSTRUCTOR (Course Instructor) - Assigned courses only (materials, tasks, attendance, feedback, own earnings)
4. STUDENT (Enrolled Student) - Enrolled courses only (materials, tasks, attendance, feedback, own balance)

# LAYOUT & SHARED UI RULES (CRITICAL)
You MUST implement this exact layout structure for all authenticated app screens.

Layout:
```
[Header - fixed at top, visible to all authenticated users]
├── [Sidebar - shared, filtered by role]
└── [Main Content Area - scrollable]
```

Header (ALWAYS VISIBLE FOR ALL AUTHENTICATED ROLES):
- Logo (click -> role-specific dashboard)
- Dashboard (visible to all, routes to the role's dashboard)
- Notifications (visible to all, role filtered content)
- User Menu (Profile, Settings, Logout) (visible to all)

Sidebar:
- ONE shared sidebar structure, filtered dynamically by role permissions.
- Hide unauthorized items, do NOT disable them.
- Routes must follow the sitemap from UX_BLUEPRINT.md
- Scope is enforced by role (e.g Instructors only see "My Courses", Students only see their enrolled courses, Sales cannot see Instructor Dues/Feedback)

# VISUAL DIRECTION
Create a clean, professional, and operational focused design. Prioritize clarity, scannability, and efficiency for daily administrative work.

- Aesthetic: Minimalist enterprise UI. Avoid decorative illustrations, excessive gradients, or playful elements.
- Typography: Inter, Satoshi, or system-ui. Prioritize legibility. Use a clear type scale.
- Spacing: Consistent spacing scale (4pt based, e.g 4,8,12,16,24,32,48...)
- Color Palette (establish and use consistently):
  - Background: #0F1026 or clean light background (#F8F9FA / #FFFFFF). Prioritize readability and high contrast.
  - Surfaces/Cards: #FFFFFF with subtle borders
  - Primary: A professional accent (e.g slate/indigo/teal) used for primary actions
  - Text: Primary #1F2937, Secondary #4B5563, Muted #6B7280
  - Status colors: Success, Warning, Danger, Info with accessible contrast
  - Borders: #E5E7EB
- Components: Build a reusable component set from your first screen (buttons, inputs, selects, tables, cards, badges, tabs, modals, alerts, skeletons, empty states)
- States: Must account for Loading (skeleton), Empty, Error, Success, and No Permission states
- Interactions: Clear focus states, confirmations for destructive/sensitive actions (archive course, approve/reject payment, late override), inline validation, prevent double submits

# RESPONSIVENESS & ACCESSIBILITY
- Mobile first. Breakpoints: >=360px up to 4K
- Mobile (360-640px): Hide sidebar, convert to off-canvas drawer. Convert data tables to card lists where appropriate. Touch friendly controls.
- Tablet (641-1024px): Collapsible sidebar, maintain readability
- Desktop (1025px+): Full sidebar + fixed header, maximize scannable space
- Accessibility: Semantic HTML (<header>, <nav>, <main>, <aside>), proper labels, focus visible, aria-live for errors/notifications, keyboard navigable, sufficient color contrast, alt-text where applicable.

# LANGUAGE
Primary language is English. Do not add Arabic translations or RTL layout. Keep all copy in English to match the approved docs.

# TASK INSTRUCTIONS (DO THIS IN ORDER)
1. READ & INTERNALIZE: Treat PRD, UX_BLUEPRINT, SCREEN_INVENTORY, and UI_UX_REQUIREMENTS as binding. Extract the shared layout, RBAC rules, and MVP scope.
2. ESTABLISH DESIGN SYSTEM FIRST: Before rendering any page content, define and apply a consistent design system (tokens, typography, spacing, radius, shadows, colors, and core components) that will be reused across the entire app.
3. GENERATE ONLY ONE SCREEN: Do NOT generate all screens. Generate ONLY the following representative MVP screen as your FIRST output: SCR-M-01 - Manager Dashboard (/manager/dashboard)
   - This screen is the most representative because it showcases the shared Header + Sidebar layout, role-based scoping, summary cards, financial summary, alerts, and navigation to other core areas.
4. MAKE IT REAL TO THE SPECS: Implement this dashboard to reflect the Manager needs from the PRD (Active Courses, Enrolled Students, Instructors, Invoiced, Collected, Receivables, Pending Instructor Liabilities, and urgent alerts). Use realistic placeholder data that matches the domain, but do not fabricate unrelated features.
5. FOLLOW THE SCREEN INVENTORY: Adhere to SCR-M-01's purpose, components, data, actions, navigation, states, accessibility, and responsive behavior exactly as specified.
6. BE PRODUCTION MINDED FOR STITCH: Use clean Tailwind CSS classes, reusable components, semantic structure, and mobile responsive patterns. Keep the layout modular so it can scale to the other MVP screens without redesigning the system.
7. ASK FOR APPROVAL BEFORE CONTINUING: After presenting the design system + the Manager Dashboard, explicitly ask: "Do you approve this design direction and layout to proceed to the next MVP screen, or would you like any adjustments?" Do not proceed to generate additional screens until approval is given.

# START NOW
Begin by establishing the design system and rendering ONLY SCR-M-01 (Manager Dashboard) with the shared Header and filtered Sidebar for the MANAGER role. Follow every rule above to the letter.
```