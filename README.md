# github-workflows — reusable workflows

Reusable workflows (`workflow_call`) for the organization. They are self-contained (they don't depend on
the `github-actions` repo). Composite actions live in the sibling `github-actions` repo.

## Contents (`.github/workflows/`)
- `ci.yml` — JS/TS (Yarn Berry): lint + type-check + test. Runs the **root** scripts; in a monorepo those
  should fan out to the workspaces (Turborepo or `yarn workspaces foreach`). Don't enable Yarn zero-install
  (gitignore `.yarn/cache`).
- `rust-ci.yml` — Rust: `cargo fmt --check`, `clippy -D warnings`, `cargo test`. Pass `working-directory`
  pointing at the Cargo workspace root (e.g. `backend/`).
- `semantic-release.yml` — release on merge to `main` (prerelease branches alpha/beta supported).

## How a repo consumes them

Monorepo with web (yarn) + api (rust):
```yaml
name: CI
on: [push, pull_request]
jobs:
  js:
    uses: digital-multiverse/github-workflows/.github/workflows/ci.yml@v2
  rust:
    uses: digital-multiverse/github-workflows/.github/workflows/rust-ci.yml@v2
    with:
      working-directory: backend
```

Release (on merge to main):
```yaml
name: Release
on:
  push: { branches: [main] }
jobs:
  release:
    uses: digital-multiverse/github-workflows/.github/workflows/semantic-release.yml@v2
    secrets: inherit
```
