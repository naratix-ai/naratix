# Mapping branch — decide which products a template serves

Where a template serves from is decided by mappings, resolved per product by walking up the category tree to the nearest mapped ancestor and falling back to the shop default. Because of that cascade, **one mapping at a subtree's root covers everything under it** — never enumerate leaf categories.

## Whole shop

`set-default-template`, with `template_type` saying which kind (`product-description`, `product-title`, `category-description`, `brand-description`).

Each kind keeps its own default, so setting a brand default never disturbs the product one. The previous default of that kind hands off explicitly — its flag is cleared, exactly one default remains — and comes back in the response as `previous_default`.

## A category and everything under it

The customer names the area in plain language. `search-catalog` with `entity: category` and the `taxonomy_id` the template serves finds anchor candidates (without it, it searches every taxonomy, and two can share a path); tokens match anywhere in the breadcrumb, so "kitchen knives" finds a *Knives* under *Kitchen*.

Confirm the anchor by its `path` and `taxonomy_id`, then `map-template-to-category`. Re-mapping an anchor updates it in place; mapping a title never clears a description mapping, and vice versa. The answer names a template written in another language than the taxonomy's: say so, since its texts come out in the template's language.

To stop a subtree using its own template, `map-template-to-category` with `clear` (`title`, `description` or `both`) on the anchor and no template id: the subtree falls back to its parent's mapping, else the shop default.

**Standalone:** name the target categories and give app instructions — *Taxonomies → your taxonomy → Template mappings → set the template on the anchor row (children inherit); for the whole shop, Templates → the template's menu → Set as default.*

## Archive after a switch

After a default handoff or a re-mapping replaces an old template, offer to archive it (`archive-template`, with the same `template_type`).

Archiving is a soft retirement — nothing already generated is ever lost. The server refuses while the template is still a default or mapped anywhere, and names the exact blockers. Relay those blockers and offer to re-point them first, or to clear a mapping that should fall back to its parent's.

## Proving it landed

`get-template-mapping` returns, for one category, both the mapping **stored** at that exact category and the template that **resolves** there after the parent-chain walk.

The two differ constantly, and the difference answers "why is this product getting that description?" A category with nothing stored still serves whatever its nearest mapped ancestor does. Use it before the customer judges the output — it proves the mapping landed where they think it did.

**Completion:** the customer confirms which products the template now serves (the anchor's subtree or the whole shop), and the old template is archived or deliberately kept. Connected, offer a [test drive](test-drive.md) on a product the new mapping now covers.
