---
name: Read the asset register and post meter readings
description: >-
  Walk an Accruent Maintenance Connection asset register, read its specifications and documents,
  and post meter readings that drive usage-based preventive maintenance.
api: openapi/accruent-maintenance-connection-openapi.yml
operations:
- Assets_Get
- Assets_Post
- Assets_Put
- AssetSpecifications_Get
- AssetMeterHistory_GetMeterHistoryForAsset
- AssetMeterHistory_Post
- AssetMeterHistory_Put
- AssetDocuments_Get
- Classifications_Get
- Schema_Get
generated: '2026-09-06'
method: generated
source: openapi/accruent-maintenance-connection-openapi.yml
---

# Read the asset register and post meter readings

Base URL `https://api.maintenanceconnection.com/v8`. Auth is HTTP Basic over
base64("ConnectionKey:APIKey") on HTTPS.

## Discover the shape first

`Schema_Get` — `GET /Schema/{model}`

The API ships a schema endpoint. Use it rather than assuming field names; Accruent publishes no
object reference outside the help pages, and the response bodies are wrapped in the `ApiResponse`
envelope rather than returning the entity directly.

## Walk the register

`Assets_Get` — `GET /Assets` (collection) and `GET /Assets/{assetPK}` (one record)

Page with `$top` (max 500) and `$skip`; sort with `$orderby`; narrow with `$filter`. A useful
incremental pull is `$filter=LastModifiedDate gt "YYYY-MM-DD"`, which Accruent gives as a worked
example. Resolve classification IDs with `Classifications_Get`.

## Read the detail

- `AssetSpecifications_Get` — `GET /Assets/{assetPK}/specifications/{PK}` or `GET /AssetSpecifications/{assetPK}`
- `AssetDocuments_Get` — `GET /Assets/{assetPK}/documents`
- `AssetImages_Get` — `GET /Assets/{assetPK}/images`

## Post a meter reading

`AssetMeterHistory_Post` — `POST /Assets/{assetPK}/meterhistory`
(and `POST /Assets/{assetPK}/meternumberhistory` for a named meter)

Correct a reading with `AssetMeterHistory_Put` on
`PUT /Assets/{assetPK}/meterhistory/{historyPK}`. That update is the only correction path — there
is no delete — so a reading posted against the wrong asset can be overwritten in place but never
removed.

Read back with `AssetMeterHistory_GetMeterHistoryForAsset`
(`GET /Assets/{assetPK}/meterhistory`) or
`AssetMeterHistory_GetMeterNumberHistory`
(`GET /Assets/{assetPK}/meternumberhistory/{meterNumber}`).

## Create or update assets

`Assets_Post` / `Assets_Put`, up to 200 records per batch. Both carry no idempotency guard: a
retried create after a timeout makes a second asset. Read back with a `$filter` on a field you set
before retrying.

## Watch the budget

200 requests per minute per connection, and exhaustion returns **403**, not 429, with no
`Retry-After`. A full register walk at page size 500 is the cheapest way through; do not fan out
per-asset detail calls without pacing them.
