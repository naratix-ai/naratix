# Changing the shop's rules

How a check rule is switched on or off, changed or deleted, and how a finding is taken off a product, from the chat: `edit-quality-rules`, the Consistency Rules page's own changes. Each is a small direct change, so no card shows: you ask in the chat.

## Find the rules

Read `list-quality-rules` with `engine: consistency` (and the `taxonomy_id`): each rule's `id`, name, `status` and `in_words`. A product's findings, each with its `rule_id`, come from `show-product`. Applicability rules are changed in Naratix, on the Applicability Rules page.

## Ask, then change

1. Call `edit-quality-rules` without `confirm`, with the `action` and `rule_ids`. It changes nothing and answers what would change: each rule's state before and after, how many products it has flagged, and for an edit each field before and after.
2. Put that to the user in plain words, through the app's structured question tool where it has one (the change and "Not now" as choices), as a plain question otherwise.
3. On their yes, call again with the same arguments and `confirm: true`. Never on your own judgment.

## The actions

- **`switch_on`, `switch_off`**: up to 50 rules at once. A rule switched on runs from the next check, so offer one (`run-quality-check` with `engine: consistency`). A rule switched off no longer runs, but the labels it already gave stay on the products: say so, and offer `delete` when the user wants them gone.
- **`edit`**: one rule. Pass only what changes: `name`, `severity` (the label follows it unless the rule gives one of the shop's own), `label` ("Group: Label"; `""` gives back the severity's), or `conditions` with `match`, written as for a new rule ([own-rule.md](own-rule.md)), which replace every condition the rule had. New conditions are tried on up to 20 enriched products of the rule's scope first: read the `tried` count back as for a new rule. A rule comparing two attributes takes a new name, severity or label only. An edit applies from the next check: offer one.
- **`delete`**: up to 50 rules, with their findings and the labels they gave. It cannot be undone: say so when you ask. A rule with many findings is switched off at once and deleted in the background, leaving the list once done: say so when the answer does.
- **`dismiss_findings`**: the findings of the rules in `rule_ids` on the products in `product_ids`, as the product page's dismiss. A check finds them again while the rule still matches the product, so when the user disagrees with the rule itself, offer to edit it or switch it off instead.

**Completion:** the user heard what would change, said yes, and the answer says the change was applied; or they said no and nothing changed.
