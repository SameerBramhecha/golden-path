# ADR 0001: TypeScript monorepo for the initial implementation

- Status: Accepted
- Date: 2026-11-06

## Context

Golden Path needs a CLI first and a VS Code extension later. Both interfaces must share workflow logic, validation, and domain behavior. The project is initially maintained by one developer, so repository and build complexity should remain low.

## Decision

Use TypeScript and Node.js for the initial CLI and shared core. Use npm workspaces to manage the monorepo. Start with only the CLI app and core/shared packages that have immediate implementation needs.

## Consequences

Positive:
- One primary language for the CLI, core, and VS Code extension.
- Shared types and workflow logic.
- Familiar Node.js ecosystem and straightforward local development.

Trade-offs:
- Runtime and package-manager compatibility must be managed.
- Process execution and filesystem access need careful input validation and platform-aware tests.
- Package boundaries should emerge from real responsibilities, not be created speculatively.

## Revisit when

A concrete technical limitation or project requirement makes this stack materially unsuitable.
