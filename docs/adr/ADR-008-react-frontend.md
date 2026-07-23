# ADR-008: React for the Frontend

- **Status:** Accepted
- **Date:** 2026-07-22
- **Author:** Israel Esparza

## Context

The frontend framework decision, between the two the developer already knows: React and Angular. The primary optimization target is **employability in the US market**, with a secondary goal of building a maintainable SPA for TaskFlow.

## Decision

Use **React 18+** with **TypeScript** and **Vite** as the build tool. State/data layer and component libraries will be decided in a dedicated frontend ADR when `taskflow-web` starts (candidates: TanStack Query for server state, Zustand for client state).

## Alternatives Considered

- **Angular** — excellent fit for enterprise (opinionated, batteries included, strong in banking/insurance/government). However, React appears in roughly 3–4× more US job postings, dominates the startup ecosystem, and leads into Next.js and React Native — both already relevant to the developer's profile (React Native is on the CV).
- **Vue / Svelte** — technically appealing, but neither is on the CV, and market demand (US) trails React significantly.

## Consequences

- ✅ Maximizes alignment with the most in-demand frontend skill in the target job market.
- ✅ Reinforces an existing CV skill instead of splitting effort.
- ✅ TypeScript across the frontend + OpenAPI-generated API types = end-to-end type safety.
- ⚠️ React's lack of built-in structure means we must consciously choose and document our own conventions (folder structure, state management) — which is itself a portfolio opportunity.

## Changelog

| Version | Date | Change |
|---------|------|--------|
| 1.0 | 2026-07-22 | Initial version |
