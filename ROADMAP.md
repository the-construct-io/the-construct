# Roadmap

The Construct is developed in phases. Each phase has a clear set of deliverables
and exit criteria. Work does not begin on a new phase until the previous phase
meets its exit criteria.

---

## Phase 1 — Foundation *(current)*

**Goal:** Establish the repository, infrastructure contracts, identity layer,
and core application shell. Nothing is user-facing yet. Everything that follows
is built on this foundation.

### Deliverables

- [x] GitHub org and mono-repo structure
- [x] Founding documents (README, CONTRIBUTING, CODE_OF_CONDUCT, MAINTAINERS, DECISIONS)
- [ ] Gradle multi-module build with Kotlin DSL and version catalog
- [ ] GitHub Actions CI pipeline (build, test, lint, native compile check)
- [ ] Extension SPI interfaces and `construct-service.yaml` JSON Schema
- [ ] Docker Compose stack — Keycloak, MinIO, PostgreSQL + pgvector, Traefik
- [ ] Keycloak realm configuration with OIDC clients for all phase 2 services
- [ ] `construct-core` application shell — health endpoint, OIDC validation, stub ExtensionRegistry

### Exit criteria

- `docker compose up -d` boots the full stack cleanly from a fresh clone
- SSO login flow works end-to-end through Keycloak
- `construct-core` `/health` endpoint returns UP
- CI passes on every push to `main`
- SPI interfaces are defined and documented in `construct-sdk`

---

## Phase 2 — Communication

**Goal:** Integrate Mattermost and Stalwart Mail. Deliver a unified notification
feed in the dashboard that aggregates chat and email events.

### Deliverables

- [ ] Mattermost deployment and Keycloak SSO integration
- [ ] Stalwart Mail Server deployment (SMTP / IMAP / JMAP)
- [ ] Mattermost adapter implementing `SearchProvider`, `NotificationProvider`, `DashboardWidget`
- [ ] Stalwart adapter implementing `SearchProvider`, `NotificationProvider`
- [ ] Unified notification feed in the dashboard
- [ ] Event bus integration — chat and email events published to Kafka
- [ ] Basic dashboard UI — notification feed, service health indicators

### Exit criteria

- Users can send and receive email through Stalwart via a webmail client
- Users can log into Mattermost with their Keycloak credentials
- New messages and emails appear in the unified dashboard notification feed

---

## Phase 3 — Documents & Storage

**Goal:** Integrate OnlyOffice and MinIO into the dashboard. Enable file uploads,
document creation, and collaborative editing from within the platform.

### Deliverables

- [ ] OnlyOffice deployment and Keycloak SSO integration
- [ ] OnlyOffice adapter implementing `SearchProvider`, `AIContextProvider`, `DashboardWidget`
- [ ] MinIO adapter implementing `SearchProvider`, `AIContextProvider`, `DashboardWidget`
- [ ] File browser in the dashboard (upload, download, share, preview)
- [ ] Document creation and editing from the dashboard
- [ ] File-to-document workflow (attach file from storage to a document)
- [ ] Sharing model — share files and documents with users or channels

### Exit criteria

- Users can create, edit, and share documents through OnlyOffice
- Users can upload and manage files through the MinIO-backed file browser
- Files and documents appear in unified search results

---

## Phase 4 — AI Layer

**Goal:** Deliver the local AI features across all integrated services. Unified
semantic search across the entire platform. AI assistant in the dashboard.

### Deliverables

- [ ] Ollama deployment and model management UI
- [ ] pgvector embedding pipeline — indexes content from all services
- [ ] Unified semantic search across chat, email, documents, and storage
- [ ] AI assistant in the dashboard (RAG-powered, all data local)
- [ ] Per-service AI features:
  - Email: smart compose, thread summarization
  - Chat: channel summarization, semantic search
  - Documents: document chat, rewrite, translation
  - Storage: semantic file search, auto-tagging
- [ ] AI Router with pluggable provider abstraction
- [ ] Optional cloud AI providers (Anthropic, OpenAI) — opt-in with explicit consent UI

### Exit criteria

- Unified semantic search returns results across all services
- AI assistant can answer questions using the user's own data
- All AI features operate fully offline with no external API calls by default

---

## Phase 5 — Kubernetes & Cloud Deployment

**Goal:** Deliver production-grade Kubernetes deployment via Helm. Enable
one-command deployment to any Kubernetes cluster.

### Deliverables

- [ ] Helm charts for each service
- [ ] Umbrella Helm chart for the full platform
- [ ] k3s deployment guide (on-prem Kubernetes)
- [ ] Cloud deployment guides (AWS EKS, GCP GKE, Hetzner)
- [ ] Horizontal pod autoscaling configuration
- [ ] Persistent volume configuration for each service
- [ ] Backup and restore procedures

### Exit criteria

- `helm install construct construct/construct` deploys a working platform
- The platform runs identically on k3s (on-prem) and a managed cloud Kubernetes service
- Backup and restore procedures are documented and tested

---

## Phase 6 — Open Source Launch

**Goal:** Make The Construct ready for public community adoption. Documentation,
developer experience, and the extension SDK must be polished enough for external
contributors to be productive without hand-holding.

### Deliverables

- [ ] Documentation site (construct-docs) — complete installation, configuration,
  and extension developer guides
- [ ] `construct-cli` scaffolding tool — `construct new service`
- [ ] `construct-sdk` published to Maven Central
- [ ] Example third-party extension (reference implementation)
- [ ] One-command install script for common environments
- [ ] Public demo instance
- [ ] Project website (theconstruct.io)
- [ ] Community forum or discussion space

### Exit criteria

- A developer with no prior knowledge of The Construct can build and register a
  working extension following the documentation alone
- The demo instance is publicly accessible
- The project website is live

---

## Future Considerations

Items that are acknowledged but not scheduled. They will be scoped into phases
as the project matures.

- **Calendar and scheduling service** (Nextcloud Calendar or Radicale)
- **Video conferencing** (Jitsi Meet integration)
- **Password manager** (Vaultwarden integration)
- **Git hosting** (Gitea / Forgejo integration)
- **Wiki / knowledge base** (Outline integration)
- **Agentic AI workflows** — AI agents acting across services
- **Multi-tenancy** — multiple isolated organizations on a single deployment
- **Federated identity** — federation between multiple Construct instances
- **Mobile applications** — iOS and Android clients
- **End-to-end encryption** — for storage and messaging
- **FIPS compliance certification**
- **FedRAMP authorization** (for US government cloud deployments)

---

## Version Policy

The Construct follows [Semantic Versioning](https://semver.org/).

- Patch versions (`0.1.x`) — bug fixes, security patches
- Minor versions (`0.x.0`) — new features, non-breaking changes
- Major versions (`x.0.0`) — breaking changes to the extension SPI or deployment model

The extension SPI is considered stable at `1.0.0`. Until then, minor versions
may include breaking SPI changes with a migration guide.
