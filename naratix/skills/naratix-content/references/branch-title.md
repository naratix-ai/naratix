# Title template branch

Produces a title template: a title prompt, and optionally a long-title prompt — not a DSL pair.

1. The profile's `title_style` holds style, component order, and length cap; voice comes from `brand_voice`. Ask only what is missing — a shop with no profile is asked just these title questions and one line of voice — plus the title language, and whether the shop exports a second, longer title (a subtitle or long-title field) — if it does, it gets its own prompt. Ask the title language as its own question in the same one-at-a-time sequence, never as an announcement: propose the language of the taxonomy the titles are for (`list-taxonomies`) as the first choice and leave room for another ("Titles in French, your taxonomy's language, or another language?"). It is normally that one, not the language of the chat: titles are written in their template's language, in a test drive and a launch alike.
2. Compose the prompt against **Title prompts** in [companion-prompt.md](companion-prompt.md): a title prompt is the whole instruction the model gets, so it states the task, the facts rule, the style, the order, the cap, `{{language}}`, one line of voice, and the second text. Check it by [Writing instructions](../../naratix-operator/references/writing-instructions.md) before delivering.
3. Deliver:
   - **Connected:** `create-template` with `template_type: product-title`, which takes a `prompt` (and `long_title_prompt` for the second title) instead of the DSL fields. Pass `from_template_id` to revise one — same copy-on-write semantics as a description template; what you leave out, including the long-title prompt, is carried over.
   - **Standalone:** the prompt(s) as copy blocks plus *Templates → New title template → paste into Prompt* (and the long-title prompt into Long title prompt).
4. Connected: offer to **try it**, running the [test drive](test-drive.md) but ending in `generate-title` (`product_id`, the new `title_template_id`; the taxonomy, omitted for the shop's default taxonomy). Without `title_template_id` it uses the template the product's category is mapped to, else the shop's default, as a launch would, and says which. It returns a `run_id`; `list-runs` (`kind: generation`, that `run_id`) returns the finished title once `completed`, so measure it against the cap with the customer. A test replaces nothing live: the title joins the product's title history and the current one stays. Then ask where the template applies — the whole shop or one category and what is under it — by [branch-mapping.md](branch-mapping.md); `list-templates` names the default it would replace (`is_default`).

A title prompt that has never written a title is a guess. Titles fail on the products with the least data and on the ones with the most, so the test drive covers both before the cap and the order are trusted.

**Completion:** the customer answered the title language as a question; the prompt is delivered, restates all three `title_style` answers (style, order, cap) plus the language, says what happens to a missing element, and — when the shop exports a second title — says what that title is, in its own prompt or as `sub_title`; the customer has seen real titles or declined, and chose where the template applies.

## SEO meta

`generate-seo` writes `meta_title` and/or `meta_description` for one product. It has no template — pass the `taxonomy_id` the bulk run will use, so the test drive is written in that taxonomy's language (`list-taxonomies` shows it), the one `launch-content` writes in; `language` only overrides it. Choose which fields to write (both default to true). Like a title test it replaces nothing live: the result joins the product's SEO history, and reads back the same way, `list-runs` with the `run_id` it returned.

Offer it when a customer talks about search listings or snippets rather than the product page itself.
