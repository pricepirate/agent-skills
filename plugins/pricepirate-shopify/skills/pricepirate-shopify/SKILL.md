---
name: pricepirate-shopify
description: Sets up and operates PricePirate, the repricing app for Shopify stores selling on Idealo and Google Shopping, through the pricepirate-shopify MCP server. Use when the user wants to import Shopify products into PricePirate, connect them to marketplace listings, design or change pricing strategies, review or apply price changes, or analyse competitors and marketplace positions.
---

# PricePirate for Shopify

PricePirate is a Shopify app. It watches the marketplace listings (Idealo, Google Shopping and others) of a shop's products, compares the shop's offer with competitors, and proposes or applies new Shopify prices according to a pricing strategy.

All data access goes through the `pricepirate-shopify` MCP server. It is scoped to the one Shopify store the user approved during sign-in, and it can do everything the shop's API key can do.

## Before you start

1. Check that the `pricepirate-shopify` tools are available. If they are missing, tell the user to connect the server (URL `https://app.pricepirate.com/mcp`) and approve access in Shopify. Never ask for an API key or token.
2. Call `shop_get` and greet the user with the shop name, so both of you know which store you are working on.
3. Answer in the user's language. Most PricePirate users write German; use the formal "Sie" in German.

## Safety rules

These rules apply to every workflow.

- Price changes become real Shopify prices. Never call `price_updates_apply_price_changes` without showing the user the exact changes (product, old price, new price) and getting an explicit yes for that set.
- Never apply with `all=true` unless the user asked for all pending changes in this conversation.
- Prefer reversible steps. Deactivating a tracked variant keeps its configuration; deleting it does not. Ask before any delete.
- Never change prices in Shopify directly. PricePirate only changes prices through its own price changes.
- A strategy without a minimum price or unit cost can undercut the shop's margin. Before assigning a strategy, confirm how the floor is set (unit cost plus margin, or a fixed minimum price).
- Auto-apply is a shop setting in the PricePirate app and cannot be changed through the API. Recommend keeping it off until the user has reviewed the first price changes, and tell them where to find it (PricePirate → Einstellungen → Preisänderungen).
- Work in batches the user can review (for example 20 to 50 variants) and report progress after each batch.

## Workflows

### Find products to import

Goal: show the user which Shopify products are not in PricePirate yet.

1. Search the Shopify catalog with the catalog tools (by title, vendor, tag, collection, or a list of EANs or SKUs).
2. Compare with `tracked_variants_list` to mark which variants are already tracked.
3. Summarise: how many variants exist, how many are tracked, and the untracked ones grouped by vendor or collection. Point out variants without a barcode (EAN/GTIN); they are much harder to match to marketplace listings.

### Import products

Goal: track a set of Shopify products in PricePirate and connect them to marketplace listings.

1. Agree on the scope (a collection, a vendor, a supplier file with EANs, or an explicit list).
2. If the user provides a supplier list (CSV, Excel, pasted text), extract the EANs or SKUs and look them up in the Shopify catalog. Report matched and unmatched codes before importing. Do not create products in Shopify; only existing products can be imported.
3. Import with `tracked_variants_enable`, using either `productIds` or one `collectionId` per call.
4. Request listing matches with `listings_match`, then read the suggestions with `listings_get_listing_data`.
5. Connect a listing with `listings_connect` only when the match is clear (same EAN, or same brand, model and variant). List uncertain matches for the user instead of guessing.
6. Activate the connected variants with `tracked_variants_activate`. Each plan limits the number of active variants and daily credits. If a call fails because of a plan limit, stop, tell the user how many variants were activated, and point them to the plan selection in the app instead of retrying.

### Design a pricing strategy

Goal: give each tracked variant a strategy that matches the user's goals.

1. Call `pricing_get_strategies` to read the available strategies and their argument schemas. Do not assume argument names; use the schema.
2. Ask for the decisions the strategy needs: the target position (for example cheapest, or top 3), the floor (unit cost plus minimum margin, or a fixed minimum price), shipping cost if the strategy compares total prices, and rounding.
3. Use Shopify data where it helps: unit cost and current price from the catalog, competitor prices and positions from `listings_get_listing_data`, `variant_metrics_get_variant_metrics` and the reports.
4. Prefer one template for many variants: create it with `pricing_create_template` and bind variants with `tracked_variants_update_pricing_strategy`. Use a per-variant strategy only for exceptions.
5. After assigning, run `price_updates_rerun_price_change` for a few variants and show the resulting proposals, so the user sees what the strategy would do before anything is applied.

### Review and apply price changes

1. List pending changes with `price_updates_list` (filter by status, collection or variants).
2. Show them as a table: product, current price, proposed price, change in percent, reason.
3. Flag outliers: drops of more than 10%, prices below unit cost, and changes the strategy marks as needing a fix.
4. Apply only the confirmed changes with `price_updates_apply_price_changes` and the exact `priceChangeIds`; reject the others with `price_updates_reject` if the user wants.

### Analyse the market

Use `reports_get` for questions like "where am I losing position", "who are my main competitors" or "how volatile are prices". See `references/tools.md` for the report names. Summarise findings in a few sentences and a small table; do not dump raw data.

## Reference

- `references/tools.md`: the tool list grouped by workflow, with notes on parameters that are easy to get wrong.
