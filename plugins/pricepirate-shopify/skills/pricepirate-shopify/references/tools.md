# pricepirate-shopify tools

The MCP tools mirror the PricePirate public API one to one. Each tool name is the API operation name in snake_case. The REST path is listed so you can match tools with the API documentation at https://app.pricepirate.com.

Tools marked "writes" change data in PricePirate. Tools marked "Shopify prices" change live prices in the user's store.

## Shop

| Tool | REST | Notes |
|---|---|---|
| `shop_get` | `GET /shop` | Shop domain, name, currency, country. Call once at the start. |

## Shopify catalog (read-only)

| Tool | REST | Notes |
|---|---|---|
| `catalog_variants_search` | `GET /catalog/variants` | Search Shopify variants by title. Paginate with `after`. |
| `catalog_products_list` | `GET /catalog/products` | Products and variants with SKU, barcode, price, stock, unit cost, weight and whether each variant is already tracked. Filters: query, vendor, tag, collection, status. |
| `catalog_variants_lookup` | `POST /catalog/variants/lookup` | Up to 250 EANs or SKUs per call. Returns matched variants and the codes that did not match. |
| `catalog_collections_list` | `GET /catalog/collections` | Collections, for "track everything in collection X". |

## Tracked variants (writes)

| Tool | REST | Notes |
|---|---|---|
| `tracked_variants_list` | `GET /tracked-variants` | Filter by `variants` (Shopify variant IDs), status or collection. |
| `tracked_variants_enable` | `POST /tracked-variants` | Imports variants from exactly one source: `productIds` or `collectionId`. |
| `tracked_variants_activate` | `POST /tracked-variants/activate` | Starts listing synchronization. Price changes appear only once a strategy is set. |
| `tracked_variants_deactivate` | `POST /tracked-variants/deactivate` | Pauses a variant and keeps its configuration. Prefer this over delete. |
| `tracked_variants_delete` | `DELETE /tracked-variants` | Removes variants and orphaned listings. Ask first. |
| `tracked_variants_update_pricing_strategy` | `PATCH /tracked-variants/pricing-strategy` | Assigns or clears a strategy or template for one variant. With a template bound, the template's market wins. |

## Listings (writes)

| Tool | REST | Notes |
|---|---|---|
| `listings_get_listing_data` | `GET /listings` | Status, match suggestions and connected listing for a variant, or a listing by `source`, `country` and `externalId`. |
| `listings_match` | `POST /listings/matches` | The body is a JSON array, not an object. |
| `listings_connect` | `PUT /listings/connections` | Connects one variant to one suggested listing (`listingKey`). |
| `listings_disconnect` | `DELETE /listings/connections` | Disconnects the listing for one variant and market. |
| `listings_update_snapshot_settings` | `PATCH /listings/snapshot-settings` | Daily snapshot count and time windows. More snapshots use more credits. |

## Pricing (writes)

| Tool | REST | Notes |
|---|---|---|
| `pricing_get_strategies` | `GET /pricing-strategies` | Available strategies and their argument schemas. Read before creating anything. |
| `pricing_list_templates` | `GET /pricing-templates` | Saved templates. |
| `pricing_create_template` | `POST /pricing-templates` | `priceChangeSource` and `priceChangeCountry` go together (both or neither). `dynamicDiscountsEnabled` needs a plan with dynamic discounts. |
| `pricing_update_template` | `PATCH /pricing-templates/{id}` | Changing the market re-prices every bound variant. |
| `pricing_delete_template` | `DELETE /pricing-templates/{id}` | Ask first. |

## Price changes (Shopify prices)

| Tool | REST | Notes |
|---|---|---|
| `price_updates_list` | `GET /price-changes` | Filter by `statuses`, `timeRange`, variants or collection. `perPage` is 10, 20, 50 or 100. |
| `price_updates_apply_price_changes` | `POST /price-changes/apply` | Changes live Shopify prices. Pass `priceChangeIds` or `all=true`, never both. Confirm with the user first. |
| `price_updates_reject` | `POST /price-changes/reject` | Rejects pending changes. |
| `price_updates_rerun_price_change` | `POST /price-changes/recalculate` | Recalculates the proposal for one variant. Use it to preview a new strategy. |

## Metrics and reports (read-only)

| Tool | REST | Notes |
|---|---|---|
| `product_metrics_list` | `GET /product-metrics` | Product-level metrics with sorting and filters. |
| `variant_metrics_get_variant_metrics` | `GET /variant-metrics` | Time series for one variant. |
| `reports_get` | `GET /reports/{report}` | One report per call, see the list below. |

### Reports

| Report | Answers |
|---|---|
| `average-price-trend` | How the average market price moves over time |
| `cheapest-trend` | How the cheapest offer moves over time |
| `competitor-leaderboard` | Which competitors appear most and how they price (paginated, searchable) |
| `headroom-and-exposure` | Where the shop could raise prices, and where it is exposed to being undercut |
| `offers-trend` | How the number of offers changes |
| `position-distribution` | How often the shop is in position 1, 2, 3 and so on |
| `price-ranges` | Price spread per product |
| `price-change-funnel` | How many price changes were proposed, applied and rejected |
| `price-change-status-trend` | Price-change statuses over time |
| `price-direction-heatmap` | Whether prices went up or down, by product and day |
| `volatility-list` | Products with the most volatile prices |
