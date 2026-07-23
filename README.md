# TaskFlow

A Trello-style project management platform — Kanban boards, tasks, and teams — built with production-grade engineering practices.

> **Status:** Sprint 1 — project scaffolding. See the [board](https://github.com/users/NoaTechSolutions/projects/4) for live progress.

## What this project is

TaskFlow is a full-stack portfolio project built the way a real product team would build it: written requirements, user stories with acceptance criteria, architecture decision records, sprint planning, and a documented migration path from modular monolith to microservices.

The engineering process is as much the deliverable as the application itself.

## Tech stack

**Backend**
- Java 21 (LTS) · Spring Boot 4.1
- PostgreSQL 16 · Flyway migrations · Spring Data JPA
- Spring Security — JWT (RS256) with refresh token rotation, OAuth2 (Google, GitHub)
- OpenAPI 3 / Swagger UI · RFC 7807 problem details

**Frontend**
- React 18 · TypeScript · Vite

**Quality & delivery**
- JUnit 5 · Testcontainers · ArchUnit
- Docker · GitHub Actions · SonarCloud

## Repository structure

    taskflow/
    ├── apps/
    │   ├── api/        # Spring Boot backend (modular monolith)
    │   └── web/        # React frontend
    ├── services/       # future extracted microservices
    └── docs/           # planning, architecture, and decision records

## Documentation

| Document | Contents |
|----------|----------|
| [Vision](docs/VISION.md) | Product vision and MVP scope |
| [Requirements](docs/REQUIREMENTS.md) | Functional and non-functional requirements |
| [User Stories](docs/USER-STORIES.md) | 25 stories with Given/When/Then criteria |
| [Data Model](docs/DATA-MODEL.md) | ER diagram and schema design decisions |
| [Architecture](docs/ARCHITECTURE.md) | C4 diagrams, module boundaries, migration roadmap |
| [Sprint Plan](docs/SPRINT-PLAN.md) | Sprint breakdown and task cards |
| [ADRs](docs/adr/) | Every significant technical decision, with rationale |

## Getting started

    git clone https://github.com/NoaTechSolutions/taskflow.git
    cd taskflow
    cp .env.example .env
    docker compose up

Local environment lands in Sprint 1 (CARD 1.3). Until then, the API can be built with `./mvnw verify` from `apps/api`.

## Author

**Israel Esparza** — Full Stack Developer
[noatechsolutions.com](https://noatechsolutions.com) · Richmond, CA

## License

MIT — see [LICENSE](LICENSE).