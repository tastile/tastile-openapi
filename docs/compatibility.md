# Compatibility — OpenAPI spec version vs Core product version

> tastile-root `docs/adr/0013-independent-product-versioning.md` §5 の実装。
> OpenAPI の版と Core の製品版は **独立した SemVer 系列** であり、互換性は
> バージョン番号の一致ではなく **契約検証** と本マトリクスで保証する。

## 1. 3 つの版軸

| 軸 | 正本 | 現在値 | bump 条件 |
| --- | --- | --- | --- |
| spec リリース版 | git tag `vX.Y.Z` + `CHANGELOG.md` | `v3.8.0` | wire format の互換性変化 (MAJOR=破壊的 / MINOR=後方互換追加 / PATCH=cosmetic) |
| API 契約版 | `openapi.yaml` `info.version` | `1.2.0` | v1 API 面の契約版。spec リリース版とも Core 製品版とも独立 |
| Core 製品版 | `tastile-core` `crates-v1` workspace version | `2.0.0` (release-2-0-0) / `1.0.2` (release-1-1-0 系) | Core の release train が決定 |

**要求しないこと**: spec 版 = Core 版の一致、`info.version` = spec 版の一致、
`info.version` = Core 版の一致。いずれも独立に進化する。

## 2. 互換性マトリクス

| spec 版 | tag / commit | 生成元 Core | Core 側の版・SHA | consumer pin | 検証証跡 |
| --- | --- | --- | --- | --- | --- |
| v3.7.0 | `v3.7.0` / `86e4964` | `dump_openapi` @ `release-1-1-0` | 製品版系 1.x / `1380ec30` | root `openapi/` submodule、web PR #238、cli PR #27、android PR #114 | byte-identical regen 確認済み (core `368` branch `baf72742` 生成物と一致) |
| v3.8.0 | `v3.8.0` (tag pending merge) / branch `27` | `dump_openapi` @ `release-2-0-0` | 製品版 2.0.0 / `e8219616` (core PR #400) | 未更新 — consumer regen は merge 後 | redocly lint 0 errors (旧 58) / semantic diff 0 ops・0 schemas 変更 |

過去版の対応は `CHANGELOG.md` を参照。各エントリが生成元 Core SHA を記録する。

## 3. 契約更新の連鎖 (drift を構造的に防ぐ)

Core の API 契約が変わった場合、以下の順で連鎖する。**Core 製品版の
bump はこの連鎖を自動的に要求しない** — 契約が変わった時だけ動く。

1. Core `release-*` 上で `dump_openapi` を再生成し、前版との semantic diff
   (path/schema/operation の追加・変更・削除) を確認
2. `openapi.yaml` を再生成し、semver 判定 (breaking → MAJOR / additive →
   MINOR / cosmetic → PATCH) で `CHANGELOG.md` に生成元 Core SHA と共に記録
3. spec 版 tag (`vX.Y.Z`) を打つ
4. consumer (web / cli / android / desktop) が submodule pin を更新し、
   生成 client の再生成・型検査・drift check を実行
5. tastile-root `docs/releases/version-matrix.json` の `contract_pins` を更新

逆方向 (consumer が先に pin を上げる) は行わない。常に Core → spec →
consumer → root の一方向。

## 4. 検証手段 (版番号に依存しない保証)

- **byte-identical regen**: Core `dump_openapi` 出力を YAML 化したものと
  `openapi.yaml` が一致すること (v3.7.0 で実施済み)
- **router/OpenAPI parity**: Core `crates-v1/api/tests/openapi_router_parity.rs`
  が実 router と schema の method/parity を検証
- **consumer drift check**: cli `openapi-drift` workflow、web/cli の型再生成
  + typecheck、android `generateV1Api` + coverage guard
- **既知債務**: Redocly lint baseline は tastile-openapi#27 で追跡
  (main でも同種 error が存在。本マトリクスは lint-clean を保証しない)
