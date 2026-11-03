# PricePirate for Shopify

Set up and run [PricePirate](https://pricepirate.com), the repricing app for Shopify stores that sell on Idealo and Google Shopping, from a conversation with Claude.

> **Beta.** The PricePirate connector is being rolled out to selected shops. You can add the plugin now; signing in works once the beta is enabled for your shop.

## What it does

The plugin bundles two parts:

- The **PricePirate connector** (`pricepirate-shopify`), a remote MCP server at `https://app.pricepirate.com/mcp`. It gives Claude tools to find and import Shopify products, connect them to marketplace listings, create and assign pricing strategies, review proposed price changes, and read competitor and position reports.
- The **`pricepirate-shopify` skill**, which tells Claude to load the current workflows and safety rules from the connector (`get_guide`) and to confirm every price change with you before it is applied.

## Use it

1. Add the plugin. In Claude, go to **Customize → Plugins → Add → Add marketplace**, enter `pricepirate/agent-skills` and install **PricePirate for Shopify**. In Claude Code, run `claude plugin marketplace add pricepirate/agent-skills` and `claude plugin install pricepirate-shopify@pricepirate`.
2. Connect. In Claude, open the plugin's **Connectors** tab and connect PricePirate; on Team and Enterprise plans, an Owner adds the connector first. In Claude Code, run `/mcp`, select `pricepirate-shopify` and choose **Authenticate**.
3. Approve access. A browser window opens your Shopify admin, where you approve access for one store inside the PricePirate app. You never copy an API key.
4. Ask Claude, for example "Which of my Shopify products are not in PricePirate yet?" or "Suggest a pricing strategy with a 15% minimum margin."

## Data

The plugin runs no code on your machine. Everything goes through the PricePirate connector at `app.pricepirate.com`, operated by PricePirate:

- Claude sends your requests as tool calls, for example product searches, import lists, strategy settings and the price changes you approve.
- The connector returns data from your PricePirate account for the one store you approved: shop name, products and variants with prices and costs, marketplace listings, competitor offers, pricing strategies and proposed price changes.
- Applied price changes become real prices in your Shopify store. Claude asks for your explicit confirmation first.

Access is limited to the approved store and can be revoked at any time in the PricePirate app (Einstellungen → KI-Assistenten). See the [privacy policy](https://pricepirate.com/en/legal/privacy/) and [terms](https://pricepirate.com/en/legal/terms/).

## License

MIT
