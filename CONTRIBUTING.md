# Contributing to The Construct

Thank you for your interest in contributing. The Construct is built in the open,
and every contribution — code, documentation, bug reports, or design feedback —
moves the project forward.

---

## Before You Start

- Read the [Code of Conduct](CODE_OF_CONDUCT.md). It applies to all spaces
  associated with The Construct.
- Read [DECISIONS.md](DECISIONS.md) to understand the architectural choices
  that have already been made and why.
- Check the [open issues](https://github.com/construct-platform/construct/issues)
  before starting work on something new. If your idea isn't there yet, open an
  issue to discuss it before writing code.

---

## Ways to Contribute

### Reporting bugs

Open an issue using the **Bug Report** template. Include:
- The version of The Construct you are running
- Your deployment environment (Docker Compose / Kubernetes / cloud provider)
- Steps to reproduce the issue
- What you expected to happen vs what actually happened
- Relevant logs (redact any secrets or personal data)

### Suggesting features

Open an issue using the **Feature Request** template. Describe:
- The problem you are trying to solve
- How it aligns with the [core principles](README.md#core-principles)
- What the user experience should look like

Features that compromise the privacy-first or sovereignty principles of the
platform are unlikely to be accepted regardless of technical merit.

### Documentation

Documentation lives in `construct-docs/`. If you find something unclear,
missing, or incorrect, a pull request is welcome without prior discussion.

### Code

See the [Development Setup](#development-setup) section below.

---

## Development Setup

### Prerequisites

- JDK 21 (GraalVM CE recommended for native compilation)
- Gradle 8.x (the wrapper is included — use `./gradlew`)
- Docker 24+ and Docker Compose v2
-- Podman
- `ktlint` and `detekt` (run via Gradle, no separate install needed)

### Clone and build

```bash
git clone https://github.com/construct-platform/construct
cd construct
./gradlew build
```

### Run the full stack locally

```bash
cd deploy/docker-compose
cp .env.example .env
# Edit .env — at minimum set the required passwords
docker compose up -d
```

### Run construct-core in dev mode

Quarkus dev mode gives you live reload and the Dev UI:

```bash
cd construct-core
../gradlew quarkusDev
```

The Dev UI is available at http://localhost:8080/q/dev

---

## Pull Request Process

1. Fork the repository and create a branch from `main`
2. Branch naming: `feat/short-description`, `fix/short-description`, `docs/short-description`
3. Make your changes
4. Run the full test suite: `./gradlew test`
5. Run the linter: `./gradlew ktlintCheck detekt`
6. Update documentation if your change affects user-facing behaviour
7. Open a pull request against `main` with a clear description of what changed and why
8. Link the issue your PR addresses using `Closes #issue-number`

### Pull request checklist

- [ ] Tests pass locally (`./gradlew test`)
- [ ] Linter passes (`./gradlew ktlintCheck detekt`)
- [ ] No new secrets or credentials committed
- [ ] Documentation updated where relevant
- [ ] DECISIONS.md updated if an architectural decision was made or changed
- [ ] Docker Compose stack still boots cleanly (`docker compose up -d`)

---

## Architectural Decisions

Before proposing a significant change to the architecture, read [DECISIONS.md](DECISIONS.md).
If you believe a decision should be revisited, open an issue to discuss it.
Do not open a pull request that reverses an architectural decision without
prior discussion — it will be closed.

New architectural decisions made during the contribution process should be
documented in DECISIONS.md as part of the pull request.

---

## Commit Style

The Construct follows [Conventional Commits](https://www.conventionalcommits.org/):

```
feat(core): add unified search endpoint
fix(keycloak): correct realm import on first boot
docs(contributing): add dev setup instructions
chore(deps): bump Quarkus to 3.17.0
```

Types: `feat`, `fix`, `docs`, `chore`, `refactor`, `test`, `perf`, `ci`

---

## Code Style

Kotlin code is formatted with `ktlint` and statically analysed with `detekt`.
The configuration lives in the root `build.gradle.kts`. CI enforces both on
every pull request.

Key conventions:
- No mutable state in service classes — use immutable data classes
- All public APIs must have KDoc comments
- No `!!` (null assertion operator) — handle nullability explicitly
- Prefer coroutines over blocking calls in service layer code

---

## Security Issues

Do not report security vulnerabilities as public GitHub issues.
Email **jordan@thefreshpass.com** with details.
We will respond within 48 hours and coordinate a responsible disclosure.

---

## Questions

If you have a question that is not answered here or in the documentation,
open a [Discussion](https://github.com/the-construct-io/the-construct/discussions/)
rather than an issue.
