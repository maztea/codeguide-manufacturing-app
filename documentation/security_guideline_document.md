# Security Guidelines for `codeguide-manufacturing-app`

This document provides a comprehensive set of security best practices tailored to the `codeguide-manufacturing-app` starter kit. By following these guidelines, you will ensure a robust, resilient, and secure foundation for your Manufacturing Execution System (MES) or ERP application.

---

## 1. Authentication & Authorization

**1.1 Robust Authentication**
- Continue using **Better Auth** for secure sign-up/sign-in flows.
- Enforce strong password policies:
  - Minimum length: 12 characters.  
  - Complexity: include uppercase, lowercase, digits, and symbols.  
  - Use bcrypt or Argon2 with per-password salts.  
- Implement Multi-Factor Authentication (MFA) for privileged roles (e.g., supervisors).

**1.2 Session Management**
- Ensure session tokens are cryptographically secure and unpredictable.  
- Store session cookies with `HttpOnly`, `Secure`, and `SameSite=Strict` attributes.  
- Configure idle and absolute timeouts (e.g., 30-minute idle, 12-hour absolute).
- On logout or password change, immediately revoke all active sessions.

**1.3 Role-Based Access Control (RBAC)**
- Extend the user schema to include explicit roles (e.g., WarehouseStaff, Planner, Supervisor).  
- Implement server-side authorization middleware for every Next.js API route:
  - Validate session and user role before executing business logic.  
  - Deny or return `403 Forbidden` for unauthorized access attempts.

**1.4 JWT Considerations (if used)**
- Always specify and verify a secure signature algorithm (e.g., HS256, RS256).  
- Reject tokens with `alg: none`.  
- Enforce token expiration (`exp`) and optionally `nbf` (not before) claims.  
- Rotate signing keys periodically and support key revocation.

---

## 2. Input Validation & Output Encoding

**2.1 API Request Validation**
- Use a schema validation library (e.g., Zod) for all API route inputs:
  - Validate required fields, types, formats (e.g., dates, numeric quantities).  
  - Enforce business rules: `qty > 0`, unique `bom_code`, valid status transitions.

**2.2 Defend Against Injection**
- Leverage **Drizzle ORM** with parameterized queries to prevent SQL injection.  
- Never build raw SQL strings by concatenation.  
- Sanitize any dynamic database identifiers (e.g., table or column names) strictly via a whitelist.

**2.3 Prevent Cross-Site Scripting (XSS)**
- In React components, avoid dangerously setting HTML.  
- Use Next.js built-in escaping for template variables.  
- On any server-rendered pages, apply context-aware encoding for user-supplied data.

**2.4 Protect Against CSRF**
- For state-changing API routes (`POST`, `PUT`, `DELETE`), implement anti-CSRF measures:
  - Use a synchronizer token pattern or double-submit cookie approach.  
  - Verify CSRF token on each request.

**2.5 File Upload Security**
- If your manufacturing app allows file uploads (e.g., import BOM CSV):
  - Validate MIME type and extension against an allow-list.  
  - Scan files for malware before processing.  
  - Store uploads outside the webroot with randomized filenames.

---

## 3. Data Protection & Privacy

**3.1 Encryption in Transit & At Rest**
- Enforce HTTPS (TLS 1.2+) for all web and API traffic.  
- Use HSTS (`Strict-Transport-Security`) header with an appropriate max-age.  
- Encrypt sensitive database columns (e.g., PII) using AES-256 at rest if required by regulation.

**3.2 Secret Management**
- Remove hardcoded secrets from code.  
- Utilize a secrets management solution (e.g., AWS Secrets Manager, HashiCorp Vault) for database credentials, API keys.
- Load secrets at runtime and restrict access to only the application process.

**3.3 Data Minimization & Masking**
- Return only necessary fields in API responses (avoid leaking internal IDs or audit fields).  
- Mask PII in logs (e.g., only log last four digits of an employee number).

**3.4 Secure Database Connections**
- Use TLS/SSL when connecting to PostgreSQL.  
- Configure the least-privileged database user for the application (e.g., no `SUPERUSER` rights).

---

## 4. API & Service Security

**4.1 Rate Limiting & Throttling**
- Implement per-IP and per-user rate limits on authentication and critical endpoints (e.g., `/api/auth/login`).

**4.2 CORS Configuration**
- Restrict `Access-Control-Allow-Origin` to known frontend domains (e.g., `https://app.example.com`).  
- Limit allowed methods and headers explicitly.

**4.3 API Versioning**
- Namespace your API routes (e.g., `/api/v1/bom`, `/api/v2/inventory`) to safely roll out breaking changes.

**4.4 Proper HTTP Verbs & Status Codes**
- Use `GET` for reads, `POST` for creation, `PUT/PATCH` for updates, and `DELETE` for removals.  
- Return `4xx` for client errors, `5xx` for server errors, and include minimal error details.

---

## 5. Web Application Security Hygiene

**5.1 Security Headers**
- Content-Security-Policy (CSP): restrict sources for scripts, styles, images, frames.  
- X-Frame-Options: `DENY` or `SAMEORIGIN` to prevent clickjacking.  
- X-Content-Type-Options: `nosniff`.  
- Referrer-Policy: `strict-origin-when-cross-origin`.

**5.2 Secure Cookies**
- Set `HttpOnly`, `Secure`, and `SameSite=Strict` on all session or auth cookies.

**5.3 Avoid Client-Side Storage of Secrets**
- Do not store tokens or sensitive data in `localStorage` or `sessionStorage`.

**5.4 Subresource Integrity (SRI)**
- When loading third-party scripts or styles, include integrity hashes.

---

## 6. Infrastructure & Configuration Management

**6.1 Container Security**
- Base your Docker images on minimal, up-to-date distributions (e.g., `node:18-alpine`).  
- Scan images for known vulnerabilities before deployment (use tools like Trivy).

**6.2 Environment Variable Validation**
- At startup, parse and validate critical env vars (e.g., `DATABASE_URL`, `JWT_SECRET`) with Zod.  
- Fail fast if required variables are missing or malformed.

**6.3 Secure Defaults & Hardening**
- Disable Next.js debug mode (`next dev` only).  
- Turn off verbose error stacks in production.  
- Close unnecessary network ports in containers and servers.

**6.4 Patch Management**
- Regularly update application dependencies and OS packages.  
- Subscribe to security mailing lists (e.g., NodeJS Security, PostgreSQL security updates).

---

## 7. Dependency Management & Supply Chain Security

- Use lockfiles (`package-lock.json`) for deterministic installs.  
- Vet new dependencies for active maintenance and community trust.  
- Integrate automated SCA tools (e.g., Dependabot, GitHub Advanced Security) to alert on vulnerable packages.
- Minimize third-party footprint; remove unused libraries.

---

## 8. Logging, Monitoring & Incident Response

- Implement structured logging (e.g., Pino) for API routes, including request IDs and user context (avoid PII).  
- Configure log retention and secure log storage.  
- Integrate application performance monitoring (APM) and alerting for unusual activities (e.g., sudden spike in failed logins).
- Establish an incident response plan outlining communication channels, severity definitions, and rollback procedures.

---

## 9. Testing & Verification

- **Unit Tests:** Validate business logic (e.g., material issuance, quantity calculations).  
- **Integration Tests:** Test end-to-end API + database transactions.  
- **E2E Tests:** Simulate critical workflows (login → create work order → issue material) using Playwright or Cypress.  
- **Security Tests:** Include automated scans (SAST, DAST) and periodic penetration testing.

---

By embedding these security practices throughout your development lifecycle, the `codeguide-manufacturing-app` will maintain a strong security posture, safeguarding both your application and your manufacturing data.
