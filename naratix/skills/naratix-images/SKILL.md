---
name: naratix-images
description: Improving product photos in Naratix with Image Studio — finding new photos on the web, cleaning or replacing backgrounds, generating new photos from existing ones — and approving the results. Use when the user wants better, more or cleaner product images, asks how an image run went, or wants its results approved.
---

# Naratix images

**Image Studio** works on a product's photos and proposes **image candidates**: new or improved photos a person approves before they reach the product. It can find new photos of the product on the web, edit the backgrounds of the existing ones, or generate new photos from them. Every launch is sized and agreed first (the operator skill's go-ahead rules). Every AI-made image is marked as AI-modified, which the law requires.

## Improve images

1. **Pick the products** with the products list's filters on `launch-images`.
2. **Settle the choices** from what the user wants:

   | Choice | Default | What it means |
   |---|---|---|
   | `run` | — | `find_images` (new photos from the web), `edit_backgrounds`, `generate_variants` (new photos made from the existing ones). One or more. |
   | `images` | all | Which of each product's photos: `primary`, `first_2`, `first_4` or `all`. |
   | `apply_to` | main | The selected main products, their variations, or both. |
   | `quality` | 2k | Output size: 1k, 2k or 4k. |
   | `background` | white | White, a `scene` (describe it in `scene`), or `keep` the photo's own. |
   | `variants` | 1 | With `generate_variants`: new photos per product, up to 4. |

   Two choices change what happens to the shop's photos; use them only when the user asks: `auto_approve_strong` approves the strong results without review, and `replace_originals` puts approved images in place of the originals instead of alongside them.
3. **Size it:** call without `confirm`. The result gives the products and photos the run covers. Above the per-run limit the note says so, and the go-ahead confirms that size too; a run past the hard ceiling is refused, so split the selection.
4. **Launch** with `confirm: true` after the user's yes, then follow it as the operator skill describes.

**Completion:** the launch returned an `enrichment_id` and the user has the link to the image review.

## Review the results

`list-runs` with `kind: enrichment` gives an Enrichment that improved images its `candidate_counts`: `strong` (the checks rate them good), `review` (a person should look), `rejected`, and `applied`.

`show-images` with the `enrichment_id` reads the candidates as the image review shows them: per product, each edit beside its original, the generated variants and the photos found, with their status, the checks' verdict and what approving would do. `status: review` keeps the ones a person should look at; `category_id` and `search` narrow it, and `after` pages. Use it to tell the user what needs their eye.

Approving, dismissing or undoing one by one is the user's call: in the image review the launch links to, or in the image candidates view `show-images` opens in apps that show views. What the user does in the view reaches you as context on your next turn.

When the user explicitly asks to approve all the strong ones, it is `control-enrichment` with `action: approve_all_strong` and the `enrichment_id`. Without `confirm` it counts the strong images and changes nothing. Where they go is the user's answer for every approval, asked once: beside the photos (`gallery: add`) or in place of the photos they improve (`gallery: replace`). An answer they already gave goes as `gallery` with the count and the card shows it picked; otherwise the card asks it, or you ask in the chat. Never pick for them, and never ask again what they answered. Call again with their answer and `confirm: true`. When none of the strong images improves a photo (generated variants, photos found), the count says so and there is nothing to ask: they are added. Never approve on your own judgment.
