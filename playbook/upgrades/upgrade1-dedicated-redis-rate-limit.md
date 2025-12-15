# Upgrade 1 — Dedicated Redis + Stronger Rate Limiting

## Trigger
Do this when:
- cache/session pressure increases
- you need consistent rate limiting across multiple app instances

## Changes
- move Redis to a dedicated service/container (or dedicated host)
- add rate limits at:
  - edge (if available)
  - gateway
  - backend

## Security notes
- apply stricter limits to auth + payment/webhook endpoints
- prefer fail-closed for sensitive endpoints
