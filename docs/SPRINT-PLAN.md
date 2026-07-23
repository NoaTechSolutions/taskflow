# TaskFlow — Sprint Plan & Learning Roadmap

- **Version:** 1.0
- **Date:** 2026-07-22
- **Author:** Israel Esparza
- **Status:** Living document (refined at each sprint planning)
- **Related:** VISION.md · REQUIREMENTS.md · USER-STORIES.md

## 0. How this plan works

Each sprint (~1 week) has: an **objective** (one sentence — if the sprint ends and this isn't true, the sprint failed), **task cards**, and a **retro** at the end.

Every task card follows this template (and will be copied into GitHub issues in Phase 1):

```
### CARD <id> · <title>
Story: <US-xx> | Points: <n>
GOAL: what "done" means, in one line.
STEPS: suggested path (not mandatory — deviating and explaining why is encouraged).
YOUR CHALLENGE 🔥: the part you must implement YOURSELF before asking Claude for code.
MENTOR PROMPT 🤖: paste this into Claude in IntelliJ to set the learning context.
STUDY 📚: official docs / articles to read BEFORE coding.
```

### Working agreement with your mentor (Claude in IntelliJ)

Paste this ONCE at the start of each session:

> You are my senior backend mentor for TaskFlow, my Spring Boot 3.5 / Java 21 portfolio project. Your job is to make me a better engineer, not to write my project for me. Rules: (1) When I ask how to do something, first ask me what I think the approach should be. (2) Give me concepts, trade-offs, and small illustrative snippets — not complete implementations, unless I explicitly say "show me the full solution" after I've attempted it. (3) Review my code like a strict senior in a pull request: naming, design, security, performance, tests. (4) When I make a mistake, explain WHY it's wrong and what problem the correct pattern solves. (5) Quiz me occasionally on what we just did. (6) Correct my English when I write something unnatural — I'm practicing. Current task: [PASTE THE CARD HERE].

---

## Sprint 0 · Planning & Documentation ✅ (in progress)

**Objective:** All Phase-0 documents exist and are approved.

- [x] ADR-001…008
- [x] VISION.md
- [x] REQUIREMENTS.md
- [x] USER-STORIES.md
- [x] SPRINT-PLAN.md (this document)
- [ ] DATA-MODEL.md
- [ ] ARCHITECTURE.md
- [ ] GitHub repos + Kanban board + issues created (bridge to Sprint 1)

---

## Sprint 1 · Professional Scaffolding (Platform Epic starts)

**Objective:** `docker compose up` starts API + PostgreSQL; CI is green on a PR; Swagger shows a documented health endpoint; errors come back as RFC 7807.

### CARD 1.1 · Repository & project skeleton
Story: — | Points: 2
**GOAL:** `taskflow-api` repo exists with a running Spring Boot 3.5 / Java 21 app, Maven wrapper, `.gitignore`, `.editorconfig`, and a README with badges placeholder.
**STEPS:** Spring Initializr (web, validation, data-jpa, security, postgresql, flyway, actuator, devtools) → commit as `chore: bootstrap project` → protect `main` (PRs only, require CI).
**YOUR CHALLENGE 🔥:** Write the README structure yourself, in English, from memory of great READMEs you've seen. Then ask Claude to critique it — not to write it.
**MENTOR PROMPT 🤖:** "I just bootstrapped the project. Quiz me: what does each starter I chose actually bring transitively, and what would break if I removed it? Then review my package structure proposal for a modular monolith with modules auth/workspace/board/shared."
**STUDY 📚:** docs.spring.io/spring-boot/ (Getting Started + "Structuring Your Code") · start.spring.io

### CARD 1.2 · Modular monolith package structure
Story: — | Points: 3
**GOAL:** Package layout `com.noatech.taskflow.{auth,workspace,board,shared}` with enforced boundaries; an ArchUnit test fails the build if `board` imports `auth.internal.*`.
**STEPS:** Define per-module structure (`api/`, `domain/`, `internal/`) → add ArchUnit dependency → write boundary rules as tests.
**YOUR CHALLENGE 🔥:** Write the ArchUnit rules yourself after reading its docs. This is the "discipline" promised in ADR-002 — make the architecture self-defending.
**MENTOR PROMPT 🤖:** "Explain the difference between package-by-layer and package-by-feature, and where a modular monolith fits. Then review my ArchUnit rules: do they actually prevent the coupling that would block a future microservice extraction?"
**STUDY 📚:** archunit.org/userguide · Simon Brown's "Modular Monoliths" talk (search on YouTube)

### CARD 1.3 · Dockerized local environment
Story: US-25 | Points: 3
**GOAL:** `docker compose up` starts PostgreSQL 16 + the API (multi-stage Dockerfile) + Mailpit (mail catcher); `.env.example` documents every variable.
**STEPS:** Multi-stage Dockerfile (build with Maven image, run on `eclipse-temurin:21-jre-alpine` as non-root user) → compose file with healthchecks → app waits for DB healthy.
**YOUR CHALLENGE 🔥:** Get the final image under 300 MB and explain each Dockerfile layer's cache behavior. Bonus: use Spring Boot's layered JAR feature.
**MENTOR PROMPT 🤖:** "Review my Dockerfile like a DevOps engineer: layer caching, image size, security (non-root, no secrets). Explain Spring Boot layered JARs and why they improve rebuild times."
**STUDY 📚:** docs.docker.com/build/building/multi-stage · Spring Boot docs "Container Images"

### CARD 1.4 · Flyway baseline + JPA auditing
Story: US-24 | Points: 3
**GOAL:** `V1__baseline.sql` creates a `users` table skeleton; Hibernate runs with `ddl-auto: validate`; every entity extends an `Auditable` base with `created_at/by`, `updated_at/by` auto-populated.
**STEPS:** Configure Flyway → write V1 by hand (no generators) → `@EnableJpaAuditing` + `AuditorAware` reading the security context (temporary stub until auth exists).
**YOUR CHALLENGE 🔥:** Write the SQL yourself: correct PostgreSQL types (use `timestamptz`, not `timestamp` — research why), UUID primary keys with `gen_random_uuid()`.
**MENTOR PROMPT 🤖:** "Quiz me on timestamp vs timestamptz in PostgreSQL and UUIDv4 vs UUIDv7 as primary keys. Then review my V1 migration and my Auditable superclass."
**STUDY 📚:** flywaydb.org/documentation · wiki.postgresql.org "Don't Do This" (read it ALL — gold) · Vlad Mihalcea's blog on UUID keys

### CARD 1.5 · Global error handling (RFC 7807)
Story: US-22 | Points: 3
**GOAL:** All errors return `application/problem+json` with a stable project `code`; validation errors list field problems; stack traces never leak.
**STEPS:** `@RestControllerAdvice` + Spring's built-in `ProblemDetail` → error code catalog enum → handle validation, not-found, conflict, and fallback 500.
**YOUR CHALLENGE 🔥:** Design the error code catalog (naming convention, HTTP mapping) yourself — it's an API design exercise, not a coding one.
**MENTOR PROMPT 🤖:** "Explain RFC 7807 and Spring's ProblemDetail support. Then act as an API design reviewer for my error catalog: are my codes stable, consistent, and useful for a frontend?"
**STUDY 📚:** RFC 7807 (read the actual RFC — it's short) · Spring Framework docs "Error Responses"

### CARD 1.6 · OpenAPI + CI pipeline
Story: US-23 | Points: 3
**GOAL:** Swagger UI live at `/swagger-ui` (non-prod only); GitHub Actions runs build + tests + SonarCloud on every PR; README badges show build status and coverage.
**STEPS:** springdoc-openapi dependency → API metadata → GitHub Actions workflow (`mvnw verify` with cached dependencies) → SonarCloud free tier → JaCoCo report upload.
**YOUR CHALLENGE 🔥:** Write the GitHub Actions YAML from scratch (no copy-paste from templates). Make it fail fast and cache Maven deps correctly.
**MENTOR PROMPT 🤖:** "Review my GitHub Actions workflow: caching strategy, when jobs run, secret handling. Explain how JaCoCo instruments code and what coverage % actually measures (and its limits)."
**STUDY 📚:** springdoc.org · docs.github.com/actions · docs.sonarsource.com/sonarcloud

**Sprint 1 exit ritual:** retro (what slowed me down? what did I learn?) + demo GIF in the README + tag `v0.1.0`.

---

## Sprints 2–3 · Authentication & Security (Epic 1)

**Objective (S2):** Register, verify email, login, refresh with rotation — all tested against real PostgreSQL.
**Objective (S3):** OAuth2 (Google + GitHub), password reset, logout, profile; rate limiting on auth endpoints.

### CARD 2.1 · User entity + registration
Story: US-01 | Points: 3
**GOAL:** POST `/api/v1/auth/register` creates an UNVERIFIED user; BCrypt hashing; anti-enumeration response.
**YOUR CHALLENGE 🔥:** Decide and defend the DTO↔entity mapping approach (MapStruct vs manual) in a mini-ADR comment on the PR.
**MENTOR PROMPT 🤖:** "Quiz me on BCrypt: what does the cost factor do, why is it slow ON PURPOSE, what's a pepper vs salt? Review my registration flow for account enumeration leaks (timing included)."
**STUDY 📚:** OWASP Authentication Cheat Sheet · Spring Security docs "Password Storage"

### CARD 2.2 · Email verification flow
Story: US-02 | Points: 3
**GOAL:** Verification token (hashed at rest, 24h TTL) emailed via Mailpit locally; endpoint activates account.
**YOUR CHALLENGE 🔥:** Tokens must be stored HASHED (like passwords) — reason out why before reading about it. Design the token table yourself.
**MENTOR PROMPT 🤖:** "Why must single-use tokens be hashed at rest? Walk me through the threat model. Then review my token entity and expiry/cleanup strategy."
**STUDY 📚:** OWASP Forgot Password Cheat Sheet (same principles apply)

### CARD 2.3 · JWT login (RS256)
Story: US-03 | Points: 5
**GOAL:** POST `/login` returns access JWT (15 min, RS256) + refresh token; Spring Security filter chain validates JWTs statelessly on every request.
**STEPS:** Generate RSA keypair (config, not committed) → JwtEncoder/Decoder (Nimbus via spring-security-oauth2-jose) → stateless SecurityFilterChain → `@AuthenticationPrincipal` resolution.
**YOUR CHALLENGE 🔥:** Draw (paper or Mermaid) the FULL request lifecycle through the Spring Security filter chain before writing config. If you can't draw it, you don't understand it yet.
**MENTOR PROMPT 🤖:** "I drew the Spring Security filter chain for a stateless JWT setup — check my understanding. Quiz me: RS256 vs HS256, what goes in JWT claims and what must NEVER go there, token size trade-offs."
**STUDY 📚:** Spring Security docs (Servlet → Authentication Architecture — read twice) · jwt.io/introduction · RFC 7519

### CARD 2.4 · Refresh token rotation with reuse detection
Story: US-04 | Points: 5
**GOAL:** Refresh rotates tokens; reuse of a rotated token revokes the family; integration test proves the revocation cascade.
**YOUR CHALLENGE 🔥:** Design the `refresh_tokens` table (family ID, parent chain, used/revoked flags) YOURSELF on paper first. This design is the whole card.
**MENTOR PROMPT 🤖:** "Challenge my refresh-token-family design with attack scenarios: stolen token, race between legitimate user and attacker, logout semantics. Where are the edge cases?"
**STUDY 📚:** Auth0 blog "Refresh Token Rotation" · OWASP Session Management Cheat Sheet

### CARD 2.5 · Integration test foundation (Testcontainers)
Story: transversal | Points: 3
**GOAL:** `@SpringBootTest` slice hitting REAL PostgreSQL via Testcontainers; auth flows from US-01…04 covered end-to-end; runs in CI.
**YOUR CHALLENGE 🔥:** Make the container start ONCE for the whole suite (singleton pattern) and measure the difference in build time.
**MENTOR PROMPT 🤖:** "Explain the test pyramid for a Spring Boot app: what belongs in unit vs @DataJpaTest vs full @SpringBootTest? Review my Testcontainers setup for speed."
**STUDY 📚:** testcontainers.com/guides · Spring Boot docs "Testing"

### CARD 3.1 · OAuth2 login (Google + GitHub)
Story: US-05 | Points: 5
**YOUR CHALLENGE 🔥:** Explain (voice note to yourself, in English!) the difference between OAuth2 *authorization* and OIDC *authentication*, and which one "Login with Google" actually is. Then implement account linking by verified email.
**MENTOR PROMPT 🤖:** "Quiz me hard on the authorization code flow with PKCE: every actor, every redirect, every token. Then review my success-handler that bridges OAuth2 login to MY JWT issuance."
**STUDY 📚:** Spring Security docs "OAuth 2.0 Login" · oauth.net/2 · the RFC 6749 diagrams

### CARD 3.2 · Password reset + logout
Story: US-06, US-07 | Points: 3
**YOUR CHALLENGE 🔥:** On password reset, ALL refresh tokens must die. Find every other place in the codebase where this "revoke all sessions" rule should also apply (hint: there are at least 2 more).
**MENTOR PROMPT 🤖:** "Review my reset flow against the OWASP Forgot Password Cheat Sheet, point by point. What am I missing?"

### CARD 3.3 · Rate limiting on auth endpoints
Story: NFR-04 | Points: 3
**YOUR CHALLENGE 🔥:** Choose between Bucket4j in-memory vs a filter with a cache, knowing Redis comes later. Write a mini-ADR: what breaks when we scale to 2 instances, and why is that acceptable NOW?
**MENTOR PROMPT 🤖:** "Explain token bucket vs sliding window rate limiting. Review my choice of what to key on (IP? email? both?) and the trade-offs."
**STUDY 📚:** bucket4j.com · Cloudflare blog "How we built rate limiting"

**Sprint 3 exit:** tag `v0.3.0` — a recruiter can register, verify (Mailpit), login with Google, and watch tokens rotate in Swagger. Update the README demo GIF.

---

## Sprint 4 · Workspaces & Teams (Epic 2) — outline, refine at planning

**Objective:** Multi-workspace membership with roles enforced at the service layer; invitations by email working end-to-end.
Cards: workspace CRUD + slug strategy (US-09) · invitations with TTL (US-10) · role management + ownership transfer (US-11) · list/leave (US-12) · archive (US-13) · **method-security deep dive**: custom `@PreAuthorize` with a permission evaluator.
**KEY CHALLENGE 🔥:** Design the authorization model (who can do what, where is it checked, how is it tested) and write `docs/SECURITY-MODEL.md` yourself.

## Sprints 5–6 · Boards, Lists & Tasks (Epic 3) — outline, refine at planning

**Objective:** Full Kanban CRUD with persistent fractional ordering, labels, comments, archiving, and the optimized board-view endpoint.
Cards map to US-14…US-21.
**KEY CHALLENGES 🔥:** implement fractional ordering + rebalancing strategy from scratch (no library) · write the concurrency test for simultaneous task moves · kill an intentionally-introduced N+1 using Hibernate statistics and `@EntityGraph`.

## Sprint 7 · Hardening & Public Launch — outline

**Objective:** Deployed to production with CI/CD; observability dashboards; security pass done.
Cards: production deploy (platform TBD — ADR required) · structured logging + correlation IDs · Prometheus/Grafana · OWASP dependency check + ZAP baseline scan · load test with k6 · final README + architecture diagrams + demo video.

## Phases 8+ (post-MVP, each opens with its own ADR)

Realtime (WebSocket/STOMP) → Files (S3/MinIO) → Notifications (async + scheduler) → Caching (Redis) → Events (outbox pattern + RabbitMQ/Kafka) → **Microservices extraction** (auth first — the RS256 bet pays off) → then Project #2: the e-learning platform.

## Changelog

| Version | Date | Change |
|---------|------|--------|
| 1.0 | 2026-07-22 | Initial version — S1–S3 detailed, S4+ outlined (rolling wave) |
