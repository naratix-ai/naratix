# Following and controlling running work

## Following work

Launches join an **Enrichment**, one **Batch** per launch; `list-runs` with `kind: enrichment` reads its counts and its Batches. A card follows it: end your turn and read its context on the next one. With no card, follow it with `list-runs` at a sensible interval. With the chat closed, the user gets one email once everything in an Enrichment you started has finished.

When an attribute Enrichment finishes, check its products without asking, as the `naratix-attributes` skill's *Follow the run* says.

## Has it finished?

An Enrichment has **finished** once its `status` is completed, failed or cancelled. While it is active, a fresh launch may still be preparing (`preparing` counts its products before any run exists), so zero counts are no sign of an end: say it is running.

A paused Enrichment has not finished: it is on hold until resumed.

## Reading its counts

An Enrichment's own counts (`total_products`, `completed`, `failed` and the rest in `list-runs`; `products`, `done` and the rest in `show-enrichments`) cover every Batch it holds: report those. `batches` lists only the newest 20 live Batches, the newest 20 finished ones and the newest of each kind of work, and `batches_total` says how many it holds, so never add up `batches` for a total. A Batch's `number` is its Batch # in the app: name a Batch by it. `unplaced` (in `show-enrichments`; in `list-runs`, a categorization Batch's `skipped`, and each product's `outcome` `skipped`) counts the products categorization could not place, with too little data to go on: each kept its category, or stays uncategorized if it had none. They are not done, so say how many. `show-enrichments` reads the 20 newest open Enrichments: when `more_open` is true, `list-runs` with `kind: enrichment` and `status: open` lists them all.

## The Enrichments card

`show-enrichments`, and `control-enrichment` on its Enrichment, draw the Enrichments card. It follows running Enrichments live, pauses, resumes, cancels and retries in place, holds what you sized for the go-ahead, and tells you when one finishes. Its Review press asks in the chat for that kind's review, its context naming the tool and the Enrichment: open it with that tool (`show-mining`, `show-images`, `show-categorization` or `show-content`) and the `enrichment_id`.

## Pause, resume, cancel, retry

`control-enrichment` pauses, resumes, cancels or retries an Enrichment or one Batch, on the user's word only:

- **pause** holds it at once; nothing is lost.
- **cancel** cannot be undone: unfinished products are dropped. It needs `confirm` after the user's yes.
- **resume** and the retries start work again: size first, then `confirm`. Offer **retry_failures**, which re-runs only what failed; **retry** re-runs every product, the successes too, so run it only when the user asks for exactly that.
- **approve_all_exact** is a verdict on the results; **approve_all_strong** only puts the strong images in the products' photos and leaves their verdicts as they are. Both count first, then `confirm` on the user's word.

Completion: the user said which action, and heard what it keeps or loses before any `confirm`.
