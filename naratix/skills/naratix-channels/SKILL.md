---
name: naratix-channels
description: Sales channels in Naratix — the connectors to Mirakl marketplaces, VTEX stores and a partner's system through the API connector, their setup, and sending products to them. Use when the user wants products published, pushed or exported (VTEX, Mirakl, a file, the partner's PIM), wants a failed delivery, a partner batch or a push's missing files sent again or Mirakl's reports pulled now, asks what a marketplace rejected and why, asks which channels the shop is connected to or why one refuses its key, wants one switched on or off or made the default, wants the partner push rules installed, wants a Mirakl attribute mapping generated, or wants products labelled.
---

# Naratix channels

A **connector** links the shop to a place its products go: a Mirakl marketplace, a VTEX store, or a partner's system (a PIM, for instance) through the API connector. Each connector has a type, can be switched on or off, and one connector per type is the default the app uses.

## See the channels

`list-connectors` shows each connector's type, whether it is on and whether it is the default. For an API connector it shows its delivery `health` (`not_provisioned`: the partner's inbound credentials are not generated yet) and its `push_rules`. For a Mirakl connector, `refused_by_channel` true means Mirakl refused its key in the last hour: open its form, as *Credentials* says. Credentials never appear there.

## Set up a channel

Only the shop's owner changes connectors, as in the app. With `setup-connector`:

- `disable` stops a connector sending: no uploads or pushes, and an API connector takes no partner batches, until `enable` switches it back on. Imports can still read from it.
- `set_default` makes it the connector the app uses for its type.
- `install_push_rules` installs the standard rules on an API connector that has none yet (`list-connectors` reads its `push_rules` false): confirmed categories are sent to the partner, the ones it accepts are locked, and a failed delivery is labelled. Rules already there stay as the owner switched them: for any change, point the owner to the connector's Rules tab in the app. Once rules are there, confirming categories (`confirm-categories`) pushes, so it waits for the user's yes as the `naratix-operator` skill's *The go-ahead* says. Nothing is sent until the partner's inbound credentials exist; the result says so.

## Credentials

Credentials — a VTEX app token, a Mirakl key, a partner's client secret — are typed by the shop's owner in the form `setup-connector` shows, never in the chat. `credentials` with the `connector_id` opens the form for an existing connector; with `type` (`vtex`, `mirakl` or `api`) instead, the form creates a new one. Ask for no token, key or secret yourself, and pass none to any tool; when the user pastes one into the chat anyway, tell them to change it at its source, since the chat keeps it.

Before you open the form, when a launch names a missing key, when `list-connectors` reads `refused_by_channel`, and when the form answers, read [references/credentials.md](references/credentials.md).

## Map attributes to Mirakl

A Mirakl marketplace has its own fields. Bringing a taxonomy in from Mirakl, and a sync that changes it, already generate its mapping, so `generate-mirakl-mapping` is for generating it again, on the taxonomy brought in from that marketplace (the shop's owner only). It has the AI work out which of the taxonomy's attributes and values fill which Mirakl field; a taxonomy with no marketplace fields is refused before anything starts. Fields already picked on the Mapping tab are kept and the AI fills the empty ones, and the result is live for the next import or export with no review step.

1. Pass the Mirakl `connector_id` and the `taxonomy_id`, and get the user's yes as the `naratix-operator` skill's *The go-ahead* says.
2. Its card follows it; where no card shows, `list-runs` with `kind: activity`, the `since` it returned and `search: mapping` reads the outcome: the entry whose title ends in this taxonomy's name; a title that begins "Could not" means it failed.
3. The user is notified when it is ready; the Mapping tab shows it, and any field can be changed there.

**Completion:** the user heard that picked fields are kept and the result goes live with no review, said yes, heard how it ended, and knows where the Mapping tab shows it.

## Send products

`export-products` sends a selection, chosen with the products list's filters, to one `channel`, as the products list's Export menu does.

0. **The products.** Reviewed products are the usual choice (`review_status: approved`); show the selection with `list-products` before the preview.
1. **Ask the user where the products go** unless they said: a file, a VTEX store, a Mirakl marketplace or the partner's PIM. Their answer also settles what is sent: Mirakl upload or files only (`upload`), VTEX images only (`images_only`) and the PIM mode (`mode`).
2. **The connector.** For a `vtex` or `mirakl` push, call `list-connectors` first, even when the user named the channel, and name the target connector to them (the default one, switched on); with none switched on, or several, ask which before the preview, and pass it as `connector_id`. A `pim-sync` push goes through the shop's API connector, which its preview's `target` names, and takes no `connector_id`; when that is not the partner the user means, the owner switches the other API connector off (`setup-connector`, `disable`) before the push.
3. **The channel's options**: before you set any, or say what a channel sends or skips, read [references/export-options.md](references/export-options.md). Every option left out takes the app's default, and the file and VTEX cards show them picked for the user to change. An option is refused on a channel it is not for.

### The push

1. Call without `confirm`, once *Send products* has settled where and through which connector: the result gives how many products go, where, and a sample. Show the user all three.
2. Call again with `confirm: true` after their yes, with the options the card shows. A push to a channel cannot be called back. The same selection sent to the same place with the same options twice within ten minutes is refused the second time; another format or other options make a new export.
3. Follow it as the `naratix-operator` skill's *Following work* says. Where no card shows, read only this push: `list-runs` with `kind: export` and the `since`, `connector_id` and `taxonomy_id` it returned, leaving out any it did not return or returned null. Without them, its rows and `totals` count every push since. A Mirakl push's id for `mirakl-reports` is its row's `export_id` there, not a run id:
   - a file, or Mirakl files only: `list-runs` hands over its `download_url` once it is done; a file's row is the one with the `export_id` the push returned. Give the user no link before that. Mirakl files that read `files` 0 once done had nothing to build: say so, and that the export's `panel_url` shows why.
   - a Mirakl push: `status` done means the files are built. The upload goes after that, when the marketplace next takes one, and each row's `upload.state` says where it stands: `waiting` (`next_upload_at` is the next slot), `sending`, `sent`, `failed` (pass its `error` on as written: it says what to do next) or `nothing_sent` (nothing was there to upload; the export's `panel_url` shows why). Tell the user the products were sent only once it reads `sent`; `totals.mirakl` reading `running` and `uploads_pending` 0 says the whole push has gone; `uploads_failed` above 0 says part of it did not (each failed row's `error` says why).
   - a VTEX push writes one row per 50 products: report its `totals.vtex` (`sent`, the products VTEX received, `skipped` and `errors_total`) once its `running` is 0, never one row's counts or the size of the selection.
   - a `pim-sync` push is followed on the API connector's page in the app, which its `panel_url` opens.

**Completion:** the push was confirmed and `list-runs` shows it gone (a Mirakl `upload.state` of `sent`; for VTEX, the user has heard `totals.vtex`'s `sent`, `skipped` and `errors_total` once its `running` is 0), or failed with the reason relayed to the user; a file's link was handed over; for `pim-sync`, the user has the connector page's link.

## Send again

`setup-connector` sends again what did not arrive: an API connector's failed deliveries (`resend_deliveries`) and partner batches (`replay_batch`), a Mirakl push's files that never went (`upload_missing`), and asks Mirakl for its reports now (`pull_reports`). Each send is a push, asked in the chat first. Before any of them, read [references/sending-again.md](references/sending-again.md).

## Share links

Preview links of products' descriptions, to send someone outside the shop, are the `naratix-content` skill's *Share links*.

## Labels that push

Some labels push: when a rule sends labelled products to a channel, `add-product-labels` says where they go and waits for `confirm` after the user's yes. Other labels are added at once.

Taking labels off is `add-product-labels` with `remove_labels`, and always waits: without `confirm` it says how many products carry them, names a few, and where taking each off sends the products (`removal_pushes`, a rule on the removal; it cannot be called back). Ask in the chat, through the app's question tool where it has one (the change and "Not now" as choices), plain words otherwise; on the yes call again with `confirm: true`.

Labels are named "Group: Label" (`search-catalog` with `entity: label`) and sit on each product's category in one taxonomy (`taxonomy_id`, else the shop's default), so products with no category there are counted and left out: say how many.

## When a marketplace rejects products

When the user asks what a Mirakl marketplace rejected, or why, and before you read a Mirakl push's report, read [references/rejections.md](references/rejections.md).
