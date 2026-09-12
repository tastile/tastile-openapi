# Changelog

All notable changes to the canonical OpenAPI specification are documented
here. The version follows Semantic Versioning; a breaking change to the
wire format is a MAJOR bump.

## v1.0.0 — 2026-09-12

- Release candidate contract for the 2026-09-19 free release (C07;
  freeze pending core #132 merge + final regen from the integrated SHA).
- 19 paths, 133 component schemas.
- Generated from `tastile-core`'s `dump_openapi` binary
  (`crates-v1/api/src/bin/dump_openapi.rs`) at core
  `release-0-6-0` `3fa63fa59afd4cabcf2a5dca2fddbe1bb2e79539`
  (Merge PR #132, review fixes + C04F snapshot-key fix; spec output
  verified zero-diff against this revision).
- Added (backward-compatible):
  - `POST /v1/auth/signout` (credential revoke, idempotent 204 only).
  - `DELETE /v1/owners/{kind}/{id}` + `OwnerDeleteResponseSchema`
    (logical delete, 30-day retention; `kind = 0` only, `1..4` → 404).
  - `GET /v1/owners/{kind}/{id}/export` (`application/x-ndjson`,
    one `{ table, row }` object per line; `kind = 0` only).
    No metadata component by design — the operation documents the
    line contract inline.
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