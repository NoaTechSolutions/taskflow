# ADR-002: Monolith-First Architecture with Planned Microservices Migration

- **Status:** Accepted
- **Date:** 2026-07-22
- **Author:** Israel Esparza

## Context

TaskFlow needs an initial architecture. Microservices are fashionable and heavily requested in job postings, but they introduce significant operational complexity (service discovery, distributed transactions, network failures, observability overhead). Martin Fowler's "MonolithFirst" principle argues that most successful microservice systems started as monoliths, because module boundaries are only well understood after the domain has been explored.

As a one-person project with learning goals, we want both: the productivity of a monolith early on, and the experience (and portfolio story) of a real migration to microservices later.

## Decision

Start with a **modular monolith**: a single Spring Boot application, internally organized into clearly bounded modules (e.g., `auth`, `workspace`, `board`, `task`, `notification`), each with its own package structure and minimal coupling between modules.

In a later phase (planned as Phase 7), extract the modules with the strongest independence signals — most likely `auth` and `notification` — into standalone services.

## Alternatives Considered

- **Microservices from day one** — maximum buzzword value, but slows initial development dramatically, and boundaries drawn too early are usually wrong. High risk of an unfinished project.
- **Plain layered monolith (no module boundaries)** — fastest start, but the future migration becomes a painful rewrite instead of an extraction.

## Consequences

- ✅ Fast early development; a single deployable unit; simple debugging.
- ✅ Module boundaries enforced from day one make the future extraction realistic.
- ✅ The migration itself becomes a documented, interview-ready case study ("why, what, how, what went wrong").
- ⚠️ Requires discipline: no cross-module imports of internal classes; communication through well-defined interfaces/events.
- ⚠️ Some patterns (e.g., domain events between modules) add ceremony compared to a naive monolith.

## Changelog

| Version | Date | Change |
|---------|------|--------|
| 1.0 | 2026-07-22 | Initial version |
