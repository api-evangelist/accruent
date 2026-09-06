---
name: Create and dispatch a Maintenance Connection work order
description: >-
  Create a work order against an asset in Accruent Maintenance Connection, add its tasks, assign
  labor, and record the actual labor and parts consumed — the core CMMS flow.
api: openapi/accruent-maintenance-connection-openapi.yml
operations:
- Assets_Get
- WorkOrders_Post
- WorkOrders_Get
- WorkOrderTasks_Post
- WorkOrderAssignments_Post
- WorkOrderLaborCostActuals_Post
- WorkOrderPartActuals_Post
- WorkOrderStatusUpdates_GetStatusUpdatesForWorkOrder
generated: '2026-09-06'
method: generated
source: openapi/accruent-maintenance-connection-openapi.yml
---

# Create and dispatch a work order

Base URL `https://api.maintenanceconnection.com/v8`. Every operation below is verified present in
Accruent's published Swagger 2.0 document.

## Before you start — read this

**This API has no undo.** There is no DELETE operation on any of its 126 paths, and the two status
surfaces (`WorkOrderStatusUpdates_GetStatusUpdatesForWorkOrder`,
`PurchaseOrderStatusUpdates_GetStatusUpdatesForPurchaseOrder`) are GET-only, so you cannot write a
cancelling transition either. A work order you create through this API stays created. Confirm with
the human before the POST, not after.

**There is no idempotency key.** If a POST times out you cannot safely retry it. Re-read with
`WorkOrders_Get` and an `$filter` on a field you set before creating again.

## Authenticate

Send `Authorization: Basic <base64 of "ConnectionKey:APIKey">` on every request, over HTTPS.
Plain HTTP returns 401 even with a valid credential. Set `Accept: application/json` and
`Content-Type: application/json`.

## 1. Find the asset

`Assets_Get` — `GET /Assets?$filter=<expr>&$top=<n>`

Filter with the documented OData subset: `eq ne gt ge lt le and or not` plus `( )` grouping, and
dotted traversal into `Ref` elements, e.g. `RepairCenterRef.ID eq "Main"`. Default page size is 50,
maximum 500, paged with `$top` and `$skip`. There is no total-count field, so do not assume you
have seen every match until a page comes back short.

Read the asset's `PK` from the response — it is the `assetPK` every child route needs.

## 2. Create the work order

`WorkOrders_Post` — `POST /workorders`

Post one `WorkOrderViewModel`, or up to 200 in a batch. Related records go in as
`EntityRefViewModel` stubs (`RepairCenterRef`, `AssetRef`, `LaborRef`, …) carrying an ID, not as
embedded objects. Resolve those IDs first with `Classifications_Get`, `Companies_Get`,
`Labors_Get` or `LookupTableValues_Get` rather than guessing them.

A `409 Conflict` means the ID you supplied is not unique — fetch the existing record and use
`WorkOrders_Put` instead of posting again.

## 3. Add tasks

`WorkOrderTasks_Post` — `POST /WorkOrders/{workOrderPK}/Tasks`

## 4. Assign labor

`WorkOrderAssignments_Post` — `POST /WorkOrders/{workOrderPK}/Assignments`

Look labor PKs up with `Labors_Get` first.

## 5. Record what was actually consumed

- `WorkOrderLaborCostActuals_Post` — `POST /WorkOrders/{workOrderPK}/LaborActuals`
- `WorkOrderPartActuals_Post` — `POST /WorkOrders/{workOrderPK}/PartActuals`

Part actuals draw down inventory. Check availability with `PartLocations_Get` before posting.

## 6. Read status history

`WorkOrderStatusUpdates_GetStatusUpdatesForWorkOrder` — `GET /WorkOrders/{workOrderPK}/StatusUpdates`

Read-only. You can see how the work order moved; you cannot move it from here.

## Errors and limits

| Status | Meaning | What to do |
|---|---|---|
| 400 | Validation exception | Fix the body or the query option |
| 401 | Bad credential, or not over SSL | Check the Basic token and the scheme |
| 403 | **Rate limit** — 200 requests/minute exceeded | Back off; this API does not use 429 and returns no `Retry-After` |
| 404 | Resource does not exist | Re-resolve the PK |
| 409 | Conflict — ID not unique | `PUT` instead of `POST` |
| 500 | Internal error | Contact Maintenance Connection support |
| 501 | Not implemented | Do not retry |

None of these are declared in the spec — all 255 operations declare only a 200 — so handle them
from this table, not from a generated client.
