---
name: Create and manage a StockX seller listing
description: Create a single ask/listing for a product variant, poll the async operation, then activate, deactivate, or delete it.
api: openapi/stockx-public-openapi-original.json
operations: [Search, GetVariants, Create, GetListingOperations, FindById, ActivateListing, DeactivateListing, DeleteListing]
---

# Create and manage a StockX seller listing

Use this to put one item up for sale and manage its lifecycle.

## Auth
Both `x-api-key` and `Authorization: Bearer <token>` are required on every call. Base URL `https://api.stockx.com/v2`.

## Steps
1. **Resolve the variant** — `Search` (`GET /catalog/search`) → `GetVariants` (`GET /catalog/products/{productId}/variants`) to get the exact `variantId` (size) to sell.
2. **Create the listing** — `Create` (`POST /selling/listings`) with the `variantId`, amount, and currency. This is asynchronous: it returns an operation.
3. **Poll the operation** — `GetListingOperations` (`GET /selling/listings/{listingId}/operations`) or `FindOperationById` until the create succeeds.
4. **Read the listing** — `FindById` (`GET /selling/listings/{listingId}`) to confirm status.
5. **Manage state** — `ActivateListing` (`PUT /selling/listings/{listingId}/activate`), `DeactivateListing` (`PUT /selling/listings/{listingId}/deactivate`), or `DeleteListing` (`DELETE /selling/listings/{listingId}`).

## Rules
- Writes are asynchronous — never assume success from the `201`; poll the operation.
- No idempotency-key header exists; guard against duplicate `Create` calls yourself by checking existing listings first (`GetAllListings`, `GET /selling/listings`).
- Rate limit: 1 req/s, 25,000/24h. Errors are `{ statusCode, message }`.
