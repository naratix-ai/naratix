# Checking a whole catalogue with `health`

When the user asked for the whole catalogue, or a set past the limit, split it yourself into at most 4 parts (about 20,000 products), without asking, since a check needs no go-ahead.

1. **The categories.** Take the categories from answers already in the chat; only when nothing in hand names them, walk the tree once (`list-taxonomies` with its `taxonomy_id`), the check's one taxonomy card, with the `taxonomy_id` an answer already gave (`search-catalog`'s `taxonomy`, a rule's `taxonomy_id`) rather than listing the taxonomies first.
2. **The parts.** Pass the taxonomy's top-level categories as `category_ids` in groups, each with its subcategories, plus one pass with `categorization: uncategorized` and the taxonomy's `taxonomy_id`, itself one of the 4, and run them all; a group refused in turn gives its count, so split it again. Each part draws its own card, the one place a question draws several, so make as few parts as the refusal's count and limit allow (8,400 products over a 5,000 limit make two groups).
3. **More than 4 parts.** When the parts would pass 4, at the start or on a re-split, stop splitting: say in one line how many parts the catalogue would take, and offer the health check for the categories they care about, or `consistency` and `applicability`, which take it whole.
4. **Tell the user** in one line that the catalogue is checked in N parts, and that a description shared across parts is counted within each part only; follow every `run_id`, and read the findings together.
