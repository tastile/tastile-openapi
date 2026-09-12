# AGENTS.md — tastile-openapi

> Pointer-only dispatcher. This repository is a **generated artifact**: the
> canonical OpenAPI 3.1 spec for the Tastile v1 API. The wire-format source
> of truth lives in `tastile-core` (`crates-v1/api/src/openapi.rs`,
> `dump_openapi` binary), and the YAML in this repo is regenerated from that
> source. Do not hand-edit `openapi.yaml`.

## Canonical contract

| Topic | Read first |
| --- | --- |
| Workspace policy, invariants, branch workflow | `/home/basic/work/tastile/AGENTS.md` |
| Project-init (canonical Git remote, release refs, GH Projects) | <https://github.com/rebuildup/project-init/tree/release-0-1-1> |
| Regeneration contract (how `openapi.yaml` is produced) | This repo's `README.md` § "Regenerating `openapi.yaml`" |

All durable ticket workflow (`release-<major>-<minor>-<patch>` sprint
branches per ADR-0007, GitHub Issue-numbered ticket branches, mandatory
Draft PRs), recovery policy (ADR-0008), orchestration / worker lease
fencing, and external side-effect journals live in the parent repository.
Do not duplicate them here.

## Branch workflow (pointer)

- `main` — released / integrated state.
- `release-<major>-<minor>-<patch>` — active sprint branch (per ADR-0007).
- Ticket branches are named by GitHub Issue number only (no `feature/*` /
  `fix-*` slug prefix).
- Legacy note: the historic `publish-v1.0.0` branch was the pre-ADR-0007
  publishing lane; it is retained for archaeology but must not be
  force-renamed to match the new pattern.

## Source-language rule (§28)

- Source code, identifiers, code comments, commit messages: **English**.
- GitHub Issues, PR descriptions, internal review comments: **Japanese**.
