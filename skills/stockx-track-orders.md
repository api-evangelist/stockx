---
name: Track StockX sales orders and shipping documents
description: List active and historical sales, read an individual order, and fetch its shipping documents.
api: openapi/stockx-public-openapi-original.json
operations: [GetOrders, GetHistoricalOrders, GetOrder, GetOrderShipmentDocuments, GetOrderShipment]
---

# Track StockX sales orders and shipping documents

Use this to fulfill and reconcile sales.

## Auth
`x-api-key` + `Authorization: Bearer <token>` on every call. Base URL `https://api.stockx.com/v2`.

## Steps
1. **Active sales** — `GetOrders` (`GET /selling/orders/active`) with `pageNumber`/`pageSize`, filterable by `orderStatus`, `productId`, `variantId`.
2. **History** — `GetHistoricalOrders` (`GET /selling/orders/history`) with a `fromDate`/`toDate` window for reconciliation.
3. **One order** — `GetOrder` (`GET /selling/orders/{orderNumber}`) for full detail.
4. **Shipping docs** — `GetOrderShipmentDocuments` (`GET /selling/orders/{orderNumber}/shipping-document`) then `GetOrderShipment` (`GET /selling/orders/{orderNumber}/shipping-document/{shippingId}`) to fetch the label/PDF for a pending shipment.

## Rules
- Shipping-document responses can be `application/pdf` — handle binary, not just JSON.
- Rate limit: 1 req/s, 25,000/24h. Errors are `{ statusCode, message }`; `404` means the order number is unknown to your seller account.
