# Project Requirements Document (PRD)

**Project Name:** codeguide-manufacturing-app  
**Document Type:** Project Requirements Document  
**Created For:** AI Model Reference  

---

## 1. Project Overview

codeguide-manufacturing-app is a full-stack web application starter kit designed as the foundation for building a Manufacturing Execution System (MES) or Enterprise Resource Planning (ERP) solution. It provides out-of-the-box user authentication, a protected dashboard interface, a rich UI component library, and a type-safe data layer. By offering these core capabilities, it eliminates boilerplate work and accelerates development of specialized manufacturing modules—such as Bill of Materials (BOM), inventory management, and production workflows.

The project is being built to help development teams quickly launch reliable, secure, and maintainable manufacturing software. Key objectives include:

- Secure, role-based user management (e.g., planners, warehouse staff, supervisors)
- A modular, type-safe codebase in TypeScript from front end through database
- Easy containerized deployment (Docker & Docker Compose)

Success criteria for the first release:

1. Developers can sign up, sign in, and access a protected dashboard.  
2. CRUD operations for BOM, Inventory, and Work Orders exist via RESTful API and UI pages.  
3. End-to-end type safety and seamless local-to-production parity.

---

## 2. In-Scope vs. Out-of-Scope

### In-Scope (First Version)

- User authentication & session management using Better Auth  
- Role-Based Access Control (RBAC) middleware for API routes  
- Protected dashboard layout with KPI placeholders (OEE, inventory levels, plan adherence)  
- Core UI components (tables, charts, forms) via Shadcn UI + Tailwind CSS  
- REST API routes under `/app/api/` for BOM, Inventory, Work Orders  
- Database schema definitions in `/db/schema/` using Drizzle ORM  
- Database migrations with `drizzle-kit`  
- Docker and Docker Compose configurations for app + PostgreSQL  
- Input validation with Zod, structured logging with Pino  

### Out-of-Scope (Later Phase)

- Real-time updates via WebSockets or server-sent events  
- Advanced scheduling, production planning algorithms  
- Mobile-native or offline support  
- Notifications (email/SMS) and audit trails  
- Multi-tenant architecture or advanced analytics dashboards  

---

## 3. User Flow

When a new user lands on the application, they see a public landing page with an option to sign up or sign in. After registering (providing email, password, and initial role), the user’s session is managed by Better Auth and stored in a secure cookie. Upon successful authentication, they are redirected to the **Dashboard** page. The dashboard shows summary cards for Overall Equipment Effectiveness (OEE), inventory levels, and production plan adherence. A left-side navigation menu lists modules: **BOM**, **Inventory**, and **Work Orders**.

Clicking each module takes the user to a list page. For example, in the **BOM** section, a data table displays all BOM records, with buttons to `Create`, `Edit`, or `Delete`. Creating a new BOM opens a form where the user enters a BOM code, description, and line items. Submitting triggers a POST to `/api/bom`, validated by Zod and persisted by Drizzle ORM. Similar flows exist for **Inventory** (adding stock, viewing levels) and **Work Orders** (creating orders, issuing materials, completing production). Errors (e.g., insufficient stock) return user-friendly messages, and successes refresh the list view.

---

## 4. Core Features

- **Authentication & Authorization**: Sign-up, sign-in, session handling, and RBAC middleware  
- **Protected Dashboard**: KPI overview and navigation menu for modules  
- **UI Component Library**: Shadcn UI + Tailwind CSS for tables, charts, forms, inputs, buttons  
- **API Routes**: Next.js API endpoints for BOM, Inventory, Work Orders under `/app/api/`  
- **Type-Safe ORM & Schema**: Drizzle ORM for database models, `drizzle-kit` for migrations  
- **Database Transactions**: Atomic operations for critical flows (e.g., material issuance)  
- **Input Validation**: Zod schemas on API boundaries  
- **Structured Logging**: Pino for consistent server logs  
- **Containerization**: Dockerfiles and Docker Compose for local and production parity  

---

## 5. Tech Stack & Tools

Frontend:
- Next.js (App Router)  
- React 18+  
- TypeScript  
- Tailwind CSS & Shadcn UI  
- NextThemes (dark/light mode)

Backend:
- Next.js API Routes  
- Better Auth library for authentication  
- Drizzle ORM & `drizzle-kit` migrations  
- PostgreSQL database

DevOps & Tools:
- Docker & Docker Compose  
- Zod for input + environment validation  
- Pino for structured logging  
- Jest for unit tests, Playwright or Cypress for E2E tests

IDE & Plugins (optional):
- VS Code  
- GitHub Copilot, Cursor, Windsurf

---

## 6. Non-Functional Requirements

Performance:
- Page load time < 2 seconds on 3G throttle  
- API response time < 200ms for simple queries

Security:
- HTTPS enforced end-to-end  
- OWASP Top 10 mitigation (SQL injection, XSS, CSRF)  
- Encrypted session cookies, secure JWT handling

Compliance:
- GDPR-compliant cookie banner and data handling  
- Role-based data access control

Usability:
- Responsive design (desktop + tablet)  
- Accessible (WCAG AA) forms and navigation  
- Dark/light theme support for varied factory environments

---

## 7. Constraints & Assumptions

- Node.js >= v16 and PostgreSQL must be available in target environments.  
- Docker must be supported for local and production setups.  
- Better Auth library is compatible with Next.js App Router.  
- Users have modern browsers (Chrome, Edge, Firefox).  
- Drizzle ORM supports required transaction patterns.  
- No external AI models are required in this phase.

---

## 8. Known Issues & Potential Pitfalls

- **Concurrency / Race Conditions**: Simultaneous material issuance may cause inventory mismatches. Mitigation: wrap inventory checks and updates in database transactions with row-level locking.
- **Schema Drift**: Manual database changes can diverge from migration history. Mitigation: enforce `drizzle-kit` migrations in CI/CD and run `drizzle-kit check` before deployments.
- **RBAC Complexity**: Evolving permission sets may be hard to maintain. Mitigation: start with a simple roles matrix and build middleware with clear role-to-endpoint mapping.
- **Large Data Volumes**: Rendering huge tables or charts can slow down the UI. Mitigation: implement pagination and lazy-loading, use virtualization for tables.

---

**End of Document**

This PRD provides a clear, unambiguous blueprint for the AI model and subsequent technical documents (Tech Stack, Frontend Guidelines, Backend Structure, etc.).