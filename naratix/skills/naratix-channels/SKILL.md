---
name: naratix-channels
description: Sales channels in Naratix — the connectors to Mirakl marketplaces, VTEX stores and a partner's system through the API connector, their setup, and sending products to them. Use when the user wants products pushed or exported (VTEX, Mirakl, a file, the partner's PIM), asks what a marketplace rejected and why, asks which channels the shop is connected to, wants one switched on or off or made the default, wants the partner push rules installed, or wants a Mirakl attribute mapping generated.
---

# Naratix channels

A **connector** links the shop to a place its products go: a Mirakl marketplace, a VTEX store, or a partner's system (a PIM, for instance) through the API connector. Each connector has a type, can be switched on or off, and one connector per type is the default the app uses.

## See the channels

`list-connectors` shows each connector's type, whether it is on, whether it is the default, and for API connectors whether deliveries to the partner are healthy. Credentials never appear there.

## Set up a channel

Only the shop's owner changes connectors, as in the app. With `setup-connector`:

- `disable` stops a connector sending: no uploads or pushes, and an API connector takes no partner batches, until `enable` switches it back on. Imports can still read from it.
- `set_default` makes it the connector the app uses for its type.
- `install_push_rules` (API connector only) installs the standard rules: confirmed categories are sent to the partner, the ones it accepts are locked, and a failed delivery is labelled. After that, confirming categories pushes — the operator skill's push rules apply. Nothing is sent until the partner's inbound credentials exist; the result says so.

## Credentials

Credentials — a VTEX app token, a Mirakl API key, a partner's client secret — are typed by the shop's owner in the form `setup-connector` shows, never in the chat. `credentials` with the `connector_id` opens the form for an existing connector; with `type` (`vtex`, `mirakl` or `api`) instead, the form creates a new one. Ask for no token, key or secret yourself, and pass none to any tool; when the user pastes one into the chat anyway, tell them to change it at its source, since the chat keeps it.

The form answers you with which connector was saved, never with what was typed. Where the app shows no form, the result says so and links the connector's page. A partner's inbound credentials are generated on the API connector's page in the app.

## Map attributes to Mirakl

A Mirakl marketplace has its own fields. `generate-mirakl-mapping` has the AI work out which of the taxonomy's attributes and values fill which Mirakl field. Fields already picked on the Mapping tab are kept and the AI fills the empty ones, and the result is live for the next import or export with no review step. Say both, get the user's yes, then call with `confirm: true`. The user is notified when it is ready; the Mapping tab shows it, and any field can be changed there.

## Send products

`export-products` sends a selection, chosen with the products list's filters, to one `channel`:

- `vtex` — the VTEX store: products already there are updated with their SKUs, specifications and images; products not yet on VTEX are skipped. The preview counts only the products that go, those already on VTEX with a brand (`products`); it names how many are not on VTEX yet (`not_on_vtex`) and how many on VTEX have no brand, which VTEX requires, and its sample shows only products that go. When none would go it refuses and says why: creating products on VTEX is done in the app, with the products list's Export to VTEX and "Create products if not existent in VTEX" on, and so is setting their brands. A main product on VTEX with no variations stops the push; pass `skip_variations: true` only once the user agrees to send those without variations.
- `mirakl` — the Mirakl marketplace: files built from the Mirakl mapping are uploaded, and the marketplace then accepts or rejects each product. The shop's default Mirakl connector uploads; without one the files are only generated, and the preview names any other connector switched on (`connector_id`).
- `file` — a CSV, XLSX or JSON file (`format`) with titles, descriptions, SEO text and attributes; the result's `download_url` downloads it once it is built, for 30 days.
- `pim-sync` — the products as they are now, to the partner's PIM through the API connector; the shop's owner only.

1. Call without `confirm`: the result gives how many products go, where, and a sample. Show the user all three.
2. Call again with `confirm: true` after their yes. A push cannot be called back.
3. Follow it with `list-runs` (`kind: export`, with the `since` the push returned). A `pim-sync` push is followed on the API connector's page in the app, which its `panel_url` opens.

**Completion:** the push was confirmed and `list-runs` shows it done, or failed with the reason relayed to the user; for `pim-sync`, the user has the connector page's link.

## When a marketplace rejects products

A Mirakl marketplace reports on each product a few minutes after a push. `mirakl-reports` lists the rejected ones (`status: rejected`) with the reasons: the Mirakl field and the marketplace's message. Warnings are `status: warning`.

1. Tell the user how many were rejected and why, grouped by reason.
2. The user fixes the values in the app, on each product's page (the row links there).
3. After their yes, send exactly those products again: `export-products` with `channel: mirakl`, their `product_ids`, `taxonomy_id` and `connector_id`, one push per taxonomy and marketplace.

