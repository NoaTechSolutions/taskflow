# ADR-001: Use Architecture Decision Records

- **Status:** Accepted
- **Date:** 2026-07-22
- **Author:** Israel Esparza

## Context

TaskFlow is being built as a professional-grade portfolio project. Beyond the code itself, the goal is to demonstrate real software engineering discipline: how decisions are made, documented, and revisited over time. Without a written record, the reasoning behind technical choices fades quickly, and future changes (e.g., migrating from a monolith to microservices) lose their traceability.

## Decision

We will document every significant architectural and technical decision as an **Architecture Decision Record (ADR)**, using a lightweight MADR-style template:

- **Status** (Proposed / Accepted / Deprecated / Superseded)
- **Context** — the problem and the forces at play
- **Decision** — what we chose
- **Alternatives considered** — what we rejected and why
- **Consequences** — trade-offs, both positive and negative

ADRs are numbered sequentially and never edited after acceptance. A change of direction produces a new ADR that supersedes the old one.

## Alternatives Considered

- **No formal documentation** — fastest, but defeats the learning and portfolio purpose.
- **A single running "decisions" document** — simpler, but becomes messy, and decisions can't be individually superseded.
- **Heavy templates (e.g., full RFC process)** — overkill for a one-person project.

## Consequences

- ✅ Every technical choice has a written, interview-ready rationale.
- ✅ The decision history tells the story of the project's evolution — especially valuable when we migrate to microservices.
- ✅ Practicing a real industry standard used by companies like AWS, Spotify, and GitHub.
- ⚠️ Small ongoing writing overhead per decision.

## Changelog

| Version | Date | Change |
|---------|------|--------|
| 1.0 | 2026-07-22 | Initial version |
