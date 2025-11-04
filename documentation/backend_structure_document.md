# Backend Structure Document for codeguide-manufacturing-app

## 1. Backend Architecture

This backend follows a clear, modular design that separates responsibilities into layers and makes it easy to add new manufacturing modules.

**Key Components and Design Patterns**
- **Next.js API Routes**: Each REST endpoint lives under `/app/api/`, keeping routing and handlers together.
- **Layered Architecture**:
  - **API Layer**: Receives requests, handles authentication/authorization, and delegates to business logic.
  - **Service Layer**: Implements manufacturing workflows (e.g., issuing materials, creating work orders).
  - **Data Access Layer**: Uses Drizzle ORM to query PostgreSQL in a type-safe way.
- **Full-Stack TypeScript**: Ensures type consistency from database to frontend, reducing runtime errors.
- **Docker Containerization**: The backend and database run in containers for consistency across environments.

**Scalability, Maintainability, and Performance**
- **Stateless Services**: API routes are stateless, letting you spin up multiple instances behind a load balancer.
- **Modular Code Structure**: Concerns are split across folders (`/app/api/`, `/lib/`, `/db/schema/`), making it easy to navigate and extend.
- **Type-Safe ORM**: Drizzle ORM’s compile-time checks prevent query mistakes and help optimize database interactions.
- **Containerization**: Docker images ensure that “it works on my machine” is a guarantee in development and production.

---

## 2. Database Management

**Database Technology**
- PostgreSQL (relational SQL database)
- Drizzle ORM (type-safe query builder)
- drizzle-kit for schema migrations

**Data Storage and Access**
- **Structured Tables**: Business entities like `Bom`, `Inventory`, `WorkOrder`, and `MaterialIssue` are each mapped to tables.
- **Migrations**: Version-controlled migrations automatically apply schema changes in development, staging, and production.
- **Transactions**: Critical workflows (e.g., checking stock and issuing materials) run in PostgreSQL transactions to guarantee data consistency.
- **Indexing**: Common query fields (e.g., `bom_code`, `work_order_id`) are indexed to accelerate lookups.
- **Backups**: Regular automated dumps or snapshots ensure data recovery options.

---

## 3. Database Schema

### Human-Readable Schema Overview

Users and Authentication:
- **User**: id, email, password_hash, role, created_at
- **Role**: name (e.g., Planner, Warehouse, Supervisor)

Manufacturing Entities:
- **Bom**: id, bom_code, description, created_at
- **BomItem**: id, bom_id, part_number, quantity_required
- **Inventory**: id, part_number, quantity_on_hand, warehouse_location
- **WorkOrder**: id, work_order_id, bom_id, quantity_to_produce, status, created_at, scheduled_date
- **MaterialIssue**: id, work_order_id, bom_item_id, quantity_issued, issued_by, issued_at

### PostgreSQL Schema (SQL)
```sql
-- Users and Roles
CREATE TABLE "Role" (
  name TEXT PRIMARY KEY
);

CREATE TABLE "User" (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email TEXT UNIQUE NOT NULL,
  password_hash TEXT NOT NULL,
  role TEXT NOT NULL REFERENCES "Role"(name),
  created_at TIMESTAMPTZ DEFAULT now()
);

-- Bill of Materials
CREATE TABLE "Bom" (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  bom_code TEXT UNIQUE NOT NULL,
  description TEXT,
  created_at TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE "BomItem" (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  bom_id UUID NOT NULL REFERENCES "Bom"(id) ON DELETE CASCADE,
  part_number TEXT NOT NULL,
  quantity_required INTEGER NOT NULL
);

-- Inventory
CREATE TABLE "Inventory" (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  part_number TEXT UNIQUE NOT NULL,
  quantity_on_hand INTEGER NOT NULL,
  warehouse_location TEXT,
  updated_at TIMESTAMPTZ DEFAULT now()
);

-- Work Orders
CREATE TABLE "WorkOrder" (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  work_order_id TEXT UNIQUE NOT NULL,
  bom_id UUID NOT NULL REFERENCES "Bom"(id),
  quantity_to_produce INTEGER NOT NULL,
  status TEXT NOT NULL,
  scheduled_date DATE,
  created_at TIMESTAMPTZ DEFAULT now()
);

-- Material Issues
CREATE TABLE "MaterialIssue" (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  work_order_id UUID NOT NULL REFERENCES "WorkOrder"(id) ON DELETE CASCADE,
  bom_item_id UUID NOT NULL REFERENCES "BomItem"(id),
  quantity_issued INTEGER NOT NULL,
  issued_by UUID NOT NULL REFERENCES "User"(id),
  issued_at TIMESTAMPTZ DEFAULT now()
);
```

---

## 4. API Design and Endpoints

**RESTful Approach**
- Each resource (BOM, Inventory, WorkOrder) has a dedicated route under `/api`.
- Standard HTTP methods are used: `GET` for reads, `POST` for creation, `PUT/PATCH` for updates, and `DELETE` for removals.

**Key Endpoints**
- **Authentication** (`/api/auth`)
  - `POST /signup` – Create a new user account.
  - `POST /login` – Authenticate and start a session.
  - `POST /logout` – End the session.

- **Bill of Materials** (`/api/bom`)
  - `GET /api/bom` – List all BOMs.
  - `POST /api/bom` – Create a new BOM.
  - `GET /api/bom/{bom_code}` – Get details of a specific BOM.
  - `PUT /api/bom/{bom_code}` – Update a BOM’s metadata or items.
  - `DELETE /api/bom/{bom_code}` – Remove a BOM.

- **Inventory** (`/api/inventory`)
  - `GET /api/inventory` – List all inventory records.
  - `POST /api/inventory` – Add or restock an item.
  - `PUT /api/inventory/{part_number}` – Update stock levels or location.
  - `DELETE /api/inventory/{part_number}` – Remove an item from inventory.

- **Production / Work Orders** (`/api/production`)
  - `GET /api/production/work-orders` – List work orders.
  - `POST /api/production/work-orders` – Create a new order.
  - `PUT /api/production/work-orders/{work_order_id}` – Update status or schedule.
  - `POST /api/production/work-orders/{work_order_id}/issue-material` – Issue materials for an order.

Each route uses Drizzle ORM for type-safe queries and wraps critical updates in transactions.

---

## 5. Hosting Solutions

**Development**
- **Docker Compose**: Local setup spins up API, PostgreSQL, and (optionally) Redis with a single `docker-compose up`.

**Production**
- **Cloud Provider**: AWS is recommended, using:
  - **ECS/Fargate** (serverless containers) or **EKS** (Kubernetes).
  - **RDS (PostgreSQL)** for a managed relational database.
  - **Elastic Load Balancer** to distribute traffic.
  - **ECR** for storing container images.

**Benefits**
- **Reliability**: Managed services with built-in failover.
- **Scalability**: Services auto-scale based on load.
- **Cost-Effectiveness**: Pay-as-you-go billing, and you only provision what you need.

---

## 6. Infrastructure Components

- **Load Balancer / Reverse Proxy**: AWS ALB or NGINX directs traffic to multiple container instances.
- **CDN**: Cloudflare or AWS CloudFront to serve frontend assets and offload static content.
- **Caching**: Redis for session storage or frequently-read reference data.
- **Message Broker (Optional)**: RabbitMQ or AWS SQS for long-running manufacturing tasks.
- **Container Registry**: AWS ECR (or Docker Hub) to host container images.
- **Configuration Management**: Environment variables managed securely via AWS Secrets Manager or Parameter Store.

These components work together so that requests are balanced, static files are served quickly, and data updates remain fast and reliable.

---

## 7. Security Measures

- **HTTPS/TLS**: Encrypt all in-transit data.
- **Authentication**: Better Auth library manages secure sign-in and sessions.
- **Authorization (RBAC)**: Users have assigned roles (Planner, Warehouse, Supervisor) that gate access to endpoints.
- **Input Validation**: Zod schemas validate request bodies before processing.
- **Data Encryption at Rest**: Enable encryption for the RDS database and any backups.
- **Security Headers**: Use Helmet or a similar middleware to set HTTP headers like Content Security Policy and HSTS.
- **CORS Configuration**: Restrict front-end origins to the known application domains.

---

## 8. Monitoring and Maintenance

- **Logging**: Pino for structured logs; ship logs to CloudWatch Logs or Elasticsearch.
- **Error Tracking**: Sentry for capturing and alerting on unhandled exceptions.
- **Metrics & Dashboards**: Prometheus for metrics collection and Grafana for visualization.
- **Health Checks**: Docker and AWS health checks for container readiness.
- **Automated Backups**: Scheduled RDS snapshots and SQL dumps for disaster recovery.
- **Dependency Updates**: Dependabot or Renovate to keep libraries and Docker images up to date.
- **Database Migrations**: `drizzle-kit` ensures schema changes are tracked and applied safely across environments.

---

## 9. Conclusion and Overall Backend Summary

This backend structure provides a solid, scalable foundation for a manufacturing application. It combines a modern full-stack TypeScript setup with a robust relational database, secure authentication, and container-based deployment. Key takeaways:

- **Modular and Type-Safe**: Layers separated by function and enforced by TypeScript and Drizzle ORM.
- **Production-Ready**: HTTPS, RBAC, data encryption, and automated backups cover essential security and reliability needs.
- **Scalable Infrastructure**: Docker, AWS services, and managed databases ensure you can grow with demand.
- **Maintainable Workflows**: Clear migration paths, logging, and monitoring keep the system healthy.

With this structure in place, adding manufacturing modules—such as detailed work-order workflows, real-time inventory adjustments, and custom dashboards—is straightforward and safe. The stack’s consistency from development to production minimizes surprises and accelerates delivery of new features.