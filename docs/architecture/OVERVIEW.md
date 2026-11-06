# Architecture Overview

## Design objective

Keep Golden Path local-first, modular, and small enough to maintain as a solo open-source project. The CLI is the first user interface. A VS Code extension will be added later and will reuse the same workflows.

## Logical architecture

```text
Developer
   |
   +--> CLI (M0–M3)
   |
   +--> VS Code extension (M4)
             |
             v
       Golden Path core
       - configuration
       - workflow orchestration
       - validation
       - state and logging
             |
             v
       Capability modules
       - application detection
       - Dockerfile generation
       - image build
       - registry
       - deployment
       - GitOps
       - infrastructure
             |
             v
       External tool adapters
       - Docker / BuildKit
       - Docker Compose
       - Kubernetes
       - Git
       - Argo CD or Flux
       - Terraform
       - container registry
```

## Initial implementation boundary

M0 should introduce only the minimum TypeScript workspace, CLI application, and core package required to prove the architecture. Do not create empty packages for capabilities that are not yet implemented.

The CLI should own terminal interaction and argument parsing. The core should own application workflows and reusable business logic. The CLI must not become the only home for logic that the future editor extension needs.

## External processes

Interactions with Docker, Terraform, Git, and other command-line tools should be isolated behind adapters. Adapters should:
- validate inputs before invocation;
- avoid shell-string interpolation where argument arrays are available;
- capture exit codes and useful output;
- support cancellation and timeouts where appropriate;
- avoid logging credentials or secret values.

## Local-first and state

Normal local workflows should not require a Golden Path-hosted service. Any state initially required should live locally and be documented. Avoid introducing a database, message broker, or remote API in the first milestones.

## Deployment model

Docker Compose is the simplest local deployment target. Kubernetes workflows are GitOps-capable: desired state is stored in Git and reconciled by a selected controller. Cloud infrastructure is provisioned through Terraform in a later milestone and must have an explicit, verifiable cleanup workflow.

## Evolution

Add modules only when there is a real responsibility and stable boundary. Record meaningful technology and architecture decisions in `docs/adr/`.
