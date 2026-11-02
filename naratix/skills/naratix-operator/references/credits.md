# Credits

A shop on credits holds a balance: each launch uses credits, and one the shop has too few left for is refused. A shop not on credits gets no `credits` in any answer; say nothing about them there.

## Before the yes

Every launch preview (the call without `confirm`) carries `credits`: the `total`, the `needed` share of it the shop must have left to start (90%), what the shop has `available` and will have `after`, `refused`, and `rows`, the steps the total is made of, largest first, each a `label`, its credits `per_unit`, the `units` it covers and its `credits`.

- **Show the table with the question that asks for the yes:** one line per row (step, per unit × units, credits), then the total and what is left after. The card draws the same table; where no card shows, write it.
- **Name where most of it goes**, the one or two largest rows, in a sentence: "most of it is reading web pages, 7 a product".
- **`exact` false means "up to":** work a run skips gives its credits back (a photo it skips, a text with no template, a product it cannot place), so what is left after is "at least".
- **`after` below zero:** it can start, and may end up to that much below zero; say both.
- **The card asks again** when the user changes an option that changes the credits, and its table follows; Start waits for it. A yes typed after such a change carries the card's settings, as its context says.
- **A single text** (`generate-title`, `generate-description`, `generate-seo`) starts at once, with no preview: its answer says what it used and what is left; pass that on.

Completion: the user saw what the launch uses, step by step, and what is left, before saying yes.

## Doing it in one launch

Some work shares a step, so together it uses fewer credits than apart. When the user's goal includes both, offer them together before the yes:

- **Attributes and new photos.** When `launch-mining` reads web pages, ask "Find new product photos as well?": `find_photos: true` finds them on the same pages, for fewer credits than `launch-images` with `find_images` afterwards, which searches the web again. The card offers the same switch, and its table follows it.

## "How many credits does it take?"

`dashboard-summary` answers it: its `credits` has what the shop has `available`, and `actions`, each action's credits per `unit`, by quality `tiers`, by attribute `bands`, or, for attribute enrichment, its steps and how the reading steps grow with the attributes a category has (`grows`). Answer from those, in credits. For the user's own products, offer the launch's preview: it counts them exactly.

## Short of credits, or asked to use fewer

Only then suggest using fewer, and preview again to show the new total:

- leave out the largest rows the user can do without: a source the products don't need, EPREL outside appliances, extra web searches, fewer new photos per product, a lower quality (Max → Standard → Preview);
- a smaller selection, the rest later.

Otherwise keep the user's choices: fewer credits means less done. When `refused` is true, the card holds Start: say what it needs to start and what the shop has, each once, offer the options above, and for more credits the Naratix team at https://api.naratix.ai/support.
