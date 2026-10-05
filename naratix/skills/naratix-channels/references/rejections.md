# When a marketplace rejects products

A Mirakl marketplace reports on each product some time after the upload: wait until `list-runs` (`kind: export`) reads `upload.report_received` true for the push, since before that no product reads as rejected. `mirakl-reports` then lists the rejected ones (`status: rejected`) with the reasons: the Mirakl field and the marketplace's message. Warnings are `status: warning`.

Read one push with its `mirakl_export_id` alone (the Mirakl row's `export_id` in `list-runs`; it brings the push's own taxonomy and connector), once its `upload.report_received` is true: the result counts what that push `sent`, `rejected`, `warning`, `no_errors` and `not_reported_yet`; `from_later_push`, its products whose result a later push's report took over; and `not_sent_no_category`: the push's products with no category in the taxonomy, never sent (offer to place them first). `not_reported_yet` waits for a report only while `awaiting_report` is true: once it is false none is coming, and `list-runs` says how the upload ended. It marks each row `this_push`; rows come 50 a page, so page with `next_offset` until it is null before grouping by reason, and `total` is the count. A row with `this_push` false is another push's result for that product, so never report it as this push's. Without the id the list holds every product's latest result, older pushes included.

1. Tell the user how many were rejected and why, grouped by reason. In apps that show views, the card groups them by reason, links each product's page and the products list under the rejection's chip, and hands a group back to you to send again when the member may push.
2. The user fixes the values in the app, on each product's page (the row links there).
3. After their yes, send exactly those products again: `export-products` with `channel: mirakl`, their `product_ids`, `taxonomy_id` and `connector_id`, one push per taxonomy and marketplace.

**Completion:** the user heard the rejections grouped by reason, and only the products they fixed went again, after their yes.
