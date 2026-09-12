# Changelog

All notable changes to the canonical OpenAPI specification are documented
here. The version follows Semantic Versioning; a breaking change to the
wire format is a MAJOR bump.

## v1.0.0 — 2026-09-12

- First frozen contract for the 2026-09-19 free release (C07).
- 19 paths, 134 component schemas.
- Generated from `tastile-core`'s `dump_openapi` binary
  (`crates-v1/api/src/bin/dump_openapi.rs`) at core
  `release-0-6-0` `7fce98ffb7d3a47260352bfcad5b2afb9dfc6575`
  (Merge PR #131, C06 account lifecycle).
- Added (backward-compatible):
  - `POST /v1/auth/signout` (credential revoke, idempotent 204).
  - `DELETE /v1/owners/{kind}/{id}` + `OwnerDeleteResponseSchema`
    (logical delete, 30-day retention).
  - `GET /v1/owners/{kind}/{id}/export` + `OwnerExportResponseSchema`
    (NDJSON portability stream).
  - `ApiTokenCreateRequestSchema` (`POST /v1/api-tokens`, explicit
    `expires_at` per ADR-0010 §D-1.1).
- Removed (dead endpoints, routes no longer in the router; MAJOR bump
  per this repo's versioning rule):
  - `/v1/executions/{id}/facts`, `/v1/executions/{id}/tasks`,
    `/v1/plans/{id}/completion-result`, `/v1/sessions/{id}/close`.
- Kept: `RequestKindSchema::MarkTask` (server-implemented,
  `decision_runtime_repo`, kind 5).

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