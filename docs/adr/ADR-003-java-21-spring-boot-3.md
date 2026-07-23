# ADR-003: Java 21 LTS with Spring Boot 3.5

- **Status:** Accepted
- **Date:** 2026-07-22
- **Author:** Israel Esparza

## Context

We need to choose the language version and framework for the backend. The developer's professional background is Java + Spring Boot (COMSATEL, 2019–2025), so the ecosystem is fixed; the open questions are *which versions* and *why*.

## Decision

- **Java 21 (LTS)** as the language baseline.
- **Spring Boot 3.5.x** (latest stable minor of the 3.x line) as the application framework.

Key features we intend to actively use:

- **Virtual threads (Project Loom)** — massively cheaper concurrency for I/O-bound work; Spring Boot 3.2+ supports them with a single property.
- **Records** — concise, immutable DTOs.
- **Pattern matching & sealed types** — cleaner domain modeling (e.g., task state machines).
- Spring Boot 3.x specifics: Jakarta EE 10 namespace, native Micrometer observability, `ProblemDetail` (RFC 7807) for standardized API errors.

## Alternatives Considered

- **Java 17 LTS** — safe and widely deployed, but Java 21 is equally LTS, fully supported by Spring Boot 3.2+, and signals up-to-date knowledge. No compatibility reason to stay behind.
- **Java 8/11** — still common in legacy enterprises, but choosing them for a greenfield 2026 project would be a red flag.
- **Kotlin** — attractive, but it would dilute the core goal: showcasing strong *Java* backend skills that match the developer's CV.
- **Spring Boot 2.x** — end of OSS support; no reason for a new project.

## Consequences

- ✅ Modern language features reduce boilerplate and improve code readability.
- ✅ Virtual threads give a compelling performance story without reactive-stack complexity (no need for WebFlux).
- ✅ Long support window (Java 21 LTS: through 2029+).
- ⚠️ Some older libraries may lag on Java 21 support — must verify each dependency.

## Changelog

| Version | Date | Change |
|---------|------|--------|
| 1.0 | 2026-07-22 | Initial version |
