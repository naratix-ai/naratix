---
name: naratix-channels
description: Sales channels in Naratix — the connectors to Mirakl marketplaces, VTEX stores and a partner's system through the API connector, their setup, and sending products to them. Use when the user wants products pushed or exported (VTEX, Mirakl, a file, the partner's PIM), asks what a marketplace rejected and why, asks which channels the shop is connected to, wants one switched on or off or made the default, wants the partner push rules installed, wants a Mirakl attribute mapping generated, or wants products labelled.
---

# Naratix channels

A **connector** links the shop to a place its products go: a Mirakl marketplace, a VTEX store, or a partner's system (a PIM, for instance) through the API connector. Each connector has a type, can be switched on or off, and one connector per type is the default the app uses.

## See the channels

`list-connectors` shows each connector's type, whether it is on, whether it is the default, and for API connectors whether deliveries to the partner are healthy. Credentials never appear there.

## Set up a channel

Only the shop's owner changes connectors, as in the app. With `setup-connector`:

- `disable` stops a connector sending: no uploads or pushes, and an API connector takes no partner batches, until `enable` switches it back on. Imports can still read from it.
- `set_default` makes it the connector the app uses for its type.
- `install_push_rules` installs the standard rules on an API connector that has none yet (`list-connectors` reads its `push_rules` false): confirmed categories are sent to the partner, the ones it accepts are locked, and a failed delivery is labelled. Rules already there stay as the owner switched them: for any change, point the owner to the connector's Rules tab in the app. Once rules are there, confirming categories (`confirm-categories`) pushes, so it waits for the user's yes as the `naratix-operator` skill's *The go-ahead* says. Nothing is sent until the partner's inbound credentials exist; the result says so.

## Credentials

Credentials — a VTEX app token, a Mirakl key, a partner's client secret — are typed by the shop's owner in the form `setup-connector` shows, never in the chat. `credentials` with the `connector_id` opens the form for an existing connector; with `type` (`vtex`, `mirakl` or `api`) instead, the form creates a new one. Ask for no token, key or secret yourself, and pass none to any tool; when the user pastes one into the chat anyway, tell them to change it at its source, since the chat keeps it.

Before you open the form, tell the owner what to have ready, and that what they type goes straight to Naratix, encrypted, and you never see it:

- **VTEX**: the store's API address, `https://{account}.vtexcommercestable.com.br/api`, and an app key and app token from VTEX Admin's application keys, with access to read and write the catalogue.
- **Mirakl**: the marketplace's API address, `https://{your-marketplace}.mirakl.net/api`, and its two keys. The shop key reads the category tree and attributes, sends products and reads the marketplace reports; the API key only brings products in. Either one saves; optionally the Mirakl shop id.
- **API connector**: the partner's endpoint, its OAuth token URL, a client id and a client secret.

An address with another path is refused with the shape to use; a bare address gets `/api` added. A launch whose key is missing names that key and starts nothing, before any card: a Mirakl push needs the shop key unless it builds the files only (`upload: false`); tell the user which task that key is for, and open the form for that connector.

Each save of a Mirakl or VTEX connector tries its keys on a call that changes nothing. The form answers you with which connector was saved and what that check found — "Connected" with the categories it sees, which key was refused, or that the address did not answer — never with what was typed. A refusal means the owner corrects that key or the address and saves again; a check that says the keys were not checked, means saving again in a few minutes; one that says the channel could not be reached right now means checking the address, or saving again in a few minutes. "Connected" with the API key alone still needs the shop key before products are sent. Where the app shows no form, the result says so and links the connector's page, where the same list applies: wait for the owner's word that it is saved, then read it back with `list-connectors`. A partner's inbound credentials are generated on the API connector's page in the app.

**Completion:** the form's answer reads Connected (with the shop key when products will be sent), or the owner knows what to correct, or, with no form, `list-connectors` shows the saved connector.

## Map attributes to Mirakl

A Mirakl marketplace has its own fields. Bringing a taxonomy in from Mirakl, and a sync that changes it, already generate its mapping, so `generate-mirakl-mapping` is for generating it again, on the taxonomy brought in from that marketplace (the shop's owner only). It has the AI work out which of the taxonomy's attributes and values fill which Mirakl field; a taxonomy with no marketplace fields is refused before anything starts. Fields already picked on the Mapping tab are kept and the AI fills the empty ones, and the result is live for the next import or export with no review step. Pass the Mirakl `connector_id` and the `taxonomy_id`. Say both, get the user's yes, then call with `confirm: true`. Its card follows it; where no card shows, `list-runs` with `kind: activity`, the `since` it returned and `search: mapping` reads the outcome: the entry whose title ends in this taxonomy's name; a title that begins "Could not" means it failed. The user is notified when it is ready; the Mapping tab shows it, and any field can be changed there.

**Completion:** the user heard that picked fields are kept and the result goes live with no review, said yes, heard how it ended, and knows where the Mapping tab shows it.

## Send products

`export-products` sends a selection, chosen with the products list's filters, to one `channel`, as the products list's Export menu does. **Ask the user where the products go** unless they said: a file, a VTEX store, a Mirakl marketplace or the partner's PIM. For a `vtex` or `mirakl` push, call `list-connectors` first, even when the user named the channel, and name the target connector to them (the default one, switched on); with none switched on, or several, ask which before the preview, and pass it as `connector_id`. A `pim-sync` push goes through the shop's API connector, which its preview's `target` names, and takes no `connector_id`; when that is not the partner the user means, the owner switches the other API connector off (`setup-connector`, `disable`) before the push. Their answer also settles what is sent: Mirakl upload or files only (`upload`), VTEX images only (`images_only`) and the PIM mode (`mode`).

Every other option left out takes the app's default, and the file and VTEX cards show them picked for the user to change. An option is refused on a channel it is not for.

### `file`

A file to download for 30 days, always with each product's code, external ID, category and title.

- `format`: `csv` (default), `xlsx` or `json`.
- `split_mode`: `single` (default, one file) or `per_category` (one file per category).
- `columns`: what else the file holds, any of `titles`, `descriptions`, `seo`, `attributes`, `images`, `category_data`, `source_urls`, `scraped_pages`, `harvests`, `labels`. Default: titles, descriptions, SEO text and attributes.
- With `attributes`: `attributes_format` is `dedicated_columns` (default, one column each) or `json` (one column); `unit_in_attribute_header: true` puts the unit in each column name.
- With `images`: `images_format` is `columns` (default, one per image) or `single` (one column).

A product with no enriched attributes or title in the taxonomy is left out of the file and listed in gap-report.csv in the download: the preview counts them (`left_out`), so offer to enrich them first. Renaming columns and the fallback to unrestricted values are done in the app: the products list's Export to file.

### `vtex`

Products already on the VTEX store are updated with their brand, SKUs, specifications and SKU images; products not yet on VTEX are skipped. The preview counts only the products that go, those already on VTEX with a brand (`products`); it names how many are not on VTEX yet (`not_on_vtex`) and how many on VTEX have no brand, which VTEX requires, and its sample shows only products that go. When none would go it refuses and says why.

- `images_only: true` sends only the variations' images, to SKUs already on VTEX; it takes none of the options below.
- `specifications_source`: `default` (each product's and variation's own) or `aggregation` (the variations' alternative attributes gathered on the product).
- `remove_diacritics`: Romanian diacritics are stripped from text fields unless `false`.
- `skip_variations: true` sends main products without their variations. A main product with no variations stops the push; pass it only once the user agrees to send those without variations.
- `skip_variations_images: true` leaves the variations' images out.

Done in the app, never from the chat: creating products on VTEX (the products list's Export to VTEX with "Create products if not existent in VTEX" on) and setting brands.

A product whose category has no VTEX category is skipped while sending, and the preview cannot count it: the export's `panel_url` in `list-runs` lists these products (see When VTEX skips products).

### `mirakl`

Files built from the Mirakl mapping are uploaded, and the marketplace then accepts or rejects each product. The shop's default Mirakl connector uploads. With none, only the files are built, and the preview names any other connector switched on: ask the user which marketplace, then pass its `connector_id`. `upload: false` builds the files only, even with a default connector.

### `pim-sync`

The products go to the partner's PIM through the API connector; the shop's owner only. They go as they are now; `mode: enrich_then_push` enriches each again first, which counts as a launch. A selection above 5,000 products is refused before the card: narrow it into parts, by category for instance, each its own push and its own yes.

### The push

1. After `list-connectors` (see *Send products*), call without `confirm`: the result gives how many products go, where, and a sample. Show the user all three.
2. Call again with `confirm: true` after their yes, with the options the card shows. A push to a channel cannot be called back. The same selection sent to the same place with the same options twice within ten minutes is refused the second time; another format or other options make a new export.
3. Follow it as the `naratix-operator` skill's *Following work* says. Where no card shows, read only this push: `list-runs` with `kind: export` and the `since`, `connector_id` and `taxonomy_id` it returned, leaving out any it did not return or returned null. Without them, its rows and `totals` count every push since. A Mirakl push's id for `mirakl-reports` is its row's `export_id` there, not a run id:
   - a file, or Mirakl files only: `list-runs` hands over its `download_url` once it is done; a file's row is the one with the `export_id` the push returned. Give the user no link before that. Mirakl files that read `files` 0 once done had nothing to build: say so, and that the export's `panel_url` shows why.
   - a Mirakl push: `status` done means the files are built. The upload goes after that, when the marketplace next takes one, and each row's `upload.state` says where it stands: `waiting` (`next_upload_at` is the next slot), `sending`, `sent`, `failed` (pass its `error` on as written: it says what to do next) or `nothing_sent` (nothing was there to upload; the export's `panel_url` shows why). Tell the user the products were sent only once it reads `sent`; `totals.mirakl` reading `running` and `uploads_pending` 0 says the whole push has gone.
   - a VTEX push writes one row per 50 products: report its `totals.vtex` (`sent`, the products VTEX received, `skipped` and `errors_total`) once its `running` is 0, never one row's counts or the size of the selection.
   - a `pim-sync` push is followed on the API connector's page in the app, which its `panel_url` opens.

**Completion:** the push was confirmed and `list-runs` shows it gone (a Mirakl `upload.state` of `sent`; for VTEX, the user has heard `totals.vtex`'s `sent`, `skipped` and `errors_total` once its `running` is 0), or failed with the reason relayed to the user; a file's link was handed over; for `pim-sync`, the user has the connector page's link.

## When VTEX skips products

The Exports page (the row's `panel_url`) lists every skipped product with its reason. Each reason is fixed in the app, then the products are pushed again:

- **Its category has no VTEX category**: move the product into a category imported from VTEX, or send the categories to VTEX (Categories page, Export → VTEX categories) and then the attributes (Attributes page, Export to VTEX).
- **It has no brand**: set the brand on the product.
- **It is not on VTEX yet**: the products list's Export to VTEX, with "Create products if not existent in VTEX" on.

## Labels that push

Some labels push: when a rule sends labelled products to a channel, `add-product-labels` says where they go and waits for `confirm` after the user's yes. Other labels are added at once, and only ever added.

## Share links

Preview links to send someone outside the shop show each product's current description: for one product, the **Share preview** button on its page's Content tab; for many, the products list's Export → "Share links (descriptions)" in the app. `generate-description`'s `preview_url` shows a new test text, not the current one: offer it only for a test drive the user asked for.

## When a marketplace rejects products

A Mirakl marketplace reports on each product some time after the upload: wait until `list-runs` (`kind: export`) reads `upload.report_received` true for the push, since before that no product reads as rejected. `mirakl-reports` then lists the rejected ones (`status: rejected`) with the reasons: the Mirakl field and the marketplace's message. Warnings are `status: warning`.

Read one push with its `mirakl_export_id` alone (the Mirakl row's `export_id` in `list-runs`; it brings the push's own taxonomy and connector), once its `upload.report_received` is true: the result counts what that push `sent`, `rejected`, `warning`, `no_errors` and `not_reported_yet`; `from_later_push`, its products whose result a later push's report took over; and `not_sent_no_category`: the push's products with no category in the taxonomy, never sent (offer to place them first). `not_reported_yet` waits for a report only while `awaiting_report` is true: once it is false none is coming, and `list-runs` says how the upload ended. It marks each row `this_push`; rows come 50 a page, so page with `next_offset` until it is null before grouping by reason, and `total` is the count. A row with `this_push` false is another push's result for that product, so never report it as this push's. Without the id the list holds every product's latest result, older pushes included.

1. Tell the user how many were rejected and why, grouped by reason. In apps that show views, the card groups them by reason, links each product's page and the products list under the rejection's chip, and hands a group back to you to send again when the member may push.
2. The user fixes the values in the app, on each product's page (the row links there).
3. After their yes, send exactly those products again: `export-products` with `channel: mirakl`, their `product_ids`, `taxonomy_id` and `connector_id`, one push per taxonomy and marketplace.

**Completion:** the user heard the rejections grouped by reason, and only the products they fixed went again, after their yes.
