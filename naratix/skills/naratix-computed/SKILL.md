---
name: naratix-computed
description: Computed attributes in Naratix — attributes whose value a rule derives from the product's other attributes and fields (a size class from a diagonal, a default every product of a category shares, a name joined from two fields). Use when the user wants an attribute filled by a rule or a formula instead of enrichment, asks what a computed attribute is or why one holds a value, wants a rule written, changed or removed, wants the AI to suggest rules, or wants the suggested rules reviewed; also when an attribute is always derivable from others and enriching it would only guess.
---

# Naratix computed attributes

A **computed attribute** is an attribute of the taxonomy whose value a **rule** derives from the product's other attributes and fields, the same way for every product. The rule runs with no AI and reads only what the product already holds, so it never invents: where its inputs are empty, it leaves the attribute empty. Use it for values that follow from others by logic: a size class from a measurement, a total from two numbers, a display name joined from fields, a value every product of a category shares.

Computed attributes run through the Naratix connection; without it, say so. The go-ahead and following background work go by the `naratix-operator` skill. Filling attributes from the web and photos is the `naratix-attributes` skill.

## How it works

- **A rule lives on one attribute** the taxonomy already has. New attributes are added in the app, on the Taxonomies page.
- **Its mode** decides who comes first, and the user picks it, since it changes what enrichment does:
  - `compute_only` (the app's *Compute only*): enrichment skips the attribute and the rule owns the value. Right when no seller states it: a class, a range, a joined name.
  - `mine_too` (*Enrich, compute what's missing*): enrichment looks first and the rule fills what it leaves empty. Right when listings sometimes state the value and the rule is a fallback, such as a category default.
- **An enriched value always stands.** A computed value fills empty attributes only. Under `mine_too`, where a branching rule's own condition gives another value than enrichment found, the enriched value stays and the product gets the *Computed Conflict* label for a person to look at.
- **Values follow the products.** After a save, the taxonomy's products are computed again in the background (minutes for a big catalogue), and each product again whenever its values change, an enrichment included.
- `list-products` with `computed: has` lists the products holding computed values; `show-product` shows them among the attributes.

## Three ways to get rules

Every time you offer rules, name the three in one line so the user can choose: **their own**, described in words and written by you (*A rule in the user's words*); **yours**, suggested by you for a whole category and reviewed one by one (*Rules you suggest*); or **Naratix's AI's**, suggested for a category or the whole taxonomy (*Rules the AI suggests*). Start with `list-computed-attributes` for the taxonomy: the rules already live, and proposals that may already wait for a verdict.

Explain a rule in plain words, as a sentence about the products ("TVs up to 40 inches are Small, the rest Large"); show its rule text only when the user asks for it.

## A rule in the user's words

1. **Find the attributes.** `search-catalog` with `entity: attribute` gives each one's id, `code`, type and, for a list attribute, where its allowed values are; with a `category_id` it lists that category's attributes. Name the attribute to compute and the ones it reads, in the taxonomy's own words. A list attribute's result must be one of its allowed values, written exactly.
2. **Write the rule** by [references/rules.md](references/rules.md): pick the kind that says the user's logic, and read its gotchas before the first call.
3. **Try it.** `save-computed-rule` without `confirm` saves nothing: it checks the rule and tries it on up to 30 products of the attribute's category. A rule that does not hold comes back saying what to fix: fix it and call again, without asking the user about the syntax. Then tell the user in one short reply:
   - the rule in plain words, and what it read across the products tried: how many empty values it fills, how many enriched values it agrees and disagrees with, with two or three of the examples;
   - many disagreements usually mean the rule says something other than what the user meant: reword it together first;
   - `no_data` means none of the products tried is enriched yet: the examples still show what it gives.
4. **Ask the mode** in that same reply, as *How it works* puts it, suggesting the one that fits.
5. **Save** with `confirm: true` and the `mode` after their yes. Replacing a rule keeps the old one in the answer (`previous`): it can be saved back.

**Completion:** the user heard the rule in plain words with its trial and picked the mode, and said yes before it was saved; or it was reworded until it says what they meant.

## Rules the AI suggests

1. **Start it.** `suggest-computed-rules` with the `taxonomy_id`, and a `category_id` for one category; without it, it covers every category of the taxonomy that holds products. Call it without `confirm` first: put what it covers to the user, say that every proposal waits for their verdict, and start it with `confirm: true` on their yes. It reads each category's attributes and sample products, and leaves most attributes without a proposal: an attribute it cannot derive honestly gets none.
2. **Read the proposals** a few minutes later with `list-computed-attributes` and `status: pending` (`category_id` narrows to one category's): each carries its rule, its suggested mode, the AI's reason and how confident it is.
3. **Go through them with the user**, a few at a time, each in plain words with its reason. `decide-computed-proposals` with `verdict: approve` and no `confirm` tries each on products of its category: say what it fills, agrees and disagrees with.
4. **Decide on their word only.** Approve or reject with `confirm: true`, up to 25 at a time; `mode` approves them all under the mode the user picked instead of each one's suggestion. A proposal that no longer holds, or whose attribute got a rule of its own since, stays pending and the answer says why. Reject bars that exact rule from being suggested again.
5. **A proposal the user wants changed**: write the changed rule with `save-computed-rule` (*A rule in the user's words*, from step 3); saving it rejects the proposal, so it leaves the list.

**Completion:** every proposal the user looked at is approved, rejected or replaced by a rule of their own, on their word, and they know where the rest wait.

## Rules you suggest

You can do what Naratix's AI does for a category, without starting it: read what it reads, write the rules, and leave them for the user's verdict.

1. **Read the category.** `propose-computed-rules` with its `category_id` and no `proposals` answers its attributes (each with how many sampled products fill it), the ones open for a rule (`target_attribute_codes`) and sample products with their enriched values, 50 attributes at a time: read the next page (`next_offset` as `offset`) while you need its attributes as sources or targets.
2. **Write the rules** by [references/suggesting.md](references/suggesting.md) and [references/rules.md](references/rules.md). Most attributes get none.
3. **Store them** with `proposals`, up to 25 per call. Each is checked as the app checks a rule; one refused comes back with why: fix it or leave it out. None goes live.
4. **Review them with the user** from step 3 of *Rules the AI suggests*.

**Completion:** the proposals are stored and the user has gone through them, or knows none qualified and why.

## Change or remove a rule

- **Change:** `save-computed-rule` with the new rule replaces the live one, tried and agreed like a new rule.
- **Remove:** `save-computed-rule` with `remove: true`, previewed first. The attribute stops being computed, its computed values are cleared as the products are computed again, and enrichment fills it from then on.
- **Undo a verdict** in the app, on Mission Control's Rule proposals (the answer's `panel_url`).
- **Check the rules' health** in the app, on Quality → Computed health: a source that no longer exists, a rule that fills almost nothing, a range no value reaches.
