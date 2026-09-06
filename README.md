# tastile-openapi

Canonical OpenAPI 3.1 specification for the **Tastile v1 API**.

This repository is consumed by the Tastile workspace shell
(`tastile/tastile-root`) as a git submodule at `openapi/`. The Rust source
of truth lives in `tastile-core/crates-v1/api/src/openapi.rs` (utoipa
derive). The YAML in this repo is **regenerated** from that source by the
`dump_openapi` binary in `tastile-core` — never hand-edit `openapi.yaml`.

## Consumer repos

Each consumer reads `openapi.yaml` (or a JSON rendering produced from it)
at build time:

| Consumer | How it consumes |
| --- | --- |
| `tastile-core` | Source of truth (Rust utoipa → `dump_openapi` → this repo). |
| `tastile-web` | `bun run sync:openapi` reads `../../openapi/openapi.yaml` and produces `openapi.json` / `public/openapi.yaml`. `bunx openapi-typescript` then generates `*.d.ts`. |
| `tastile-android` | Gradle `openapi-generator` plugin input source is `../../openapi/openapi.yaml`. |
| `tastile-desktop` | Future consumer — not wired yet. |

Relative paths assume the consumer lives one directory below
`tastile-root/` in the workspace shell:

```text
tastile-root/
├── openapi/                # this submodule (mounted at openapi.yaml)
├── tastile-core/
├── tastile-web/
├── tastile-android/
└── tastile-desktop/
```

A consumer that has its own `openapi/` directory must not shadow this one;
the submodule path is fixed at the workspace-shell level.

## Versioning

Semantic Versioning. The MAJOR field increments on any breaking change
to the wire format (renamed field, removed endpoint, changed response
envelope). MINOR increments on backward-compatible additions (new
endpoint, new optional field). PATCH is reserved for cosmetic edits to
this repo (no effect on generated types).

The version is recorded in two places:

- `CHANGELOG.md` — human-readable history.
- `git tag` — canonical machine-readable version (`vMAJOR.MINOR.PATCH`).

## Regenerating `openapi.yaml`

From the workspace root (`tastile/tastile-core`):

```bash
bash .wslc/verify-run.sh \
  'cargo run --manifest-path crates-v1/Cargo.toml -p api --bin dump_openapi \
     > /tmp/openapi.json'

# Convert JSON to YAML (single-line `paths` to keep diffs small):
yq -P --indent=2 /tmp/openapi.json > ../openapi/openapi.yaml
```

The submodule pointer must then be bumped in `tastile-root`:

```bash
cd tastile-root
git add openapi
git commit -m "chore(openapi): regen from core @ <core-commit-sha>"
```

## CI contract

A submodule pointer bump in `tastile-root` is the public signal that the
API contract changed. Downstream repos (`tastile-web`, `tastile-android`)
MUST refresh their generated types before shipping a release that
includes the bump.

## Cross-repo invariants

- `openapi.yaml` is OpenAPI 3.1. Older 3.0.x specs will be rejected by
  the schema validator.
- All wire-format integers are signed smallint or int (per v1/10 §2 — no
  enum-as-string, no JSONB).
- Numeric enum values are documented in the description of each enum
  property (e.g. `CommandResult.APPLIED = 0`) — do not duplicate the
  integer values into the description, link instead.
- Operation IDs are stable across versions. Renaming an operation ID is
  a breaking change (MAJOR bump).

## License

Dual-licensed under MIT or Apache-2.0 at your option.