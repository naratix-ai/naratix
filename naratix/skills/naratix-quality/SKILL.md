---
name: naratix-quality
description: Quality checks in Naratix — how complete and consistent a shop's product data is, the check rules that flag faulty products, and the rules a market expects. Use when the user asks how good their catalogue is or wants it checked, wants a rule that flags products ("flag dresses with no colour", a legal requirement), asks why written content keeps coming out thin, asks what a check found, or wants rules written for a taxonomy.
---

# Naratix quality

A **check** labels the products it finds fault with and changes no product data. It makes no AI calls, so offer one whenever the data is in doubt: when the user asks how good their data is, when written content comes out thin in the same places (a template writes only from what the products carry), and before a large content launch. Checks run through the Naratix connection; without it, say so.

The go-ahead, errors and following background work go by the `naratix-operator` skill; filling what a check or rule finds empty goes by the `naratix-attributes` skill. The guided starter *Check my catalogue* walks this skill's order: a user who started it is already here.

## Explaining checks to the user

The first time you run a check in a conversation, and whenever the user asks what checks are for or wants an example rule, say it in one line: a check flags missing or contradictory product information before a marketplace or a regulator objects, and labels the products it flags. It helps spot what is missing and is not legal advice. An example rule comes from *The user's market*.

Name the engines in plain words, health first, then the shop's own rules, and give `applicability` one line, as *The three engines* puts it, its taxonomy included. Say more only when they ask what it is: its rules come from the cold-start (*Rules have to exist first*), so it has nothing to check until the cold-start has run.

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
   - **The user asked for the whole catalogue**, or a set past the limit: split it into parts yourself, as [references/whole-catalogue.md](references/whole-catalogue.md) says.
   - **The user asked for part of it** and the part is unclear: ask which.
4. **Follow it.** The call returns at once with a run still going: say so, then follow it as [references/following-a-check.md](references/following-a-check.md) says.
5. **Read the findings** with the user and name what each implies. Attributes that are simply missing are a job for enriching them, `launch-mining` (the `naratix-attributes` skill), rather than for a better template. A rule the user disagrees with is changed or switched off in Naratix, on the Consistency Rules page (Applicability Rules for applicability); the products are fine.

**Completion:** the user knows which engine ran over what, and either has the findings or knows the run is still going and how it will be checked.

When an attribute Enrichment finishes, its products are checked without asking, as the `naratix-attributes` skill's *Follow the run* says.

## Rules have to exist first

`consistency` and `applicability` evaluate rules. A taxonomy nobody has written rules for comes back clean for products nobody examined, which reads exactly like good news and is not: `consistency` then checks placeholder values only (below), and `applicability` starts nothing (its `reason` is no_applicability_rules), so offer its cold-start, previewed first.

Rules read enriched products only: a rule of the user's own checks a product once it is enriched, and the cold-start writes and tests its rules on enriched products.

Read `list-quality-rules` before running either engine, and in a catalogue check, read `list-runs` with `kind: enrichment` alongside it: whether an attribute Enrichment has finished says which rules you can offer. For `consistency`, read it with the `taxonomy_id` and `status: active`: the taxonomy has rules of its own switched on when `own` is above 0; the rest are the built-in pack, which only flags values left as a placeholder word such as "null", "undefined" or "...". In a catalogue check, a taxonomy whose `own` is 0 gets no `consistency` run: say only placeholder values would be checked, and offer rules as below. Asked for one anyway, it runs, and its result says only placeholder values were checked (its `reason` is junk_pack_only): report it as that, never as consistent data.

Rules come from the AI (*Cold-start*) or from the user's own words (*A rule described in words*).

Every time you offer rules, name both routes in one line, in plain words, so the user can choose: their own rule with `create-quality-rule`, from the moment products are in, checked once the products are enriched; and the rules the AI writes for the whole taxonomy with `cold-start-rules`, either "ready now, your products are enriched" or "waits for the first enrichment", as `list-runs` with `kind: enrichment` says.

## Cold-start: rules written by the AI

`cold-start-rules` writes a taxonomy's rules with the AI, on the user's yes:

- Call it without `confirm` first. It starts nothing and says what it covers: the taxonomy (for `consistency`, its categories with no rules yet) or the categories you named, with the ids of any it left out. Put that to the user, with no number for what it covers, and call again with `confirm: true` only after their explicit yes. Say why it can work now: it writes and tests its rules from the enriched products. Say how many are enriched only from a count of enriched products: the `done` of the Batch whose `kind` is `attributes`, that Batch alone, in `list-runs` (`kind: enrichment`), or a rule trial's `enriched_product_count`, where 1,000 means 1,000 or more; never a Batch's `products`, which counts its failed products too.
- It writes rules and the ones that hold up go live, so start it for a taxonomy the user wants rules on, once its products are enriched. Over products nobody has enriched the AI has nothing to test its rules on and few switch on: the card says so, and the user may still start.
- For `consistency`, the AI tests the rules it writes on sample enriched products and switches on the ones that hold up; the rest stay drafts that a person looks at in Naratix. `applicability` rules apply as soon as they are written. `health` has no cold-start: its scoring is built in.
- A rule comparing two attributes of one product (width against height) and a rule on which attributes apply to which kind of product both come from here: pass the user's words as `steer_prompt`.

When the products are already enriched, one check follows the cold-start by itself as soon as the rules are ready. The started result names it: follow `check_batch_id` as the `batch_id` of `list-runs` (`kind: quality-check`) for `consistency`, or `check_run_id` as the `run_id` with `engine: applicability`, pending until the rules exist.

A `consistency` check shows no run until the first category's rules are ready, then its runs arrive category by category; it is done when its batch reads `unfinished` 0 and `product_count` at `check_product_count`, which stays null until every part has started. That is the check: follow it rather than starting another, and offer to read the findings with the user once it is done. With nothing enriched yet no check follows, nor for an account without permission to edit products (the note says so): offer one once the products are enriched.

**Completion:** the user said yes to what the cold-start covers, and its check is being followed to its end, or they know why none follows.

## The user's market

A legal or market rule depends on where the shop sells. Whenever you offer the user a rule of their own or give an example rule, ask the market in that same turn, once per conversation, offering a guess for them to confirm, built from the taxonomy's language and the channels' names (`list-connectors`).

- Take the language from the `search-catalog` answer the rule's attribute search gives anyway (`taxonomy.language`), so the question draws no card of its own; a language is spoken in several countries, so the guess is theirs to correct.
- When the user already named a country, still ask, in one confirming line that names the guess with its evidence ("Your taxonomy is in French and your Mirakl marketplace sells in France: France only, or other countries too?"), and carry on in the same turn with the attribute search and the trial, so the question adds no round trip.
- Every rule you offer as an example is one of that market's, never a generic one. Then propose one rule that market expects of their products (an energy class left empty, a safety or composition field missing), once `search-catalog` with `entity: attribute` shows the taxonomy has that attribute: write it with the taxonomy's own attribute names and values, in the catalogue's language, as *A rule described in words* says.

## A rule described in words

When the user says what a faulty product looks like ("flag dresses with no colour", "a title with 'lot de' is suspect"), write it as one check rule with `create-quality-rule`: read [references/own-rule.md](references/own-rule.md) before the first call.

The chat creates rules and nothing more: switching a rule on or off, changing it and deleting it happen in Naratix, on the rule's page the result links.
