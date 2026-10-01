---
name: naratix-attributes
description: Enriching product attributes in Naratix — filling a taxonomy's attribute values (colour, size, capacity…) from the product's own data, web pages, photos and the EU energy-label registry. Use when the user wants attributes filled or enriched, again or for the first time, asks how an attribute enrichment is going or why products failed, or asks what the results hold or what their Match Quality means.
---

# Naratix attributes

**Enriching attributes** fills a product's attributes — the fields its category defines in the taxonomy, such as colour, width or energy class — by reading what is known about the product and deciding each value. Every launch is sized and agreed first (the operator skill's go-ahead rules). It writes values into the products; nothing leaves Naratix until someone pushes to a channel.

## Enrich attributes

1. **Pick the products** with the products list's filters on `launch-mining`: a category (its subcategories come too), labels, a search, named products. It fills the attributes of one taxonomy — `taxonomy_id`, or the category's own. A shop without a fitting taxonomy can enrich in a language instead (`language`), filling each product's latest category.
2. **Settle the choices.** Set these from what the user said, and ask only where it matters to them:

   | Choice | Default | What it means |
   |---|---|---|
   | `method` | fast | Fast reads every source once and decides each value. Cross-check (beta) lets the sources vote per value and holds disagreements as **conflicts** for a person to settle; offer it when the user wants to see where sources disagree. |
   | `apply_to` | main | The selected main products, their variations, or both. |
   | `look_in` | product_data, web, photos | Where to look: the product's own data, web pages found for it, its photos. Add `eprel` yourself for fridges, washing machines and dishwashers — the EU energy-label registry is the most trusted source there and finds nothing for other products. |
   | `replace_existing` | false | Off fills empty attributes only; on also rewrites values already there. |
   | `add_to_enrichment_id` | a new Enrichment | An **Enrichment** groups launches for review and follow-up. Add to an existing one of the same taxonomy, by its id from `list-runs` with `kind: enrichment`, when the user is continuing earlier work. |
   | `instructions` | none | Notes for this run, per step (reading product data, web pages or photos; deciding values; matching to allowed options). Write them by the operator skill's *Writing instructions*. A note replaces the taxonomy's and category's note for the same step. `save_instructions` keeps them on the taxonomy for later runs, replacing its note for that step; the size quotes that note, so merge what should stay. A category's own note still wins in that category. |

   `extra_fields` enriches fields the taxonomy lacks, for this run only. Use it only when the user asks for such fields.
3. **Size it:** call without `confirm`. The result gives the number of products and a plain account of what goes in and what comes out; tell the user both.
4. **Launch** with `confirm: true` after their yes. Keep the `enrichment_id` it returns.

**Completion:** the launch returned an `enrichment_id`, and the user has the panel link to its review grid.

## Follow the run

`list-runs` with `kind: enrichment` and the `enrichment_id` returns the Enrichment's totals and its batches; it has finished once its `status` leaves active (the operator skill's rule), and a large one takes hours. `kind: attributes` with the same `enrichment_id` lists its newest runs, one per product.

When the Enrichment has finished and its taxonomy has rules — `list-quality-rules` with `engine: consistency` and `status: active`, or any with `engine: applicability` — check its products against them: `run-quality-check` with the `enrichment_id`, the Enrichment's `taxonomy_id` (from `list-runs` above) and `engine: consistency`, and again with `engine: applicability` when the taxonomy has applicability rules. The app checks nothing by itself, and a check only labels what it finds: run it without asking, tell the user it ran, and offer to read the findings with them once it is done.

Each run shows its `stage` (queued, sources, scraping, harvest, then consolidation or extraction, matching, saving, done) and, when it failed, a `failure_reason`: `no_sources` or `unusable_sources` means no usable page was found for the product, and a clearer title, code or brand helps; any other reason is a step that failed. `control-enrichment` pauses, resumes, cancels or retries the launch (the operator skill's rules apply); retrying failures clears unusable sources first, so the search runs again.

## Read the results

Each run's `match_quality` counts how its attributes got their values:

- **exact** — matched one of the attribute's allowed values directly;
- **ai_matched** — the AI picked the allowed value closest to what the sources said;
- **free_text** — the attribute takes any text, so the found value is kept as written;
- **conflict** — cross-check only: the sources disagreed;
- **empty** — no value was kept: no source gave one, or what was found fits no allowed value or failed a check.

`show-mining` with the `enrichment_id` reads the results as the review grid shows them, one category at a time: each product's review status and its values with their Match Quality, and the category's totals. Its default `cells: attention` keeps only the values a person should look at, and `match_quality: conflict` keeps the products holding one; page with `after`. To find a product the user names, pass `title_contains` or `code_contains`, the grid's own Product and Code search; the view opens searched. `product_id` reads one product's review page, and adding `attribute` reads one value's evidence: the sources behind it and the competing values. `attribute` takes the attribute's exact name; `search-catalog` with `entity: attribute` finds it by part of its name or code in the taxonomy, with whether it is required and, for a list, its allowed values. Use it to tell the user what needs their eye, then send them to the grid (`panel_url`).

Enriched products wait **In review**. Approving or rejecting them is the user's call: in the review grid the launch links to, or in the attribute review view `show-mining` opens in apps that show views, where the user also accepts, rejects and edits values; with `set-review-status` on the Enrichment's products (`enrichment_id`) when the user says so; or, for every product whose values are all Exact, `control-enrichment` with `action: approve_all_exact`, which counts them first and waits for `confirm`. In the view the user can also tick products, or every matching one, and choose **Enrich again** or **Reset values**: when values are wrong, in conflict or empty, offer that as the next step. What the user does in the view reaches you as context on your next turn.
