# Reading and reviewing the results

`show-mining` with the `enrichment_id` reads the results as the review grid shows them, one category at a time: each product's review status and its values with their Match Quality, and the category's totals.

- **Its categories.** Its `categories` lists the Enrichment's categories 50 at a time: `category_search` finds one by name, and `category_offset` set to the answer's `categories_next_offset` (null on the last page) reads the next 50.
- **The values it keeps.** Its default `cells: attention` keeps only the values a person should look at, and a `match_quality` filter (`match_quality: conflict`, `match_quality: empty`) keeps the products holding one and those values with them. A search or a `match_quality` filter is refused on a category of more than 20,000 products.
- **Narrow it to the user's ask**, as the `naratix-operator` skill's *One card per question* says.
- **Per attribute.** For an Enrichment of up to 100 products in up to 20 categories, `by_attribute: true` adds each attribute's values per Match Quality across all its categories.
- **Finding products.** To find a product the user names, pass `title_contains` or `code_contains`, the grid's own Product and Code search; the view opens searched. Several products the user named are one call with `product_ids`; its view moves between their review pages.
- **One product, one value.** `product_id` reads one product's review page, and adding `attribute` reads one value's evidence: the sources behind it and the competing values (the view opens on that value). Those answers carry the review page's own fields, which are for you to read: tell the user what they mean (which sources back a value, what else was found, why it was chosen), never their field names. `attribute` takes the attribute's exact name; `search-catalog` with `entity: attribute` finds it by at least 3 characters of its name or code in the taxonomy, with whether it is required and, for a list, how many values it allows and the resource that lists them (`allowed_values_uri`).

Use it to tell the user what needs their eye, then point them to the review: the view shown here, which switches category, searches and pages by itself, or the grid (`panel_url`) where none shows.

## Approving and fixing values

Enriched products wait **In review**. Approving or rejecting them is the user's call:

- in the review grid the launch links to, or in the attribute review view `show-mining` opens in apps that show views, where the user also accepts, rejects and edits values;
- with `set-review-status` on the Enrichment's products (`enrichment_id`) when the user says so;
- or, for every product whose values are all Exact, `control-enrichment` with `action: approve_all_exact`, which counts them first and waits for `confirm`.

In the view the user can also tick products, or every matching one, and choose **Enrich again** or **Reset values**. For values that are wrong, offer Reset values with enrich again on them; for empty values or conflicts, Enrich again. Enrich again reuses the products' last settings, so values already there stay unless that launch replaced them. What the user does in the view reaches you as context on your next turn.
