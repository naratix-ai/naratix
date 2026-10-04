# Naratix

[Naratix](https://naratix.ai) gets every product in your catalogue ready to sell. It brings in your products and category tree, places products in categories, enriches their attributes, improves their photos, writes titles, descriptions and SEO text, checks quality, and sends the results to your marketplace, store or PIM.

With this plugin you do all of it by asking your AI assistant in plain words. The assistant works in your Naratix shop and shows cards where you follow and review the work. It asks before it starts anything or sends anything to a sales channel.

## 1. Get an account

New to Naratix? Create an account at [api.naratix.ai/app/register](https://api.naratix.ai/app/register) before you connect, or press Create one on the sign-in page your AI app opens. It comes with an empty shop to start in.

## 2. Connect your AI app

Each app opens Naratix's sign-in page: sign in, then press Authorize. There are no keys to copy. Step by step, with screens: [api.naratix.ai/connect](https://api.naratix.ai/connect). The same guide is in [the Naratix app](https://api.naratix.ai/app): press the **AI apps** button (the robot icon at the top right). It flashes until an app is connected, and its guide tells you when yours is.

**Claude (Pro, Max)** — on the web: Customize → Plugins → Add → Add marketplace → Add from a repository → `naratix-ai/naratix` → Sync, then press Add next to Naratix in Discover. Then open Connectors and press Connect. Installed on the web, it is on every device.

**Other Claude plans** — on the connect page, choose Claude, then the plan next to Pro or Max, and press Add to Claude. This adds the Naratix tools without the plugin's step-by-step guidance.

**Claude (Team, Enterprise)** — an Owner adds Naratix once. First the connection, which installing the plugin does not add: Organization settings → Connectors → Add → Custom → Web, named `Naratix`, link `https://api.naratix.ai/mcp`. Then the plugin: download [naratix.zip](https://github.com/naratix-ai/naratix/releases/latest/download/naratix.zip), then Organization settings → Plugins & skills → Add → Upload a plugin, and set Default access → Installed by default. Each member then connects once: Customize → Connectors → Naratix → Connect. A teammate's invitation is accepted on My Shops in Naratix before its shop appears.

**ChatGPT** — Plugins → Naratix → Install, then sign in. Until Naratix is listed there: Settings → Security and login → turn on Developer mode, then Plugins → +, name `Naratix`, link `https://api.naratix.ai/mcp`, sign-in OAuth; press Scan tools, sign in and press Authorize, then Create. In a chat: + → Developer mode → Naratix. On Business, Enterprise and Edu an admin allows Developer mode first. Developer mode adds the Naratix tools without the plugin's step-by-step guidance.

**Claude Code** —

```
/plugin marketplace add naratix-ai/naratix
/plugin install naratix@naratix
```

Then run `/mcp`, pick Naratix, choose Authenticate, sign in and press Authorize.

## 3. Ask

Start with **"What's in my Naratix shop?"** The assistant finds your shop, tells you what is in it and what is running, and offers the next step each time. Some steps need earlier ones, and what you already have sets the order. From an empty shop, a typical path:

| Step | You can ask |
|---|---|
| Connect your channel | "Connect my Mirakl shop" · "Connect my VTEX store". The shop owner does this. |
| Bring in your catalogue | "Bring in my categories and products from Mirakl" · "Import my products from a file" (CSV, Excel or JSON up to 20 MB, picked in the card) · "Import my categories from a CSV file". Ask what the file should look like for a sample. When a file does not fit, such as a title row above the header or category levels in separate columns, the assistant fixes a copy and you check it in the card. |
| Audit the taxonomy | "Audit my taxonomy". The audit only reads and suggests fixes. Nothing changes until you apply them, onto a copy or in place. |
| Check the products | "Check my catalogue for problems". Checks only label what they find. |
| Place in categories | "Put my products in categories" |
| Enrich attributes | "Enrich 10 fridges first so I can see the results", then steer the rest: "Write dimensions in cm, and take the colour from the photo." What should last is saved as an instruction once you say yes, and the trial runs again before the rest. |
| Improve photos | "Find cleaner photos for my sofas" |
| Titles and descriptions | "Set up descriptions for my marketplace". The assistant builds a template with you, from descriptions you like or your shop's own pages, and tries it on a product first without changing its live text. |
| Your own quality rules | "Flag products whose title has no brand". Your rules are checked once products are enriched. |
| Review | "What's waiting for my review?" |
| Send | "Send the approved fridges". The assistant asks where they go: a file (CSV, Excel or JSON), your VTEX store, your Mirakl marketplace or your PIM; the card then offers that channel's own options. · "What did the marketplace reject?" |
| Follow | "What's running right now?" |

Your app may also list guided starts as prompts or slash commands: *Set up my shop*, *Enrich my products*, *Fix my taxonomy*, *Set up descriptions*, *Set up titles*, *Check my catalogue*, *Publish to my channel*, *Review my results*.

## Your data and keys

- **You say yes first.** Every launch and every push waits for your go-ahead.
- **Your shops only.** The assistant works signed in as you, only in the shops you can open, with your role's permissions.
- **Keys never enter the chat.** The shop owner types channel keys (Mirakl, VTEX) into Naratix's own form: in the chat where your app shows it, otherwise on the connector's page in Naratix. They go straight to Naratix and are stored encrypted. Your AI assistant never sees them. Never paste a key into a message.
- **What the plugin holds.** Its skills are instructions your assistant reads; they send nothing: `naratix-operator` for the whole journey, plus `naratix-imports`, `naratix-taxonomy`, `naratix-categories`, `naratix-attributes`, `naratix-images`, `naratix-channels`, `naratix-content` and `naratix-quality`. The Naratix connection, `https://api.naratix.ai/mcp`, carries your requests to Naratix.

## Help

Questions and problems: [api.naratix.ai/support](https://api.naratix.ai/support).
