---
name: naratix-images
description: Improving product photos in Naratix with Image Studio — finding new photos on the web, cleaning or replacing backgrounds, generating new photos from existing ones or, for a product with none, from a description — and approving the results. Use when the user wants better, more or cleaner product images, asks what can be done for their photos, asks how an image run went, or wants its results approved.
---

# Naratix images

**Image Studio** works on a product's photos and proposes **image candidates**: new or improved photos a person approves before they reach the product. It can find new photos of the product on the web, edit the backgrounds of the existing ones, or generate new photos from them. One exception to approval is `fill_empty` (*Improve images*, step 2). Every launch is sized and agreed first (the `naratix-operator` skill's go-ahead rules). A generated photo or a photo set in a scene is marked as AI-made, which the law requires; a photo found on the web comes as it was published.

A change by hand to one product's Gallery (a photo added by its link, removed, reordered or copied to its variations) is `edit-product`, as the `naratix-operator` skill's [products](../naratix-operator/references/products.md) page says.

## Recommend from the shop's photos

Start from the shop's own gaps, with one `list-products` call: its card is the question's one card, and its `total` the count. Filter it by the gap the user asked about, `photos: without` (no photos) by default, or `photos: one` (a single photo) when they want more photos. Tell the user what you found ("212 products have one photo"), offer the other counts (`photos: one`, `photos: several`, one category) as their next question, then recommend:

| What the shop needs | Run |
|---|---|
| Photos for products that have none | `find_images`, or `generate_variants` with `from_description` when the web has none of them or the user asks for made photos (*Draw from a description*) |
| More photos for products that have one | `find_images` for more of the real product, or `generate_variants` with `variants` 2–3 |
| A marketplace's white background, colour or shape | `edit_backgrounds` with `background: white` (or `background: colour`), and `aspect` |
| Lifestyle shots for a category | `edit_backgrounds` or `generate_variants` with `background: scene` and the scene in words |

When the main products show no photos, the photos may sit on their variations, as VTEX stores often keep them: size the run with `apply_to: variations` before offering `find_images`.

## Improve images

1. **Pick the products** with the products list's filters on `launch-images`.
2. **Settle the choices** from what the user wants:

   | Choice | Default | What it means |
   |---|---|---|
   | `run` | — | `find_images` (new photos from the web), `edit_backgrounds`, `generate_variants` (new photos made from the existing ones). One or more. |
   | `images` | all | Which of each product's photos: `primary`, `first_2`, `first_4` or `all`. |
   | `apply_to` | main | The selected main products, their variations, or both. |
   | `quality` | 2k | Output size: 1k, 2k or 4k. |
   | `background` | white | White, a `colour` (`background_colour`, as #RRGGBB), a `scene` (describe it in `scene`), or `keep` the photo's own. `keep` applies to edits only: a generated photo comes out on the white, colour or scene chosen, and on white with `keep`. |
   | `aspect` | 1:1 | The shape edited and generated photos come out in, width:height, such as 4:5 when a marketplace asks for it. |
   | `min_size` | — | With `find_images`: leaves out found photos smaller than this many pixels on a side. |
   | `variants` | 1 | With `generate_variants`: new photos per product, up to 4. |
   | `from_description` | off | With `generate_variants`: a product with no photo gets its new photos drawn from its data and `description` (*Draw from a description*). |

   `auto_approve_strong` approves the strong results without review, which changes what happens to the shop's photos: use it only when the user asks.

   At most one question is the user's to answer, once, in the chat or on the card: on a `find_images` run without `auto_approve_strong` over products that have no photos, whether those get the strong found photos at once, so the other steps have something to work on (`fill_empty`, which never goes with `auto_approve_strong`); on `edit_backgrounds` with `auto_approve_strong`, whether approved photos go in place of the originals instead of alongside them (`replace_originals`). Explain `fill_empty` before the go-ahead, and pass an answer the user already gave; otherwise the card asks it, and their go-ahead needs it.

   **More settings.** Every other Image Studio setting is a parameter too: upscaling, a gradient, the edits' size, redrawing failed cutouts, instructions for the new photos and which photos they learn from, what the check also looks for, and how Find searches. When the user's words or their photos point to one, ask with the question tool as [references/more-settings.md](references/more-settings.md) says, and say when it uses more credits.

   **Draw from a description.** When the products have no photo, offer `generate_variants` with `from_description`:

   - Ask the user for a short description of what the products look like ("a bunch of 5 ripe yellow Cavendish bananas") and pass it as `description`. It is optional, and one description serves every product in the run, so launch a mixed selection per kind of product.
   - Before the go-ahead, say what it makes: a typical product drawn from each product's title, brand, category, attributes, description and their words, not a photo of the exact product, with no logos, brand names or printed text unless their description asks for them. Each one waits for a person to approve it, even with `auto_approve_strong`.
   - Products that have photos still generate from them. With `find_images` in the same run, a product whose photos the find fills generates from those; the rest are drawn.

3. **Try 5 products first** when the selection is over about 20 products: pick 5 spread over its categories from one `list-products` page with the user's filters (each row names its category), launch them as `product_ids` with the same choices, and review the results with the user. Then run the rest into the same Enrichment (`add_to_enrichment_id`) with the user's filters again. Those filters still hold the 5 trial products, so say before the go-ahead that those run again and get new candidates; on `photos: without`, the trial products that got photos drop out. The user may decline the trial.
4. **Size it, then launch**, as the `naratix-operator` skill's *The go-ahead* says: the size gives the products and photos the run covers. Above the per-run limit the note says so, and the go-ahead confirms that size too; a run past the hard ceiling is refused, so split the selection. The launch card follows the run; without one, follow it as the `naratix-operator` skill describes. Products an attributes enrichment is still finding photos for wait for it: the app shows them under "Waiting for attributes", and each starts once that enrichment has finished, so it works on the photos it found. That is the run working, not stuck.

**Completion:** the launch returned an `enrichment_id` and the user has the link to the image review.

## Review the results

`list-runs` with `kind: enrichment` gives an Enrichment that improved images its `candidate_counts`, a count that draws no view. What each outcome means for the user:

- `strong`: the checks rate the result good.
- `review`: a person should look before it goes in.
- `rejected`: the checks found a problem, such as another product or a cut-off edge.
- `applied`: in the product's photos, approved or put in at once.
- `skipped`: the product ran and nothing came out of it, such as no photo of it found on the web.
- `failed`: the run failed for that product; offer `control-enrichment` with `action: retry_failures`.

`candidate_counts` holds the first four; products `skipped` or `failed` come from `show-images`' `counts` (failed also from the Batch's `failed` in `list-runs`).

`show-images` with the `enrichment_id` reads the candidates as the image review shows them: per product, each edit beside its original, the generated variants and the photos found, with their status, the checks' verdict and what approving would do: use it to tell the user what needs their eye.

- `status: review` keeps the ones a person should look at; `category_id`, `search` or `product_ids` (several named products in one call) narrow it.
- `categories` lists the run's categories 25 at a time: `category_search` finds one by name, and `category_offset` set to the answer's `categories_next_offset` (null on the last page) reads the next 25.
- Narrow it to the user's ask, as the `naratix-operator` skill's *One card per question* says; the view opens on your filters and shows the run's progress while it goes on.

Approving, dismissing or undoing one by one is the user's call: in the image review the launch links to, or in the image candidates view `show-images` opens in apps that show views.

When the user explicitly asks to approve all the strong ones, it is `control-enrichment` with `action: approve_all_strong` and the `enrichment_id` (the whole Enrichment, never one Batch). Without `confirm` it counts the strong images and changes nothing.

Where they go is the user's answer for every approval, asked once, in the chat: no card asks it. Through the app's question tool where it has one, three choices: beside the photos (`gallery: add`), in place of the photos they improve (`gallery: replace`), and "Not now". An answer they already gave goes as `gallery` with the count, and only the go-ahead is asked. Call again with their answer and `confirm: true`. When none of the strong images improves a photo (generated variants, photos found), the count says so and there is nothing to ask: they are added.

**Completion:** the user knows how many results are strong, to review, rejected or failed and where to approve them, and any approve-all ran only on their word, with where the images go answered.
