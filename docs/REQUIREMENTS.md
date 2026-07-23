# TaskFlow — Requirements Specification

- **Version:** 1.0
- **Date:** 2026-07-22
- **Author:** Israel Esparza
- **Status:** Draft (pending review)
- **Related:** VISION.md v1.0

## 1. Functional Requirements

Grouped by module. ID format: `FR-<MODULE>-<NN>`. Priority: **M**ust / **S**hould / **C**ould (MoSCoW).

### 1.1 Authentication & Users (AUTH)

| ID | Requirement | Priority |
|----|-------------|----------|
| FR-AUTH-01 | A visitor can register with email + password. The account remains `UNVERIFIED` until email confirmation. | M |
| FR-AUTH-02 | The system sends a verification email with a single-use, time-limited token (24h). | M |
| FR-AUTH-03 | A registered user can log in with email + password and receives an access token (JWT, 15 min) and a refresh token (7 days, rotating). | M |
| FR-AUTH-04 | A user can log in via OAuth2 with Google or GitHub. First-time social login auto-provisions an account. | M |
| FR-AUTH-05 | A user can refresh the access token using a valid refresh token. Reuse of a rotated refresh token revokes the whole token family. | M |
| FR-AUTH-06 | A user can log out (refresh token revoked server-side). | M |
| FR-AUTH-07 | A user can request a password reset via email (single-use, time-limited token, 1h). | M |
| FR-AUTH-08 | A user can view and edit their profile (display name, avatar URL). | S |
| FR-AUTH-09 | Passwords are stored hashed with BCrypt (cost ≥ 10). Plaintext passwords never logged. | M |

### 1.2 Workspaces & Membership (WS)

| ID | Requirement | Priority |
|----|-------------|----------|
| FR-WS-01 | An authenticated user can create a workspace (name, slug). Creator becomes `OWNER`. | M |
| FR-WS-02 | A workspace `OWNER`/`ADMIN` can invite users by email. Non-registered invitees receive an invitation valid 7 days. | M |
| FR-WS-03 | Roles per workspace: `OWNER` (full control, transferable, exactly one), `ADMIN` (manage boards & members), `MEMBER` (work on boards). | M |
| FR-WS-04 | An `OWNER` can archive a workspace; archived workspaces are read-only. | S |
| FR-WS-05 | A member can leave a workspace (except the `OWNER`, who must transfer ownership first). | S |
| FR-WS-06 | A user sees the list of workspaces they belong to. | M |

### 1.3 Boards, Lists & Tasks (BOARD)

| ID | Requirement | Priority |
|----|-------------|----------|
| FR-BOARD-01 | A workspace member can create boards (name, description, background color). | M |
| FR-BOARD-02 | A board contains ordered lists (columns). Lists can be created, renamed, reordered, and archived. | M |
| FR-BOARD-03 | A list contains ordered tasks. Tasks can be created with a title; all other fields optional. | M |
| FR-BOARD-04 | A task supports: description (Markdown), one assignee (workspace member), due date, labels (workspace-scoped, colored), position. | M |
| FR-BOARD-05 | Tasks can be moved within a list and across lists (drag & drop); position persists using a fractional ordering strategy. | M |
| FR-BOARD-06 | Members can comment on tasks. Comments are editable/deletable by their author; admins can delete any comment. | M |
| FR-BOARD-07 | Tasks can be archived (soft delete) and restored. | S |
| FR-BOARD-08 | Board view returns lists + tasks in a single optimized endpoint (avoid N+1). | M |

### 1.4 Platform (PLAT)

| ID | Requirement | Priority |
|----|-------------|----------|
| FR-PLAT-01 | All API endpoints are documented via OpenAPI 3; Swagger UI available in non-prod environments. | M |
| FR-PLAT-02 | Errors follow RFC 7807 (`application/problem+json`) with stable, documented error codes. | M |
| FR-PLAT-03 | All entities carry audit fields: `created_at`, `created_by`, `updated_at`, `updated_by`. | M |
| FR-PLAT-04 | Paginated collections use consistent parameters (`page`, `size`, `sort`) and response envelope. | M |
| FR-PLAT-05 | Health and readiness endpoints exposed (`/actuator/health`). | M |

## 2. Non-Functional Requirements

ID format: `NFR-<NN>`.

### 2.1 Security

| ID | Requirement |
|----|-------------|
| NFR-01 | All traffic over HTTPS in production; HSTS enabled. |
| NFR-02 | Stateless authentication: no server-side sessions; JWT signed with RS256 (asymmetric) so future services can verify tokens without sharing secrets. |
| NFR-03 | Authorization enforced at the service layer (not only controllers): every workspace-scoped operation validates membership + role. |
| NFR-04 | Rate limiting on authentication endpoints (login, register, password reset) to mitigate brute force. |
| NFR-05 | Input validation on all request DTOs (Bean Validation); output encoding to prevent XSS in stored Markdown. |
| NFR-06 | Secrets (DB credentials, JWT keys, OAuth client secrets) never in the repository; injected via environment variables. |
| NFR-07 | OWASP Top 10 reviewed before each release; dependency vulnerability scanning in CI. |

### 2.2 Performance

| ID | Requirement |
|----|-------------|
| NFR-08 | p95 latency < 300 ms for read endpoints, < 500 ms for writes (single-region deployment, MVP load). |
| NFR-09 | Board view endpoint (largest payload) responds < 500 ms p95 with 20 lists × 50 tasks. |
| NFR-10 | No N+1 query patterns; verified with Hibernate statistics in integration tests. |

### 2.3 Reliability & Operations

| ID | Requirement |
|----|-------------|
| NFR-11 | Target availability 99% (portfolio-grade; documented honestly). |
| NFR-12 | Database migrations are forward-only, repeatable in every environment, and run automatically on deploy. |
| NFR-13 | Structured JSON logging with correlation IDs per request. |
| NFR-14 | Metrics exposed via Micrometer/Prometheus format. |

### 2.4 Maintainability & Quality

| ID | Requirement |
|----|-------------|
| NFR-15 | Test coverage ≥ 80% on the service layer; critical flows (auth, task movement, permissions) covered by integration tests against real PostgreSQL (Testcontainers). |
| NFR-16 | Static analysis (SonarCloud) gate: zero blocker/critical issues on main. |
| NFR-17 | Conventional Commits enforced; every change to `main` goes through a PR with green CI. |
| NFR-18 | Code and documentation in English. |

### 2.5 Portability

| ID | Requirement |
|----|-------------|
| NFR-19 | Full stack runs locally with `docker compose up` — no manual setup beyond `.env`. |
| NFR-20 | Application is 12-factor compliant: config via environment, stateless processes, disposable instances. |

## 3. Explicit Exclusions (MVP)

Real-time sync (WebSockets), file attachments, email notifications beyond auth flows, full-text search, caching layer, message broker, multi-language UI, mobile clients. See VISION.md §4.

## Changelog

| Version | Date | Change |
|---------|------|--------|
| 1.0 | 2026-07-22 | Initial draft |
