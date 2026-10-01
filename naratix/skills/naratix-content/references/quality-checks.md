# Quality checks — which engine answers which question

A template can only write from what the products actually carry. When a test drive comes back thin, or a customer asks why some descriptions read worse than others, the answer is usually the data rather than the template. Three engines will say which, by labelling the products they find fault with.

**This branch needs a connection.** Standalone, say so rather than attempting it.

Offer it when a customer asks how good their data is, when generated content keeps coming out empty in the same places, and **before a big generation run** — a check first can stop a run producing thin content across a whole catalogue.

**A check changes no product data.** All three are deterministic and make no AI calls: `run-quality-check` only labels what it finds. Say that plainly, so the customer runs one whenever the data is in doubt.

## The three engines

Pick one with `engine`:

- **`health`** — scores each product's own data completeness: title quality, description, images, identifiers. Needs no setup, so it is the one to reach for first.
- **`consistency`** — evaluates the shop's check rules and flags products that break them. Needs a `taxonomy_id`.
- **`applicability`** — finds attributes that should be filled and are not, and attributes filled that do not belong on that kind of product. Needs a `taxonomy_id`.

## Choosing the products

The products are chosen with the filters of the products list in the app, so a selection here is the same set of products there. With no filter, the check covers the whole shop. The ones a check usually needs:

- `category_id` (from `search-catalog`): that category and everything beneath it; `include_subcategories: false` keeps to the category alone.
- `taxonomy_id`: the taxonomy the categorization, enrichment and confidence filters read. A category brings its own.
- `review_status`, `labels` (named "Group: Label"; `search-catalog` with `entity: label` finds them by name) and `health` (flag names). Passing labels is how to re-check only what is currently flagged, which is what a second pass wants. A name the shop does not have comes back with the list of ones it does, so a wrong guess self-corrects.
- `product_ids`, `codes`, `external_ids` or `titles` for named products. A variation stands for its main product. The result's `selection` says how many were named, how many matched and which were not found; tell the customer about any that were not.

A check also covers the variations of the products it selects.

`consistency` and `applicability` take a selection of any size, a whole catalogue included. `health` caps one run, because it scores every product in a single pass — over the limit it refuses and reports the count and the limit. The fix is a narrower selection, not a retry.

## Nothing pushes the result back

The call returns immediately with a run that is still going. Say so rather than going quiet, then poll `list-runs` (`kind: quality-check`) with the id the call returned to find out whether it finished: `run_id` with its `engine` for health and applicability, `batch_id` for consistency, which fans out into several runs sharing it; they appear under it once a worker picks the check up. Expect a catalogue-sized run to take a while — check back at a sensible interval instead of hammering it.

`list-runs` is also how to pick the thread up when a customer leaves and returns later without that id. It lists runs from all three engines, each tagged with its `engine`, so match on that rather than assuming the newest row is yours. The runs are still there.

## Turning findings into the next action

Read the findings with the customer and name what each one implies. Attributes that are simply missing are a job for enriching them — `launch-mining`, which the `naratix-attributes` skill teaches — rather than something a better template would fix. A rule the customer disagrees with is changed or switched off in Naratix, on the rules page, not a product to fix.

Completion: the customer knows which engine ran over what, and either has the findings or knows the run is still going and how it will be checked.

## Rules have to exist first

`consistency` and `applicability` evaluate rules. A taxonomy that has never been cold-started has no rules, so a check comes back clean for products nobody has examined — which reads exactly like good news and is not.

Confirm with `list-quality-rules` (read-only) before running either engine.

## Cold-start waits for the customer's yes

`cold-start-rules` writes those rules with the AI.

- Call it **without** `confirm` first. It starts nothing and says what it covers: the taxonomy (for `consistency`, its categories with no rules yet) or the categories you named, with the ids of any it left out. Put that to the customer, with no number, and call again with `confirm: true` only after their explicit yes.
- Never cold-start "to see what happens": it writes rules, and the ones that hold up go live.
- `health` has no cold-start — its scoring is built in, not derived. Another reason to start there.

For `consistency`, the AI tests the rules it writes on sample enriched products and switches on the ones that hold up; the rest stay drafts that a person looks at in Naratix.

When the products are already enriched, one check of them follows the cold-start by itself, as soon as the rules are ready. The started result names it: follow `check_batch_id` as the `batch_id` of `list-runs` (`kind: quality-check`) for `consistency`, or `check_run_id` as the `run_id` with `engine: applicability`, pending until the rules exist. A `consistency` check shows no run until the first category's rules are ready, then its runs arrive category by category; it is done when `unfinished` is 0 and `product_count` reaches the `check_product_count` the result gave. That is the check: follow it rather than starting another, and offer to read the findings with the customer once it is done. With nothing enriched yet no check follows; offer one once the products are enriched. Nor does one follow for an account without permission to edit products: the note says so. Over products nobody has enriched, the test has nothing to try the rules on and few switch on: the card says so, and enriching first (`launch-mining`) is the better order, though the customer may still start. `applicability` rules apply as soon as they are written.

The chat creates rules and nothing more: switching a rule on or off, changing it and deleting it happen in Naratix, on the rules page the result links.

## A rule described in words

When the user says what a faulty product looks like ("flag dresses with no colour", "a title with 'lot de' is suspect"), write it as one check rule with `create-quality-rule`. It makes no AI calls.

1. **Scope:** `taxonomy_id`, and `category_id` for one leaf category from `search-catalog`. A rule checks only the products placed in its own category, so name the leaf, or leave `category_id` out for every category of the taxonomy.
2. **Conditions:** each an `attribute`, an `operator` and its `value` or `values`, named as the taxonomy spells the attribute; `__title` reads the title. A wrong attribute or an operator its type does not take comes back with the ones that fit. `match: any` flags a product when one condition holds; the default flags it when all hold. `severity: red` gives flagged products the Critical Conflict label, `severity: amber` the Review Flag.
3. **Try it:** call without `confirm`. It saves nothing and says how many of up to 20 enriched products it flags, with examples. Read the rule back to the user in plain words with that count. A rule that flags every product tried, or none, usually says something other than what the user meant: reword it together first.
4. **Save** with `confirm: true` after their yes. The rule is switched on at once, and one check of the enriched products follows: follow its `check_batch_id` and `check_product_count` as above, then offer to read the findings.

What a check rule reads, and where the rest goes:

- A condition on an attribute the product does not carry at all is false. "Is not set" catches an attribute that is there and empty, so a rule for "no colour" flags only products whose enrichment left Colour empty.
- A rule comparing two attributes of one product (width against height) and a rule on which attributes apply to which kind of product both come from `cold-start-rules`: pass the user's words as `steer_prompt`.
