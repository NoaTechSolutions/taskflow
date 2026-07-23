# TaskFlow — User Stories & Acceptance Criteria

- **Version:** 1.0
- **Date:** 2026-07-22
- **Author:** Israel Esparza
- **Status:** Draft (pending review)
- **Related:** REQUIREMENTS.md v1.0

## How to read this document

- Stories are grouped into **Epics** (→ GitHub milestones). Story IDs (`US-NN`) will become GitHub issues.
- Each story links back to its functional requirement(s) for traceability.
- **Estimates** use story points on a Fibonacci scale (1, 2, 3, 5, 8). 1 point ≈ trivial; 8 ≈ needs breaking down.
- Acceptance criteria use **Given / When / Then** — they will later drive integration tests almost word-for-word.

---

## Epic 1 — Registration & Login (AUTH core)

### US-01 · Register with email and password
**As a** visitor, **I want** to create an account with my email and a password, **so that** I can start using TaskFlow.
*(FR-AUTH-01, FR-AUTH-09 · Must · 3 pts)*

- **Given** a valid email and a password of at least 8 characters, **when** I submit the registration form, **then** an account is created with status `UNVERIFIED` and I receive HTTP 201 with my user ID.
- **Given** an email that is already registered, **when** I submit the form, **then** I receive a generic success response (no account enumeration) and no duplicate account is created.
- **Given** a password shorter than 8 characters, **when** I submit, **then** I receive HTTP 400 with a `problem+json` body listing the validation error.
- **Then** the password is stored only as a BCrypt hash — verified by inspecting the persisted entity in an integration test.

### US-02 · Verify my email address
**As a** newly registered user, **I want** to confirm my email through a link, **so that** my account becomes active.
*(FR-AUTH-02 · Must · 3 pts)*

- **Given** a fresh registration, **when** the account is created, **then** a single-use verification token (24h TTL) is generated and an email is dispatched.
- **Given** a valid, unexpired token, **when** I hit the verification endpoint, **then** my account status changes to `ACTIVE` and the token is invalidated.
- **Given** an expired or already-used token, **when** I hit the endpoint, **then** I receive HTTP 410 with a clear error code and can request a new email.

### US-03 · Log in with email and password
**As a** registered user, **I want** to log in, **so that** I can access my workspaces.
*(FR-AUTH-03 · Must · 3 pts)*

- **Given** valid credentials on an `ACTIVE` account, **when** I log in, **then** I receive an access token (JWT, 15 min) and a refresh token (7 days), and the response never includes the password hash.
- **Given** invalid credentials, **when** I log in, **then** I receive HTTP 401 with a generic message (no hint whether email exists).
- **Given** an `UNVERIFIED` account, **when** I log in, **then** I receive HTTP 403 with error code `EMAIL_NOT_VERIFIED`.

### US-04 · Stay logged in (token refresh)
**As a** logged-in user, **I want** my session to renew transparently, **so that** I'm not logged out every 15 minutes.
*(FR-AUTH-05 · Must · 5 pts)*

- **Given** a valid refresh token, **when** I call the refresh endpoint, **then** I receive a new access token and a NEW refresh token, and the previous refresh token is marked as used.
- **Given** an already-used refresh token (rotation reuse), **when** I call refresh, **then** the entire token family is revoked and I receive HTTP 401 — forcing full re-authentication.
- **Given** an expired refresh token, **when** I call refresh, **then** I receive HTTP 401.

### US-05 · Log in with Google or GitHub
**As a** visitor, **I want** to sign in with my Google or GitHub account, **so that** I can skip creating another password.
*(FR-AUTH-04 · Must · 5 pts)*

- **Given** a successful OAuth2 flow with a provider account whose email is not yet registered, **when** the callback completes, **then** an `ACTIVE` account is auto-provisioned and I receive the standard token pair.
- **Given** a provider email matching an existing local account, **when** the callback completes, **then** the provider identity is linked to that account (no duplicate user).
- **Given** the user denies consent at the provider, **when** the callback returns, **then** I land on the login page with a non-technical error message.

### US-06 · Reset my forgotten password
**As a** user who forgot their password, **I want** to set a new one via email, **so that** I can regain access.
*(FR-AUTH-07 · Must · 3 pts)*

- **Given** a registered email, **when** I request a reset, **then** a single-use token (1h TTL) is emailed; the API response is identical whether or not the email exists.
- **Given** a valid token and a valid new password, **when** I submit, **then** the password is updated, all refresh tokens for the account are revoked, and the token is invalidated.

### US-07 · Log out
**As a** logged-in user, **I want** to log out, **so that** my session cannot be reused.
*(FR-AUTH-06 · Must · 2 pts)*

- **Given** a valid refresh token, **when** I call logout, **then** the token (and its family) is revoked server-side and subsequent refresh attempts return HTTP 401.

### US-08 · Edit my profile
**As a** user, **I want** to update my display name and avatar, **so that** teammates recognize me.
*(FR-AUTH-08 · Should · 2 pts)*

- **Given** an authenticated request, **when** I PATCH my profile with a valid display name, **then** the change persists and `updated_at`/`updated_by` are set.

---

## Epic 2 — Workspaces & Teams

### US-09 · Create a workspace
**As a** user, **I want** to create a workspace, **so that** my team has a shared space.
*(FR-WS-01 · Must · 3 pts)*

- **Given** an authenticated user, **when** I create a workspace with a valid name, **then** it is created with a URL-safe unique slug and I am its `OWNER`.
- **Given** a duplicate slug, **when** I create, **then** the system auto-suffixes the slug (e.g., `acme-2`).

### US-10 · Invite teammates by email
**As a** workspace owner/admin, **I want** to invite people by email, **so that** they can join our workspace.
*(FR-WS-02 · Must · 5 pts)*

- **Given** I am `OWNER` or `ADMIN`, **when** I invite an email not in the workspace, **then** an invitation (7-day TTL) is created and an email is sent.
- **Given** the invitee already has an account, **when** they accept, **then** they become a `MEMBER` immediately.
- **Given** the invitee has no account, **when** they register through the invitation link, **then** after verification they join the workspace automatically.
- **Given** I am a plain `MEMBER`, **when** I try to invite, **then** I receive HTTP 403.

### US-11 · Manage member roles
**As a** workspace owner, **I want** to promote/demote members, **so that** I can delegate administration.
*(FR-WS-03 · Must · 3 pts)*

- **Given** I am `OWNER`, **when** I change a member's role to `ADMIN` or `MEMBER`, **then** the change applies immediately.
- **Given** any actor, **when** they attempt to demote or remove the `OWNER`, **then** the request fails with HTTP 409 — ownership must be transferred first.
- **Given** I am `OWNER`, **when** I transfer ownership to another member, **then** they become `OWNER` and I become `ADMIN`.

### US-12 · See my workspaces / leave a workspace
**As a** user, **I want** to list my workspaces and leave those I no longer participate in.
*(FR-WS-05, FR-WS-06 · Must+Should · 2 pts)*

- **Given** I belong to N workspaces, **when** I list them, **then** I receive all N with my role in each.
- **Given** I am a `MEMBER`/`ADMIN`, **when** I leave, **then** my membership is removed and my task assignments in that workspace are cleared (tasks become unassigned).
- **Given** I am the `OWNER`, **when** I try to leave, **then** I receive HTTP 409 with error code `OWNERSHIP_TRANSFER_REQUIRED`.

### US-13 · Archive a workspace
**As a** workspace owner, **I want** to archive a finished workspace, **so that** it stays readable but frozen.
*(FR-WS-04 · Should · 2 pts)*

- **Given** I am `OWNER`, **when** I archive the workspace, **then** all write operations inside it return HTTP 409 `WORKSPACE_ARCHIVED`, while reads still work.

---

## Epic 3 — Boards, Lists & Tasks

### US-14 · Create and configure a board
**As a** workspace member, **I want** to create a board, **so that** we can organize a project.
*(FR-BOARD-01 · Must · 2 pts)*

- **Given** I am a member of the workspace, **when** I create a board with a name, **then** it is created and visible to all workspace members.

### US-15 · Manage lists (columns)
**As a** member, **I want** to add, rename, and reorder lists, **so that** the board reflects our workflow.
*(FR-BOARD-02 · Must · 3 pts)*

- **Given** a board, **when** I add a list, **then** it appears at the rightmost position.
- **Given** three lists A, B, C, **when** I move C between A and B, **then** the persisted order is A, C, B and it survives reload.

### US-16 · Create a task
**As a** member, **I want** to add a task with just a title, **so that** capturing work is instant.
*(FR-BOARD-03 · Must · 2 pts)*

- **Given** a list, **when** I create a task with a title, **then** it appears at the bottom of that list with all optional fields empty.

### US-17 · Edit task details
**As a** member, **I want** to set description, assignee, due date, and labels, **so that** the task carries its full context.
*(FR-BOARD-04 · Must · 5 pts)*

- **Given** a task, **when** I set a Markdown description, **then** it is stored raw and returned sanitized for rendering.
- **Given** a task, **when** I assign a user who is NOT a workspace member, **then** I receive HTTP 422.
- **Given** a workspace label, **when** I attach it to a task, **then** it appears on the task; deleting the label removes it from all tasks.

### US-18 · Move tasks (drag & drop persistence)
**As a** member, **I want** to drag tasks within and across lists, **so that** the board reflects real progress.
*(FR-BOARD-05 · Must · 5 pts)*

- **Given** tasks T1, T2 in list A, **when** I drop T3 between them, **then** T3's position is strictly between T1 and T2 (fractional ordering) with a single row update.
- **Given** a task in list A, **when** I drop it into list B at position k, **then** its `list_id` and position update atomically.
- **Given** two concurrent moves of the same task, **when** both commit, **then** the last write wins without corrupting list ordering (verified by a concurrency integration test).

### US-19 · Comment on tasks
**As a** member, **I want** to discuss a task in comments, **so that** context stays with the work.
*(FR-BOARD-06 · Must · 3 pts)*

- **Given** a task, **when** I post a comment, **then** it appears with my identity and timestamp.
- **Given** my own comment, **when** I edit or delete it, **then** the change applies; editing marks it `(edited)`.
- **Given** someone else's comment, **when** I (plain member) try to delete it, **then** HTTP 403; an `ADMIN` succeeds.

### US-20 · Archive and restore tasks
**As a** member, **I want** to archive done/obsolete tasks, **so that** boards stay clean without losing history.
*(FR-BOARD-07 · Should · 2 pts)*

- **Given** a task, **when** I archive it, **then** it disappears from the board view but appears in the board's archive listing, from which it can be restored to its original list.

### US-21 · Load a board fast
**As a** member, **I want** the whole board (lists + tasks) in one request, **so that** it opens instantly.
*(FR-BOARD-08, NFR-09, NFR-10 · Must · 3 pts)*

- **Given** a board with 20 lists × 50 tasks, **when** I GET the board view, **then** the response completes in < 500 ms (p95) using a bounded number of SQL queries (asserted via Hibernate statistics — no N+1).

---

## Epic 4 — Platform & API Quality

### US-22 · Consistent API errors
**As an** API consumer, **I want** every error in RFC 7807 format with stable codes, **so that** I can handle failures programmatically.
*(FR-PLAT-02 · Must · 3 pts)*

- **Given** any failing request (validation, auth, not-found, conflict), **when** the response returns, **then** the body is `application/problem+json` containing `type`, `title`, `status`, `detail`, and a project-specific `code`.

### US-23 · Explorable API documentation
**As a** developer or recruiter, **I want** interactive API docs, **so that** I can try the API without reading code.
*(FR-PLAT-01 · Must · 2 pts)*

- **Given** the app running in a non-prod profile, **when** I open `/swagger-ui`, **then** every endpoint appears with request/response schemas, auth requirements, and example payloads, and I can execute calls with a bearer token.

### US-24 · Auditability
**As a** platform operator, **I want** every entity to record who created/changed it and when.
*(FR-PLAT-03 · Must · 2 pts)*

- **Given** any create or update through the API, **when** the entity persists, **then** `created_at/by` and `updated_at/by` are populated automatically from the security context (JPA auditing).

### US-25 · One-command local environment
**As a** developer, **I want** to run the entire stack with a single command, **so that** onboarding takes minutes.
*(NFR-19 · Must · 3 pts)*

- **Given** a fresh clone with Docker installed, **when** I run `docker compose up`, **then** API + PostgreSQL + mail-catcher start, migrations apply, and Swagger UI is reachable — with no manual steps beyond copying `.env.example` to `.env`.

---

## Story Map Summary

| Epic | Stories | Points | Suggested sprints |
|------|---------|--------|-------------------|
| 1. Registration & Login | US-01…US-08 | 26 | Sprints 2–3 |
| 2. Workspaces & Teams | US-09…US-13 | 15 | Sprint 4 |
| 3. Boards, Lists & Tasks | US-14…US-21 | 25 | Sprints 5–6 |
| 4. Platform & API Quality | US-22…US-25 | 10 | Transversal (starts Sprint 1) |
| **Total MVP** | **25 stories** | **76 pts** | **~6–7 sprints** |

> Sprint 0 (planning) and Sprint 1 (project scaffolding, CI, Docker, error handling, OpenAPI) precede Epic 1. Platform stories are built early because every later story depends on them.

## Changelog

| Version | Date | Change |
|---------|------|--------|
| 1.0 | 2026-07-22 | Initial draft |
