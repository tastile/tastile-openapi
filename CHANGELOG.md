# Changelog

All notable changes to the canonical OpenAPI specification are documented
here. The version follows Semantic Versioning; a breaking change to the
wire format is a MAJOR bump.

## v3.3.0 — 2026-11-17

- Wire contract from `tastile-core` `release-1-1-0` @ `23630864` (Core PR #335 /
  issue #332, delivery list reads migrated to the v1/14 §15 list contract).
- 26 paths, 162 component schemas (was 24 / 160).
- Generated from `tastile-core`'s `dump_openapi` binary at that SHA;
  YAML regenerated with PyYAML `sort_keys=False` (v1.0.1+ file style).

Backward-compatible additions (MINOR bump):

- Newly documented paths: `GET /v1/sessions/{id}/deliveries`
  (`list_session_deliveries`) and `GET /v1/endpoints/{id}/deliveries`
  (`list_endpoint_deliveries`), returning `DeliveryListResponse`
  (`items` / `count` / `next_cursor`, v1/14 §15-1) with `limit` /
  `cursor` / `state` query parameters.
- `offset` remains accepted on the wire shape but is retired: any value
  is rejected by the server.
- New component schemas: `DeliveryListItemSchema` (public projection of
  a `v1_delivery` row) and `DeliveryListResponse`.

## v3.2.0 — 2026-11-17

- Wire contract from `tastile-core` `release-1-1-0` @ `ce00fab` (Core PR #329 /
  issue #326, definition read-back for lossless CLI editing).
- 24 paths, 160 component schemas (was 24 / 159).
- Generated from `tastile-core`'s `dump_openapi` binary at that SHA;
  YAML regenerated with PyYAML `sort_keys=False` (v1.0.1+ file style).

Backward-compatible addition (MINOR bump):

- `SourceTileDetailRead.definition` (`SourceTileDefinitionSchema`,
  v1/14 §17): the write-shaped definition (`tile` / `plan` / `flows` /
  `relations` / `source_client_local_id`) read back from normalized
  persisted state. `schedule` stays on `SourceTileRead.schedule`;
  `horizon` is never persisted and never returned; relation refs are
  emitted as `Existing` (v1/14 §17-2). Enables merge-or-replace edits
  (tastile-cli #12).
- `subject_source_ref` description on relation payload schemas now
  documents that read-back always emits `Existing` and clients must
  rewrite to `Local` bound to a fresh `source_client_local_id`.

## v3.1.0 — 2026-11-17

- Wire contract from `tastile-core` `release-1-1-0` @ `28b2b3c5`
  (Core PR #327 / issue #300 plus the already-integrated monthly
  contract of core #322–#325, on top of the v3.0.0 base `0c130c5`).
- 24 paths, 159 component schemas (was 23 / 146).
- Generated from `tastile-core`'s `dump_openapi` binary at that SHA;
  YAML regenerated with PyYAML `sort_keys=False` (v1.0.1+ file style).

Backward-compatible additions (MINOR bump):

- Newly documented path: `GET /v1/timeline` (`get_timeline`, v1/14 §4).
  The endpoint was already served by the API — including the
  `include_labels` opt-in that returns the non-blocking LABEL
  (`role = 1`) placements — but absent from the published contract, so
  typed consumers could not call it (core #300 / PR #327).
- New component schemas: `TimelineItemSchema` and its nested view
  schemas (`TimelineSpanSchema`, `TimelineContentSchema`,
  `TimelineVisualSchema`, `TimelineInsideSchema`,
  `TimelineSourceSchema`, `TimelineChangeKeySchema`,
  `TimelineViolationSchema`, `TimelineResolutionSchema`,
  `TimelineOwnerRefSchema`, `TimelineOwnerProfileSchema`,
  `TimelineOwnerEmbedSchema`). All discriminators are the stable
  numeric constants of v1/10 §2.
- Monthly source generation is now contractual (core #322–#325,
  integrated on `release-1-1-0` but unpublished until now):
  `SourceGenerationSchema.kind` documents `3 = monthly` and adds
  `monthly_rule` (`MonthlyRuleSchema`, required iff `kind = 3`;
  `kind` 0 = by_day / 1 = by_weekday with `day_of_month`,
  `week_of_month`, `weekday` bounds).

## v3.0.0 — 2026-11-16

- Wire contract from `tastile-core` `release-1-1-0` @
  `2a349aa1` (Core PR #313 / issue #307 on top of the v2.0.0 base
  `d3146d38`).
- 23 paths, 146 component schemas (was 20 / 140).
- Generated from `tastile-core`'s `dump_openapi` binary at that SHA;
  YAML regenerated with PyYAML `sort_keys=False` (v1.0.1+ file style).

Breaking changes (MAJOR bump):

- `GET /v1/tiles` now returns `TileListResponse`
  (`{items, count, next_cursor}`) instead of a bare array. `limit`
  default 500 -> 100, range 1..=1000 (out-of-range `422`, was silent
  clamp); `offset` -> `422`; `cursor` added. Post-projection filters
  (`lifecycle`, `search`, `exclude_future`, `range` start,
  `owner_ids`) are applied while scanning keyset batches
  (core #307 / PR #313).
- `GET /v1/candidates` now returns `CandidateListResponse` envelope
  instead of a bare array with implicit `LIMIT 500`. Ordering gains a
  `placement_id` tiebreak (core #307 / PR #313).
- `GET /v1/labels` now returns `LabelListResponse` envelope instead of
  a bare array. `limit` default 200 -> 100, clamp -> `422`; ordering
  `label ASC` via opaque text cursor (core #307 / PR #313).
- `GET /v1/sessions` now returns `SessionListResponse` envelope instead
  of a bare array with implicit `LIMIT 200`. Ordering
  `created_at DESC, id DESC` (core #307 / PR #313).
- `GET /v1/source-tiles/{id}/completion` keeps its bare-array
  complete-set shape, but all paging parameters (`limit`, `cursor`,
  `offset`, `occurrence_*`, `placement_*`) are now rejected `422`
  instead of silently ignored (core #307 / PR #313).
- Newly documented paths (previously absent from the spec):
  `GET /v1/candidates` (`list_candidates`), `GET /v1/labels`
  (`list_labels`), `GET /v1/sessions` (`list_sessions`).

Contract clarifications (non-breaking):

- Non-numeric paging params surface as axum `400` at deserialize;
  numeric out-of-range / `offset` / corrupt or foreign cursor remain
  `422` `VALIDATION` (v1/14 §15-2 note).
- `/v1/tiles` `view_mode`/`granularity` are accepted but ignored;
  `range` uses only its start bound (v1/14 §15-3 note).
- Remaining uncontracted list surfaces (deliveries, recurring
  instances, endpoints, access/*, api-tokens, prompts/pending,
  schedule-reference-catalog, calendar/*) are explicitly out of §15
  scope pending core#315 (v1/14 §15-5).

## v2.0.0 — 2026-11-16

- Wire contract from `tastile-core` `release-1-1-0` @
  `d3146d38305c8bc27074195c24f642ca25b02212` (Core PRs #302–#306 plus
  the #310/#311 CI-trigger change; issues #294–#298).
- 20 paths, 140 component schemas (was 19 / 133).
- Generated from `tastile-core`'s `dump_openapi` binary
  (`crates-v1/api/src/bin/dump_openapi.rs`); YAML regenerated with
  PyYAML `sort_keys=False` to match the v1.0.1 file style.

Breaking changes (MAJOR bump):

- `ReflowSourceTileRequest.expected_revision` is now **required**
  (non-null `int64`). Requests that omit it are rejected `422`;
  a stale revision is rejected `409` (core #298 / PR #302).
- `GET /v1/source-tiles` now returns `SourceTileListResponse`
  (`{items, count, next_cursor}`) instead of a bare array. Supports
  `limit` (default 100, max 1000) and an opaque `cursor`; `offset`
  is rejected `422` (core #297 / PR #305).
- `GET /v1/source-tiles/{id}/placements` now returns
  `PlacementTileListResponse` (same envelope) instead of a bare array.
- `SourceTileDetailRead.occurrences` and `.placements` are now paged
  `{items, count, next_cursor}` objects instead of arrays. Occurrence
  and placement cursors are independent (core #297 / PR #305).

Added (backward-compatible):

- `GET /v1/placements` (`list_placements`) → `PlacementListResponse`.
- `SourceOccurrenceRead.blocked` → `SourceOccurrenceBlockedRead`
  exposing the numeric unplaced reason (`0` INSUFFICIENT_CAPACITY,
  `1` LIMIT_CONFLICT, `2` PRIORITY_TIE_CONFLICT),
  `conflicting_source_tile_id`, and `blocked_at` (core #295 / PR #304).
- `AggregateMetaSchema.unplaced_occurrences` listing occurrences that
  could not be placed, with reasons (core #295 / PR #304).
- `limit`/`cursor` query parameters and `422` validation responses on
  the migrated list endpoints.

Non-wire behavioral notes for consumers: same-priority conflicts now
resolve deterministically (`priority DESC, source_tile_id ASC`) and the
loser stays visible as a blocked occurrence; source-tile update/reflow
is scoped to the target source so unrelated placements are preserved
(core #294 / PR #306). Cursor pagination replaces the previous silent
caps, so clients must follow `next_cursor` to retrieve complete lists.

## v1.0.1 — 2026-09-27

- Issue #153 wire contract refresh for the 2026-09-27 production release
  train (Core 1.0.1, Web 1.0.2, CLI 1.0.0).
- 19 paths, 133 component schemas (path / schema count unchanged from
  v1.0.0; 153 work in Core is internal — `AuthContext`, `ScopeSet`,
  central `require()` middleware — not exposed as new endpoints on the
  Core surface).
- Generated from `tastile-core`'s `dump_openapi` binary
  (`crates-v1/api/src/bin/dump_openapi.rs`) at core `release-1-0-1`
  `8beacd06497ee81e49cdb5f1a0ca0eaf4fb410cd` (3 cherry-picked 153
  commits: `30ab141` granular API-token scope + AuthContext central
  pattern, `f729831` complete central `require()` pattern across
  handler inventory, `8beacd0` `Scope::All` acts as wildcard +
  parse rejects empty input).
- Added (backward-compatible):
  - `ScopeSchema` (`tastile.read` | `tastile.write` | `tastile.all` —
    enum-style three-state scope type surfaced to clients that mint
    API tokens via the new granular path).
  - `x-tastile-required-scope` extension attribute on existing
    endpoint operations (e.g. `signout`: `none`). Lets clients and
    Code generators enforce per-route scope checks without parsing
    prose descriptions.
- Note: Web-side `/cli/authorize` and `/api/cli/token` (the PKCE flow
  for tastile-cli) live in `tastile-web`, not Core, and are not part
  of this spec. Their wire contract is documented in
  `docs/cli/issue-153.md` of `tastile-web`.

## v1.0.0 — 2026-09-12

- Release frozen contract for the 2026-09-19 free release (C07;
  core #132 merged, final regen from the integrated SHA verified
  zero-diff).
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