# Products: selections, names and named products

- `list-products` shows what a selection holds before you act on it: each row counts its enriched attributes and descriptions.
- Several categories the user names are one selection, whether or not they share a parent: pass their ids together as `category_ids` (all of one taxonomy, each with its subcategories), so one launch, one card and one Enrichment cover them.
- `search-catalog` turns a name (a category, brand, label or attribute) into the id a filter or tool takes. Search in the taxonomy's language.
- **Named products are one call.** `list-products` with `product_ids` or `codes` to act on them, `show-product` with `product_ids` to look at them; say which codes were not found and ask about them in that answer, with no second search: for a code in `selection.close_matches`, ask "did you mean …?" with its close codes, and act on one only after the user's yes. That question joins the offer for the products found, in one closing question: "Did you mean FR-2301? Either way, shall I enrich FR-1088, the thin one?"
