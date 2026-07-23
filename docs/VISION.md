# TaskFlow — Product Vision & MVP Scope

- **Version:** 1.0
- **Date:** 2026-07-22
- **Author:** Israel Esparza
- **Status:** Draft (pending review)

## 1. Vision Statement

**TaskFlow** is a collaborative project management platform — Kanban boards, tasks, and teams — built with a production-grade engineering mindset. It allows teams to organize work in shared workspaces, visualize progress on boards, and collaborate in real time.

> **One-liner:** "A Trello-style project management platform, engineered the way a real product team would build it."

## 2. Goals

### Product goals
- Let a user sign up, create a workspace, invite teammates, and manage tasks on Kanban boards within minutes.
- Provide real-time collaboration so board changes appear instantly for all members.

### Engineering goals (the real purpose of this project)
- Demonstrate professional backend engineering: clean architecture, security done right (JWT + OAuth2), automated testing, CI/CD, observability, and documented decision-making (ADRs).
- Serve as the flagship portfolio project and a living case study: from modular monolith → microservices.
- **Dogfooding:** once the MVP is live, TaskFlow's own development will be managed inside TaskFlow.

## 3. Target Users

| Persona | Description | Primary need |
|---------|-------------|--------------|
| Team lead ("Ana") | Runs a small dev/design team | Create boards, assign tasks, track progress |
| Team member ("Luis") | Individual contributor | See assigned work, update status, comment |
| Recruiter / Reviewer | Evaluates the developer | Explore a polished live demo + clean codebase |

## 4. MVP Scope (v1.0)

The MVP is deliberately small. Scope discipline is a feature.

### ✅ In scope

**Authentication & Users**
- Email/password registration with email verification
- Login with JWT (access + refresh tokens)
- OAuth2 social login: Google and GitHub
- Password reset flow
- User profile (name, avatar)

**Workspaces & Membership**
- Create/edit/archive workspaces
- Invite members by email
- Roles per workspace: `OWNER`, `ADMIN`, `MEMBER`

**Boards, Lists & Tasks**
- Boards inside a workspace
- Ordered lists (columns) inside a board
- Tasks with: title, description (Markdown), assignee, due date, labels, position
- Drag & drop reordering (position persistence)
- Task comments

**Platform**
- REST API documented with OpenAPI/Swagger UI
- Global error handling (RFC 7807 Problem Details)
- Audit fields on all entities (created/updated by/at)
- Dockerized local environment (`docker-compose up`)
- CI pipeline: build + tests + static analysis on every PR

### ❌ Out of scope for MVP (backlog for later phases)

- Real-time updates via WebSockets → **Phase: Realtime**
- File attachments (S3/MinIO) → **Phase: Files**
- Email notifications & reminders → **Phase: Notifications**
- Activity feed / audit log UI
- Full-text search
- Caching layer (Redis)
- Event-driven messaging (Kafka/RabbitMQ)
- Mobile app
- Billing/plans

## 5. Success Criteria

- A recruiter can go from the GitHub README to a **live demo** in under 1 minute.
- A new developer can run the full stack locally with a single command.
- Test coverage ≥ 80% on the service layer; all critical flows covered by integration tests.
- Zero secrets in the repository; production deploy via CI/CD only.
- Every architectural decision traceable to an ADR.

## 6. Constraints & Assumptions

- Solo developer, part-time; phases sized to sprints of ~1 week.
- Budget ≈ $0: free tiers and open-source tooling only.
- English as the working language for all code, docs, and commits.

## Changelog

| Version | Date | Change |
|---------|------|--------|
| 1.0 | 2026-07-22 | Initial draft |
