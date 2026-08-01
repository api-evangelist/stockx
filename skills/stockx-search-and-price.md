---
name: Search the StockX catalog and read market data
description: Find a product on StockX, resolve its variants (sizes), and read current bid/ask market data to price a sale.
api: openapi/stockx-public-openapi-original.json
operations: [Search, GetProduct, GetVariants, GetVariantMarketData, GetProductMarketData]
---

# Search the StockX catalog and read market data

Use this to look up a product and understand its live market before listing.

## Auth
Every request needs both headers:
- `x-api-key: <your developer-program API key>`
- `Authorization: Bearer <Auth0 access token>` (Authorization Code flow, audience `gateway.stockx.com`)

Base URL: `https://api.stockx.com/v2`.

## Steps
1. **Search** — call `Search` (`GET /catalog/search`) with `query`, `pageNumber`, `pageSize`. Read the matching `productId`.
2. **Product detail** — call `GetProduct` (`GET /catalog/products/{productId}`) for title, brand, and category.
3. **Variants** — call `GetVariants` (`GET /catalog/products/{productId}/variants`) to list sizes and their `variantId`s.
4. **Market data** — call `GetVariantMarketData` (`GET /catalog/products/{productId}/variants/{variantId}/market-data`) with `currencyCode` and `country` for the size you care about, or `GetProductMarketData` (`GET /catalog/products/{productId}/market-data`) for the whole product.

## Rules
- Respect the rate limit: 1 request/second, 25,000/24h (resets 00:00 UTC). Back off on `429`.
- Errors return `{ statusCode, message }` (JSON), not problem+json. Treat `401` as an auth/token problem, `404` as an unknown product/variant.
- Paginate search with `pageNumber`/`pageSize`.
