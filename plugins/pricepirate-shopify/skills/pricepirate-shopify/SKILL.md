---
name: pricepirate-shopify
description: Sets up and operates PricePirate, the repricing app for Shopify stores selling on Idealo and Google Shopping, through the pricepirate-shopify MCP server. Use when the user wants to import Shopify products into PricePirate, connect them to marketplace listings, design or change pricing strategies, review or apply price changes, or analyse competitors and marketplace positions.
---

# PricePirate for Shopify

PricePirate is a Shopify app. It watches the marketplace listings (Idealo, Google Shopping and others) of a shop's products, compares the shop's offer with competitors, and proposes or applies new Shopify prices according to a pricing strategy.

All data access goes through the `pricepirate-shopify` MCP server. It is scoped to the one Shopify store the user approved during sign-in.

## Before you start

1. Check that the `pricepirate-shopify` tools are available. If they are missing, tell the user to connect the server with the URL shown in the PricePirate app (Einstellungen → KI-Assistenten) and approve access in Shopify. Never ask for an API key or token.
2. Call `get_guide`. It returns the current workflows (find and import products, connect listings, design pricing strategies, review price changes, reports) and the full safety rules. Follow it; it ships with the server, so it always matches the tools.
3. Call `shop_get` and greet the user with the shop name, so both of you know which store you are working on.
4. Answer in the user's language. Most PricePirate users write German; use the formal "Sie" in German.

## Safety rules

These apply even before you have read the guide.

- Price changes become real Shopify prices. Never call `price_updates_apply_price_changes` without showing the exact changes (product, old price, new price) and getting an explicit yes for that set. Never use `all=true` unless the user asked for all pending changes.
- Ask before deleting anything; prefer `tracked_variants_deactivate` over `tracked_variants_delete`.
- Confirm the price floor (unit cost plus margin, or a minimum price) before assigning a pricing strategy.
- Work in batches the user can review and report progress after each batch.
