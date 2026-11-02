# Rows you send

How products and taxonomies come in from rows you build yourself: from a file the user dropped in the chat, or from products they typed there. The rest of the imports procedure is in [SKILL.md](../SKILL.md).

The user's app hands a dropped file to you, never to Naratix: Naratix receives only the rows you send, through `import-products` or `import-taxonomy` with `source: rows`. No card shows for rows, so you show what comes in and ask in the chat.

## Read the whole source

1. Read all of it. With a code or file tool, open the file there and count its rows exactly; a PDF or an image of a list, every page. Work from what you read, never from the first lines alone.
2. Copy every value **verbatim**, in the source's language: titles, codes, EANs, brands, category names, attribute names and values, units. Fixing a file means moving a value to its field, splitting a combined cell ("Red / XL" into a colour and a size), joining category levels into a path, trimming stray spaces; the value itself stays as written.
3. What the source lacks stays empty. A code, an EAN, a brand or an attribute value you would have to make up, translate or recall from your own knowledge goes nowhere; a row with neither a code nor an EAN waits until the user gives one. Rows carry the catalogue's facts only: no prices, stock or delivery terms, which no field takes.

## Product rows

`import-products` with `source: rows`. Each row takes `title`, `code` (or `ean`, which then becomes the code), `ean`, `external_id`, `brand`, `description`, `images` (web links, main photo first), `category` or `category_id`, `attributes` and `parent_code`.

- **Categories**: look each distinct category up in the taxonomy the import matches against (`search-catalog`, `entity: category`, its `taxonomy_id`), and send its path or its `category_id`. Products go in a leaf category; a row whose category is not found comes in without one, and `launch-categorization` places it once the import is over.
- **Attributes**: `{"name": "value"}`, each named as the taxonomy names it (`search-catalog` on the category lists them), a list for several values. Where the source's value is one of the attribute's allowed values, use that spelling; otherwise send it as written. An attribute the row's category lacks stays with the product as its source data, which enrichment reads.
- **Variations**: rows that are the sizes or colours of one product share a `parent_code`, the same category and the same brand; the import makes their main product from them, so the main product is never a row of its own.
- **Codes already in the shop**: the preview's `duplicates` names them. Ask whether they are updated, skipped or added again as new products, and send the answer as `duplicate_handling` (update, skip or create). Updated, a product takes every field the import's rows carry, so a row that leaves out a field others give clears it.

## Taxonomy rows

`import-taxonomy` with `source: rows`, its `name` and `language` asked as for a file. Each row is a category: `path`, its names from the top level down, and `attributes`, each with its `name`, `type` (text, long_text, number, date, list, multiselect or yes_no), `requirement_level`, `description`, allowed `values` for a list or multiselect, and `code` where the source has one. Parents no row names are made from the paths. A subcategory carries its parents' attributes too, so an attribute every category shares goes once, on the top one.

## Preview, ask, send

1. **Preview** the first batch without `confirm`: nothing is kept. The answer's `rows` says which fields the rows give, which attributes fill the category's own and which stay as source data only, the categories not found, the duplicates, a sample, and each row's `errors` and `warnings` by its `index` in the batch.
2. **Fix** every error from the source and preview again; a warning is the user's to hear.
3. **Ask** in one message: how many rows, the sample as it comes in, which of the source's columns went to which field, what stays unmapped, the duplicates and the warnings. Ask through your app's question tool where it has one, with the choices as options (bring them in, or change something; for duplicates update, skip or add as new; for a taxonomy, the owner's default question), and in plain chat otherwise.
4. **Send** on their yes, with `confirm: true`. A batch carries at most 200 rows and 50 KB; long descriptions fit fewer rows, and a refusal says how many fit. More than one batch: `more_rows: true` on every batch but the last, the `rows_id` the first answer returns on every later one, and `total_rows` on the last, which starts the import only if every row arrived. Send them one after another, each once the one before has answered, and tell the user how far it got ("400 of 1,250 rows in").
5. **Recover**: a batch whose answer lists errors was not kept, so fix it and send it again whole; a batch whose answer never came is sent again as it was, and it is kept once.
6. **Follow** the import the last batch starts as the `naratix-operator` skill's *Following work* says (`list-runs` with `kind: import` and its `run_id`), then tell the user what *After a products import* ([SKILL.md](../SKILL.md)) reads, or for a taxonomy what *After a taxonomy file* ([file-import.md](file-import.md)) reads.

One import takes at most 5,000 rows. For a larger source, write the fixed file with your file tools, hand it to the user to download, and open the upload card for them to pick it, as [file-import.md](file-import.md) says.

**Completion:** the user saw the sample and the mapping and said yes, every batch was kept, `received` equals the rows the source holds, and the import the last batch started reads completed, or the user heard why not.
