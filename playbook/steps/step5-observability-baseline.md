# Step 5 (OPS_1) — Observability Baseline

## Goal
Before scaling, ensure you can see:
- what is failing
- what is slow
- what changed

## Minimum signals
- logs
- metrics
- uptime checks

## Request identity
Every request should carry:
- `request_id`
- `session_id` (if logged in)
- `user_id` (if authenticated)

## Logging convention
- All `console.log` must include: `🚀`

## Tracing
- Add tracing only when microservices grow.
- Keep traces free of secrets/PII.
