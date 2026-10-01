---
name: naratix-categories
description: Placing products in categories in Naratix, and reading where they landed. Use when the user wants products categorized or re-categorized, asks why a product sits in a category, or wants to know which products could not be placed.
---

# Naratix categories

A taxonomy is the category tree a shop sells in. **Categorization** places each product in one of its categories by reading the product's title, data and photos. The category decides which attributes are enriched and which template writes the product's content, so it comes before both.

## Categorize

1. **Pick the products** with the products list's filters on `launch-categorization`: uncategorized ones (`categorization: uncategorized` with a `taxonomy_id`), a search, labels, named products. The taxonomy is the selection's, else the shop's default.
2. **Instructions**, only when the user has a rule the categorizer would not guess ("cables go under Accessories, never under the device"). Write them by the operator skill's *Writing instructions*.
3. **Size it:** call without `confirm`. The result gives the number of products and what happens to them; tell the user.
4. **Launch** with `confirm: true` after their yes, then follow the Enrichment as the operator skill describes.

**Completion:** the launch returned an `enrichment_id` and the user has the link to its review.

## Read where products landed

`list-runs` with `kind: categorization` returns each product's run: the category it went to, the ranked `candidates` with their confidence, and the `outcome`. Each product goes to the category ranked first (`auto_picked`); `skipped` means there was too little data to go on, and the product kept its category.

`show-categorization` reads the same as the app shows it, one thing at a time: `enrichment_id` gives a categorization Enrichment's products with their old and new category and verdict (`filter`: changed, unchanged, failed, corrected); `product_id` gives one product's category, the suggestions of its latest run and who set what when, plus its labels; `category_id` gives a category's labels and lifecycle.

Where the app shows views, these reads open as one, and the user fixes things there: a product's category from the run, a suggestion chosen, a category changed, a product's or a category's labels, Draft or In review. A label change that sends something to a channel asks the user first, in the view. Without views, changing a category or its labels happens in the app, on the review the launch links to: say where, and carry on when the user is back.

## Confirm categories

A category's lifecycle runs Draft, In review, Confirmed. Confirmed is the one the assistant sets, with `confirm-categories`, and only on the user's word: in a shop connected to a partner through the API connector, confirming is what sends the categories to that partner. Treat it as a push.

1. Find the categories with `search-catalog`, one taxonomy per call: `search-catalog` searches every taxonomy, so pass its `taxonomy_id`. Each one brings its subcategories unless `include_subcategories` is false.
2. Call without `confirm`: the result gives how many categories it covers and where they go. Tell the user both.
3. Call again with `confirm: true` after their yes.

Draft and In review are set in the category's view (`show-categorization` with its `category_id`), or in the app.
