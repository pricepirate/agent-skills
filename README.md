# PricePirate agent skills

Skills and MCP plugins that let AI assistants work with [PricePirate](https://pricepirate.com).

> **Beta.** The MCP server at `https://app.pricepirate.com/mcp` is being rolled out to selected shops. The plugin can be installed already, but signing in only works once the beta is enabled for your shop.

| Plugin | For | Contains |
|---|---|---|
| [`pricepirate-shopify`](plugins/pricepirate-shopify) | PricePirate for Shopify | MCP server `pricepirate-shopify` and the skill `pricepirate-shopify` |

Signing in runs through OAuth: your assistant opens a browser window, and you approve access to your store inside the PricePirate app in Shopify. You never copy an API key.

The workflows and safety rules live in the server itself (`get_guide`), so every client gets the same, current guidance. The skill only teaches agents to load it.

## Install

### Claude Code

```bash
claude plugin marketplace add pricepirate/agent-skills
claude plugin install pricepirate-shopify@pricepirate
```

Then run `/mcp`, select `pricepirate-shopify` and choose **Authenticate**.

### Codex

```bash
codex plugin marketplace add pricepirate/agent-skills
```

Install `pricepirate-shopify` from the plugin list and sign in when asked.

### Claude (web, desktop, mobile)

1. Open [**Customize → Connectors**](https://claude.ai/customize/connectors), click **+ Add → Add custom connector**, name it `PricePirate` and paste the URL `https://app.pricepirate.com/mcp`. Click **Connect** and approve access in Shopify. On Team and Enterprise plans, an Owner adds the connector first.
2. That's it: the server brings its own guide (the `get_guide` tool) and prompts such as "PricePirate einrichten" in the **+** menu, so no skill upload is needed.

### ChatGPT

1. Under **Settings → Apps → Advanced**, turn on developer mode and create an app with the URL `https://app.pricepirate.com/mcp`. Connect and approve access in Shopify.

### Other coding agents

Paste this into the agent:

```text
Add the MCP server pricepirate-shopify (Streamable HTTP, OAuth) at https://app.pricepirate.com/mcp and install the skill from https://github.com/pricepirate/agent-skills/tree/main/plugins/pricepirate-shopify/skills/pricepirate-shopify. Then sign in and help me set up PricePirate.
```

## Repository layout

```text
.claude-plugin/marketplace.json   # marketplace for Claude Code
.agents/plugins/marketplace.json  # marketplace for ChatGPT and Codex
plugins/<plugin>/
  plugin.json                     # portable manifest (Agent Plugins), OpenAI metadata
  mcp.json                        # portable MCP server config
  .claude-plugin/plugin.json      # Claude Code manifest
  .mcp.json                       # Claude Code MCP server config
  skills/<skill>/SKILL.md
```

## Releases

Each plugin is released on its own. A tag `<plugin>-v<version>`, for example `pricepirate-shopify-v0.1.0`, builds `<plugin>-skill.zip` with the plugin's skill folder and attaches it to a GitHub release. Bump `version` in both manifests of the plugin before tagging.

## License

MIT
