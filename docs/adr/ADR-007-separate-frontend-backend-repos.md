# ADR-007: Separate Frontend and Backend Repositories

- **Status:** Accepted
- **Date:** 2026-07-22
- **Author:** Israel Esparza

## Context

TaskFlow consists of a Spring Boot REST API and a React SPA. We must decide whether they live in a single repository (monorepo) or in two separate repositories.

## Decision

Two repositories:

- **`taskflow-api`** — Spring Boot backend (the portfolio's centerpiece).
- **`taskflow-web`** — React frontend.

Planning is unified in a single **GitHub Project (Kanban)** at the account level, aggregating issues from both repos.

## Alternatives Considered

- **Monorepo** — one clone, atomic cross-stack changes, popular in big tech (with dedicated tooling like Nx/Turborepo/Bazel). But for this project it mixes two toolchains in one CI config, muddies the language statistics and visual identity of the backend repo, and the coordination benefits are minimal for a solo developer.

## Consequences

- ✅ Mirrors the most common industry setup: independent deploy cycles, clean per-repo CI/CD.
- ✅ `taskflow-api` stays 100% Java — clear identity for recruiters scanning GitHub.
- ✅ Each repo gets its own focused README, releases, and versioning.
- ⚠️ API contract changes require coordinated PRs in two repos → mitigated by OpenAPI as the single source of truth (frontend types can be generated from the spec).
- ⚠️ Two repos to keep configured (branch protection, actions, secrets).

## Changelog

| Version | Date | Change |
|---------|------|--------|
| 1.0 | 2026-07-22 | Initial version |
