# Export options per channel

What each channel of `export-products` takes and sends.

## `file`

A file to download for 30 days, always with each product's code, external ID, category and title.

- `format`: `csv` (default), `xlsx` or `json`.
- `split_mode`: `single` (default, one file) or `per_category` (one file per category).
- `columns`: what else the file holds, any of `titles`, `descriptions`, `seo`, `attributes`, `images`, `category_data`, `source_urls`, `scraped_pages`, `harvests`, `labels`. Default: titles, descriptions, SEO text and attributes.
- With `attributes`: `attributes_format` is `dedicated_columns` (default, one column each) or `json` (one column); `unit_in_attribute_header: true` puts the unit in each column name.
- With `images`: `images_format` is `columns` (default, one per image) or `single` (one column).

A product with no enriched attributes or title in the taxonomy is left out of the file and listed in gap-report.csv in the download: the preview counts them (`left_out`), so offer to enrich them first. Renaming columns and the fallback to unrestricted values are done in the app: the products list's Export to file.

## `vtex`

Products already on the VTEX store are updated with their brand, SKUs, specifications and SKU images; products not yet on VTEX are skipped. The preview counts only the products that go, those already on VTEX with a brand (`products`); it names how many are not on VTEX yet (`not_on_vtex`) and how many on VTEX have no brand, which VTEX requires, and its sample shows only products that go. When none would go it refuses and says why.

- `images_only: true` sends only the variations' images, to SKUs already on VTEX; it takes none of the options below.
- `specifications_source`: `default` (each product's and variation's own) or `aggregation` (the variations' alternative attributes gathered on the product).
- `remove_diacritics`: Romanian diacritics are stripped from text fields unless `false`.
- `skip_variations: true` sends main products without their variations. A main product with no variations stops the push; pass it only once the user agrees to send those without variations.
- `skip_variations_images: true` leaves the variations' images out.

Done in the app, never from the chat: creating products on VTEX (the products list's Export to VTEX with "Create products if not existent in VTEX" on) and setting brands.

A product whose category has no VTEX category is skipped while sending, and the preview cannot count it. The Exports page, the export's `panel_url` in `list-runs`, lists every skipped product with its reason. Each reason is fixed in the app, then the products are pushed again:

- **Its category has no VTEX category**: move the product into a category imported from VTEX, or send the categories to VTEX (Categories page, Export → VTEX categories) and then the attributes (Attributes page, Export to VTEX).
- **It has no brand**: set the brand on the product.
- **It is not on VTEX yet**: the products list's Export to VTEX, with "Create products if not existent in VTEX" on.

## `mirakl`

Files built from the Mirakl mapping are uploaded, and the marketplace then accepts or rejects each product. Without `connector_id` the shop's default Mirakl connector uploads; with none, only the files are built, and the preview names any other connector switched on: ask the user which marketplace, then pass its `connector_id`, which uploads through that one. `upload: false` builds the files only, even with a default connector.

## `pim-sync`

The products go to the partner's PIM through the API connector; the shop's owner only. They go as they are now; `mode: enrich_then_push` enriches each again first, which counts as a launch. A selection above 5,000 products is refused before the card: narrow it into parts, by category for instance, each its own push and its own yes.
