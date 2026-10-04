# Companion Prompt patterns

The Companion Prompt is the Template DSL body's inseparable other half. It carries three things: **brand voice**, **audience**, and **guidance for each placeholder, keyed by its name**. Write it by [Writing instructions](../../naratix-operator/references/writing-instructions.md); this page adds what is particular to descriptions.

## What the model already receives

Every description call is built the same way. The Companion Prompt adds to it and never repeats it:

- **Naratix's own rules**, before the Companion Prompt: the product data is the only source of facts; typical statements about what a product of this kind does are fine, invented specifics are not; no prices, stock, warranties, promotions, internal codes, comparisons with other brands or superlatives; brand and model names as the data writes them; write in the description's language and translate everything else; only text in each field, since the template adds headings and layout; no notes about the data or the instructions.
- **The language and the total length**, from the template's settings: "about N words in total; when the template instructions give a length per section, follow those".
- **The placeholder names**, as the names of the fields the model fills. A name is an instruction: `benefit_heading` asks for a heading about a benefit, `creative_header` invites wordplay.
- **The product data** as the message: title, category path, the feed's description, the enriched attributes.

After the answer, Naratix removes any heading a model wrapped around a field, straightens French apostrophes inside words, and strips Romanian diacritics when the template says so. A Companion Prompt line asking for any of these is a second copy of the rule, and a character rule can break the text it means to fix: one model dropped every apostrophe it was told to straighten.

## The shape

```markdown
## Brand voice
<the profile's brand_voice, as instructions: person, register, sentence length, what the brand never claims>

## Audience
<the profile's audience, as instructions: who reads, what they know, what they need translated into outcomes>

## Section guidance
- <placeholder name>: <what this field does for the reader, its length in words or sentences>
- <array placeholder>: <how many items, and what one item is>
- image_count: <how to choose 0–4 for this kind of product>
```

## Rules that make the pair work

1. **Cover every placeholder.** Each placeholder name in the DSL gets a guidance line under `## Section guidance`, addressed by its exact name. An unguided placeholder gets generic filler; a guided one gets the brand.
2. **Lengths add up.** Per-section lengths must sum to about the template's `word_count`: sections of 80–120, 120–180 and 120–180 words plus a list and an FAQ describe 600 words, whatever the total says, and the model writes to the sections. Models land above the total even when the numbers agree, so set `word_count` to the length the customer wants and never pad it.
3. **Match length to the data.** A long template on a product with a handful of facts repeats them. Offer long templates for categories whose products carry rich data, and say so when a customer wants length everywhere.
4. **Arrays get count + per-item shape.** For `array<…>` placeholders say how many items and what one item is ("4–6 bullets, each one capability and what it brings").
5. **Say what the first sentence does.** Where the opening matters, name its job — the product's main use, or its strongest benefit for this audience — and check the openers on a test batch.
6. **`image_count` is guidance, not markup.** When the DSL uses `@images({{integer::image_count}})`, tell the model how to choose the number; the engine clamps it to the images that exist.
7. **Voice and audience come from the profile.** Rephrase the stored `brand_voice` and `audience` into working instructions; don't invent a new voice at authoring time. Leave tone to the voice lines: a persona ("detect the tone from the category") adds a second voice.
8. **Language rules match the template's language.** Spacing before `:` and `;` is French; a rule written for one language is wrong in a template for another.
9. **Injected values need no guidance.** `product_title` and `product_images` are filled by the engine, never by the model — listing them under Section guidance is noise.
10. **Edit in lockstep.** Renaming, adding, or removing a DSL placeholder means regenerating the matching guidance lines in the same delivery. A prompt that guides placeholders the DSL no longer has (or misses ones it gained) is the most common way quality quietly degrades.

## Title prompts

A title template's prompt is the **whole** instruction the model gets: Naratix adds nothing before it, only the product data as the message (the attributes, the category, and the feed's own title when it is a real name). So a title prompt states everything a Companion Prompt leaves to Naratix:

- **the task** in one line: a product title for an online shop;
- **the facts rule:** only what the data gives; a missing element is left out, never guessed;
- **the style**, from `title_style.style` — keyword (dense with the terms a buyer searches), natural (a fluent phrase), minimal (the bare essentials);
- **the order** of the parts, from `component_order`, as a format with slots;
- **the length cap** in characters, from `length_cap`, with which part to drop first when a title would pass it;
- **the language** as `{{language}}`, which Naratix fills from the template's language, in a test drive and a launch alike;
- **one line of brand voice**, condensed from `brand_voice`;
- **the second text**, below.

A title call writes two texts. Without a long-title prompt, one call writes both `title` and `sub_title` from this one prompt, so it must say what `sub_title` is — the longer title or subtitle an export can send to a channel field — and give it its own cap. With a **long-title prompt**, each text gets its own call and its own prompt — the title prompt then speaks only of the title, and the long-title prompt, written the same way, only of the long title. On a real catalogue the separate prompts were preferred about five to one. Offer a long-title prompt whenever the shop exports that second title.
