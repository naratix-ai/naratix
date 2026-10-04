---
name: naratix-imports
description: Bringing products and taxonomies into Naratix — from a Mirakl marketplace, a VTEX store or a file — and keeping a channel's taxonomy in step. Use when the user wants products or a category tree brought in, asks whether an import finished or how it went, has a file that does not import, or wants a taxonomy updated from its channel.
---

# Naratix imports

Products and taxonomies come into a shop from its sales channels — a Mirakl marketplace or a VTEX store, through the shop's connector — or from a file. With a channel or a taxonomy file the **taxonomy** comes first: products are matched to its categories as they arrive, and that match is what gives each product its category and attribute values. With only a product file the products come first, unmatched, each keeping its category from the file, and are placed in a taxonomy once one is in (`launch-categorization`). A taxonomy with no categories matches nothing: an import or a launch against one is refused until its import finishes. `list-connectors` shows the shop's channels; a missing one is created by the shop's owner in the form `setup-connector` shows (`action: credentials` with its `type`), as the `naratix-channels` skill describes.

## Bring in a taxonomy

`import-taxonomy` creates a new taxonomy from a channel (`source`: mirakl or vtex), or from a file (below).

1. Ask the user for its **name** and its **language**: the language the channel writes its category names in, which may not be the one the shop sells in, and which never changes afterwards. When the user is unsure, import it, then read a few category names with `search-catalog` (`entity: category`, its `taxonomy_id`) and check them with the user before going on. A file taxonomy in the wrong language, or one needed in a second language, is a clone in the app (the `naratix-taxonomy` skill's *Another language*). A Mirakl or VTEX taxonomy in the wrong language is imported again under the right one, with another name or after the wrong one is deleted on the Taxonomies page: a clone is no longer linked to the channel, so `sync-taxonomy` cannot update it and the channel's product imports still match against the original.
2. Call without `confirm`: the result says what it does. From Mirakl it also has the AI map the taxonomy's attributes to Mirakl's fields. Get the user's yes.
3. Whether it becomes the shop's **default** taxonomy, the one the products list opens on and every tool reads without a `taxonomy_id`, is the shop's owner's answer for every import, asked once, unless the shop has no other taxonomy with categories: then it becomes the default without asking. Ask it as the `naratix-operator` skill's [cards](../naratix-operator/references/cards.md) page says: an answer they already gave goes as `make_default` on your call; otherwise the card asks it, or you ask in the chat where no card shows. Only the owner is asked (the result's note says so); anyone else leaves `make_default` out.
4. Call again with `confirm: true` and their answer (`make_default: true` or `make_default: false`), then follow it as the `naratix-operator` skill's *Following work* says (where no card shows, `list-runs` with `kind: import` and the `run_id` it returned) until its `status` is completed, or failed: then tell the user its `failure_reason`. When it did not become the default, pass its `taxonomy_id` from then on. From Mirakl, the attribute mapping is made after the import completes: its card follows it, and without a card `list-runs` (`kind: activity`, `search: mapping`, `since` the import's `created_at`) shows it once it is ready: the entry whose title ends in this taxonomy's name, and none other; a title that begins "Could not" means it failed. Offer products into the taxonomy only after that.
5. Once it is in, offer an audit of its attributes and values (`start-audit`, which the `naratix-taxonomy` skill teaches) while its `list-runs` row's `audited` is false: it only reads and suggests, and nothing changes until the user applies it. For a channel's taxonomy, applying it as a new taxonomy leaves the channel's own as it is.

**Completion:** the import's `status` is completed and the user knows whether it became the default and has been offered its audit, or has heard its `failure_reason`.

## Bring in products

`import-products` pulls products from a channel (`source`: mirakl or vtex). Every product arrives as a new product, matched against the taxonomy imported from the same connector unless the user picks another (`taxonomy_id`). Without a taxonomy from that channel most products arrive with no category and no attribute values, so bring the taxonomy in first.

- **Mirakl**: the whole catalogue, or one `catalog`. The values the seller filled in on Mirakl fill the attributes. With `build_variant_families: true`, products sharing a Mirakl variant group come in as variations of one product; ask the user, since otherwise each comes in on its own and grouping them later means pulling again. It needs a taxonomy to match against.
- **VTEX**: `which_products` is all, some of the store's categories (`vtex_categories`, each named by its path in the store, like "Garden/Tools") or a collection (`collection_id`, which the user reads in the VTEX admin). Only active products come in, and products already in the shop are skipped, unless the user says otherwise (`active_only`, `skip_existing`); `no_attributes_only` keeps just the products with no specifications on VTEX yet.

1. Call without `confirm`: the result says what comes in and lists `earlier_pulls` from the same connector.
2. When `earlier_pulls` is not empty for Mirakl, put two things to the user in one message, before any yes: a Mirakl pull always adds the whole catalogue as new products, so pulling again duplicates the products the earlier pull brought in (say when it ran and how many); and whether products sharing a Mirakl variant group should come in as variations of one product (`build_variant_families`), since grouping them later means pulling again. Go ahead only if they still want the pull.
   - **Products missing** after an earlier pull: read that pull first (`list-runs`, `kind: import`, its `run_id` from `earlier_pulls`). When it reads completed_with_issues, its `issues` are the likely missing products. Say what brings them in: a category not found needs the taxonomy updated (`sync-taxonomy`), but the rows themselves come in only through a new whole pull, which duplicates the rest, or a products file of just those rows (*From a file*). Updating the taxonomy alone brings no product in.
3. On the user's yes, call again with `confirm: true` and follow it as the `naratix-operator` skill's *Following work* says (where no card shows, `list-runs` with `kind: import` and `run_id`).

**Completion:** its `status` is completed or completed_with_issues, and the user has heard what *After a products import* reads.

### After a products import

From a channel or a file alike, once the import is over, `list-runs` with its `run_id` reads `counts` (`total`; `saved`, created or updated, variations included, so not the new products; `errors`; `warnings`, rows that came in with a problem; `skipped`) and `issues`, the row problems grouped, most common first, with three rows each. In one reply:

1. Tell the user the counts with the link, and the most common issues in plain words, offering `issues_url`, where they download the rows to fix. When nothing came in, read the issues with the user and offer a corrected file; when every code was already in the shop there is nothing to fix.
2. Offer two next steps together: `run-quality-check` with `engine: health` and the import's `run_id` as `import_id`, which checks the saved products' titles, EANs, descriptions and photos (when the row's `health_check` is false, too many came in for one check: offer it per category once they are placed); and the journey's next catalogue step (`import-taxonomy` when the shop has no taxonomy, then `launch-categorization`).

## Keep a taxonomy in step

`sync-taxonomy` updates a taxonomy from the channel it came from; `list-taxonomies` names that channel in `synced_from`. Applied changes cannot be undone.

- **Mirakl**: without `confirm` it computes a **preview** and changes nothing. Read it from `list-runs` (`kind: import`, `run_id`) once done: `changes` counts the categories and attributes that would be added, changed or removed, and the categories flagged and kept because products use them. Show it to the user. Where cards show, put the apply to them with `apply: true` and no `confirm`, which draws the Apply card; elsewhere, on their yes, call with `confirm: true`. Applying reads Mirakl again, so a change made there since the preview is applied too, and regenerates the attribute mapping when anything changed. A call with neither computes a new preview. After an audit applied in place, the preview shows each attribute the audit renamed as removed and the channel's own as added: applying puts the channel's names, types and allowed values back.
- **VTEX**: there is no preview. Without `confirm` nothing starts: tell the user that the categories and attributes VTEX dropped are deleted, even ones products use, and call with `confirm: true` only on their yes.

**Completion:** the user saw the Mirakl preview's `changes`, or heard what VTEX deletes, before saying yes, and the sync's `list-runs` row reads completed, or they heard its `failure_reason`.

## From a file

An import always starts from a file the user picks in the card; a file attached in the chat is only yours to fix and hand back. `import-products` or `import-taxonomy` with `source: file` shows a card where the user picks the file (up to 20 MB), checks the columns matched to its fields and starts it; nothing starts from your call, so `confirm` does not apply. The card goes past its Map step only once the fields the import needs have a column: a product's title and code, a category's name, id and parent id.

- **Products**: say each row needs a title and a product code, the code stored as text. Pass `taxonomy_id` to match categories against another taxonomy than the shop's default, or `match_categories: false` to bring them in without categories. When the shop already has products (`has_products` in `list-shops`), ask once in the chat whether codes already in the shop are updated or skipped, before the card shows, and say in the same question that a JSON file updates them whatever the answer; pass the answer as `duplicate_handling`, and the card shows it picked. For a file you know is JSON, ask nothing and leave `duplicate_handling` out. A JSON file is never matched to a taxonomy and builds no variant families: its products arrive without categories (offer `launch-categorization` once it ends), so for categories or variants ask for a CSV or XLSX. Left out from a CSV or XLSX, those codes come in again as new products, so in such a shop say which rows are new, updated or skipped rather than that every row is created. When the card's context says the user picked a `.json` file after they chose to skip those codes or add them as new, tell them that this file updates the products with those codes.
- **Taxonomy**: a CSV or JSON file, not XLSX; for any other file, offer to convert it to a CSV as *What a taxonomy file holds* says, hand it back for download, then show the card. Ask its **name** and **language** (the one the file's category names are written in) first, as for a channel, and pass them. Whether it becomes the default is asked as for a channel (step 3); the card asks it before Create.

Once the card's context says it started, the card follows the import and its context says when it ends; call `list-runs` (`kind: import`, `run_id`) only when the user asks or comes back. An app that shows no card, or a larger file, goes through the app instead: the result's `panel_url` is the app's New import page for products, the Taxonomies page for a taxonomy.

**Completion:** the card's context says the import started, or the user has the `panel_url` where no card shows; once it ends, the user has heard what *After a products import* reads for products, or *After a taxonomy file* for a taxonomy.

### When the file does not fit

The card puts what it read into your context: the file's name and `upload_id`, its columns, which field each was matched to, the fields still without a column, a few sample rows, its separator, whether it is saved as UTF-8, the separator and header row its first lines suggest (`layout_guess`), product codes a spreadsheet turned into numbers (`code_check`), the user's picks, and any refusal the card showed. When the user says the file fails, read that context and say plainly what is wrong.

1. **A layout problem** — encoding, separator, a title row above the header, rows to skip, one column to split or several to join (category levels into one path with `/`, image columns into one cell with `,`), decimal commas, stray spaces: call the same tool again with `source: file`, the `upload_id`, `reshape` and the picks the card's context holds, so they show picked. It writes a fixed copy, and only the newest copy can be started; the user's file is kept as it is. Its answer draws no card: the card the user already has redraws on the copy at its Map step, keeping the picks made on it, and the user checks it and starts it there. When that card is no longer in the chat, open it again on the copy: the same tool with `source: file`, the copy's `upload_id` and the picks the context holds (`duplicate_handling`, `taxonomy_id` or `match_categories: false`; for a taxonomy its name, language and `make_default`). Fix again from the same `upload_id` or the copy's: every fix reads the user's file, so row numbers are always that file's.
2. **A JSON file** is not reshaped: fix it with your own file tools, or ask for a CSV export (or XLSX, for products).
3. **Codes turned into numbers** (`5.94E+12`) in a CSV have already lost digits: ask for a new export with that column stored as text. From an XLSX file the full digits are kept.
4. **Anything else**, where you can work on files: a PDF, an image or any other file cannot go in as it is, whether it is attached in the chat with no card shown yet or was picked in a card (then ask them to attach it in the chat). A taxonomy file goes as *From a file*'s **Taxonomy** says: the card takes CSV or JSON only. For a products file, in one message: tell the user in one line that the card takes a CSV, XLSX or JSON file of up to 20 MB, which they pick in it; offer to convert it, saying what happens next: you give them the converted CSV to download, then an import card opens where they pick it, check the columns and start it; list the rows the source gives no product code; and, when the shop has products (`has_products`), ask the codes question of *From a file* in the same message. On their yes, write the file as *What a products file holds* says:
   - Before you write the category column, look up each distinct category the source names in the target taxonomy (`search-catalog`, `entity: category`, its `taxonomy_id`, in the taxonomy's language) and write the matching leaf name, or its full path. A source category that matches only a parent, or nothing, leaves its rows without a category: tell the user how many rows, and offer `launch-categorization` once the import has finished (the `naratix-categories` skill).
   - Rows the source gives no product code stay out of the file until the user gives their codes; never make one up.
   - Hand the file back for download, then show the card for them to pick it: `import-products` with `source: file` and their `duplicate_handling`.

`template_url` in the result is a sample file the user opens in their browser; you cannot download it yourself. Offer it when the user asks what the file should look like.

### What a products file holds

- Row 1 is the header. Any header names work: they are matched to the fields for you, and `file.targets` in the result says what each field holds.
- Each row needs a title and a product code, the code stored as text.
- The category is the taxonomy's leaf name, or its full path joined by `/` with no spaces around it (not the " / " breadcrumb `search-catalog` shows).
- Images and keywords are comma-separated in one cell.
- In a CSV or XLSX, rows that share a variant group code become variants of one product.
- The shop SKU goes in a column of its own.
- A column that matches no field still comes in: the whole row is kept with the product as its source data, which categorization and attribute enrichment read, but it fills no field of its own. Only with a taxonomy matched and the card's Options' **Process Mirakl attribute columns** on does a column headed by one of the taxonomy's attribute codes fill that attribute.
- Sample rows you show the user use the file's real separator, so what they copy matches the file.

### What a taxonomy file holds

- In a CSV, each row carries its category's name, id and parent id; an empty parent id means a top-level category. `file.fields` in the result says what each column holds.
- A row holds one attribute of that category, with its code, type, requirement level and description, and one allowed value. A further value takes a row of its own, which repeats the category's id and the attribute or leaves them empty.
- An attribute's type reads as text, number, date, list (one value), multiselect (several values), yes/no or long text, and its requirement level as required, optional or recommended (mandatory, obligatoire and facultatif too); an empty cell comes in as text and required, and the import's notification names any other word, which comes in the same way.

### After a taxonomy file

`list-runs` returns the `categories` and `attributes` the taxonomy now holds. Read them before offering `import-products`: a taxonomy with no categories means the file's columns were matched wrong, so check them with the user and bring the corrected file in under another name. The empty taxonomy keeps its name; the user can delete it on the Taxonomies page.
