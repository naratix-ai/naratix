---
name: naratix-insights
description: What a shop's own numbers say to do in Naratix — its store search, Google Analytics and Google Search Console, read as insights and as raw numbers, a plan proposed from them, acted on, and what earlier changes did. Use when the user asks what their searches, sales, views or Google traffic say, what to fix or improve next, why a category does not sell or a search finds nothing, wants an insight acted on, set aside or put on the sourcing list, wants the thresholds changed, or asks whether a change worked.
---

# Naratix insights

An **insight** is something a connected source says to do, with the traffic at stake: a search the store finds nothing for, a category viewed and not bought, products Google shows just below its top results. The shop's Insights page lists them; in the chat you read the same list, open one with everything behind it, read the numbers themselves, and press what the page presses. The user owns this: insights and their numbers are the shop owner's to read and act on, as in the app.

The go-ahead and following background work go by the `naratix-operator` skill; reviewing what a run wrote goes by the skill of its kind (`naratix-attributes`, `naratix-content`). The guided starter *Act on my insights* walks this skill's order.

## Read

1. **The sources first.** `list-insights` answers each source's card before its rows: what it read (the days, the window), its thresholds, how many insights it found and how many are listed, whether an analysis runs. A store search source with searches waiting (`waiting`) has not read them yet: offer `act-on-insights` `action: analyse`. A source that found nothing at its thresholds may have them set too strict.
2. **The list.** Rows come most traffic at stake first, 50 a page, with `counts` per source, kind and state. Narrow with `source`, `kind`, `state` or `search` instead of paging.
3. **Open the few with the most at stake**, three to five, with `insight_id`. The opened insight carries every fact (the searches with their counts, the words, the products with their numbers, the pages, the filters, the numbers another source holds for it), what was already changed from it, `offers` (what `act-on-insights` takes for it) and `previews`: what each action would start now, on how many products, which attributes, the credits it uses, or why it cannot.
4. **The numbers themselves** where they add something no insight names: `insight-numbers` by `view` over 7, 28 or 90 copied days, sorted and searched: categories and products by views and units per 100 views beside the shop's own figure, product pages and other pages by impressions, clicks and position, the searches behind a page or product, searches on the rise, the store's searches by no results or no clicks, and one product from every source.

**Completion:** you know what each source read, the insights with the most at stake are opened, and the numbers you lean on are read, not assumed.

## Judge

Reason across sources; that is where the chat sees more than one page:

- A search the store finds nothing for that Google shows the shop's pages for is demand the shop already meets on the web: fix the store's search first (synonyms, search words, titles).
- A category that does not convert whose products sit just below Google's top results: better data and text on its products serve both.
- A category that does not convert is judged on all its views; its enrichment reaches the products listed (`thin_views` of `views`). Say that share before proposing it.
- A title word listed in a product's `unconfirmed` is in neither its description nor its attributes. Read the product (`show-product`) and judge: a word that names the same thing in shoppers' words belongs in the title; a word that states something the product is not does not.
- A category page whose impressions are mostly one search is a question about that search: does the page answer it? A search in another language or for another thing is no reason to rewrite the description.
- A product that sells for the few views it gets is worth showing more: SEO text, a title in the words shoppers use.
- A search for the store's own name, an order number or a delivery question says nothing about a category page or a product.
- Small counts are noise: a rise from 3 to 9, a category with a few dozen views. The thresholds exist for that; say when a figure is too small to mean anything.
- A search for something the shop does not sell is a sourcing question, not a fix: the sourcing list holds it.
- Never promise a gain. Say what a change answers and that its effect is measured afterwards beside similar products.

## Propose

A short ranked plan, three steps at most, in the user's words: for each, the insight or numbers it answers, what it starts (how many products, which attributes or texts) and the credits it uses, from the preview. Ask which to do, through the app's question tool where it has one.

## Act

`act-on-insights` with the action and its `insight_id` (or `connector_id` for a source's own actions) and no `confirm` changes nothing: it answers what the press would do, the page's own preview. Put that to the user; on their yes call again with the same arguments and `confirm: true`.

- **Synonyms**: the opened insight proposes pairs; you may propose your own groups of words shoppers use for the same thing. The user agrees on each.
- **Search words** on the products that fit; `hide: true` also keeps them from shoppers, a store-wide setting of the index: ask about it on its own.
- **A filter that emptied a search**: the preview lists findings; a finding that says `value_missing` takes `add_value`, one that says `unfilled` takes `enrich`, each with its `filter` and `value`. A filter that stands for nothing yet is linked first (`link_filter` with `target` and, for an attribute, `attribute_id` from `search-catalog`).
- **A new category** for a kind of product no category holds: agree on its name and parent (`parent_id`, from the preview's parents or `search-catalog`).
- **Titles, SEO text, a category's description, an enrichment**: each takes the next batch the page would take; texts and values wait for review in the app.
- **Set aside**: `dismiss` (30 days), `not_relevant` (for good), `add_to_sourcing` for what the shop does not sell, `put_back` to undo any of them.
- **Thresholds** (`set_thresholds`) take only the names the source has; its insights are found again with them.
- **Roll back** a change from the History (`roll_back` with its `change_id`): the store's search or the catalog goes back as it was; runs reviewed in the app do not roll back.

## Follow a change you made

When you change products with another tool on your own suggestion (titles, SEO text, enrichment, edits), offer to follow it: `act-on-insights` `action: follow` with a `name` the History shows, `why` in one sentence, and the products (`product_ids`, or the run's `enrichment_id`), with `changed_on` when they changed on an earlier day. It changes nothing; every source measures the products over the four weeks before and 2, 4, 8 and 12 weeks after, beside similar products.

## Come back to what changed

`insight-numbers` with `view: changes` lists every change of the shop made from an insight or followed, newest first, as the app's **Changes made** shows them, each with what every source measured. Say it in the app's words: a day against a day before and after, the similar products' movement, the difference. Where `enough` is false the change had too little traffic before for a movement to mean anything: say so, never a verdict. A change too recent has its next figure's date. Then propose what to try next.

A change to the store's search also has a measure whose `subject` is searches: the searches it was made for, beside the index's other searches. Its movement is in points of the share with no result (`moved_in`), so say "the share with no result went from 73% to 10%, the other searches' from 10% to 15%". For one product, `view: product` lists the changes made on it (`changes`): name them beside its numbers, and read their figures with `view: changes`.

**Completion:** the user knows what the numbers say, chose what to do, what they said yes to was pressed, and they know when its first figures arrive.
