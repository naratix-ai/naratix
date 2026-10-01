# Naratix

Get every product in your catalogue ready to sell, from your AI assistant. [Naratix](https://naratix.ai) brings in your products and taxonomies from Mirakl, VTEX or a file, places products in categories, enriches their attributes, improves their photos, writes titles, descriptions and SEO text, checks quality, audits your taxonomy, and sends reviewed products to your marketplace, store or PIM.

Say what you want — "bring in my Mirakl catalogue", "enrich the fridges", "set up descriptions for my marketplace", "what failed this week?" — and the assistant does it in your shop, explaining each step and asking before it launches work or sends anything to your sales channel.

## What's inside

- **The operator** (`naratix-operator`): runs the whole catalogue journey — shops, imports, categories, attribute enrichment, images, content, quality checks, reviews and pushes to your channels.
- **Area skills**: `naratix-imports`, `naratix-taxonomy`, `naratix-categories`, `naratix-attributes`, `naratix-images`, `naratix-channels`, and `naratix-content` (titles, descriptions and SEO text, and the templates and instructions that write them).
- **The Naratix connection** (`https://api.naratix.ai/mcp`): the tools the assistant uses in your shop, with cards and views for launches, reviews, imports and connector credentials where your app shows them.
- **Guided starts** your app may show as prompts or slash commands: *Set up my shop*, *Enrich my products*, *Fix my taxonomy*, *Set up descriptions*, *Set up titles*, *Check my catalogue*, *Publish to my channel*, *Review my results*.

## Install

**Claude (Pro, Max)** — in Claude on the web: Customize → Plugins → Discover, find Naratix, Add. Before the listing is live: Customize → Plugins → Add → Add marketplace → `naratix-ai/naratix` → Sync, then Add next to Naratix in Discover. Install on the web so the plugin is on every device.

**Claude (Team, Enterprise)** — an Owner adds Naratix once. First the connection, which installing the plugin does not add: Organization settings → Connectors → Add → Custom → Web, named `Naratix`, link `https://api.naratix.ai/mcp`. Then the plugin: download [naratix.zip](https://github.com/naratix-ai/naratix/releases/latest/download/naratix.zip), then Organization settings → Plugins & skills → Add → Upload a plugin, and set Default access → Installed by default. Each member then connects it once: Customize → Connectors → Naratix → Connect.

**ChatGPT** — Plugins → Naratix → +, then sign in. Before the listing is live, the tools alone connect in Developer mode: Settings → Security and login → Developer mode → Plugins → + → `https://api.naratix.ai/mcp`.

**Claude Code** —

```
/plugin marketplace add naratix-ai/naratix
/plugin install naratix@naratix
```

On first use your app opens Naratix in the browser: sign in with your normal Naratix account and approve. No keys to copy.

## What it sends where

The skills are instructions your assistant reads; they send nothing. The Naratix connection sends your requests to Naratix at `api.naratix.ai`, signed in as you, and works only in the shops you can open in Naratix, with your role's permissions. Connector credentials are typed into Naratix's own form in the chat and never appear in the conversation. Every launch, and anything that pushes to a sales channel, waits for your yes.

## Support

Questions and problems: [api.naratix.ai/support](https://api.naratix.ai/support).
