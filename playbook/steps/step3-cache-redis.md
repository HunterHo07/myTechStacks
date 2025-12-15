# Step 3 (BE_2) — Add Cache (Redis) + Data Safety

## When to add Redis
Add Redis when you need at least one:
- sessions (fast session store)
- caching expensive endpoints
- rate limiting
- idempotency keys for webhooks/payments
- streams / lightweight queueing

## Key rules
- Use clear key namespaces:
  - `sess:{sessionId}`
  - `cache:{route}:{hash}`
  - `rl:{scope}:{id}`
  - `idem:{provider}:{key}`
- Always set TTL for cache keys.
- Never store secrets in Redis.

## Idempotency baseline
For payment/webhook endpoints:
- compute idempotency key from provider reference + event id
- store key with TTL
- if key exists: return the previously computed safe response (no double processing)

## Cache invalidation baseline
- Prefer short TTL for derived data.
- For admin/back-office:
  - invalidate on writes
  - cache read-heavy lists with TTL
