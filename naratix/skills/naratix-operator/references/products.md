# Products: selections, names, named products and changing one

- `list-products` shows what a selection holds before you act on it: each row counts its enriched attributes and descriptions.
- Several categories the user names are one selection, whether or not they share a parent: pass their ids together as `category_ids` (all of one taxonomy, each with its subcategories), so one launch, one card and one Enrichment cover them.
- `search-catalog` turns a name (a category, brand, label or attribute) into the id a filter or tool takes. Search in the taxonomy's language.
- **Named products are one call.** `list-products` with `product_ids` or `codes` to act on them, `show-product` with `product_ids` to look at them; say which codes were not found and ask about them in that answer, with no second search: for a code in `selection.close_matches`, ask "did you mean …?" with its close codes, and act on one only after the user's yes. That question joins the offer for the products found, in one closing question: "Did you mean FR-2301? Either way, shall I enrich FR-1088, the thin one?"
- **What happened to a product**: `show-product` lists its latest ten changes, each in the app's words, by whom and the title or category it set. For its whole History, as the product page's History tab shows it, pass `history` (`all`, or one kind: `attributes`, `content`, `images`, `category`): 50 changes a page, and `history_next_offset` as `history_offset` for the next.

## Changing one product

`edit-product` changes one product as its page in the app does: title, description, SEO texts, brand, code, external id, category, attribute values, and its photos (the Gallery). It draws no card, so the go-ahead is asked in the chat.

1. **Read what is there** when the user's ask depends on it: `show-product` for the values, texts and category; the Gallery's positions come in `edit-product`'s own answer (`gallery`, from 1, as `launch-images` counts `photo_positions`).
2. **Preview every change in one call**, without `confirm`: all the fields the user asked for together. `reset_values` (the Attributes tab's Reset) and `variation` (create a copy under a main product, or delete a variation) each go in a call of their own. Texts, values and the category change in `taxonomy_id`, else the shop's default; write them in that taxonomy's language.
3. **Ask.** Put the answer's `changes` to the user, each before and after, with its `warnings`, and say that what `cannot_undo` names cannot be taken back. Ask through the app's structured question tool where it has one, the change and "Not now" as choices; a plain question otherwise.
4. **On their yes**, call again with the same arguments and `confirm: true`, plus the preview's `gallery_fingerprint` when the call names Gallery positions: a Gallery changed since is refused, so preview again and ask again. Done when the answer's `applied` is true; a refusal there changed nothing, so tell the user what it says.

- **Brand and category belong to the main product**; its variations follow. A variation's answer names its main product.
- **A list attribute takes its allowed values only**; a refusal names them, and `show-product`'s `allowed_values_uri` reads the full list. An empty list of values clears an attribute.
- **A description is replaced whole**: send the full new text. A product with no description yet gets one from `generate-description` first.
- **Photos are added by their link** (`add_images`), and each link is checked to open a photo. A photo on the user's computer goes in through the product page's Images tab in the app (`panel_url`): the chat cannot carry the file.
- **The verdict stays as it was.** Once the user is happy with the product, offer `set-review-status` on it.

## Small changes asked in the chat

Deleting products and changing brands draw no card. Called without `confirm`, the tool changes nothing and answers what would change: the count, before and after, a sample. Put that to the user in the chat, through the app's question tool where it has one (the change and "Not now" as choices), plain words otherwise; on their yes call again with the same arguments and `confirm: true`.

- **Deleting products** is `delete-products` with the products as any selection, only when the user asks to delete. Its count names the main products and the variations that go with them; say it cannot be undone and that copies already sent to a channel stay there. A selection that narrows nothing is the whole shop (`whole_shop`): its note says so, and say it too. On the yes, add `expected_products`, the count the user said yes to; a selection that counts otherwise by then is refused, so put the new count to the user. A named variation is refused: one goes alone through `edit-product` with `variation: delete`. It runs in the background.
- **Brands** are `manage-brands`, ids from `search-catalog` with `entity: brand`:
  - `action: create` (`name`); `rename` (`target_brand_id`, `name`). A brand spelled the same but for case is named in the note: ask whether that one is meant.
  - `merge`: `target_brand_id` goes into `into_brand_id`; its products move there and it is deleted. Offer it for duplicates ("SAMSUNG" and "Samsung"). A brand on many products merges in the background: say so when the answer does.
  - `delete`: only a brand no product carries; a brand in use is merged instead.
  - `assign`: `target_brand_id` to a product selection; it replaces their brand, and a replaced brand cannot be restored, so say how many lose one.
  - `from_attribute`: each product's brand from its enriched value of `attribute` (the taxonomy's name for it, usually "Brand"); products that have a brand keep it unless `overwrite: true`. Show the sample's before and after.
  - Variations always carry their main product's brand.
