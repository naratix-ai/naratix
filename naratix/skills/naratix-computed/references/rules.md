# Writing a computed rule

A rule is a JSON object: its `kind`, and the fields that kind takes. `save-computed-rule` takes it as `rule`; `list-computed-attributes` shows live rules and proposals in the same shape, so a rule there is a model to start from. A rule that does not hold comes back saying what to fix.

## Sources: what a rule reads

| Source | Reads |
|---|---|
| `"attr:<code>"` | another attribute's enriched value, by the `code` `search-catalog` gives; a computed attribute's value too, once it is computed |
| `"field:title"`, `"field:description"`, `"field:code"`, `"field:brand.name"`, `"field:category.name"`, `"field:category.full_path"` | the product's own fields |
| `"generated:title"`, `"generated:description"` | the title or description Naratix wrote for it |
| `"collection:images.count"` | how many photos it has |

An attribute holding several values reads as all of them. A rule never reads its own attribute, and two rules never read each other in a loop.

## Kinds

Pick the kind that says the user's logic. The examples use attribute codes; use the taxonomy's own.

### case: conditions, first match wins

```json
{"kind": "case",
 "branches": [
   {"label": "OLED", "then": "Yes", "when": {"combinator": "AND", "children": [
     {"source": "attr:panel_type", "operator": "in", "values": ["OLED", "QD-OLED"]}]}}
 ],
 "else": "No"}
```

- Branches are tried in order; the first whose condition holds gives its `then`. None holds: the `else`, or empty without one. A rule with no branches and only an `else` gives every product of the attribute's categories that value: a category default.
- A condition (`when`) is an AND group of conditions, or an OR whose children are each an AND group; nothing nests deeper. Each condition: `source`, `operator`, and `value` or `values`.
- Operators: `equals`, `not_equals`, `in` (with `values`), `contains`, `not_contains`, `is_set`, `is_not_set`, `gt`, `gte`, `lt`, `lte`. Conditions read attribute and field sources only.
- Text compares ignore case and spaces at the ends, and hold when any of the source's values matches. An empty source makes its condition false, except `is_not_set`.
- `gt`, `gte`, `lt` and `lte` read the first number in the value whatever its unit: "500 GB" reads as 500, "1 TB" as 1. Check how the values are written before comparing across units.
- A branch with `"default": true`, and the `else`, only fill: where enrichment found another value, nothing is flagged. A conflict is flagged only under `mine_too`, for a branch without `default`.

### bucket: a number into ranges

```json
{"kind": "bucket", "source": "attr:diagonal",
 "buckets": [{"max": 40, "label": "Small"}, {"max": 55, "label": "Medium"}, {"max": null, "label": "Large"}]}
```

- The first bucket whose `max` the value does not exceed gives its `label`. `max` values rise strictly; a last `"max": null` catches the rest, and without one a value above every `max` stays empty.
- The source must be a plain number: "55" works, "55 inch" leaves the attribute empty. For numbers written with units, use `case` with `lt` and `gte`, which reads the number out of the text.
- `missing_behavior`: `null-if-source-missing` (the default) or `first-bucket-if-missing`.

### concat: joined text

```json
{"kind": "concat", "parts": [{"source": "field:brand.name"}, {"lit": " "}, {"source": "attr:model"}]}
```

Each part is a `source` or a fixed text (`lit`); separators are `lit` parts. `missing_behavior`: `skip-missing` (the default: an empty part is left out with the text beside it, and the result trimmed), `null-if-any-missing`, or `keep-as-empty-string`.

### formula: arithmetic

```json
{"kind": "formula", "inputs": {"v": "attr:voltage", "a": "attr:current"}, "expression": "v * a", "round": 0}
```

- The expression takes numbers, the names of its `inputs`, `+ - * /` and brackets; no functions. Division by zero is an error.
- Each input must be a number. `missing_behavior`: `null-if-any-missing` (the default) or `treat-missing-as-zero`. `round` is a whole number of decimals.

### coalesce, count, any_has_value, count_if: over a list of sources

- `{"kind": "coalesce", "sources": ["attr:colour", "attr:frame_colour"]}`: the first source that has a value.
- `{"kind": "count", "sources": [...]}`: how many have a value.
- `{"kind": "any_has_value", "sources": [...]}`: true or false.
- `{"kind": "count_if", "sources": [...], "predicate": "equals", "value": "Yes"}`: how many hold the predicate: `has_value`, `equals` (exact match), `gte` or `lte` (numbers).

## Before saving

- A list attribute's results (`then`, `else`, bucket labels) are copied exactly from its allowed values: `search-catalog` names where to read them.
- Prefer a rule that leaves products empty over one that guesses: an honest gap is filled by enrichment, a wrong value is not.
