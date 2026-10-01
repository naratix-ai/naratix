---
name: naratix-taxonomy
description: Taxonomies and their audits in Naratix — the category tree, each category's attributes and allowed values, and the AI audit that suggests fixes to them. Use when the user wants their taxonomy cleaned up, audited or rebuilt, asks how an audit went, or wants an audit applied.
---

# Naratix taxonomy

A **taxonomy** is the shop's category tree: every category, the attributes each one expects (colour, width, energy class), and each attribute's allowed values. Enrichment fills those attributes, content is written from them, and channels receive them, so a messy taxonomy shows up everywhere downstream.

An **audit** has the AI review the attributes and values of the taxonomy's leaf categories and suggest fixes: rename, retype or remove attributes, suggest missing ones, add or remove values, and merge values that repeat. Nothing changes until the audit is applied.

## Audit a taxonomy

1. `list-taxonomies` for the taxonomy. If the user wants only some categories, find them with `search-catalog` and that `taxonomy_id`, and pass the ids of leaf results; ids that are not leaves come back in `unmatched_category_ids`.
2. `start-audit` without `confirm` returns how many categories it covers; tell the user and get their yes.
   - `treat_all_attributes_as_local` reviews every attribute as the category's own. Use it only when the user wants a new taxonomy out of the audit: an audit made this way cannot be applied in place.
   - `include_inherited_attributes` also reviews what a category inherits from its parents; `lock_units_of_measurement` keeps the taxonomy's units as they are.
3. Call again with `confirm: true`, then follow it with `list-runs` (`kind: audit`) until `status` is completed; `categories_audited` against `categories_total` is its progress.

## Read an audit's suggestions

`show-audit` reads a finished audit: whether and how it was already applied, then per category and attribute, what it would rename, retype or remove, the attributes it suggests, and the values it would add, reject or merge, with a count per kind over the whole audit. In apps that show views, the user can browse and filter it themselves.

1. Read `applied` first. The suggestions still list after an apply, so say how it was applied before any talk of applying:
   - Applied in place: say so and when. Another in-place apply writes it again; offer it only if the user asks.
   - A new taxonomy: name it. `implement-audit` refuses a second one.
   - `changed_nothing`: the apply wrote no category. Say so; the taxonomy is as that apply found it.
   - Nothing recorded, with `apply_log_since` set: the audit is older than the apply log, and an apply before that day left no record. Ask the user whether they applied it before offering to apply it in place.
2. Then the counts in `changes`: tell the user what the audit found in a sentence or two.
3. Narrow to what the user asks about instead of paging through everything; each call draws the view again:
   - `by: category` lists the categories with what each one changes, under their parent path. The audit never renames, moves or adds a category, so this is the taxonomy after the audit.
   - `category_id` from that list reads one category: every suggestion, and the attributes it leaves as they are.
   - `change`, `category` or `attribute` narrow either.
4. Before asking how to apply it, explain `apply`: what each mode takes into a new taxonomy and in place, and why a way is not open (a treat-as-local audit, an audit already implemented).
5. End each answer with the next step you can take for the user, as a question: open a category, or show one kind of change. While `applied` is null, also offer to apply it their way; once it is applied, offer what follows instead: open the taxonomy it was applied to (`applied.panel_url`), or audit the taxonomy again. A user who has never seen an audit should never have to guess what comes next.

Nothing in it applies anything.

## Apply an audit

Two ways, both the user's call:

- `implement-audit` builds a **new taxonomy** from the audit; the original stays untouched. The safe choice.
- `apply-audit-in-place` changes the **live taxonomy** and cannot be undone. Explain that, and call it only after an explicit yes.

An audit older than the log that proves removing values is safe leaves duplicate and rejected values when applied in place, and the apply's result says so. After an apply, `list-runs` shows `removals_refused`: value removals the apply refused for the same reason. Tell the user; re-running the audit and applying the new one removes them.

`panel_url` opens the app's audits page; `applied.panel_url` opens the taxonomy it was applied to. Editing a taxonomy by hand (renaming, moving, attributes, values) happens in the app.
