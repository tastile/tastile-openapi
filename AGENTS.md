# AGENTS.md — tastile-openapi

> This repository publishes the canonical OpenAPI 3.1 contract snapshot for Tastile v1. The generator source of truth lives in `tastile-core`; never hand-edit `openapi.yaml`.

## Governance

- Top-level contract: [`constitution/CONSTITUTION.md`](constitution/CONSTITUTION.md)
- Current Operating Model: [`organization/profiles/release-driven-solo.md`](organization/profiles/release-driven-solo.md)
- Regeneration contract: [`README.md`](README.md)
- project-init governance baseline: `rebuildup/project-init@release-0-3-0`
- project-init managed Skills source: current `rebuildup/project-init` via `bunx skills`

## Canonical boundaries

- Rust/utoipa generation source: `tastile-core/crates-v1/api/src/openapi.rs`.
- `openapi.yaml`: generated distributed contract snapshot; never hand-edit.
- this repository: contract version/history and published Git identity.
- `tastile-root`: workspace submodule pointer identity.
- web/android/desktop generated clients: downstream consumer artifacts, not sources of truth.

A valid contract update must preserve the derivation chain:

`core source SHA -> generated contract -> openapi repo SHA/tag -> root submodule pointer -> consumer regeneration evidence`.

## Branch / release workflow

- `main`: released / integrated state.
- `release-x-y-z`: active release integration line.
- ticket branch: GitHub Issue number only.
- ticket PRs target the active release branch.
- normal integration into `main` comes only from the release PR.
- landing method: merge commit only.
- historic branch names remain archaeology; do not rewrite them just to match the current model.

## Agent Skills

- bootstrap: `mise run skills-bootstrap`
- update: `mise run skills-update`
- canonical managed layout: `.agents/skills/` + `.claude/skills/`
- `skills-lock.json` is project-local source/content freshness metadata.
- `skills install` is not the sole fresh-clone path because current restore is universal-path-only.

## Language

- source/config/commit messages: English.
- Issues / PR descriptions / internal review / governance docs: Japanese.
