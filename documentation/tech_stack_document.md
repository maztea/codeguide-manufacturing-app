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
- **Drizzle ORM (Exclusive ORM Choice)**
  - Our sole and exclusive Object-Relational Mapping solution. Drizzle provides a type-safe way to read and write database rows with TypeScript-first design. It prevents runtime errors by matching your code's types to the database schema at compile time.
- **Drizzle-kit**
  - The official toolkit for Drizzle ORM that manages database migrations—controlled, repeatable changes to your tables as your app evolves.  
- **Zod**  
  - Validates all incoming data (API inputs, environment variables) to catch bad requests early.  
- **Pino (Structured Logging)**  
  - Records server events in a consistent format. Helpful for debugging production issues or auditing actions.

**How these pieces work together:**
  - API routes receive requests, use Zod to validate inputs, check user permissions via Better Auth, and run Drizzle ORM queries (our exclusive data access layer) against PostgreSQL inside a transaction for data integrity.

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
- **Drizzle ORM & Drizzle-kit** (exclusive database access and migration solution)
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
  - TypeScript and our exclusive Drizzle ORM integration prevent type mismatches, reducing runtime crashes and data errors.  
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
- **Next.js API Routes + Better Auth + PostgreSQL + Drizzle ORM (exclusive)** give us secure, type-checked data handling and user management with a single, unified ORM approach.  
- **Docker**, **GitHub**, and **GitHub Actions** make development and deployment simple and reliable.  
- **Zod** and **Pino** add robust validation and logging.  
- **Comprehensive testing** ensures key manufacturing workflows (BOM management, material issuance, production tracking) run smoothly.

Together, this stack provides a rock-solid foundation for any Manufacturing Execution System or ERP application—so you can focus on the unique business logic that makes your factory run.

## 7. ORM Strategy - Drizzle Only

**Our Exclusive ORM Choice: Drizzle ORM**

We have standardized exclusively on **Drizzle ORM** as our single Object-Relational Mapping solution. This strategic decision provides several key advantages:

**Why Drizzle ORM Only:**
- **Type Safety First**: Full TypeScript integration with compile-time type checking
- **Performance Optimized**: Lightweight and fast with minimal overhead
- **SQL-like Syntax**: Intuitive query builder that feels natural to SQL developers
- **Migration Management**: Built-in migration system with drizzle-kit
- **No ORM Bloat**: Clean, focused API without unnecessary complexity

**No Alternative ORMs:**
- We do not use Prisma, TypeORM, Sequelize, or any other ORM libraries
- All database access must go through Drizzle ORM
- This ensures consistency, maintainability, and reduced bundle size
- Single source of truth for database operations across the entire application

**Database Operations:**
- All CRUD operations use Drizzle's query syntax
- Transactions are handled through Drizzle's transaction API
- Schema changes are managed exclusively through drizzle-kit migrations
- Type safety is maintained from database to frontend