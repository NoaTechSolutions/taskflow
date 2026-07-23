# ADR-009: Monorepo for All TaskFlow Components

- **Status:** Accepted
- **Date:** 2026-07-22
- **Author:** Israel Esparza
- **Supersedes:** ADR-007 (Separate Frontend and Backend Repositories)

## Context

ADR-007 chose two repositories (`taskflow-api`, `taskflow-web`) following the classic corporate polyrepo pattern. Re-evaluating against this project's actual priorities surfaced a mismatch:

1. **Portfolio discoverability.** A recruiter should grasp the whole system from a single link. With polyrepo, the story fragments across repositories.
2. **The microservices phase multiplies repos.** The roadmap ends with extracted services (`auth-service`, `notification-service`, ...). Polyrepo would mean 4–5 repositories that a solo developer must configure and maintain (branch protection, CI, secrets, READMEs — each times N).
3. **Docs scope.** The Phase-0 documentation describes the *system*, not one component; it belongs at a root shared by all components.
4. **Cross-component changes are frequent** for a solo dev evolving an API and its client together; atomic commits/PRs beat coordinated pairs of PRs.
5. **Monorepo ≠ anti-microservices.** Google, Meta, and Uber operate thousands of services from monorepos. Microservice separation is about *deployment and runtime independence*, not folder layout.

## Decision

A single repository, `taskflow`, containing every component:

```
taskflow/
├── docs/                  # ADRs, vision, requirements, architecture (Phase 0 docs)
├── apps/
│   ├── api/               # Spring Boot modular monolith
│   └── web/               # React SPA
├── services/              # future extracted microservices (Phase 8+)
├── .github/workflows/     # CI/CD with path filters per component
└── README.md              # system-level entry point
```

Mitigations for ADR-007's original concerns:

- **Independent CI/CD** → GitHub Actions **path filters** (`paths: ["apps/api/**"]`) so each component's pipeline runs only when that component changes; deploys tagged per component (`api-v0.3.0`).
- **Repo language identity** → a strong root README as the narrative entry point; `.gitattributes` linguist rules if language stats need tuning.
- **Tooling scale limits** (the real monorepo pain at big-company scale: Bazel, VFS, merge queues) → irrelevant at this project's size.

## Alternatives Considered

- **Keep two repos (ADR-007)** — standard in many companies, but the coordination and maintenance overhead lands entirely on one person, and the portfolio narrative fragments. Rejected for this context.
- **Two repos + an "umbrella" meta-repo** with links and docs — adds a third thing to maintain; solves discoverability but nothing else.

## Consequences

- ✅ One link tells the whole story: docs, backend, frontend, and (later) each extracted service, all navigable.
- ✅ One repo to protect, configure, and keep green; single Kanban attaches naturally.
- ✅ Atomic cross-component changes; system-level docs live where they apply.
- ✅ Learning bonus: path-filtered CI pipelines are a real-world monorepo skill.
- ⚠️ CI workflows are slightly more complex than single-purpose pipelines.
- ⚠️ Discipline required so component boundaries stay clean without repo walls (ArchUnit still guards the backend's internal modularity).
- 📝 Process note: ADR-007 remains in the log as `Superseded` — the decision history itself documents how the project's priorities were weighed. The docs/README index must be updated on migration to Git.

## Changelog

| Version | Date | Change |
|---------|------|--------|
| 1.0 | 2026-07-22 | Initial version — supersedes ADR-007 |
