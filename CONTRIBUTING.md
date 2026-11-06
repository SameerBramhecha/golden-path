# Contributing

Thanks for your interest in Golden Path.

## Before starting

- Check existing issues and the roadmap.
- For substantial changes, open an issue first to discuss scope and approach.
- Keep changes focused.

## Development workflow

1. Fork the repository and create a branch from `main`.
2. Install dependencies using the package manager and lockfile selected by the project.
3. Make the smallest complete change.
4. Add or update tests and documentation.
5. Run formatting, linting, tests, and build checks.
6. Open a pull request describing the problem, solution, and validation performed.

## Pull requests

- Use a clear, imperative title.
- Link related issues.
- Include tests for behavior changes.
- Document user-visible behavior and breaking changes.
- Do not include secrets, credentials, generated build output, or unrelated formatting churn.

## Design principles

- Keep business logic out of CLI presentation code.
- Use adapters around external tools and processes.
- Make destructive actions explicit.
- Return actionable errors; do not silently swallow failures.
- Preserve existing user files unless the user explicitly approves a change.

Open a GitHub issue for bugs, feature proposals, or design questions.
