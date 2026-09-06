---
name: Run purchasing from order to receipt to invoice
description: >-
  Create a Maintenance Connection purchase order, add line items, receive against it, and record
  the supplier invoice — the CMMS procurement chain.
api: openapi/accruent-maintenance-connection-openapi.yml
operations:
- Companies_Get
- Parts_Get
- PartVendors_Get
- PurchaseOrders_Post
- PurchaseOrders_Get
- PurchaseOrderLineItems_Post
- Receipts_Post
- ReceiptLineItems_Post
- Invoices_Post
- PurchaseOrderStatusUpdates_GetStatusUpdatesForPurchaseOrder
generated: '2026-09-06'
method: generated
source: openapi/accruent-maintenance-connection-openapi.yml
---

# Purchase order to receipt to invoice

Base URL `https://api.maintenanceconnection.com/v8`. Auth is HTTP Basic over
base64("ConnectionKey:APIKey") on HTTPS.

## Stop and read this before any POST

This chain writes financial records — purchase orders, receipts, invoices — and **the API provides
no way to reverse any of them.** There is no DELETE, no void, no cancel, and the purchase order
status surface is read-only. Get explicit human confirmation before every step below, and never
run this flow speculatively or as a retry of an uncertain earlier attempt.

There is also no idempotency key. If a POST times out, do **not** repost. Query
`PurchaseOrders_Get` with an `$filter` on a value you set and confirm whether the record landed.

## 1. Resolve the vendor and the parts

- `Companies_Get` — `GET /Companies?$filter=...` for the vendor record
- `Parts_Get` — `GET /Parts?$filter=...`
- `PartVendors_Get` — `GET /Parts/{partPK}/vendors` for the vendor-specific part record and pricing

## 2. Create the purchase order

`PurchaseOrders_Post` — `POST /purchaseorders`

Related records go in as `EntityRefViewModel` stubs carrying IDs you resolved in step 1. A `409`
means the ID is not unique.

## 3. Add line items

`PurchaseOrderLineItems_Post` — `POST /PurchaseOrders/{purchaseOrderPK}/LineItems`
(up to 200 per batch)

## 4. Receive

- `Receipts_Post` — `POST /PurchaseOrders/{purchaseOrderPK}/Receipts`
- `ReceiptLineItems_Post` — `POST /PurchaseOrders/{purchaseOrderPK}/Receipts/{receiptPK}/LineItems`

Receiving moves inventory. Verify against `PartLocations_Get` afterwards.

## 5. Invoice

`Invoices_Post` — `POST /PurchaseOrders/{purchaseOrderPK}/Invoices`

## 6. Read status

`PurchaseOrderStatusUpdates_GetStatusUpdatesForPurchaseOrder` —
`GET /PurchaseOrders/{purchaseOrderPK}/StatusUpdates`. Read-only.

## Errors and limits

Same table as every other Maintenance Connection flow: 400 validation, 401 credential or non-SSL,
**403 rate limit at 200 requests/minute** (not 429, no `Retry-After`), 404 missing, 409 duplicate
ID, 500 internal, 501 not implemented. The spec declares none of them — all 255 operations declare
only a 200 — so a generated client will not surface them for you.
