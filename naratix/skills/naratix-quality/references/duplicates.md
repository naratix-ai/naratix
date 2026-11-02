# Finding duplicate products

Naratix looks for products that are the same product listed twice, or variants of one, and groups them. Two scans, both engines of `run-quality-check`, both using credits, so both wait for the user's yes:

- **`duplicates`**: the products list's *Find duplicates & group variants*, over a selection chosen with the products list's filters (none: the whole shop). The AI compares codes, titles and attributes and decides the close calls. One scan runs at a time per shop; another one still running is refused with when it started.
- **`exact_duplicates`**: the Quality Lab's *Run dedup*, over every enriched product of one `taxonomy_id`, grouping products that share a code or the same title. No AI. It takes no other filter.

Offer `exact_duplicates` first when the user suspects products imported twice; offer `duplicates` for near-duplicates and variant groups.

## Run one

1. Call `run-quality-check` with the `engine` and the products, without `confirm`. It starts nothing: it says what it covers, and in a shop on credits, what it uses (the card shows the same).
2. Put that to the user; on their yes the card's button starts it, or call again with `confirm: true`.
3. Follow it with `list-runs` (`kind: quality-check`, the same `engine`; `exact_duplicates` also by the answer's `run_id`). A row's `status` reads completed once it is done, and its `findings` is how many groups it found.

## What happens next

The groups are reviewed and merged in Naratix, on the **Dedupe / Variant Grouping** page, under Quality Lab: which product to keep, how the others relate to it, and the merge itself. Merging stays in the app: say so, give the user the answer's `panel_url`, and never offer to merge from the chat.

**Completion:** the user said yes to the scan, knows how many groups it found or that it is still running, and knows the groups are reviewed and merged in the app.
