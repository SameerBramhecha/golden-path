# Golden Path

**An open-source developer tool for simplifying application builds and deployments.**

Golden Path aims to help developers detect, containerize, build, publish, and deploy applications from the terminal or code editor. It orchestrates established tools rather than replacing them.

> Status: Early development. APIs, commands, and architecture may change.

## Project goals

- Detect supported application types and frameworks.
- Reuse existing Dockerfiles or generate reviewable ones from maintained templates.
- Build container images locally and optionally publish them to a registry.
- Deploy applications locally and to Kubernetes.
- Support GitOps workflows for Kubernetes deployments.
- Provision temporary cloud environments and clean them up safely.

## Initial application support

Java (Maven/Gradle), .NET, React, Node.js, and Python.

## Principles

- Open source first; prefer established tools.
- Local first; normal workflows should not require a Golden Path-hosted service.
- CLI first; the VS Code extension follows the core workflow.
- Shared core; avoid duplicated business logic.
- Developer control; generated files are reviewable and destructive actions are explicit.
- Incremental delivery; each milestone produces a testable capability.

## Roadmap

| Milestone | Focus |
|---|---|
| M0 | Project foundation |
| M1 | Application detection and containerization |
| M2 | Local image build and registry publishing |
| M3 | CLI workflows |
| M4 | Local deployment, GitOps proof of concept, and VS Code |
| M5 | Ephemeral cloud infrastructure and deployment |
| M6 | Security and maturity |

See [the project plan](docs/roadmap/PROJECT_PLAN.md).

## Development status

Currently in **M0 — Project Foundation**. The first implementation target is a small, testable TypeScript CLI and shared core.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md).

## License

Apache License 2.0. See [LICENSE](LICENSE).
