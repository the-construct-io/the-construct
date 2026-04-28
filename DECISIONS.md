# Architecture Decision Record

This document records the significant architectural and technical decisions made
for The Construct, the reasoning behind them, and the alternatives that were
considered. It is a living document — new decisions are added here as they are made.

Contributors proposing changes to these decisions should open an issue for
discussion before submitting a pull request.

---

## ADR-001 — JVM as the core runtime

**Status:** Accepted  
**Date:** 2025

### Decision
The Construct core platform is built on the Java Virtual Machine (JVM).

### Reasoning
- **Production pedigree.** The JVM has 30 years of continuous production use in
  the most critical systems on earth — financial infrastructure, government systems,
  healthcare, and telecommunications. The Construct is infrastructure; it deserves
  an infrastructure-grade runtime.
- **Ecosystem depth.** Maven Central hosts 600,000+ open source libraries covering
  every integration The Construct will ever need — AI (LangChain4j), identity
  (Keycloak clients), storage (MinIO SDK), observability (Micrometer, OpenTelemetry).
- **Contributor pool.** Java and Kotlin together represent approximately 12 million
  professional developers globally, concentrated in exactly the enterprise and
  government sectors The Construct targets.
- **Compliance trail.** Government and regulated deployments require auditable,
  vendored dependencies, FIPS-compliant cryptography, and SBOM generation — all
  mature and well-documented on the JVM.
- **Polyglot.** The JVM runs Kotlin, Java, Scala, Clojure, and Groovy in the same
  process. Extension developers are not constrained to one language.

### Alternatives considered
- **Go** — excellent for single-purpose network services, lacks the enterprise
  ecosystem depth and compliance trail required for this platform's target deployments.
- **Rust** — optimal for systems programming where memory control is paramount;
  the ecosystem for enterprise application development is immature by comparison.
- **Node.js** — strong for I/O-heavy lightweight services; not suited for a
  long-lived platform targeting regulated enterprise environments.

---

## ADR-002 — Kotlin as the primary language

**Status:** Accepted  
**Date:** 2025

### Decision
Kotlin is the primary language for all The Construct source code.

### Reasoning
- **Null safety.** Null safety at compile time eliminates an entire category of
  runtime errors that are endemic in Java codebases.
- **Coroutines.** Kotlin coroutines provide structured concurrency as a first-class
  language feature — essential for a dashboard that must aggregate responses from
  multiple services simultaneously.
- **Expressiveness.** Data classes, sealed classes, extension functions, and
  type-safe builders reduce boilerplate without sacrificing readability.
- **Interoperability.** Kotlin is 100% interoperable with Java. Every Jakarta EE
  and Spring library works without adaptation.
- **Build DSL alignment.** The Gradle Kotlin DSL means build scripts and application
  code share the same language, tooling, and IDE support.
- **JetBrains backing.** Kotlin is maintained by JetBrains, widely adopted at Google
  (Android), and has a strong standardization trajectory.

### Alternatives considered
- **Java 21.** Records, pattern matching, and virtual threads make modern Java
  genuinely pleasant. Kotlin was chosen for null safety and coroutines specifically.
  Java interop means Java can still be used in extension modules.
- **Scala.** More powerful type system but a significantly steeper learning curve
  and a smaller contributor pool for this domain.

---

## ADR-003 — Quarkus as the application framework

**Status:** Accepted  
**Date:** 2025

### Decision
Quarkus is the application framework for construct-core and all first-party services.

### Reasoning
- **Jakarta EE + MicroProfile standards.** Quarkus implements open standards governed
  by the Eclipse Foundation — not a single vendor's framework. The Construct is
  built on the principle of sovereignty; its own foundation should reflect that.
- **GraalVM native compilation.** Quarkus produces native binaries that start in
  under 50ms and use a fraction of the RAM of a traditional JVM deployment. For a
  platform running 6+ services on a single on-prem machine, this is a meaningful
  operational advantage.
- **Kotlin first-class.** Quarkus has invested specifically in Kotlin support
  including coroutines, unlike some Jakarta EE runtimes.
- **Dev mode.** Quarkus dev mode with live reload and the unified Dev UI significantly
  accelerates the inner development loop.
- **Red Hat / IBM backing.** Open governance, not controlled by a single for-profit
  entity. Contrast with Spring Boot, which is now owned by Broadcom.

### Alternatives considered
- **Spring Boot.** The most widely adopted Java framework and a reasonable choice.
  Rejected primarily because of the Broadcom acquisition of VMware/Pivotal creating
  vendor governance risk — a direct contradiction of The Construct's sovereignty
  principles. The Spring ecosystem is also heavier in terms of startup time and
  memory footprint than Quarkus native.
- **Micronaut.** Similar philosophy to Quarkus, excellent native compilation support.
  Quarkus was preferred for its broader Jakarta EE standards compliance and stronger
  Kotlin investment.
- **WildFly / Open Liberty.** Pure Jakarta EE runtimes without the cloud-native
  optimizations. Valid for traditional deployments; Quarkus was chosen for native
  compilation and developer experience.

---

## ADR-004 — Gradle with Kotlin DSL as the build system

**Status:** Accepted  
**Date:** 2025

### Decision
Gradle with the Kotlin DSL is the build system for all modules.

### Reasoning
- **Performance.** Gradle's incremental builds, build cache, and parallel execution
  are significantly faster than Maven for multi-module projects at scale.
- **Language alignment.** The Kotlin DSL means build configuration uses the same
  language as application code — same IDE support, same type checking, same refactoring
  tools.
- **Version catalog.** Gradle's `libs.versions.toml` version catalog centralizes all
  dependency versions across the mono-repo without copy-paste.
- **Extensibility.** Custom build logic is code, not XML configuration.

### Alternatives considered
- **Maven.** More predictable and familiar in enterprise environments. The XML
  verbosity and lack of incremental build performance at scale led to choosing Gradle.
  Maven is supported for extension developers who prefer it.

---

## ADR-005 — Traefik as the API gateway

**Status:** Accepted  
**Date:** 2025

### Decision
Traefik is the API gateway and reverse proxy for all services.

### Reasoning
- **Docker and Kubernetes native.** Traefik reads service configuration directly
  from Docker labels and Kubernetes ingress resources — no separate routing
  configuration files to keep in sync with the deployment.
- **Automatic TLS.** Let's Encrypt integration is built in. Zero configuration
  for HTTPS in production.
- **Deployment parity.** The same Traefik configuration works in Docker Compose
  (on-prem) and Kubernetes (cloud), achieving the deployment parity The Construct
  requires.
- **Open source.** Traefik is MIT licensed and CNCF listed.

### Alternatives considered
- **Nginx.** Mature and performant but requires manual configuration that diverges
  between Docker and Kubernetes deployments.
- **Caddy.** Excellent automatic TLS, simpler than Nginx. Less native integration
  with container orchestration.
- **Apache APISIX / Kong.** More powerful plugin systems but significantly more
  operational complexity for phase 1.
- **Spring Cloud Gateway.** Would work well with a Spring stack; rejected alongside
  the Spring Boot decision (see ADR-003).
- **Envoy.** Extremely powerful but operationally complex. A future consideration
  for large-scale deployments.

---

## ADR-006 — Keycloak for identity and SSO

**Status:** Accepted  
**Date:** 2025

### Decision
Keycloak is the identity provider for all services in The Construct.

### Reasoning
- **Comprehensive standards support.** OIDC, OAuth2, SAML 2.0, and LDAP in a
  single deployment.
- **Native integrations.** Mattermost, MinIO, OnlyOffice, and Grafana all support
  Keycloak OIDC out of the box — no custom adapters.
- **Self-hosted.** The identity layer never leaves the user's infrastructure.
  A critical requirement for a sovereignty-first platform.
- **Red Hat backed.** Mature, enterprise-tested, open source under Apache 2.0.
- **Realm export/import.** Configuration can be version-controlled and applied
  automatically on first boot.

### Alternatives considered
- **Authentik.** Modern, Python-based, strong UI. Considered seriously. Keycloak
  was preferred for its longer enterprise track record and broader native integration
  support with the services The Construct bundles.
- **Zitadel.** Cloud-native identity platform with excellent developer experience.
  Younger project with less integration surface area.
- **Building on top of OAuth2 libraries.** Rejected. Identity is a critical
  security component — it should be a battle-tested dedicated system, not a
  custom implementation.

---

## ADR-007 — MinIO for object storage

**Status:** Accepted  
**Date:** 2025

### Decision
MinIO is the object storage layer for The Construct.

### Reasoning
- **S3-compatible API.** Applications written against MinIO work identically against
  AWS S3, Google Cloud Storage, or any other S3-compatible provider. This is the
  key to deployment parity — storage code does not change between on-prem and cloud.
- **Kubernetes native.** MinIO has a dedicated Kubernetes operator and first-class
  Helm chart support.
- **Performance.** MinIO benchmarks at multi-GB/s throughput on commodity hardware.
- **Open source.** AGPL v3 licensed with a commercial license option.

### Alternatives considered
- **Nextcloud.** A full collaboration platform with storage. Considered as an
  alternative to building the document and storage layers. Rejected because The
  Construct's extension model requires clean service boundaries — Nextcloud is a
  monolith that is difficult to integrate cleanly with other services.
- **Ceph.** Enterprise-grade distributed storage. Significant operational complexity
  unsuitable for the on-prem deployments The Construct targets.
- **SeaweedFS.** Lightweight and fast but a smaller ecosystem than MinIO.

---

## ADR-008 — Extension SPI and manifest-based plugin model

**Status:** Accepted  
**Date:** 2025

### Decision
The Construct supports third-party service extensions via a manifest-based plugin
system. Every first-party service is itself a registered extension — there are no
private internal APIs unavailable to third parties.

### Reasoning
- **Platform over product.** The extension model is what distinguishes a platform
  (which others build on) from a product (which others use). The VS Code, IntelliJ,
  and Kubernetes ecosystems demonstrate the community value of this approach.
- **Fairness.** First-party services having privileged access creates an uneven
  ecosystem. The Construct's own services must work within the same constraints
  as community extensions.
- **Composability.** Users with niche requirements — a CRM, a ticketing system, a
  project management tool — can integrate them into the unified dashboard, search,
  and AI pipeline without waiting for the core team to build it.

### Extension contract
- A `construct-service.yaml` manifest declares capabilities, permissions, and
  integration points.
- Kotlin SPI interfaces define the contracts: `ConstructService`, `SearchProvider`,
  `AIContextProvider`, `DashboardWidget`, `NotificationProvider`, `EventConsumer`.
- Services communicate via the event bus (Kafka) — never via direct inter-service
  calls.
- Permissions are declared in the manifest and enforced at the API gateway level.

---

## ADR-009 — PostgreSQL with pgvector as the primary database

**Status:** Accepted  
**Date:** 2025

### Decision
PostgreSQL 16 with the pgvector extension is the primary database for The Construct.

### Reasoning
- **Proven reliability.** PostgreSQL is the most trusted open-source relational
  database. It handles the transactional data (users, service config, metadata)
  and the vector embeddings (AI/RAG pipeline) in a single system.
- **pgvector.** Eliminates the need for a separate vector database service in
  phase 1. The unified search and RAG pipeline operate on the same Postgres
  instance as the rest of the platform data.
- **Operational simplicity.** One database technology to operate, backup, monitor,
  and tune rather than two.

### Alternatives considered
- **pgvector + separate vector DB (Qdrant, Weaviate, Chroma).** A dedicated vector
  database would outperform pgvector at large scale. Deferred — pgvector is
  sufficient for phase 1 and reduces operational complexity. A separate vector
  database can be introduced later if benchmarks show a need.

---

## ADR-010 — Ollama for local AI inference

**Status:** Accepted  
**Date:** 2025

### Decision
Ollama is the local AI runtime. Cloud AI providers (Anthropic, OpenAI) are
supported via an AI Router abstraction but are opt-in and off by default.

### Reasoning
- **Privacy.** The Construct's core promise is that user data does not leave
  their infrastructure. An AI feature that phones home to a cloud provider by
  default breaks this promise fundamentally.
- **Air-gap compatibility.** Government and regulated deployments operate without
  internet access. Local AI is the only viable model for these deployments.
- **Ollama maturity.** Ollama is the most operationally straightforward way to
  run open-weight models (Llama, Mistral, Gemma, Phi) on commodity hardware,
  with a clean REST API and Docker-native deployment.

### AI Router abstraction
An `AIProvider` interface abstracts the underlying model source. The platform
ships with an `OllamaProvider` as the default. `AnthropicProvider`,
`OpenAIProvider`, and `AzureOpenAIProvider` implementations are available but
require explicit configuration and display a clear data-sharing disclosure to
the administrator on activation.
