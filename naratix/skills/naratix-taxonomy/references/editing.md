# Editing a taxonomy

`edit-taxonomy` changes a taxonomy as the app's Categories, Attributes and Taxonomies pages do: one change per call, named by `action`. It draws no card: each change is asked in the chat.

## The ask

1. Call without `confirm`. Nothing changes; the answer says what would, in `changes` (each `before` → `after`, a null `before` for something new, a null `after` for something gone), in `takes` for a delete, and in `warnings`.
2. Put it to the user in their language: the before and after, what a delete takes with it (each count in `takes`), each warning, and that a delete cannot be undone. Ask through the app's structured question tool where it has one, the change and "Not now" as choices; in plain chat otherwise.
3. On their yes, call again with the same arguments and `confirm: true`. `result` holds the id written.

**Completion:** the user heard the before and after, and for a delete everything in `takes`, before their yes; its answer reads `applied` true.

Several changes are one call each, each asked; changes the user listed together may be asked as one question, every before and after in it.

## Finding what to change

- Categories: `search-catalog` with `entity: category` and the `taxonomy_id`, or `list-taxonomies` walking the tree. `category_id` names the category; `parent_category_id` the one it goes under (left out: the top level).
- Attributes: `search-catalog` with `entity: attribute`. A list attribute's allowed values: its `allowed_values_uri`.
- Values are named by their label as the user sees it; a value's code works too. A wrong one is refused with the attribute's values listed: take the right one from there.

## The actions

- `add_category` (`taxonomy_id`, `name`, `parent_category_id`): its code is made from the name unless `code` is passed. `rename_category` (`category_id`, `name`) renames it in every subcategory's path too. `move_category` (`category_id`, `parent_category_id`) moves it with its subcategories; never under itself.
- `delete_category`: its subcategories move up a level; the products placed in it lose that placement and every title, description and attribute version written for them there; its template mappings and attribute links go too. Refused when a promoted subcategory would share a name on its new level. A category holding many products is deleted in the background: its subcategories move up at once, and it leaves the tree once its products' versions are gone; say so when the answer does.
- `add_attribute` (`taxonomy_id`, `name`, `type`; `values` for a `LIST_SINGLE_VALUE` or `LIST_MULTIPLE_VALUES`; `category_ids` to attach it, their subcategories included). Attached nowhere, it is enriched for no product: ask where it belongs.
- `rename_attribute` (`attribute_id`, `name`). `delete_attribute`: gone from every category; the values products hold for it are no longer shown, enriched or sent.
- `add_values`, `remove_values` (`values`), `rename_value` (`value`, `new_value`): a kept or renamed value keeps its code. Products holding a renamed or removed value keep it as saved until enriched again or edited: offer to enrich the products of its categories afterwards (`launch-mining`).
- `set_default` (`taxonomy_id`): the shop's owner only. The products list opens on the default and every tool reads it when no taxonomy is named.
- `clone_taxonomy` (`taxonomy_id`, `name`, default "<name> copy"): a copy in the same language, filled in the background; it starts with no products. A copy in another language is the app's Clone (*Another language*).
- `copy_categories` (`taxonomy_id` of a copy): places the products in the copy as they sit in the original, in the background. Products already placed in the copy keep their place unless `overwrite: true`; say which the user wants.

## A taxonomy kept in step with its channel

A taxonomy from Mirakl or VTEX takes the edit, and the answer warns that its next sync may change it back, and that the channel knows nothing added here: products sent there need what the channel has. Say so before the yes; for lasting changes offer a copy (`clone_taxonomy`) or the app.
