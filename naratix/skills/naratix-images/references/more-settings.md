# Image Studio's other settings

Every setting Image Studio offers in the app is a `launch-images` parameter. Left out, each takes the app's default, so pass only what the user chose. The launch card shows one of them, `upscale`, beside an edit; the rest are asked in the chat.

Offer one when the user's words or the shop's photos point to it, and ask before the size: with the app's question tool where it has one, the options as choices, in plain text otherwise. Say what it changes and, where the table says so, that it uses more credits.

| When | Offer | Say |
|---|---|---|
| Photos are small or come out blurred | `upscale: true` (on by default at 2k and 4k, off at 1k) | Upscaling uses credits for the photos that need it; off, a small photo comes out smaller and is flagged. |
| A marketplace asks for a size other than the quality | `target_size` (`1k`, `2k`, `4k`), edits only | |
| Cutouts fail on hard products (glass, hair, thin parts) | `redraw_if_cutout_fails: true`, on white, colour or gradient | It uses more credits, and a redrawn photo is AI-made and marked so. |
| A soft studio backdrop | `background: gradient`, `background_gradient` top then bottom as #RRGGBB | A generated photo still comes out on white. |
| A look for the new photos (an angle, props to avoid, brand rules) | `generation_notes` | Ask whether they join the house style (`generation_notes_mode: add`, the default) or replace it (`replace`). |
| Some photos show another variant, or the main photo is the best | `reference_photos` (`primary`, `first_2`, `first_4` by default, `all`) | |
| The shop has only infographics or composites of a product | `recreate_from_marketing: true` | The result is generated, checked like any generated photo. |
| A concern the check should catch (a watermark, a sticker) | `check_for`, in their words | It adds checks, never removes one. |
| Find returns little or nothing: products hard to find on the web | `search_mode`: `aggressive` (more searches and pages), `ean` (the barcode first, for products with barcodes), `translated` (titles in another language than the shop's); or `search_engine: bing` when Google finds nothing usable | Bing is slower. Products with saved web pages reuse them. |
| They know where good photos are, or a site's photos are poor | `preferred_sites`, `blocked_sites` | |
| Found photos are lifestyle shots with much background | `max_background` (a percentage) | |

**One product's photos.** For a single named product (`product_ids` with one id, `apply_to: main`), `photo_positions` picks the photos to work on instead of `images`, and with `generate_variants` `reference_positions` picks the photos it learns from instead of `reference_photos`. Positions count from 1 in the order `show-product` lists the photos; the refusal names how many there are.
