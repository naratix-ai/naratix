# Following and controlling running work

## Has it finished?

An Enrichment has **finished** once its `status` is completed, failed or cancelled. While it is active, a fresh launch may still be preparing (`preparing` counts its products before any run exists), so zero counts are no sign of an end: say it is running.

## The Enrichments card

`show-enrichments`, and `control-enrichment` on its Enrichment, draw the Enrichments card. It follows running Enrichments live, pauses, resumes, cancels and retries in place, holds what you sized for the go-ahead, and tells you when one finishes. Its Review press asks in the chat for that kind's review, its context naming the tool and the Enrichment: open it with that tool (`show-mining`, `show-images`, `show-categorization` or `show-content`) and the `enrichment_id`.

## Pause, resume, cancel, retry

`control-enrichment` pauses, resumes, cancels or retries an Enrichment or one Batch, on the user's word only:

- **pause** holds it at once; nothing is lost.
- **cancel** cannot be undone: unfinished products are dropped. It needs `confirm` after the user's yes.
- **resume** and the retries start work again: size first, then `confirm`. Offer **retry_failures**, which re-runs only what failed; **retry** re-runs every product, the successes too, so run it only when the user asks for exactly that.
- **approve_all_exact** and **approve_all_strong** are verdicts on the results: count first, then `confirm` on the user's word.

Completion: the user said which action, and heard what it keeps or loses before any `confirm`.
