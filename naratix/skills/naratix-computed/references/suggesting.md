# Suggesting rules for a category

How to choose which attributes of a category earn a computed rule, when you write the proposals yourself (`propose-computed-rules`). A person approves or rejects each one: they approve a rule they can check in one reading that is right for every product.

## What earns a rule

A rule restates a fact the product already holds, in the form another attribute asks for. Propose one only when every product that has the inputs gets the right value from it:

- **The same fact held twice.** Two attributes meaning the same thing (one per language, label or marketplace, a general one and a specific one): the emptier takes the fuller one's value, mapped onto its allowed values.
- **A fact contained in another.** A yes or no, or a single value, read from a list attribute that already enumerates it, or from a value that states it.
- **A measure in another form.** A range or class from a number, a unit conversion, a count, arithmetic between numbers.
- **A fact fixed by the category itself.** Only when the category's name and path make it true of every product in it, as a fill-only default (`"default": true`, or the `else`).

An attribute already filled on the sampled products still earns a rule when one follows: those samples prove it, and the rule fills the products not sampled.

## What gets no rule

- A description, a name, a marketing text or any other prose: written per product, never derived.
- A product field (title, description, code, brand, category name) copied into the attribute unchanged: it duplicates text and is refused.
- Logic that holds for most products but not all, or needs knowledge the inputs do not carry.

Three sound rules beat ten doubtful ones, and none is a fine answer.

## Check each rule on the samples

Before storing, run the rule in your head on the sample products that carry its inputs. Where the attribute already holds a value, the rule must give the same one; a rule that disagrees on any sample is wrong: fix it or drop it. Read values as the samples write them (units, decimals, wording), and copy list results exactly from the allowed values.

Write each proposal's `rationale` so the user can check it in one reading: the inputs and the logic, with the taxonomy's own attribute names. `confidence` is your honest chance that they approve it unchanged.

**Completion:** every rule you store holds on every sample product that carries its inputs, and none copies a field or writes prose.
