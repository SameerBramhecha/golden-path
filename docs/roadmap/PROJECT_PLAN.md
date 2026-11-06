# Golden Path — Project Plan

## Product vision

Golden Path is an open-source, local-first developer application delivery tool. It simplifies the path from application source code to a running deployment through a CLI and, later, a VS Code extension.

Golden Path orchestrates established tools. It is not a replacement container runtime, Kubernetes distribution, infrastructure provider, or hosted control plane.

## Goals

- Detect an application's language and common build system.
- Reuse an existing Dockerfile or generate a reviewable one from maintained templates.
- Build container images locally and optionally publish them to a registry.
- Deploy to local container and Kubernetes environments.
- Support GitOps reconciliation for Kubernetes.
- Provision temporary cloud infrastructure and remove it safely.
- Present a coherent workflow in the CLI and editor.

## Initial non-goals

- A mandatory Golden Path cloud account or hosted backend.
- A central database, message broker, or microservice platform.
- Reimplementing Docker, BuildKit, Kubernetes, Terraform, or GitOps controllers.
- Supporting every language, framework, cloud, and registry at launch.
- Silently overwriting user-authored deployment files.
- Destroying infrastructure immediately after deployment.

## Initial application families

1. Java — Maven and Gradle
2. .NET
3. React
4. Node.js
5. Python

Detection should identify project family and relevant build metadata, not assume every repository follows one framework layout.

## Product principles

- Open-source first; favor established tools.
- Local-first; normal local workflows do not depend on a Golden Path-hosted service.
- CLI first; the editor extension follows the core workflow.
- One shared core; avoid duplicated business logic.
- GitOps-capable, not GitOps-dependent. GitOps applies to Kubernetes desired state; Compose is a simpler local deployment option.
- Developer control: preview generated-file diffs and require confirmation for destructive actions.
- Safe defaults: do not bake secrets into images or silently delete resources.
- Incremental delivery: each milestone must produce a testable capability.

## Proposed architecture

Developer → CLI / VS Code extension → Shared Golden Path core (configuration, workflows, validation, state, logging) → Capability modules (detection, Dockerfile generation, build, registry, deployment, GitOps, infrastructure) → adapters for Docker/BuildKit, Compose, Kubernetes, GitOps controller, Terraform, and registries.

Initially, the VS Code extension should invoke the CLI and consume structured output. A separate local service or IPC layer is not required for the first version.

## Proposed technology choices

| Area | Initial direction |
|---|---|
| Language/runtime | TypeScript / Node.js |
| CLI | Commander.js candidate |
| Configuration validation | Zod candidate |
| Editor | VS Code Extension API |
| Build | Docker Buildx / BuildKit |
| Local containers | Docker Compose |
| Local Kubernetes | Select Kind or k3d after a proof of concept |
| Infrastructure as code | Terraform, invoked as a user-installed executable initially |
| Registry | Docker Hub first |
| GitOps | Evaluate Argo CD and Flux; decision pending |
| Image scanning | Trivy |
| Tests | Vitest plus integration tests |
| CI | GitHub Actions |
| License | Apache 2.0, subject to dependency/license review |

These are implementation directions, not a commitment to bundle every tool.

## Milestones

### M0 — Project foundation

Deliverables:
- Public repository and governance files.
- Architecture overview and decision records.
- TypeScript workspace and shared configuration.
- Minimal CLI executable and shared core package.
- Formatting, linting, unit tests, and CI.
- GitHub Project with milestone and task tracking.

Exit criteria:
- A fresh clone can install dependencies, build, test, and run the CLI.
- CI validates pull requests and changes to the default branch.
- Scope and architecture decisions are documented.

### M1 — Application detection and containerization

Deliverables:
- Detection for Java, .NET, React, Node.js, and Python.
- Fixtures and representative sample applications.
- Existing Dockerfile detection and safe reuse.
- Dockerfile template generation when no Dockerfile exists.
- Preview/diff before writing generated files.
- Appropriate .dockerignore generation.

Acceptance criteria:
- Detection tests cover each application family and common edge cases.
- Existing Dockerfiles are never silently overwritten.
- Generated Dockerfiles build for representative samples.
- Templates use reproducible dependency installation, avoid embedding secrets, and use non-root runtime users where practical.

### M2 — Build and publish

Deliverables:
- Local image builds using Docker Buildx / BuildKit.
- Image naming and tagging.
- Build output, cancellation, and actionable errors.
- Optional Docker Hub authentication and push.
- Local build/run works without publishing to a registry.

Acceptance criteria:
- Representative images build and run locally.
- Registry credentials are handled without logging secrets.
- Build failures produce useful diagnostics.

### M3 — CLI workflows

Deliverables:
- CLI command structure and help.
- Initial commands for initialization, diagnosis, detection, Dockerfile generation, build, and push.
- Interactive and non-interactive modes where appropriate.
- Stable JSON output and documented exit codes.
- Cross-platform packaging strategy.

Acceptance criteria:
- Commands work from a fresh checkout or sample project.
- CLI presentation is separated from core business logic.
- JSON mode is suitable for editor integration and automation.

### M4 — Local deployment, GitOps proof of concept, and VS Code

Deliverables:
- Docker Compose deployment workflow.
- Local Kubernetes workflow using selected Kind or k3d option.
- Kubernetes manifest generation.
- GitOps proof of concept and controller decision (Argo CD or Flux).
- VS Code extension invoking the CLI and presenting structured results.

GitOps flow:
1. Build the image.
2. Make it available to the target cluster (load it into Kind/k3d or push to a registry).
3. Update Git-managed manifests or Helm values.
4. Commit/push the desired-state change.
5. Let the controller reconcile the cluster.
6. Surface synchronization and health status.

GitOps is not required for Compose deployments.

### M5 — Ephemeral cloud deployment

Deliverables:
- Terraform adapter and one selected cloud provider.
- Provision, deploy, inspect/test, destroy, and cleanup verification.
- Ownership metadata and optional TTL metadata.
- State protection and recovery guidance for partial failures.

Lifecycle: provision → deploy → test/inspect → explicit destroy or expiry → verify cleanup.

Safety requirements:
- Do not run broad destructive operations without clear scope and confirmation.
- Keep Terraform state until destruction and cleanup have been assessed.
- Report partial failures and resources that may continue to incur cost.
- A local TTL cannot guarantee cleanup while the developer's machine is offline; document that limitation.

### M6 — Security and maturity

Deliverables:
- Trivy image scanning.
- SBOM generation.
- Image signing and provenance exploration.
- Secret-handling hardening.
- Health checks, logs, and deployment diagnostics.
- Deployment history and policy controls where justified.

## Repository layout

Create packages only when needed; avoid empty abstractions.

```text
golden-path/
├── apps/
│   ├── cli/
│   └── vscode-extension/       # introduced in M4
├── packages/
│   ├── core/
│   ├── shared/
│   ├── detection/              # introduced in M1
│   └── dockerfile-engine/      # introduced in M1
├── templates/
├── examples/
├── docs/
│   ├── architecture/
│   ├── adr/
│   ├── roadmap/
│   └── user-guides/
├── tests/
├── .github/
│   └── workflows/
├── README.md
├── CONTRIBUTING.md
├── CODE_OF_CONDUCT.md
├── SECURITY.md
└── LICENSE
```

## Engineering approach

- Use a TypeScript monorepo with npm workspaces unless implementation constraints justify a change.
- Start with a minimal CLI and core package; add packages when a real module boundary exists.
- Keep external process execution behind small adapters with explicit input/output contracts.
- Validate user configuration at boundaries.
- Prefer deterministic unit tests and fixture-based detection tests.
- Run integration tests against real Docker/Kubernetes tools only where supported.
- Keep CI fast and predictable; separate slower integration workflows when necessary.

## Architecture decisions to record

- CLI command framework and configuration schema.
- Kind versus k3d.
- Argo CD versus Flux.
- Registry authentication storage and credential helper behavior.
- Cloud provider for the first ephemeral environment.
- Terraform version support and license/distribution review.
- Exact monorepo package boundaries as implementation evolves.

## Immediate next steps

1. Initialize the npm workspace and TypeScript configuration.
2. Create the minimal CLI and core packages.
3. Add unit tests, linting, formatting, and GitHub Actions.
4. Verify a fresh checkout can build and run.
5. Begin M1 detection after M0 exit criteria are met.
