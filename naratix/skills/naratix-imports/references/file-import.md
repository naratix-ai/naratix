# File imports

How products and taxonomies come in from a file, and what to do when one does not fit. The rest of the imports procedure is in [SKILL.md](../SKILL.md).

## From a file

An import always starts from a file the user picks in the card; a file attached in the chat is only yours to fix and hand back. `import-products` or `import-taxonomy` with `source: file` shows a card where the user picks the file (up to 20 MB), checks the columns matched to its fields and starts it; nothing starts from your call, so `confirm` does not apply. The card goes past its Map step only once the fields the import needs have a column: a product's title and code, a category's name, id and parent id.

- **Products**: say what each row needs, as *What a products file holds* says. Pass `taxonomy_id` to match categories against another taxonomy than the shop's default, or `match_categories: false` to bring them in without categories. When the shop already has products (`has_products` in `list-shops`), ask once in the chat whether codes already in the shop are updated or skipped, before the card shows, and say in the same question that a JSON file updates them whatever the answer; pass the answer as `duplicate_handling`, and the card shows it picked. For a file you know is JSON, ask nothing and leave `duplicate_handling` out. A JSON file is never matched to a taxonomy and builds no variant families: its products arrive without categories (offer `launch-categorization` once it ends), so for categories or variants ask for a CSV or XLSX. Left out from a CSV or XLSX, those codes come in again as new products, so in such a shop say which rows are new, updated or skipped rather than that every row is created. When the card's context says the user picked a `.json` file after they chose to skip those codes or add them as new, tell them that this file updates the products with those codes.
- **Taxonomy**: a CSV or JSON file, not XLSX; for any other file, offer to convert it to a CSV as *What a taxonomy file holds* says, hand it back for download, then show the card. Ask its **name** and **language** (the one the file's category names are written in) first, as for a channel, and pass them. Whether it becomes the default is asked as for a channel (step 3 of *Bring in a taxonomy* in [SKILL.md](../SKILL.md)); the card asks it before Create.

Once the card's context says it started, the card follows the import and its context says when it ends; call `list-runs` (`kind: import`, `run_id`) only when the user asks or comes back. An app that shows no card, or a larger file, goes through the app instead: the result's `panel_url` is the app's New import page for products, the Taxonomies page for a taxonomy.

**Completion:** the card's context says the import started, or the user has the `panel_url` where no card shows; once it ends, the user has heard what *After a products import* ([SKILL.md](../SKILL.md)) reads for products, or *After a taxonomy file* for a taxonomy.

## When the file does not fit

The card puts what it read into your context: the file's name and `upload_id`, its columns, which field each was matched to, the fields still without a column, a few sample rows, its separator, whether it is saved as UTF-8, the separator and header row its first lines suggest (`layout_guess`), product codes a spreadsheet turned into numbers (`code_check`), the user's picks, and any refusal the card showed. When the user says the file fails, read that context and say plainly what is wrong.

1. **A layout problem** — encoding, separator, a title row above the header, rows to skip, one column to split or several to join (category levels into one path with `/`, image columns into one cell with `,`), decimal commas, stray spaces: call the same tool again with `source: file`, the `upload_id`, `reshape` and the picks the card's context holds, so they show picked. It writes a fixed copy, and only the newest copy can be started; the user's file is kept as it is. Its answer draws no card: the card the user already has redraws on the copy at its Map step, keeping the picks made on it, and the user checks it and starts it there. When that card is no longer in the chat, open it again on the copy: the same tool with `source: file`, the copy's `upload_id` and the picks the context holds (`duplicate_handling`, `taxonomy_id` or `match_categories: false`; for a taxonomy its name, language and `make_default`). Fix again from the same `upload_id` or the copy's: every fix reads the user's file, so row numbers are always that file's.
2. **A JSON file** is not reshaped: fix it with your own file tools, or ask for a CSV export (or XLSX, for products).
3. **Codes turned into numbers** (`5.94E+12`) in a CSV have already lost digits: ask for a new export with that column stored as text. From an XLSX file the full digits are kept.
4. **Anything else**, where you can work on files: a PDF, an image or any other file cannot go in as it is, whether it is attached in the chat with no card shown yet or was picked in a card (then ask them to attach it in the chat). A taxonomy file goes as *From a file*'s **Taxonomy** says: the card takes CSV or JSON only. For a products file, in one message: tell the user in one line that the card takes a CSV, XLSX or JSON file of up to 20 MB, which they pick in it; offer to convert it, saying what happens next: you give them the converted CSV to download, then an import card opens where they pick it, check the columns and start it; list the rows the source gives no product code; and, when the shop has products (`has_products`), ask the codes question of *From a file* in the same message. On their yes, write the file as *What a products file holds* says:
   - Before you write the category column, look up each distinct category the source names in the target taxonomy (`search-catalog`, `entity: category`, its `taxonomy_id`, in the taxonomy's language) and write the matching leaf name, or its full path. A source category that matches only a parent, or nothing, leaves its rows without a category: tell the user how many rows, and offer `launch-categorization` once the import has finished (the `naratix-categories` skill).
   - Rows the source gives no product code stay out of the file until the user gives their codes; never make one up.
   - Hand the file back for download, then show the card for them to pick it: `import-products` with `source: file` and their `duplicate_handling`.

`template_url` in the result is a sample file the user opens in their browser; you cannot download it yourself. Offer it when the user asks what the file should look like.

## What a products file holds

- Row 1 is the header. Any header names work: they are matched to the fields for you, and `file.targets` in the result says what each field holds.
- Each row needs a title and a product code, the code stored as text.
- The category is the taxonomy's leaf name, or its full path joined by `/` with no spaces around it (not the " / " breadcrumb `search-catalog` shows).
- Images and keywords are comma-separated in one cell.
- In a CSV or XLSX, rows that share a variant group code become variants of one product.
- The shop SKU goes in a column of its own.
- A column that matches no field still comes in: the whole row is kept with the product as its source data, which categorization and attribute enrichment read, but it fills no field of its own. Only with a taxonomy matched and the card's Options' **Process Mirakl attribute columns** on does a column headed by one of the taxonomy's attribute codes fill that attribute.
- Sample rows you show the user use the file's real separator, so what they copy matches the file.

## What a taxonomy file holds

- In a CSV, each row carries its category's name, id and parent id; an empty parent id means a top-level category. `file.fields` in the result says what each column holds.
- A row holds one attribute of that category, with its code, type, requirement level and description, and one allowed value. A further value takes a row of its own, which repeats the category's id and the attribute or leaves them empty.
- An attribute's type reads as text, number, date, list (one value), multiselect (several values), yes/no or long text, and its requirement level as required, optional or recommended (mandatory, obligatoire and facultatif too); an empty cell comes in as text and required, and the import's notification names any other word, which comes in the same way.

## After a taxonomy file

`list-runs` returns the `categories` and `attributes` the taxonomy now holds. Read them before offering `import-products`: a taxonomy with no categories means the file's columns were matched wrong, so check them with the user and bring the corrected file in under another name. The empty taxonomy keeps its name; the user can delete it on the Taxonomies page.
