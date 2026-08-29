# Changelog

All notable changes to the canonical OpenAPI specification are documented
here. The version follows Semantic Versioning; a breaking change to the
wire format is a MAJOR bump.

## Unreleased

- Initial scaffold extracted from
  `tastile-core/crates-v1/api/src/openapi.rs` (utoipa derive).
- First published version will be tagged `v0.1.0` once the YAML matches
  the in-tree `dump_openapi` output byte-for-byte.

## v0.1.0 (planned)

- Single `openapi.yaml` containing every `/v1/*` route currently
  registered by `crates-v1/api` plus the `TileListView` /
  `TemporalView` / `RecurrenceView` mirror schemas used by
  `tastile-web`.
- README documents the consumer wiring per repo.