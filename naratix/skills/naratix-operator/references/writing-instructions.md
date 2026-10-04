# Writing instructions

Custom instructions for attribute enrichment and categorization, description templates and title prompts are all read by a language model. It follows each line as written and weighs none above another, so write every instruction to have one reading:

- **Name the thing.** An instruction about an attribute, category or value names it as the shop's taxonomy spells it.
- **Say each rule once, in one place.** A rule said twice reads as louder than the rest, and two wordings of one rule read as two rules.
- **Keep every pair of lines true together.** A conflict makes the model choose, differently from run to run. Numbers add up: section lengths sum to the total, and a character cap fits the parts it lists.
- **Use numbers where there is a measure:** "80–120 words", "at most 70 characters", "3–5 bullets".
- **Name what to do.** "Open with the product's main use" steers; a list of words to avoid puts those words in front of the model.
- **State principles.** A sample product or a finished text gets copied onto products it does not fit; describe the rule it was meant to show. A format with slots ("type, Brand, key spec") is a rule and belongs.
- **Add only what Naratix does not already do.** A restated built-in rule is a second, slightly different copy of it; [What the model already receives](../../naratix-content/references/companion-prompt.md#what-the-model-already-receives) lists what the description and title models get.
- **Keep "never" and "always" for true limits** — a channel's character cap, a claim the shop must not make. Everything else is a plain statement.
- **Say what to do when a fact is missing:** leave it out. A model asked for something the data lacks invents it. An instruction for an attribute says it of that attribute's own value, named as the taxonomy spells it: when the sources do not state it, or nothing they state matches an allowed option, leave it empty. A fallback that keeps or picks a value ("keep what the sources give", "take the closest") is not that rule, and neither is a condition on a sub-detail such as a finish or a unit.

A shop's saved instructions override the built-in rules they contradict, so every conflict with a built-in rule is one you chose: say which rule the instruction replaces.

**Completion:** before a draft is used or saved, you have read it as the model will — listed its constraints, found none that cannot hold together and no number that fails to add up, found the sentence that says what to do when a fact is missing (for an attribute: "if the sources do not give <Attribute>, leave it empty") — and read it back to the user.
