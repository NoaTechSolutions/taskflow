# ADR-005: Maven as Build Tool

- **Status:** Accepted
- **Date:** 2026-07-22
- **Author:** Israel Esparza

## Context

The backend needs a build and dependency management tool. The two realistic options in the JVM ecosystem are Maven and Gradle.

## Decision

Use **Maven** (with the Maven Wrapper, `mvnw`, committed to the repo so no local installation is required).

## Alternatives Considered

- **Gradle (Kotlin DSL)** — faster incremental builds, more flexible, growing adoption. However: Maven remains the dominant standard in the Spring/enterprise world (~70% of Spring projects), its declarative `pom.xml` is instantly readable by any Java reviewer, and its rigidity is actually a feature — less room for clever, unmaintainable build logic. Build speed differences are negligible at this project's size.

## Consequences

- ✅ Zero-surprise builds; every Java developer and every CI system understands `./mvnw clean verify`.
- ✅ Convention over configuration keeps the focus on application code, not build scripts.
- ✅ Maven Wrapper guarantees reproducible builds across machines and CI.
- ⚠️ Slower than Gradle on very large multi-module builds (not a concern at our scale).
- ⚠️ XML verbosity.

## Changelog

| Version | Date | Change |
|---------|------|--------|
| 1.0 | 2026-07-22 | Initial version |
