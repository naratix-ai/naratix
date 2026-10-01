---
name: naratix-imports
description: Bringing products and taxonomies into Naratix — from a Mirakl marketplace, a VTEX store or a file — and keeping a channel's taxonomy in step. Use when the user wants products or a category tree brought in, asks whether an import finished or how it went, or wants a taxonomy updated from its channel.
---

# Naratix imports

Products and taxonomies come into a shop from its sales channels — a Mirakl marketplace or a VTEX store, through the shop's connector — or from a file. The **taxonomy** comes first: products are matched to its categories as they arrive, and that match is what gives each product its category and attribute values. `list-connectors` shows the shop's channels; a missing one is created by the shop's owner in the form `setup-connector` shows (`action: credentials` with its `type`), as the channels skill describes.

## Bring in a taxonomy

`import-taxonomy` creates a new taxonomy from a channel (`source`: mirakl or vtex), or from a file (below).

1. Ask the user for its **name** and its **language**: the language its categories and attributes are written in, which never changes afterwards.
2. Call without `confirm`: the result says what it does. From Mirakl it also has the AI map the taxonomy's attributes to Mirakl's fields. Get the user's yes.
3. Whether it becomes the shop's **default** taxonomy, the one the products list opens on and every tool reads without a `taxonomy_id`, is the shop's owner's answer for every import, asked once. An answer they already gave goes as `make_default` on your call and the card shows it picked, still theirs to change; otherwise the card asks it with nothing picked, or you ask in the chat where no card shows. Never answer for them, and never ask again what they answered. Only the owner is asked (the result's note says so); anyone else leaves `make_default` out.
4. Call again with `confirm: true` and their answer (`make_default: true` or `make_default: false`), then follow `list-runs` (`kind: import`, with the `run_id` it returned) until its `status` is completed. When it did not become the default, pass its `taxonomy_id` from then on.

## Bring in products

`import-products` pulls products from a channel (`source`: mirakl or vtex). Every product arrives as a new product, matched against the taxonomy imported from the same connector unless the user picks another (`taxonomy_id`). Without a taxonomy from that channel most products arrive with no category and no attribute values, so bring the taxonomy in first.

- **Mirakl**: the whole catalogue, or one `catalog`. The values the seller filled in on Mirakl fill the attributes.
- **VTEX**: `which_products` is all, some of the store's categories (`vtex_categories`, each named by its path in the store, like "Garden/Tools") or a collection (`collection_id`, which the user reads in the VTEX admin). Only active products come in, and products already in the shop are skipped, unless the user says otherwise (`active_only`, `skip_existing`); `no_attributes_only` keeps just the products with no specifications on VTEX yet.

1. Call without `confirm`: the result says what comes in and lists `earlier_pulls` from the same connector.
2. When `earlier_pulls` is not empty for Mirakl, tell the user: a Mirakl pull always adds the catalogue as new products, so pulling again duplicates what the earlier pull brought (say when it ran and how many products). Go ahead only if they still want it.
3. On the user's yes, call again with `confirm: true` and follow `list-runs` (`kind: import`, `run_id`).

**Completion:** its `status` is completed or completed_with_issues, and the user has heard its `counts` — `saved`, `errors`, `skipped` — with the link. Then offer the next step on the journey.

## Keep a taxonomy in step

`sync-taxonomy` updates a taxonomy from the channel it came from; `list-taxonomies` names that channel in `synced_from`. Applied changes cannot be undone.

- **Mirakl**: without `confirm` it computes a **preview** and changes nothing. Read it from `list-runs` (`kind: import`, `run_id`) once done: `changes` counts the categories and attributes that would be added, changed or removed, and the categories flagged and kept because products use them. Show it to the user; on their yes call with `confirm: true`. Applying regenerates the attribute mapping when anything changed.
- **VTEX**: there is no preview. Without `confirm` nothing starts: tell the user that the categories and attributes VTEX dropped are deleted, even ones products use, and call with `confirm: true` only on their yes.

## From a file

A file is picked in the card, never passed through the chat. `import-products` or `import-taxonomy` with `source: file` shows a card where the user picks the file (up to 20 MB), checks the columns AI matched to its fields and starts it; nothing starts from your call, so `confirm` does not apply. The card goes past its Map step only once the fields the import needs have a column: a product's title and code, a category's name, id and parent id.

- **Products**: pass `taxonomy_id` to match categories against another taxonomy than the shop's default, or `match_categories: false` to bring them in without categories.
- **Taxonomy**: ask its **name** and **language** first, as for a channel, and pass them. Whether it becomes the default is asked as for a channel: an answer the owner already gave goes as `make_default` and shows picked on the card; otherwise the card asks it before Create.

Once the card's context says it started, follow `list-runs` (`kind: import`, `run_id`). An app that shows no card, or a larger file, goes through the app instead: the result's `panel_url` is the Imports page for products, the Taxonomies page for a taxonomy.

Once products are in, `run-quality-check` with the health engine checks their titles, EANs, descriptions and photos.
