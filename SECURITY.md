# Security Policy

## Supported Versions

The Construct is currently in early development. Security fixes are applied to
the latest version only.

| Version | Supported |
|---|---|
| latest (`main`) | ✅ |
| older releases | ❌ |

Once the project reaches `1.0.0`, a formal support window policy will be defined.

---

<!-- ## Reporting a Vulnerability

**Do not report security vulnerabilities as public GitHub issues.**

Email **security@theconstruct.io** with:

- A description of the vulnerability
- Steps to reproduce it
- The potential impact you believe it has
- Any suggested mitigations if you have them

You will receive an acknowledgement within **48 hours** and a substantive
response within **5 business days**.

We will coordinate disclosure with you before any public announcement. We ask
that you give us a reasonable window (typically 90 days) to address the issue
before public disclosure. -->

---

## Security Design Principles

The Construct is designed with security as a core architectural concern, not an
afterthought. The relevant design decisions are:

**Identity and access**
- All authentication is delegated to Keycloak — no custom auth code in the platform
- All inter-service communication within the stack is authenticated
- JWT tokens are validated at the API gateway level before reaching services
- The extension permission model enforces capability scopes at the gateway — not by trust

**Data sovereignty**
- No telemetry or usage data is transmitted outside the deployment by default
- AI features use local models by default — no user data is sent to cloud providers
  without explicit administrator configuration and user-visible consent
- Storage encryption at rest is supported via MinIO server-side encryption

**Network**
- All external traffic is TLS — Traefik handles certificate management
- Internal service-to-service traffic stays within the Docker/Kubernetes network
- No service exposes a port to the host network except through Traefik

**Secrets management**
- No secrets are committed to the repository — all secrets are injected via
  environment variables or a secrets manager
- The `.env.example` file contains only placeholder values
- CI pipelines use GitHub Actions secrets — never plaintext in workflow files

**Dependencies**
- Dependencies are pinned to specific versions
- SBOM generation is part of the build pipeline
- Dependabot is enabled for automated dependency update pull requests

---

## Known Security Considerations

**Default credentials**
The `.env.example` file ships with placeholder passwords. Deployments using
default or weak passwords are a known risk. The installer will warn on weak
passwords and refuse to start with the example file unmodified in production mode.

**Traefik dashboard**
The Traefik dashboard is exposed on port 8090 by default. In production
deployments, this port should not be publicly accessible. The deployment guide
documents firewall configuration for production environments.

**Keycloak admin console**
The Keycloak admin console should not be publicly accessible in production.
The deployment guide documents network-level restrictions for the admin endpoints.
