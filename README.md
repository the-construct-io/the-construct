# The Construct

The Construct. Our loading program. We can load anything from Email, to instant messaging, document / office suite, document storage, business administration, user administration (IAM). 

Anything we need.

> A sovereign, open-source productivity platform for teams who believe their data,
> their tools, and their infrastructure should belong to them — not to a corporation.

The Construct is a self-hosted alternative to Google Workspace and Microsoft 365,
built on open standards, is extensible, deployable anywhere, and designed to run for decades.

---

## What It Is

The Construct bundles the services your team needs to communicate, collaborate, and
operate — unified under a single dashboard, a single identity layer, and a single
deployment model that works identically whether you're running on a bare-metal server
in your office or a Kubernetes cluster in the cloud.

| Service | Purpose | Powered by |
|---|---|---|
| Chat | Team messaging & channels | Mattermost |
| Email & Mailbox | SMTP / IMAP / JMAP | Stalwart Mail |
| Documents | Collaborative editing | OnlyOffice |
| Storage | File storage & sharing | MinIO (S3-compatible) |
| Identity | SSO / OIDC / OAuth2 | Keycloak |
| AI | Local-first intelligence | Ollama + LangChain4j |
| Dashboard | Unified control plane | Construct Core (Quarkus + Kotlin) |

---

## Core Principles

1. **Sovereignty by default** — your data lives where you choose
2. **Open source, all the way down** — every component is auditable
3. **Privacy is not a setting** — it is the architecture
4. **Deploy anywhere** — on-prem, cloud, air-gapped, or hybrid
5. **Modular by design** — deploy what you need, swap what you don't
6. **Standards over proprietary APIs** — Jakarta EE, S3, SMTP, OIDC
7. **AI that serves you** — local models, no data leaves your infrastructure
8. **Built for the long run** — governed by open standards, not acquisition cycles

---

<!-- ## Quick Start

### Prerequisites

- Docker 24+ and Docker Compose v2
- 4GB RAM minimum (8GB recommended for full stack)
- A domain name (or `localhost` for local development)

### Run locally

```bash
git clone https://github.com/construct-platform/construct
cd construct/deploy/docker-compose
cp .env.example .env
# Edit .env with your configuration
docker compose up -d
```

The stack will be available at:

| Service | URL |
|---|---|
| Dashboard | https://app.localhost |
| Identity (Keycloak) | https://auth.localhost |
| Storage console (MinIO) | https://storage-console.localhost |
| Traefik dashboard | http://localhost:8090/dashboard/ |

### Run on Kubernetes

```bash
helm repo add construct https://charts.theconstruct.io
helm install construct construct/construct \
  --namespace construct \
  --create-namespace \
  --values my-values.yaml
```

See [deploy/helm/README.md](deploy/helm/README.md) for full Kubernetes documentation. -->

---

## Architecture

```
                        ┌─────────────────────────────┐
                        │       Traefik Gateway        │
                        │   (routing, TLS, auth)       │
                        └──────────────┬──────────────┘
                                       │
              ┌────────────────────────┼────────────────────────┐
              │                        │                        │
     ┌────────▼────────┐    ┌──────────▼──────────┐   ┌────────▼────────┐
     │ Construct Core  │    │     Mattermost       │   │    Stalwart     │
     │  (Dashboard)    │    │      (Chat)          │   │    (Email)      │
     └────────┬────────┘    └─────────────────────┘   └────────────────┘
              │
     ┌────────┴────────────────────────────────┐
     │                                         │
┌────▼─────┐  ┌──────────┐  ┌──────────┐  ┌──▼───────┐
│ Keycloak │  │  MinIO   │  │ OnlyOffice│  │ Postgres │
│  (SSO)   │  │(Storage) │  │  (Docs)  │  │  + pgvec │
└──────────┘  └──────────┘  └──────────┘  └──────────┘
```

Every service authenticates through Keycloak. One login, all services.

---

## Extensibility

The Construct is a platform, not just a product. Any service can be integrated
by implementing the Construct SPI and providing a `construct-service.yaml` manifest.
First-party services and third-party extensions follow exactly the same contract.

See [construct-sdk/README.md](construct-sdk/README.md) to build your own extension.

---

## Project Structure

```
construct/
├── construct-core/        # Dashboard & core platform (Quarkus + Kotlin)
├── construct-sdk/         # Extension SDK (publish to Maven Central)
├── construct-docs/        # Documentation site
├── deploy/
│   ├── docker-compose/    # Local & on-prem deployment
│   └── helm/              # Kubernetes / cloud deployment
└── .github/               # CI workflows, issue templates, PR templates
```

---

## Technology Stack

| Layer | Technology |
|---|---|
| Core language | Kotlin |
| Runtime | Quarkus (Jakarta EE + MicroProfile) |
| Build system | Gradle with Kotlin DSL |
| API gateway | Traefik |
| Identity | Keycloak |
| Database | PostgreSQL 16 + pgvector |
| Object storage | MinIO |
| AI runtime | Ollama |
| AI framework | LangChain4j |
| Container orchestration | Docker Compose / Kubernetes (Helm) |

---

## Contributing

The Construct is open to contributions. Please read [CONTRIBUTING.md](CONTRIBUTING.md)
before opening a pull request.

All contributors are expected to follow the [Code of Conduct](CODE_OF_CONDUCT.md).

---

## Maintainers

The Construct is built and maintained by the team at **[Your Company Name]**.

See [MAINTAINERS.md](MAINTAINERS.md) for the current list of maintainers and
their areas of responsibility.

---

## Status

The Construct is currently in **Phase 1 — Foundation**. The core infrastructure
is being established. It is not yet suitable for production use.

See [ROADMAP.md](ROADMAP.md) for the full development roadmap.
