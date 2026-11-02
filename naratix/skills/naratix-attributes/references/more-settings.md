# Enrichment's other settings

Every setting the app's enrichment wizard offers customers is a `launch-mining` parameter. Left out, each takes the app's default (a search setting the category's, else the taxonomy's), so pass only what the user chose. The launch card shows only `find_photos`, and where found photos go once it is on; the rest are asked in the chat.

Offer one when the user's words or the results point to it, and ask before the size: with the app's question tool where it has one, the options as choices, in plain text otherwise. Say what it changes and, where the table says so, that it uses more credits.

| When | Offer | Say |
|---|---|---|
| The products are hard to find on the web: failures say nothing usable was found, or few values come from web pages | a stronger `search_mode`: `aggressive` (title, brand and code variants), `ean` (the barcode first, for products with barcodes), `translated` (titles written in another language than the taxonomy's) | It uses more credits: past 2 searches a product, about one more credit for every two more searches. |
| The user knows which searches find the products | `search_mode: custom` with `search_template`, `\|` between searches | Each search past 2 uses more credits. A key no product carries is refused. |
| Google finds nothing usable | `search_engine: bing` | Slower. |
| Pages come from the wrong sites, or one site is trusted | `preferred_sites`, `blocked_sites` | Products with saved web pages reuse them; blocked sites still apply to those. |
| The allowed options changed and the values already found should be matched again | `rematch_only: true`, alone: no `look_in`, `find_photos` or search settings | It reads no source, so it uses fewer credits. |
| With `method: cross_check`, no value may be invented | `require_quotes: true` | Fewer attributes get filled. |
| Text values should stay in the language they were found in | `language_check: false` | Rarely wanted: by default they are rewritten in the taxonomy's language. |
| The variations should also carry the product's shared attributes | `variation_attributes: shared`, or `shared_to_parent` to write them onto the parent too (where it has exactly one variation) | |
| A family's variations share their values | `apply_to: first_variation`: one variation per product stands in for the family, its shared values written onto the parent (`variation_attributes` defaults to `shared_to_parent` there) | |
| Found photos should go to every product, or replace the photos | `found_photos` with `find_photos`: `only_empty` (the default), `add`, `replace` | Found photos go in without review, so where some products have no photo it is the user's answer: the card asks it, else ask before the yes. `replace` swaps the product's photos for good, the old ones not kept: say so plainly; the card asks for a second press. |
| With `apply_to: first_variation` and `find_photos`, the siblings should get the found photos too | `copy_photos_to_variations: true` | As `found_photos` says. |
