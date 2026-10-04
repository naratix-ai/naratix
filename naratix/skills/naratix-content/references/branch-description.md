# Description template branch

Produces a description template: a **Template DSL body** paired with its **Companion Prompt**, plus a stylesheet when the channel keeps styling.

Two ways in — author from the profile, or derive from descriptions the shop already has. They converge at Deliver.

Author the most ambitious template the ceiling allows: a limited channel gets a clean text-only template, a rich channel gets headings, lists, tables, images, **and a look**. Never emit a tag outside the ceiling, and never a class the channel strips.

## Authoring from scratch

1. Confirm the description language (connected: offer the shop's taxonomy languages from `list-taxonomies`; it need not match the interview language). The taxonomy picked here is also **the taxonomy this template serves** — keep its id and pass it to every mapping call and test drive, or the drive silently tests the shop default against no enriched attributes. **Romanian gets one more question: with or without diacritics?** The engine strips ă/â/î/ș/ț unless the template says to keep them, and some channels expect ASCII. Never assume either way.
2. Ask what the description should cover: the sections, the selling angle, and **how long it should be in words**. Length is a setting, not a mood — a customer who says "short, the channel truncates" needs a number (80–150 short, 200–350 standard, 400+ long). Brand voice and audience come from the profile, not from new questions.
   Then the **tier**: *Pro* is the model every template has used so far; *Lite* is a faster, lighter model. The same template reads differently on each, so test on the tier the customer will use, and on both when they are unsure.
3. Compose the **Template DSL body**: syntax, image-placement recipes, the loops-only image rule, and the gotchas that silently break templates are in [template-dsl.md](template-dsl.md) — read it before writing the first line.
4. When the profile's `styling` is anything but `none`, compose the **stylesheet**: give the DSL body class names and write the CSS they refer to, built from the profile's `brand_look`. The rules that keep a look honest — class matching in both directions, the one-light-scheme rule, what `inline` and `none` change — live in the look section of [template-dsl.md](template-dsl.md).
5. Compose the **Companion Prompt** as the DSL's inseparable other half — brand voice, audience, and guidance for every placeholder name in the DSL body: what the model already receives, the shape and the rules are in [companion-prompt.md](companion-prompt.md). Changing the DSL's placeholders means regenerating the matching prompt sections.

## Deriving from descriptions the shop already has

Most shops already have descriptions they like — agency-written, tuned over years. Pasted examples beat interview answers: they are direct evidence of the channel's markup, the brand's voice, and the real length. When the customer offers existing descriptions, prefer this over authoring from scratch.

1. Ask for two or three examples of the **same kind of product**, pasted as the markup the channel actually renders. Product-page links work too — connected, fetch each with `fetch-shop-page`; the fallback ladder is in the reference. What differs between the examples is content, what repeats is literal — one example alone turns that fact into guesswork, so say so when it is all they have.
2. Derive the DSL body, stylesheet, and Companion Prompt with the diff method in [derive-from-example.md](derive-from-example.md) — read it before deriving anything.
3. Show the **split table** — what was treated as fixed versus variable — right after the diff, and wait for corrections. Nothing is created before the customer has seen it.
4. Then, in this order: the marketplace by name and what it displays (ceiling and styling, when the examples come from another channel; see the reference), the remaining profile questions only, `save-generation-profile` with every field, `create-template` with the `word_count` the examples themselves measured (carried into Deliver), then the test drive offer.

The evidence in the examples also pre-fills the ceiling and styling questions when the customer opened by handing them over, rather than re-asking.

## Deliver

**Connected:** `create-template` (`template_type`, name, language, `dsl_body`, `companion_prompt`, `word_count`, `tier`, `use_diacritics` for Romanian, and `stylesheet` when there is one).

**Always pass `word_count`** — omitting it silently applies the shop-wide default instead of what the customer asked for.

To evolve an existing template, call `create-template` again with `from_template_id`: it copies on write, so the original survives untouched, a new template comes back, and anything omitted is carried over. Pointing the shop default or a mapping at the copy is a separate, explicit step — see [branch-mapping.md](branch-mapping.md).

**Standalone:** present the outputs as labelled copy blocks plus app instructions — *Templates → New description template → switch the editor to Code → paste the DSL into Template body and the Companion Prompt into Companion prompt → set Target word count (this is `word_count`) → when there is a stylesheet, paste the CSS into Stylesheet → for Romanian, set Use diacritics to the customer's answer → Save.* Name a field only as the app's template screens label it; the steps above are those labels.

## Walk before delivering

Check the pair once: the DSL body against §What silently breaks a template, the stylesheet against the look section's rules, and the Companion Prompt against §Rules that make the pair work and [Writing instructions](../../naratix-operator/references/writing-instructions.md).

Then, connected, offer a [test drive](test-drive.md). Seeing one real description beats any description of it.

**Completion:** the pair is delivered (created on the shop, or both blocks presented), the walk found nothing to fix, and — when derived — the customer confirmed the split and the round-trip check reproduced an example.

## Category and brand page descriptions

Naratix writes SEO descriptions for **category pages** and **brand pages** too. Same shape: a template carries the kind of page it writes for, and a shop holds a default template per kind.

Author one exactly as above, passing `template_type: category-description` or `brand-description`. These need the connection: the app creates product description templates only, so standalone, offer the copy blocks and say a category or brand page template cannot be created in the app yet. `template_type` is the one vocabulary every template tool speaks; `list-templates` shows every kind and narrows to one when passed.

To generate, call `generate-description` with `subject_type: category` or `brand` and the matching `subject_id` — find either with `search-catalog` (`entity: category` / `entity: brand`). With no `template_id` they use the shop's default template of that kind — *strictly* the default: a freshly created template is not it until `set-default-template` says so, and generation refuses rather than silently picking one. So either pass the new template's id explicitly, or set the default first.

The result appends to the record's description history. It does **not** overwrite the category/brand *definition* the classifier reads (`category_info` / `brand_info`) — separate fields, never touched by generation. Version history, export, and the "never lose generated content" guarantee all work as they do for products.

Outside this wizard, the same content can be produced from the Naratix app's **Categories** and **Brands** screens (per-record *Descriptions* pages, plus bulk generate and export), or over the Naratix connector API, which their developer would already have credentials for. Mention these when a customer wants to work in bulk without an agent.
