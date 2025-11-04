# Tech Stack Document for codeguide-manufacturing-app

This document explains, in everyday terms, why we picked each technology for the **codeguide-manufacturing-app** starter kit. It’s aimed at non-technical readers who want to understand how everything fits together.

## 1. Frontend Technologies
These tools power what users see and interact with in their browser:

- **Next.js (App Router)**
  - A React framework that handles page rendering on the server or client. It helps show up-to-date production and inventory data quickly.  
- **React 19**
  - The core library for building interactive components (forms, tables, charts).  
- **TypeScript**
  - Adds type checks to JavaScript. It prevents mix-ups (e.g., treating a quantity as text) by catching mistakes at build time.  
- **Tailwind CSS & Shadcn UI**
  - Utility-first styling (Tailwind) plus a set of ready-made UI components (Shadcn UI) let us build clean, consistent interfaces fast.  
- **Next-Themes**
  - Enables light/dark mode switching. Useful if your factory floor prefers darker screens.

**How these choices enhance UX:**
  - Fast page loads and real-time updates (Next.js).  
  - Predictable, bug-free forms and tables (TypeScript).  
  - Consistent look and feel across every screen (Tailwind + Shadcn UI).  

## 2. Backend Technologies
These handle data storage, business logic, and secure access:

- **Next.js API Routes**  
  - Lets us define REST endpoints (e.g., `/api/bom`, `/api/inventory`) right next to pages.  
- **Better Auth**  
  - A library for secure sign-up, sign-in, and session handling. We extend it to enforce user roles like “Planner” or “Warehouse Staff.”  
- **PostgreSQL**  
  - A reliable relational database for storing structured manufacturing data (BOMs, inventory levels, work orders).  
- **Drizzle ORM**  
  - A type-safe way to read and write database rows. It prevents errors by matching your code’s types to the database schema.  
- **Drizzle-kit**  
  - Manages database migrations—controlled, repeatable changes to your tables as your app evolves.  
- **Zod**  
  - Validates all incoming data (API inputs, environment variables) to catch bad requests early.  
- **Pino (Structured Logging)**  
  - Records server events in a consistent format. Helpful for debugging production issues or auditing actions.

**How these pieces work together:**
  - API routes receive requests, use Zod to validate inputs, check user permissions via Better Auth, and run Drizzle queries against PostgreSQL inside a transaction for data integrity.

## 3. Infrastructure and Deployment
How we host, version, and deliver the application:

- **Docker & Docker Compose**  
  - Containers wrap the app and database so they run the same way on every machine and in production.  
- **Git & GitHub**  
  - Version control system to track code changes and collaborate.  
- **GitHub Actions (CI/CD)**  
  - Automates testing and deployment pipelines, ensuring new code is checked and released smoothly.

**Benefits:**
  - One-command setup (`docker-compose up`) for local development.  
  - Reliable, repeatable deployments with minimal manual steps.  
  - Automatic testing and checks before any code goes live.

## 4. Third-Party Integrations
External services and libraries that extend functionality:

- **Better Auth** (authentication and session management)  
- **Shadcn UI** (pre-built React components for tables, forms, buttons)  
- **Drizzle ORM & Drizzle-kit** (database access and migrations)  
- **Zod** (data validation)  
- **Pino** (logging)  
- **Jest, Playwright/Cypress** (testing frameworks—unit, integration, end-to-end)

**Why they matter:**
  - We don’t reinvent wheels: these libraries are battle-tested and focus our effort on manufacturing logic instead of low-level plumbing.

## 5. Security and Performance Considerations
Measures we’ve built in to keep data safe and ensure smooth operation:

- **Secure Authentication & RBAC**  
  - Only authorized roles can create work orders, issue materials, or adjust inventory.  
- **Type Safety End to End**  
  - TypeScript and Drizzle prevent type mismatches, reducing runtime crashes and data errors.  
- **Input Validation**  
  - Zod ensures every API call meets our rules (e.g., quantity must be a positive number).  
- **Atomic Database Transactions**  
  - Multi-step operations (like issuing material and updating inventory) succeed or fail together—no half-done updates.  
- **Structured Logging**  
  - Pino captures detailed logs for troubleshooting production issues quickly.  
- **Testing Strategy**  
  - Unit tests (Jest) for core logic, integration tests for API flows, and end-to-end tests (Playwright/Cypress) to simulate real users.

## 6. Conclusion and Overall Tech Stack Summary
- We chose **Next.js + React + TypeScript** for a fast, type-safe, and interactive user interface.  
- **Tailwind CSS + Shadcn UI** deliver a consistent, easy-to-maintain design system.  
- **Next.js API Routes + Better Auth + PostgreSQL + Drizzle ORM** give us secure, type-checked data handling and user management.  
- **Docker**, **GitHub**, and **GitHub Actions** make development and deployment simple and reliable.  
- **Zod** and **Pino** add robust validation and logging.  
- **Comprehensive testing** ensures key manufacturing workflows (BOM management, material issuance, production tracking) run smoothly.

Together, this stack provides a rock-solid foundation for any Manufacturing Execution System or ERP application—so you can focus on the unique business logic that makes your factory run.