---
name: naratix-quality
description: Quality checks in Naratix — how complete and consistent a shop's product data is, the check rules that flag faulty products, and the rules a market expects. Use when the user asks how good their catalogue is or wants it checked, wants a rule that flags products ("flag dresses with no colour", a legal requirement), asks why written content keeps coming out thin, asks what a check found, or wants rules written for a taxonomy.
---

# Naratix quality

A **check** labels the products it finds fault with and changes no product data. It makes no AI calls, so offer one whenever the data is in doubt: when the user asks how good their data is, when written content comes out thin in the same places (a template writes only from what the products carry), and before a large content launch. Checks run through the Naratix connection; without it, say so.

The go-ahead, errors and following background work go by the `naratix-operator` skill; filling what a check or rule finds empty goes by the `naratix-attributes` skill. The guided starter *Check my catalogue* walks this skill's order: a user who started it is already here.

## Explaining checks to the user

The first time you run a check in a conversation, and whenever the user asks what checks are for or wants an example rule, say it in one line: a check flags missing or contradictory product information before a marketplace or a regulator objects, and labels the products it flags. It helps spot what is missing and is not legal advice.

Whenever you offer the user a rule of their own or give an example rule, take it from *The user's market*: in that same turn, ask the market once and give one rule that market expects, in the taxonomy's own attribute names.

Name the engines in plain words, health first, then the shop's own rules, and give `applicability` one line, as *The three engines* puts it, its taxonomy included. Say more only when they ask what it is: its rules come from the cold-start, which works from enriched products, so it has nothing to check until the cold-start has run.

## The three engines

Pick one with `engine`:

- **`health`** scores each product's own completeness: title, description, images, identifiers. It needs no setup and no rules, so it comes first.
- **`consistency`** flags the products that break the taxonomy's check rules. It needs a `taxonomy_id`.
- **`applicability`** finds attributes that should be filled and are not, and attributes filled that do not belong on that kind of product; it works per taxonomy, so it needs a `taxonomy_id`.

## Run a check

1. **Choose the products** with the products list's filters, so a selection here is the same set of products there. With no filter, the check covers the whole shop, the variations of the selected products included. The filters a check usually needs:
   - `category_id` (from `search-catalog`): that category and everything beneath it; `include_subcategories: false` keeps to the category alone.
   - `taxonomy_id`: the taxonomy the categorization, enrichment and confidence filters read. A category brings its own.
   - `review_status`, `labels` (named "Group: Label"; `search-catalog` with `entity: label` finds them) and `health` (flag names). Labels re-check only what is flagged now, which is what a second pass wants. A name the shop does not have comes back with the ones it has.
   - `product_ids`, `codes`, `external_ids` or `titles` for named products; a variation stands for its main product. The result's `selection` says which were not found: tell the user.
2. **Rules first**, for `consistency` and `applicability`: see *Rules have to exist first*.
3. **Run** `run-quality-check`. `consistency` and `applicability` take any selection, a whole catalogue included; `health` scores every product in one pass, so it caps one run, and over the limit it refuses with the count and the limit.
   - **The user asked for the whole catalogue**, or a set past the limit: split it yourself into at most 4 parts (about 20,000 products), without asking, since a check needs no go-ahead. Pass the taxonomy's top-level categories as `category_ids` in groups, each with its subcategories, plus one pass with `categorization: uncategorized` and the taxonomy's `taxonomy_id`, itself one of the 4, and run them all; a group refused in turn gives its count, so split it again. Each part draws its own card, the one place a question draws several, so make as few parts as the refusal's count and limit allow (8,400 products over a 5,000 limit make two groups). When the parts would pass 4, at the start or on a re-split, stop splitting: say in one line how many parts the catalogue would take, and offer the health check for the categories they care about, or `consistency` and `applicability`, which take it whole. Take the categories from answers already in the chat; only when nothing in hand names them, walk the tree once (`list-taxonomies` with its `taxonomy_id`), the check's one taxonomy card, with the `taxonomy_id` an answer already gave (`search-catalog`'s `taxonomy`, a rule's `taxonomy_id`) rather than listing the taxonomies first. Tell the user in one line that the catalogue is checked in N parts, and that a description shared across parts is counted within each part only; follow every `run_id`, and read the findings together.
   - **The user asked for part of it** and the part is unclear: ask which.
4. **Follow it.** The call returns at once with a run still going: say so, then follow it as the `naratix-operator` skill's *Following work* says. Where no card shows, `list-runs` (`kind: quality-check`) with the id the call returned says whether it finished: `run_id` with its `engine` for health and applicability, `batch_id` for consistency, which fans out into several runs sharing it once a worker picks the check up. A catalogue-sized run takes a while: check back at a sensible interval. When the user comes back without the id, `list-runs` lists every engine's runs, each tagged with its `engine`: match on that rather than taking the newest row.
5. **Read the findings** with the user and name what each implies. Attributes that are simply missing are a job for enriching them, `launch-mining` (the `naratix-attributes` skill), rather than for a better template. A rule the user disagrees with is changed or switched off in Naratix, on the Consistency Rules page (Applicability Rules for applicability); the products are fine.

**Completion:** the user knows which engine ran over what, and either has the findings or knows the run is still going and how it will be checked.

When an attribute Enrichment finishes, its products are checked without asking, as the `naratix-attributes` skill's *Follow the run* says.

## Rules have to exist first

`consistency` and `applicability` evaluate rules. A taxonomy nobody has written rules for comes back clean for products nobody examined, which reads exactly like good news and is not: `consistency` then checks placeholder values only (below), and `applicability` starts nothing (its `reason` is no_applicability_rules), so offer its cold-start, previewed first.

Read `list-quality-rules` before running either engine, and in a catalogue check, read `list-runs` with `kind: enrichment` alongside it: whether an attribute Enrichment has finished says which rules you can offer. For `consistency`, read it with the `taxonomy_id` and `status: active`: the taxonomy has rules of its own switched on when `own` is above 0; the rest are the built-in pack, which only flags values left as a placeholder word such as "null", "undefined" or "...". In a catalogue check, a taxonomy whose `own` is 0 gets no `consistency` run: say only placeholder values would be checked, and offer rules as below. Asked for one anyway, it runs, and its result says only placeholder values were checked (its `reason` is junk_pack_only): report it as that, never as consistent data.

Rules come from the AI (*Cold-start*) or from the user's own words (*A rule described in words*).

Every time you offer rules, name both routes in one line, in plain words, so the user can choose: their own rule with `create-quality-rule`, from the moment products are in, checked once the products are enriched; and the rules the AI writes for the whole taxonomy with `cold-start-rules`, which tests its rules on enriched products, so it is either "ready now, your products are enriched" or "waits for the first enrichment", as `list-runs` with `kind: enrichment` says.

## Cold-start: rules written by the AI

`cold-start-rules` writes a taxonomy's rules with the AI, on the user's yes:

- Call it without `confirm` first. It starts nothing and says what it covers: the taxonomy (for `consistency`, its categories with no rules yet) or the categories you named, with the ids of any it left out. Put that to the user, with no number for what it covers, and call again with `confirm: true` only after their explicit yes. Say why it can work now: it writes and tests its rules from the enriched products. Say how many are enriched only from a count of enriched products: the `done` of the Batch whose `kind` is `attributes`, that Batch alone, in `list-runs` (`kind: enrichment`), or a rule trial's `enriched_product_count`, where 1,000 means 1,000 or more; never a Batch's `products`, which counts its failed products too.
- It writes rules and the ones that hold up go live, so start it for a taxonomy the user wants rules on, once its products are enriched. Over products nobody has enriched the AI has nothing to test its rules on and few switch on: the card says so, and the user may still start.
- For `consistency`, the AI tests the rules it writes on sample enriched products and switches on the ones that hold up; the rest stay drafts that a person looks at in Naratix. `applicability` rules apply as soon as they are written. `health` has no cold-start: its scoring is built in.
- A rule comparing two attributes of one product (width against height) and a rule on which attributes apply to which kind of product both come from here: pass the user's words as `steer_prompt`.

When the products are already enriched, one check follows the cold-start by itself as soon as the rules are ready. The started result names it: follow `check_batch_id` as the `batch_id` of `list-runs` (`kind: quality-check`) for `consistency`, or `check_run_id` as the `run_id` with `engine: applicability`, pending until the rules exist. A `consistency` check shows no run until the first category's rules are ready, then its runs arrive category by category; it is done when its batch reads `unfinished` 0 and `product_count` at `check_product_count`, which stays null until every part has started. That is the check: follow it rather than starting another, and offer to read the findings with the user once it is done. With nothing enriched yet no check follows, nor for an account without permission to edit products (the note says so): offer one once the products are enriched.

**Completion:** the user said yes to what the cold-start covers, and its check is being followed to its end, or they know why none follows.

## The user's market

A legal or market rule depends on where the shop sells. Ask once per conversation, offering a guess for them to confirm, built from the taxonomy's language and the channels' names (`list-connectors`). Take the language from the `search-catalog` answer the rule's attribute search gives anyway (`taxonomy.language`), so the question draws no card of its own; a language is spoken in several countries, so the guess is theirs to correct. When the user already named a country, still ask, in one confirming line that names the guess with its evidence ("Your taxonomy is in French and your Mirakl marketplace sells in France: France only, or other countries too?"), and carry on in the same turn with the attribute search and the trial, so the question adds no round trip. Every rule you offer as an example is one of that market's, never a generic one. Then propose one rule that market expects of their products (an energy class left empty, a safety or composition field missing), once `search-catalog` with `entity: attribute` shows the taxonomy has that attribute: write it with the taxonomy's own attribute names and values, in the catalogue's language, as *A rule described in words* says.

## A rule described in words

When the user says what a faulty product looks like ("flag dresses with no colour", "a title with 'lot de' is suspect"), write it as one check rule with `create-quality-rule`. It makes no AI calls.

1. **Scope:** `taxonomy_id`, and `category_id` for one leaf category from `search-catalog`. A rule checks only the products placed in its own category, so name the leaf, or leave `category_id` out for every category of the taxonomy.
2. **Conditions:** each an `attribute`, an `operator` and its `value` or `values`, named as the taxonomy spells the attribute; `__title` reads the title. A wrong attribute, or an operator its type does not take, comes back with the ones that fit. `match: any` flags a product when one condition holds; the default flags it when all hold. `severity: red` gives flagged products the Critical Conflict label, `severity: amber` the Review Flag. A rule can give one of the shop's own labels instead, as `label` ("Group: Label"; a wrong name comes back with the ones it can give): `list-products` with `labels` then lists that rule's findings apart, such as every product missing legal information.
3. **Try it:** call without `confirm`. It saves nothing and says how many of up to 20 enriched products it flags, with examples. Read the rule back to the user in plain words with that count. A rule that flags every product tried, or none, usually says something other than what the user meant: reword it together first.
   - **Name its reach.** The rule reads only enriched products: the preview's `enriched_product_count` is how many of the `scope_product_count` products in its scope it reads. Say both with the trial count, as the user counts them ("it reads 400 of your 900 dresses; 500 are not enriched yet"): the rest cannot be flagged until they are enriched. The trial counts up to 1,000, so 1,000 means 1,000 or more: say "400 of more than 1,000 dresses", and when both read 1,000, that it reads more than 1,000, how many are not enriched yet being not counted.
   - **Offer to fill the gaps.** When `enriched_product_count` is below `scope_product_count`, or the rule flags an attribute left empty (an energy class, a composition or safety field), offer in the same reply, after the save question, to enrich those products: `launch-mining`, by the `naratix-attributes` skill, so load it. For an energy class on fridges, washing machines or dishwashers, say the search will include `eprel`, the EU energy-label registry, the most trusted source there. Their yes starts nothing until they confirm the launch.
4. **Save** with `confirm: true` after their yes. The rule is switched on at once, and one check of the enriched products follows: follow its `check_batch_id` as the cold-start's check, then offer to read the findings.

**Completion:** the user heard the rule in plain words with its trial count and said yes before it was saved, or it was reworded until it says what they meant.

What a check rule reads: a condition on an attribute the product does not carry at all is false. "Is not set" catches an attribute that is there and empty, so a rule for "no colour" flags only products whose enrichment left Colour empty.

The chat creates rules and nothing more: switching a rule on or off, changing it and deleting it happen in Naratix, on the rule's page the result links.
