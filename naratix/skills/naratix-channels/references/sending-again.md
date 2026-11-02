# Sending again

`setup-connector` sends again what the app's connector pages send again. Each send is a push that cannot be called back, and none draws a card: the user is asked in the chat.

| Action | What it sends | Who |
|---|---|---|
| `resend_deliveries` | an API connector's deliveries that did not arrive, to the partner's system | the shop's owner |
| `replay_batch` | a partner batch's results, again, to the partner's system | the shop's owner |
| `upload_missing` | a Mirakl push's files that never went, to the marketplace (`mirakl_export_id`) | the owner, or a member who may read products |
| `pull_reports` | nothing: it asks Mirakl now for the reports of one push (`mirakl_export_id`) or of every push | the owner, or a member who may read products |

## A send

1. **Find what to send.** `resend_deliveries` without `delivery_ids`, or `replay_batch` without `partner_batch_id`, lists what failed (50 a page, `offset` for more): each item's `what`, `status`, `at`, and `detail`, why it did not arrive. Tell the user and ask which to send. A Mirakl push's id is its row's `export_id` in `list-runs` with `kind: export`; a row whose `upload.state` is `failed` or `waiting` is one with files that never went.
2. **Preview.** Call with the ids and without `confirm`: nothing is sent, and the note says what would go, and where. Put that to the user in their language, saying it is a push that cannot be called back. Ask through the app's structured question tool where it has one, the change and "Not now" as choices; in plain chat otherwise.
3. **Send** on their yes: the same call with `confirm: true`. The same send confirmed twice within ten minutes is refused the second time, so nothing is delivered twice.
4. **Follow it.** A delivery or a batch: the connector's Live tab in the app shows how the partner answered (`panel_url`). Mirakl files: `list-runs` with `kind: export` reads the upload, then `mirakl-reports` with the `mirakl_export_id` once the reports are in.

**Completion:** the user heard what would be sent, and where, before their yes, and knows where to see how it went.

A refusal says why, in the app's words: a delivery whose retry is still coming, one still on its way, a switched-off connector, a Mirakl connector without its shop key, or no file left to send. Pass it on as written.

## Reports now

`pull_reports` sends nothing, so it needs no yes: the reports arrive in a few minutes, and the app also asks for them every few minutes on its own. Then read them with `mirakl-reports` ([rejections.md](rejections.md)).
