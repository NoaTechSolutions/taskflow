# ADR-006: Flyway for Database Migrations

- **Status:** Accepted
- **Date:** 2026-07-22
- **Author:** Israel Esparza

## Context

The database schema will evolve constantly during development. Two anti-patterns must be avoided: (1) letting Hibernate manage the schema via `ddl-auto: update` (non-deterministic, dangerous, never used in production), and (2) applying manual SQL by hand (untracked, non-reproducible).

## Decision

Use **Flyway** with versioned SQL migrations (`V1__create_users.sql`, `V2__create_workspaces.sql`, ...), executed automatically on application startup and in CI. Hibernate will run with `ddl-auto: validate` — it verifies the mapping against the real schema but never modifies it.

## Alternatives Considered

- **Liquibase** — more powerful (XML/YAML changelogs, rollbacks, DB-agnostic abstractions), but heavier. Flyway's plain-SQL approach is simpler, forces us to actually write and understand SQL, and covers 100% of our needs.
- **Hibernate `ddl-auto: update`** — acceptable only for throwaway prototypes; hides schema drift and can destroy data.

## Consequences

- ✅ Schema history is versioned in Git alongside code — every environment (local, CI, prod) converges to the identical schema.
- ✅ Migrations run against real PostgreSQL in integration tests (Testcontainers), catching SQL errors before deployment.
- ✅ Writing raw DDL keeps SQL skills sharp — a frequent interview topic.
- ⚠️ Discipline required: a migration, once merged, is immutable; fixes require a new migration.
- ⚠️ No automatic rollbacks in the free edition — we handle rollbacks as forward "undo" migrations.

## Changelog

| Version | Date | Change |
|---------|------|--------|
| 1.0 | 2026-07-22 | Initial version |
