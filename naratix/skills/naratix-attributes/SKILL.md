---
name: naratix-attributes
description: Enriching product attributes in Naratix — filling a taxonomy's attribute values (colour, size, capacity…). Use when the user wants attributes filled or enriched, asks how an attribute enrichment is going or why products failed, asks what the results hold or what Match Quality means, wants enriched values reviewed, approved, reset or enriched again, wants to try a few products first or says values come out wrong, or wants an attribute's description or an enrichment instruction changed, or the values translated.
---

# Naratix attributes

**Enriching attributes** fills a product's attributes — the fields its category defines in the taxonomy, such as colour, width or energy class — by reading what is known about the product and deciding each value. It writes values into the products; nothing leaves Naratix until someone pushes to a channel.

One product's value set by hand: `edit-product` (the `naratix-operator` skill's [products](../naratix-operator/references/products.md) page).

## Enrich attributes

1. **Pick the products** with the products list's filters on `launch-mining`: a category (its subcategories come too), labels, a search, named products. It fills one taxonomy's attributes (`taxonomy_id`, or the category's own); without a fitting taxonomy, `language` fills each product's latest category. Attributes come from the product's category: products with no category get nothing filled. The size counts them: offer to place them first (the `naratix-categories` skill), or, in a taxonomy, leave them out with `categorization: categorized`; if the user wants them all anyway, launch all.
2. **Settle the choices.** Set these from what the user said, and ask only where it matters to them:

   | Choice | Default | What it means |
   |---|---|---|
   | `method` | fast | Fast reads every source once and decides each value. Cross-check (beta) lets the sources vote per value and holds disagreements as **conflicts** for a person to settle; offer it when the user wants to see where sources disagree. |
   | `apply_to` | main | The selected main products, their variations, or both. |
   | `look_in` | product_data, web, photos | Where to look: the product's own data, web pages found for it, its photos. Add `eprel` yourself for fridges, washing machines and dishwashers — the EU energy-label registry is the most trusted source there and finds nothing for other products. |
   | `replace_existing` | false | Off fills empty attributes only; on also rewrites values already there. |
   | `add_to_enrichment_id` | a new Enrichment | An **Enrichment** groups launches for review and follow-up. Add to an existing one of the same taxonomy, by its id from `list-runs` with `kind: enrichment`, when the user is continuing earlier work. |
   | `instructions` | none | Notes for this run, per step (reading product data, web pages or photos; choosing which web pages to read; deciding values; matching to allowed options). Write them by [Writing instructions](../naratix-operator/references/writing-instructions.md). They are read together with the saved notes for the same step (`instructions_mode: add`); the card lets the user pick `replace`, for this run only. The size quotes the saved notes they meet. Guidance that should last goes in through *Steer it* below, which can add to a saved note; `save_instructions` replaces the taxonomy's note for that step. |

   `extra_fields` enriches fields the taxonomy lacks, for this run only. Use it only when the user asks for such fields.

   **More settings:** how the web is searched, re-matching only, word-for-word quotes, variations and found photos. When the user's words or the results point to one (products hard to find on the web: a stronger `search_mode`, which uses more credits), ask with the question tool as [references/more-settings.md](references/more-settings.md) says.
3. **Offer a trial** when the selection is over about 50 products: *Try a few first*, below. The user may decline it.
4. **Size it, then launch**, as the `naratix-operator` skill's *The go-ahead* says: the size gives the number of products and a plain account of what goes in and what comes out; tell the user both. Keep the `enrichment_id` the launch returns.

**Completion:** the launch returned an `enrichment_id`, and the user has the panel link to its review grid.

## Try a few first

A **trial** enriches a few products, shows where attributes come out wrong, and lets you steer before the rest runs.

1. **Trial:** pick 5 to 10 products spread over the selection's largest categories (`list-products` with the user's filters) and launch them as `product_ids` into a new Enrichment.
2. **Read it** once it has finished: `show-mining` with the `enrichment_id` and `by_attribute: true` lists each attribute's values per Match Quality across the trial's categories, the worst first. The attributes to steer have many AI-matched values, values found that match no allowed option (`unplaced`), empty values, conflicts, or values a check or a marketplace flagged (`flagged`); what the user corrected in the view counts too. Read one value's evidence only where the summary leaves you guessing or the user asks, as the `naratix-operator` skill's *One card per question* says.
3. **See what steers it already:** `search-catalog` with `entity: attribute` and the category's `category_id` lists that category's attributes with their descriptions, and the taxonomy's saved notes per step; `entity: category` shows each category's own notes.
4. **Steer it:** decide where the instruction belongs yourself, by the table below, and tell the user in plain words what you will save and where. Write the text by [Writing instructions](../naratix-operator/references/writing-instructions.md). Call `save-enrichment-instructions` without `confirm`: the card shows the text now and after, and where text is already there the user picks whether yours is added or replaces it; without the card, ask them. Save with `confirm: true` after their yes. Tell the user they can add standing instructions for their own preferences at any time.
5. **Run the trial again** once it has finished (`list-runs`): `launch-mining` with `enrichment_id` and `add_to_enrichment_id` both set to the trial and `replace_existing: true`. Without it the values already there stay and nothing changes. It also rewrites values the user corrected by hand in the trial: say so before the go-ahead. An identical launch is refused while the earlier one's Enrichment still runs.
6. **Compare** with `by_attribute: true` again, and steer once more where it still goes wrong.
7. **Run the rest:** for a first enrichment, the user's filters with the taxonomy, narrowed to the products whose attributes are not enriched yet (`mining: not_mined`); when those products already hold values, the same filters with `replace_existing: true`, and say the trial products are enriched again.

**Completion:** the user has seen the trial's attributes before and after steering, and has said whether to run the rest.

### Where an instruction belongs

| What the trial shows | Where it goes |
|---|---|
| An attribute read with the wrong meaning or unit, wherever it appears | Its description (`target: attribute_description`) |
| A value found but matching no allowed option, or the wrong option picked | A note for matching to allowed options (`step: matching_options`): the category's when it happens in one category, else the taxonomy's |
| The value shows in the product's data, its pages or its photos, yet nothing was found | A note for that source (`step: product_data`, `web` or `photos`) |
| Pages about other products were read | A note for choosing which web pages to read (`step: finding_pages`) |
| Sources that disagree and need a rule (prefer the maker's specification, metric units) | A note for deciding values (`step: deciding_values`; the fast method only) |
| Something true of this batch only | `instructions` on the launch, for this run only |

- A category's note (`target: category_note`) covers products placed in that category itself, not its subcategories, and is used there instead of the taxonomy's (`target: taxonomy_note`).
- A description is part of the taxonomy: it shows in the app and goes out wherever the taxonomy is sent. Keep it a definition of the attribute, and put rules in notes.
- On a taxonomy kept in step with a marketplace, prefer a note: an update from the marketplace can bring back its own descriptions.
- Enriching in a language reads only descriptions and the notes of that run.

## Follow the run

`list-runs` with `kind: enrichment` and the `enrichment_id` returns the Enrichment's totals and its batches, read as the `naratix-operator` skill's [controlling-work](../naratix-operator/references/controlling-work.md) page says; it has finished once its `status` is completed, failed or cancelled, a paused one has not, and a large one takes hours. `kind: attributes` with the same `enrichment_id` lists its newest runs, one per product.

When the Enrichment has finished and its taxonomy has rules — `list-quality-rules` with `engine: consistency` and `status: active` answering `own` above 0, or any with `engine: applicability` — check its products against them: `run-quality-check` with the `enrichment_id`, the Enrichment's `taxonomy_id` (from `list-runs` above) and `engine: consistency`, and again with `engine: applicability` when the taxonomy has applicability rules. The app checks nothing by itself: run it without asking, say it ran, and offer to read the findings once done.

Each run's `stage` is for you to read: tell the user the step in plain words (waiting to start, finding web pages, reading them, picking out values, deciding values, matching to allowed options, saving, done, cancelled).

When it failed, its `failure_reason` says why and what to do next in words you can pass on as written; when the product data was not enough, or nothing usable was found on the web, a clearer title, code or brand helps before enriching it again. A failure a later attempt fixed reads `settled`, with no reason; `status: failed` lists only the open ones, the Enrichment's failed count.

To retry failures, use `control-enrichment` with `action: retry_failures`, as the `naratix-operator` skill's [controlling-work](../naratix-operator/references/controlling-work.md) page says; it first clears the stored pages of products whose sources were unusable, so their search runs again.

## Read the results

Each run's `match_quality` counts how its attributes got their values:

- **exact** — matched one of the attribute's allowed values directly;
- **ai_matched** — the AI picked the allowed value closest to what the sources said;
- **free_text** — the attribute takes any text, so the found value is kept as written;
- **conflict** — cross-check only: the sources disagreed;
- **empty** — no value was kept: no source gave one, or what was found fits no allowed value or failed a check.

Apart from Match Quality, `provenance` says how far a value's sources confirm it: `show-product` gives it per value, and the `provenance` filter (`unconfirmed_facts`, `deduced`, …) selects products to look at or enrich again.

To read the results with the user, find a product or a value in them, or settle them (approving, **Enrich again**, **Reset values**), read [references/review-grid.md](references/review-grid.md): `show-mining` with the `enrichment_id` reads them as the review grid shows them.

A product in the wrong category holds values for the old category's attributes. Move it as the `naratix-categories` skill's *Read where products landed* says, then offer to enrich it again (`launch-mining` with its `product_ids`).

## Translate the values

To translate enriched values into another language's taxonomy, in place, or a list's allowed values, read [references/translation.md](references/translation.md): `launch-translation`, previewed, then launched on the user's yes.
