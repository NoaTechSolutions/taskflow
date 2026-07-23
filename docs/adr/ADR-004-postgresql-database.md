# ADR-004: PostgreSQL as Primary Database

- **Status:** Accepted
- **Date:** 2026-07-22
- **Author:** Israel Esparza

## Context

TaskFlow needs a relational database for its core domain (users, workspaces, boards, tasks, comments — highly relational data with strong integrity requirements). The developer has professional experience with MySQL, MariaDB, PostgreSQL, and MongoDB.

## Decision

Use **PostgreSQL 16+** as the primary and only datastore for the MVP.

## Alternatives Considered

- **MySQL / MariaDB** — solid and familiar, but PostgreSQL has become the de-facto default for new applications: richer type system, superior `JSONB` support (useful for flexible task metadata and audit payloads), stronger window functions for reporting, transactional DDL, and better standards compliance. MySQL already appears on the developer's CV via COMSATEL; adding depth in PostgreSQL diversifies the profile.
- **MongoDB as primary store** — the domain is fundamentally relational (many-to-many memberships, ordered lists, referential integrity). Document modeling would push complexity into the application layer for no benefit at this scale.
- **PostgreSQL + Redis from day one** — Redis is planned, but as a *cache/session layer* in a later phase (see roadmap). Adding it now would be premature optimization.

## Consequences

- ✅ One datastore to operate in the MVP → simpler local dev, CI, and deployment.
- ✅ `JSONB` gives NoSQL-style flexibility inside the relational model where genuinely needed.
- ✅ Advanced SQL (CTEs, window functions) available for analytics/reporting features.
- ✅ First-class support in Testcontainers, Flyway, and every managed cloud (Railway, Neon, RDS, Supabase).
- ⚠️ Team familiarity is slightly higher with MySQL; minor learning curve on Postgres-specific tooling (`psql`, `EXPLAIN ANALYZE` differences).

## Changelog

| Version | Date | Change |
|---------|------|--------|
| 1.0 | 2026-07-22 | Initial version |
