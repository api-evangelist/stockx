---
name: Bulk-list inventory on StockX with batch operations
description: Submit many listings in one batch, then poll the batch and its items for per-item success or rejection.
api: openapi/stockx-public-openapi-original.json
operations: [CreateListings, GetListingCreateBatch, GetListingCreateBatchItems]
---

# Bulk-list inventory on StockX with batch operations

Use this to list many items at once (ERP / inventory sync).

## Auth
`x-api-key` + `Authorization: Bearer <token>` on every call. Base URL `https://api.stockx.com/v2`.

## Steps
1. **Submit the batch** — `CreateListings` (`POST /selling/batch/create-listing`) with an array of items (each a `variantId` + amount + currency). Returns a `batchId`.
2. **Poll the batch** — `GetListingCreateBatch` (`GET /selling/batch/create-listing/{batchId}`) for overall status counts (queued / completed / failed).
3. **Inspect items** — `GetListingCreateBatchItems` (`GET /selling/batch/create-listing/{batchId}/items`), optionally filtered by `status`, to see which items succeeded and which were rejected (with rejection detail).

## Rules
- The batch is asynchronous; poll `{batchId}` until the status counts settle before treating results as final.
- Update and delete have parallel batch flows: `UpdateListings` / `DeleteListings` with matching `Get...Batch` + `Get...BatchItems` pollers.
- Rate limit: 1 req/s, 25,000/24h — batch endpoints let you stay under it. Errors are `{ statusCode, message }`.
