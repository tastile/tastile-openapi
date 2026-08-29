# Changelog

All notable changes to the canonical OpenAPI specification are documented
here. The version follows Semantic Versioning; a breaking change to the
wire format is a MAJOR bump.

## Unreleased

- None yet.

## v0.1.0 — 2026-08-29

- First published canonical OpenAPI 3.1 spec for the Tastile v1 API.
- 20 paths, 137 component schemas.
- Generated from `tastile-core`'s `dump_openapi` binary
  (`crates-v1/api/src/bin/dump_openapi.rs`).
- README documents the consumer wiring per repo (core, web, android,
  desktop).
- OpenAPI contract invariants enforced by
  `tastile-core/crates-v1/api/tests/openapi_schema.rs` and
  `openapi_router_parity.rs`:
  - No `serde_json::Value` schemas anywhere in the doc.
  - No `semantic_role` field anywhere (v1/10 §10 ban on
    "is this a break?" discriminator).
  - `TileListView` exposes the visible wire set: `id`, `title`,
    `lifecycle`, `worked_minutes`, `break_minutes`, `temporal`,
    `recurrence` (plus implementation-added `done_definition`,
    `done_rule`, `labels`, `next_action`, `objective_mode`, `plan_id`,
    `projected_next_start_at`, `resume_note`, `source`,
    `target_rest_min`, `target_work_min`).
  - `SourceTileRead.source_state`, `SourceGenerationSchema.weekday_mask`,
    and the `excluded_dates` default of `[]` are present.