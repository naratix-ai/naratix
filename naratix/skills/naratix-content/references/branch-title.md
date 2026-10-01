# Title template branch

Produces a title template: a title prompt, and optionally a long-title prompt — not a DSL pair.

1. The profile's `title_style` holds style, component order, and length cap; voice comes from `brand_voice`. Ask only what is missing, plus the title language, and whether the shop exports a second, longer title (a subtitle or long-title field) — if it does, it gets its own prompt.
2. Compose the prompt against **Title prompts** in [companion-prompt.md](companion-prompt.md): a title prompt is the whole instruction the model gets, so it states the task, the facts rule, the style, the order, the cap, `{{language}}`, one line of voice, and the second text. Check it by the operator skill's *Writing instructions* before delivering.
3. Deliver:
   - **Connected:** `create-template` with `template_type: product-title`, which takes a `prompt` (and `long_title_prompt` for the second title) instead of the DSL fields. Pass `from_template_id` to revise one — same copy-on-write semantics as a description template; what you leave out, including the long-title prompt, is carried over.
   - **Standalone:** the prompt(s) as copy blocks plus *Title Templates → New → paste into the prompt field* (and the long-title field).
4. Connected: offer to **try it**, running the [test drive](test-drive.md) but ending in `generate-title` (`product_id`, the new `title_template_id`, the taxonomy). It returns a `run_id`; `list-runs` (`kind: generation`, that `run_id`) returns the finished title once `completed`, so measure it against the cap with the customer. The new title lands in the product's title history.

A title prompt that has never written a title is a guess. Titles fail on the products with the least data and on the ones with the most, so the test drive covers both before the cap and the order are trusted.

**Completion:** the prompt is delivered, restates all three `title_style` answers (style, order, cap) plus the language, says what happens to a missing element, and — when the shop exports a second title — says what that title is, in its own prompt or as `sub_title`; the customer has seen real titles or declined.

## SEO meta

`generate-seo` writes `meta_title` and/or `meta_description` for one product. It has no template — pass the language by English name and choose which fields to write (both default to true). The result goes to the product's SEO version history and reads back the same way as a title: `list-runs` with the `run_id` it returned.

Offer it when a customer talks about search listings or snippets rather than the product page itself.
