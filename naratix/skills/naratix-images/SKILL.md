---
name: naratix-images
description: Improving product photos in Naratix with Image Studio — finding new photos on the web, cleaning or replacing backgrounds, generating new photos from existing ones — and approving the results. Use when the user wants better, more or cleaner product images, asks what can be done for their photos, asks how an image run went, or wants its results approved.
---

# Naratix images

**Image Studio** works on a product's photos and proposes **image candidates**: new or improved photos a person approves before they reach the product. It can find new photos of the product on the web, edit the backgrounds of the existing ones, or generate new photos from them. One exception to approval: with `find_images`, a product that has no photos can get its strong found photos at once, so the other steps have something to work on, when the user says so. Every launch is sized and agreed first (the `naratix-operator` skill's go-ahead rules). A generated photo or a photo set in a scene is marked as AI-made, which the law requires; a photo found on the web comes as it was published.

## Recommend from the shop's photos

Start from the shop's own gaps. Count them with `list-products` (its `total`), per category where that helps: `photos: without` (no photos), `photos: one` (a single photo), `photos: several`. Tell the user what you found ("212 products have one photo"), then recommend:

| What the shop needs | Run |
|---|---|
| Photos for products that have none | `find_images` |
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
   | `background` | white | White, a `colour` (`background_colour`, as #RRGGBB), a `scene` (describe it in `scene`), or `keep` the photo's own. `keep` applies to edits only: generated photos come out on white or in the scene. |
   | `aspect` | 1:1 | The shape edited and generated photos come out in, width:height, such as 4:5 when a marketplace asks for it. |
   | `min_size` | — | With `find_images`: leaves out found photos smaller than this many pixels on a side. |
   | `variants` | 1 | With `generate_variants`: new photos per product, up to 4. |

   Two choices change what happens to the shop's photos; use them only when the user asks: `auto_approve_strong` approves the strong results without review, and `replace_originals` puts approved images in place of the originals instead of alongside them.

   Two questions only the user answers, once, in the chat or on the card: with `find_images` on a selection holding products that have no photos, whether those get the strong found photos at once (`fill_empty`), and with `auto_approve_strong` on `edit_backgrounds`, whether those photos replace the originals (`replace_originals`). Explain `fill_empty` before the go-ahead, and pass an answer the user already gave; the card asks what is missing, and their go-ahead needs both answers.

   Set only in Image Studio in the app: a gradient background, which websites to take photos from or avoid, and written guidance for the generated photos or for how results are checked. When the user asks for one of these, say so before launching and point to the app (`panel_url`) rather than launching with the defaults.
3. **Try 5 products first** when the selection is over about 20 products: pick 5 spread over its largest categories (`list-products` with the user's filters), launch them as `product_ids` with the same choices, and review the results with the user. Then run the rest into the same Enrichment (`add_to_enrichment_id`) with the user's filters again. Those filters still hold the 5 trial products, so say before the go-ahead that those run again and get new candidates; on `photos: without`, the trial products that got photos drop out. The user may decline the trial.
4. **Size it:** call without `confirm`. The result gives the products and photos the run covers. Above the per-run limit the note says so, and the go-ahead confirms that size too; a run past the hard ceiling is refused, so split the selection.
5. **Launch** with `confirm: true` after the user's yes. Its launch card follows the run; without one, follow it as the `naratix-operator` skill describes.

**Completion:** the launch returned an `enrichment_id` and the user has the link to the image review.

## Review the results

`list-runs` with `kind: enrichment` gives an Enrichment that improved images its `candidate_counts`, a count that draws no view. What each outcome means for the user:

- `strong`: the checks rate the result good.
- `review`: a person should look before it goes in.
- `rejected`: the checks found a problem, such as another product or a cut-off edge.
- `applied`: in the product's photos, approved or put in at once.
- `skipped`: the product ran and nothing came out of it, such as no photo of it found on the web.
- `failed`: the run failed for that product; offer `control-enrichment` with `action: retry_failures`.

`show-images` with the `enrichment_id` reads the candidates as the image review shows them: per product, each edit beside its original, the generated variants and the photos found, with their status, the checks' verdict and what approving would do. `status: review` keeps the ones a person should look at; `category_id`, `search` or `product_ids` (several named products in one call) narrow it. `categories` lists the run's first 25 categories; `category_search` finds the others by name. Narrow to what the user asks about instead of paging: each call draws the view again, and in apps that show views the user pages, filters and searches in the view, which opens on your filters and shows the run's progress while it goes on. Where no view shows, page with `after`. Use it to tell the user what needs their eye.

Approving, dismissing or undoing one by one is the user's call: in the image review the launch links to, or in the image candidates view `show-images` opens in apps that show views. What the user does in the view reaches you as context on your next turn.

When the user explicitly asks to approve all the strong ones, it is `control-enrichment` with `action: approve_all_strong` and the `enrichment_id`. Without `confirm` it counts the strong images and changes nothing. Where they go is the user's answer for every approval, asked once as the `naratix-operator` skill's *Cards* says: beside the photos (`gallery: add`) or in place of the photos they improve (`gallery: replace`). An answer they already gave goes as `gallery` with the count; otherwise the card asks it, or you ask in the chat. Call again with their answer and `confirm: true`. When none of the strong images improves a photo (generated variants, photos found), the count says so and there is nothing to ask: they are added.

**Completion:** the user knows how many results are strong, to review, rejected or failed and where to approve them, and any approve-all ran only on their word, with where the images go answered.
