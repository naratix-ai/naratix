# Translating attribute values

`launch-translation` is the products list's **Translate attributes**: it translates the enriched attribute values of a selection of products, chosen with the products list's filters, in bulk. `taxonomy_id` (or a category's) is the taxonomy the products are read in.

## Pick the mode

Ask what the user wants in their words, then pick one `mode`:

- **Into another language's taxonomy** (`cross_taxonomy`): the values go into `target_taxonomy_id`, a translated copy of the products' taxonomy, matched to its allowed values; a value that matches none is left out. `cross_taxonomy_with_raw_values` carries it over too, unverified. A wrong target comes back with the copies the taxonomy has; with none, a copy is made in Naratix (the `naratix-taxonomy` skill's *Another language*).
- **In the same taxonomy** (`in_place`): the text attributes with no allowed values named in `attributes`, translated into the taxonomy's language. `in_place_raw_values` instead fills the attributes left with no matched value from the raw values found, translated.
- **A list's allowed values** (`allowed_values`): the named list attributes' allowed values, translated in the taxonomy, then the products' values matched to them. The allowed values are rewritten for every product in the taxonomy and cannot be brought back: products outside the selection keep their values in the old language, which then match none. The card counts them and asks for a second press: say both before the yes.

A wrong attribute name comes back with the ones the mode takes. `instructions` carries the user's own guidance ("keep brand names as they are"); `apply_to` covers the main products (default), their variations, or both.

## Launch and follow

Size it without `confirm`, put the card to the user as the `naratix-operator` skill's *The go-ahead* says, and launch on their yes. In a shop on credits, a product with nothing to translate uses none; allowed values use none. It reports to the Activity: follow it with `list-runs` with `kind: activity`, then offer to read a translated product with `show-product` and the `taxonomy_id` it wrote into.

**Completion:** the user said yes to what it translates, into which taxonomy, and knows how it will be followed.
