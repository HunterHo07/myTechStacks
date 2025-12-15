# Upgrade 3 — Queue + Backpressure

## Trigger
Do this when:
- heavy jobs block request latency
- you need controlled retries
- you need backpressure (don’t overload DB/providers)

## Changes
- introduce a worker model
- move heavy work to background jobs
- enforce idempotency for jobs and webhooks

## Notes
- Pick queue tech that matches your runtime compatibility.
- Keep retry policies explicit (max attempts, backoff, dead-letter).
