# Test drive — try a template on real products

Offer this after authoring a template, and after a re-mapping puts a different template in front of a category. It needs a connection; standalone, say a test drive needs one rather than attempting it.

A template reads differently on a product with five facts than on one with fifty, and the same product can come out differently twice. So a template that will write a whole category earns a **test batch**: three products — one `thin`, one `ready` with few product details, one `ready` with many — each written twice, six texts. A quick look at one product is fine when the customer only wants a feel for it; say what it cannot show.

1. `list-test-products` (pass the taxonomy the template serves) returns candidates best-first, each with a verdict and a reason. **Show them and let the customer pick — picking for them is not allowed, even when the ranking makes the answer look obvious.** For a test batch, ask for those three: the row's `attributes` count tells the two `ready` ones apart, and when no row is `thin`, the `ready` one with the fewest stands as the thin end: say so. Repeat each reason in their words: a `bare` product makes any template look worse than it is, so leave it out and say why.
2. Say which products and how many texts first (six for a test batch) and wait for an explicit yes to *those products*.
3. `generate-description` (`subject_type: product`, `subject_id`, the template, the taxonomy) returns a link and a `run_id` immediately, once per product and run. Hand the links over right away and say they fill themselves in. In apps that show views, each call draws a card that fills in by itself, the text beside the product facts it was written from, and its context tells you when it lands. Without a template it uses the one a launch would — the product's category mapping, else the shop's default; without a taxonomy, the shop's default taxonomy, and the result names it. A test replaces nothing live: the text joins the product's history and its current text stays.
4. Where no card shows, `list-runs` (`kind: generation`, each `run_id`): `completed` comes with an excerpt of the text — report it ready; `failed` comes with the reason — say plainly that it failed and nothing on the product changed.
5. Read the results with the customer, in their language, judging the texts as written in the shop's language, against the checks below. Read them yourself as well; a second opinion from another model is not a reading.

For a title template, run the same shape but end in `generate-title`; see [branch-title.md](branch-title.md).

## What to check

- **Length** against the template's sections and total. Models run long; a result well past the target means the numbers in the template do not add up, or the target was padded.
- **The same facts repeated.** A thin product under a long template says its few facts several times. That is the template's length, not the data.
- **Sentences about the data** — "as mentioned in the data", "the listing describes it as" — instead of about the product.
- **The opening.** A first sentence that restates the title, or several products opening the same way.
- **Invented specifics:** a compatibility, an included item, a figure the data does not give. Typical statements about what a product of this kind does are fine.
- **Markup** the template did not ask for: stray tags, bold inside lists.
- **Titles:** the cap on the longest product, the order, and what a missing element did.

Each finding points to one line of the template to change. Change that line, then run the same products again.

**Completion:** the customer has the links, knows which checks passed and which line a failure points to — or declined the offer.

## When several products come back thin

One thin result is a thin product. Several thin the same way is the catalogue, not the template — stop adjusting the template and offer a quality check instead (the `naratix-quality` skill).
