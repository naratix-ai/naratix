---
name: naratix-taxonomy
description: Taxonomies and their audits in Naratix — the category tree, each category's attributes and allowed values, and the AI audit that suggests fixes to them. Use when the user wants to see what a taxonomy holds or what came in (its categories, attributes and allowed values), wants it cleaned up, audited or rebuilt, asks how an audit went, wants an audit applied, or needs the tree in another language.
---

# Naratix taxonomy

A **taxonomy** is the shop's category tree: every category, the attributes each one expects (colour, width, energy class), and each attribute's allowed values. Enrichment fills those attributes, content is written from them, and channels receive them, so a messy taxonomy shows up everywhere downstream.

An **audit** has the AI review the attributes and values of the taxonomy's leaf categories and suggest fixes: rename, retype or remove attributes, suggest missing ones, add or remove values, and merge values that repeat. Nothing changes until the audit is applied.

## Show what a taxonomy holds

When the user asks what came in, or whether a taxonomy needs an audit, walk it a level at a time: `list-taxonomies` with its `taxonomy_id` lists the top categories, and with a `category_id` that category's subcategories, each with how many subcategories and attributes of its own it has (attached to it, or through its attribute groups). `search-catalog` with `entity: attribute` and the `category_id` lists a category's attributes, its own and those it inherits. In apps that show views, the user clicks through the categories, attributes and allowed values themselves, and a member who may start an audit can ask for one from there; call again only for what they ask about.

## Audit a taxonomy

Your first output in the turn is a chat message saying what an audit is: the AI reviews each leaf category's attributes and values (names, types, values that repeat, missing attributes, allowed values); it only reads and suggests, nothing changes until they apply it, and its card shows how far it has got. Give no duration. Send it before `list-shops`, `list-taxonomies` or `start-audit`: the preview card has a Start button, so the user must know what it starts before it appears.

1. `list-taxonomies` for the taxonomy. If the user wants only some categories, find them with `search-catalog` and that `taxonomy_id`, and pass the ids of leaf results; ids that are not leaves come back in `unmatched_category_ids`.
2. `start-audit` without `confirm` returns how many categories it covers; tell the user that count and ask for their yes.
   - `treat_all_attributes_as_local` reviews every attribute as the category's own. Use it only when the user wants a new taxonomy out of the audit: an audit made this way cannot be applied in place.
   - `include_inherited_attributes` also reviews what a category inherits from its parents; `lock_units_of_measurement` keeps the taxonomy's units as they are.
3. Call again with `confirm: true`, then follow it as the `naratix-operator` skill's *Following work* says (where no card shows, `list-runs` with `kind: audit`) until `status` is completed; `categories_audited` against `categories_total` is its progress. A completed audit can still have `categories_failed`: tell the user how many categories it could not review; when all of them failed there is nothing to read, so send them to the audits page (`panel_url`).

**Completion:** the user knew what an audit does before their yes, and the audit's `status` is completed, or they know its card follows it.

## Read an audit's suggestions

`show-audit` reads a finished audit: whether and how it was already applied, then per category and attribute, what it would rename, retype or remove, the attributes it suggests, and the values it would add, reject or merge, with a count per kind over the whole audit. In apps that show views, the user can browse and filter it themselves.

1. Read `applied` first. The suggestions still list after an apply, so say how it was applied before any talk of applying:
   - Applied in place: say so and when. Another in-place apply writes it again; offer it only if the user asks.
   - A new taxonomy: name it. `implement-audit` refuses a second one.
   - `changed_nothing`: the apply wrote no category. Say so; the taxonomy is as that apply found it.
   - Still being applied (its note says so, with `categories_done` of `categories_sent`): say how far it has got; its card follows the apply and its context says when it ends, and where no card shows, offer to check again in a few minutes.
   - Nothing recorded, with `apply_log_since` set: the audit is older than the apply log, and an apply before that day left no record. Ask the user whether they applied it before offering to apply it in place.
   - Applied either way: also say how many categories could not be applied (`applied.categories_failed`) and what `applied.values_kept` counts (*Apply an audit*, below).
2. Then the counts in `changes`: tell the user what the audit found in a sentence or two.
3. Narrow to what the user asks about instead of paging through everything; each call draws the view again, and in a view the user filters and opens categories themselves, so call again only for what the view does not show:
   - `by: category` lists the categories with what each one changes, under their parent path. The audit never renames, moves or adds a category, so this is the taxonomy after the audit.
   - `category_id` from that list reads one category: every suggestion, and the attributes it leaves as they are.
   - `change`, `category` or `attribute` narrow either.
4. Before asking how to apply it, explain `apply`: what each mode takes into a new taxonomy and in place, and why a way is not open (a treat-as-local audit, an audit already implemented). Name each mode by its `apply.modes[].label`, in the user's language; its `apply_mode` code goes only into the call.
5. End each answer with the next step you can take for the user, as a question: open a category, or show one kind of change. While `applied` is null, also offer to apply it their way; once it is applied, offer the next step the result's note names instead: placing products in the new taxonomy, or enriching the attributes an in-place apply changed. A user who has never seen an audit should never have to guess what comes next.

Nothing in it applies anything.

## Apply an audit

Two ways, both the user's call. Before either, check the audited taxonomy's `synced_from` in `list-taxonomies`: a taxonomy its channel updates gets the channel's attribute names, types and allowed values back at its next sync, from `sync-taxonomy` or, for a VTEX taxonomy that syncs by itself, within the hour (`show-audit`'s `apply` says which), so an in-place apply lasts only until then. Say so, and offer the new taxonomy as the way that keeps the changes.

- `implement-audit` builds a **new taxonomy** and leaves the original untouched: the safe choice. It copies only the categories the audit covered, with their parents, and starts with no products. It keeps the audited taxonomy's language, which cannot change later: pass `language` only when the user asks for another, and an audit never translates.
  1. Ask the user what to call the new taxonomy (`taxonomy_name`, unique in the shop) and pass it on every call, the preview included. Call without `confirm`: it creates nothing, and its card asks how much of the audit to apply (`apply_mode`, the modes `show-audit`'s `apply` explains). An answer the user already gave goes with your call and shows picked; where no card shows, ask in the chat.
  2. Apply everything (`apply_mode: full`) copies the attributes held on parent categories as they are, and so does `apply_mode: custom` with `preserve_parent_structure: true`, its default there; the other modes copy only the attributes the audit reviewed. When the preview says parents are left out, ask once.
  3. Create on the card, or `confirm: true` with their mode after their yes. The card follows the build; without one, `list-runs` (`kind: audit`, its `run_id`) reads `applied`. Then offer to place products in it (`launch-categorization` with its `taxonomy_id`): for a partial audit, only the products of the categories it covered.
- `apply-audit-in-place` changes the **live taxonomy** and cannot be undone. Explain that, and call it only after an explicit yes. Before the user picks a mode, warn them, apart from any one mode: in place, an attribute several categories share keeps one name and type for all of them, and its value changes (under `full`, `values_only` and `custom` alike) reach every category that shares it; only `localize` gives a category its own renamed copy and leaves the others as they are. It applies every category and attribute of the audit: a user who wants only some applies them in the app: give them `show-audit`'s `panel_url` as a link. On a running audit it applies only the finished categories, so wait for the end. It has no preview and no card that asks the mode, and an omitted `apply_mode` applies everything: settle the mode in the chat and always pass it with `confirm: true`. Its card follows the apply; where none shows, `list-runs` (`kind: audit`, its `run_id`) until `applied.at` is no earlier than the `since` it returned and `applied.under_way` is false.

`apply_removals` and `apply_value_removals` are read only with `apply_mode: custom`, which applies what full applies plus those removals; `localize` makes them already. `full` and `values_only` never remove a flagged attribute or a rejected value.

An audit older than the log that proves removing values is safe leaves duplicate and rejected values when applied in place, and the apply's result says so. After an apply, `show-audit`'s `applied.values_kept` counts the value removals the apply refused for the same reason. Tell the user; re-running the audit and applying the new one removes them.

**Completion:** the user chose the way after hearing what each keeps or changes, and `applied` names the taxonomy the audit went into.

`panel_url` opens the app's audits page; `applied.panel_url` opens the taxonomy it was applied to. Editing a taxonomy by hand (renaming, moving, attributes, values) happens in the app.

## Another language

A taxonomy's language never changes. When it is wrong, or the shop needs the same tree in a second language, the user clones it in the app (a Mirakl or VTEX taxonomy in the wrong language is imported again instead, as the `naratix-imports` skill says, since a clone is no longer kept in step with its channel): on the Taxonomies page, **Clone**, pick the target language, and translate the names and values, or the values only. The products' enriched values follow with the products list's **Translate attributes** (Cross-taxonomy). No tool does either yet, so send the user to the app. Both carry the taxonomy and attribute values only: titles and descriptions are written in the new language as below.

Titles, descriptions and SEO text in another language need no new taxonomy: a template carries its own language (the `naratix-content` skill), and `generate-seo` takes a `language`. Attributes can be enriched in a language with no fitting taxonomy (`launch-mining` with `language`).
