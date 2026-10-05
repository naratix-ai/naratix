---
name: naratix-imports
description: Bringing products and taxonomies into Naratix — from a Mirakl marketplace, a VTEX store or a file — and keeping a channel's taxonomy in step. Use when the user wants products or a category tree brought in, asks whether an import finished or how it went, has a file that does not import, or wants a taxonomy updated from its channel.
---

# Naratix imports

Products and taxonomies come into a shop from its sales channels — a Mirakl marketplace or a VTEX store, through the shop's connector — or from a file. With a channel or a taxonomy file the **taxonomy** comes first: products are matched to its categories as they arrive, and that match is what gives each product its category and attribute values. With only a product file the products come first, unmatched, each keeping its category from the file, and are placed in a taxonomy once one is in (`launch-categorization`).

A taxonomy with no categories matches nothing: an import or a launch against one is refused until its import finishes. `list-connectors` shows the shop's channels; a missing one is created by the shop's owner in the form `setup-connector` shows (`action: credentials` with its `type`), as the `naratix-channels` skill describes.

## Bring in a taxonomy

`import-taxonomy` creates a new taxonomy from a channel (`source`: mirakl or vtex), or from a file (below).

1. Ask the user for its **name** and its **language**: the language the channel writes its category names in, which may not be the one the shop sells in, and which never changes afterwards. When the user is unsure, import it, then read a few category names with `search-catalog` (`entity: category`, its `taxonomy_id`) and check them with the user before going on.
   - A file taxonomy in the wrong language, or one needed in a second language, is a clone in the app (the `naratix-taxonomy` skill's *Another language*).
   - A Mirakl or VTEX taxonomy in the wrong language is imported again under the right one, with another name or after the wrong one is deleted on the Taxonomies page: a clone is no longer linked to the channel, so `sync-taxonomy` cannot update it and the channel's product imports still match against the original.
2. Call without `confirm`: the result says what it does. From Mirakl it also has the AI map the taxonomy's attributes to Mirakl's fields. Get the user's yes.
3. Whether it becomes the shop's **default** taxonomy, the one the products list opens on and every tool reads without a `taxonomy_id`, is the shop's owner's answer for every import, asked once, unless the shop has no other taxonomy with categories: then it becomes the default without asking. Ask it as the `naratix-operator` skill's [cards](../naratix-operator/references/cards.md) page says: an answer they already gave goes as `make_default` on your call; otherwise the card asks it, or you ask in the chat where no card shows. Only the owner is asked (the result's note says so); anyone else leaves `make_default` out.
4. Call again with `confirm: true` and their answer (`make_default: true` or `make_default: false`), then follow it as the `naratix-operator` skill's *Following work* says (where no card shows, `list-runs` with `kind: import` and the `run_id` it returned) until its `status` is completed, or failed: then tell the user its `failure_reason`.
   - A new default takes over only once its categories are in: until then, tools without a `taxonomy_id` read the old one. When it did not become the default, pass its `taxonomy_id` from then on.
   - From Mirakl, the attribute mapping is made after the import completes: its card follows it, and without a card `list-runs` (`kind: activity`, `search: mapping`, `since` the import's `created_at`) shows it once it is ready: the entry whose title ends in this taxonomy's name, and none other; a title that begins "Could not" means it failed. Offer products into the taxonomy only after that.
5. Once it is in, offer an audit of its attributes and values (`start-audit`, which the `naratix-taxonomy` skill teaches) while its `list-runs` row's `audited` is false: it only reads and suggests, and nothing changes until the user applies it. For a channel's taxonomy, applying it as a new taxonomy leaves the channel's own as it is.

**Completion:** the import's `status` is completed and the user knows whether it became the default and has been offered its audit, or has heard its `failure_reason`.

## Bring in products

`import-products` pulls products from a channel (`source`: mirakl or vtex). Every product arrives as a new product, matched against the taxonomy imported from the same connector unless the user picks another (`taxonomy_id`). Without a taxonomy from that channel most products arrive with no category and no attribute values, so bring the taxonomy in first.

- **Mirakl**: the whole catalogue, or one `catalog`. The values the seller filled in on Mirakl fill the attributes. With `build_variant_families: true`, products sharing a Mirakl variant group come in as variations of one product (asked in step 2). It needs a taxonomy to match against.
- **VTEX**: `which_products` is all, some of the store's categories (`vtex_categories`, each named by its path in the store, like "Garden/Tools") or a collection (`collection_id`, which the user reads in the VTEX admin). Only active products come in, and products already in the shop are skipped, unless the user says otherwise (`active_only`, `skip_existing`); `no_attributes_only` keeps just the products with no specifications on VTEX yet.

1. Call without `confirm`: the result says what comes in and lists `earlier_pulls` from the same connector.
2. **For Mirakl**, before any yes, ask whether products sharing a Mirakl variant group should come in as variations of one product (`build_variant_families`): otherwise each comes in on its own, and grouping them later means pulling again. When `earlier_pulls` is not empty, say in the same message that a Mirakl pull always adds the whole catalogue as new products, so pulling again duplicates the products the earlier pull brought in (say when it ran and how many). Go ahead only if they still want the pull.
   - **Products missing** after an earlier pull: read that pull first (`list-runs`, `kind: import`, its `run_id` from `earlier_pulls`). When it reads completed_with_issues, its `issues` are the likely missing products. Say what brings them in: a category not found needs the taxonomy updated (`sync-taxonomy`), but the rows themselves come in only through a new whole pull, which duplicates the rest, or a products file of just those rows (*From a file*). Updating the taxonomy alone brings no product in.
3. On the user's yes, call again with `confirm: true` and follow it as the `naratix-operator` skill's *Following work* says (where no card shows, `list-runs` with `kind: import` and `run_id`).

**Completion:** its `status` is completed or completed_with_issues, and the user has heard what *After a products import* reads.

### After a products import

From a channel or a file alike, once the import is over, `list-runs` with its `run_id` reads `counts` (`total`; `saved`, created or updated, variations included, so not the new products; `errors`; `warnings`, rows that came in with a problem; `skipped`) and `issues`, the row problems grouped, most common first, with three rows each. In one reply:

1. Tell the user the counts with the link, and the most common issues in plain words, offering `issues_url`, where they download the rows to fix. When nothing came in, read the issues with the user and offer a corrected file; when every code was already in the shop there is nothing to fix.
2. Offer two next steps together: `run-quality-check` with `engine: health` and the import's `run_id` as `import_id`, which checks the saved products' titles, EANs, descriptions and photos (when the row's `health_check` is false, too many came in for one check: offer it per category once they are placed); and the journey's next catalogue step (`import-taxonomy` when the shop has no taxonomy, then `launch-categorization`).

## Keep a taxonomy in step

`sync-taxonomy` updates a taxonomy from the channel it came from; `list-taxonomies` names that channel in `synced_from`. Applied changes cannot be undone.

- **Mirakl**: without `confirm` it computes a **preview** and changes nothing. Read it from `list-runs` (`kind: import`, `run_id`) once done: `changes` counts the categories and attributes that would be added, changed or removed, and the categories flagged and kept because products use them. Show it to the user.
  - Where cards show, put the apply to them with `apply: true` and no `confirm`, which draws the Apply card; elsewhere, on their yes, call with `confirm: true`. Applying reads Mirakl again, so a change made there since the preview is applied too, and regenerates the attribute mapping when anything changed. A call with neither computes a new preview.
  - After an audit applied in place, the preview shows each attribute the audit renamed as removed and the channel's own as added: applying puts the channel's names, types and allowed values back.
- **VTEX**: there is no preview. Without `confirm` nothing starts: tell the user that the categories and attributes VTEX dropped are deleted, even ones products use, and call with `confirm: true` only on their yes.

**Completion:** the user saw the Mirakl preview's `changes`, or heard what VTEX deletes, before saying yes, and the sync's `list-runs` row reads completed, or they heard its `failure_reason`.

## From a file

A products or taxonomy file (`source: file`), a file that does not import, or a file to convert: read [references/file-import.md](references/file-import.md) before you call or answer.
