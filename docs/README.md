# TaskFlow — Project Documentation

This folder contains all planning and architecture documentation for **TaskFlow**, a project management platform (Kanban-style) built as a professional-grade portfolio project.

## Structure

| Path | Purpose |
|------|---------|
| `adr/` | Architecture Decision Records — every significant technical decision, with context and rationale |
| `VISION.md` | Product vision and MVP scope *(coming in Sprint 0)* |
| `REQUIREMENTS.md` | Functional and non-functional requirements *(coming in Sprint 0)* |
| `USER-STORIES.md` | User stories with acceptance criteria *(coming in Sprint 0)* |
| `DATA-MODEL.md` | Entity-Relationship model *(coming in Sprint 0)* |
| `ARCHITECTURE.md` | C4 diagrams and system architecture *(coming in Sprint 0)* |

## What is an ADR?

An **Architecture Decision Record** captures a single architectural decision: the context in which it was made, the options considered, the decision itself, and its consequences. ADRs are immutable — if a decision changes later, we write a *new* ADR that supersedes the old one. This gives the project a traceable decision history.

Format used: lightweight [MADR](https://adr.github.io/madr/) style.

## Versioning Convention

While these documents live in Google Drive (pre-repo phase), versions are tracked in each file's **Changelog** section at the bottom. Once the Git repository is created, this entire `docs/` folder will be committed to `taskflow-api/docs/` and versioned through Git history. From that point on, changes go through pull requests like any other code.

## Decision Log (Index)

| ADR | Title | Status | Date |
|-----|-------|--------|------|
| 001 | Use Architecture Decision Records | Accepted | 2026-07-22 |
| 002 | Monolith-first architecture | Accepted | 2026-07-22 |
| 003 | Java 21 LTS + Spring Boot 3.5 | Accepted | 2026-07-22 |
| 004 | PostgreSQL as primary database | Accepted | 2026-07-22 |
| 005 | Maven as build tool | Accepted | 2026-07-22 |
| 006 | Flyway for database migrations | Accepted | 2026-07-22 |
| 007 | Separate frontend and backend repositories | Accepted | 2026-07-22 |
| 008 | React for the frontend | Accepted | 2026-07-22 |

---
*Author: Israel Esparza — NoaTech Solutions*
