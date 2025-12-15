# Step 2 (BE_1) — Backend Baseline API

## Goal
Create a backend that can safely power multiple small projects initially, and can be upgraded later without rewrites.

## Day-1 endpoints
- `GET /health`
- `GET /version`

## Baseline non-negotiables
- Request validation at boundaries (reject bad input early)
- Standard error handler (consistent error shape)
- Logging convention: all `console.log` includes `🚀`

## Auth + permissions (server-side security)
- Auth/session: how the user proves identity (session cookie or token)
- RBAC: role-based access control (who can do what)

Minimum RBAC example
- roles: `admin`, `ops`, `finance`, `support`
- permissions: `payout.approve`, `report.export`, `user.read`, `user.write`

## Secure integrations
Payments/webhooks must include:
- signature verification
- IP allowlist (when possible)
- idempotency keys
- safe retries (no double-credit)

## Environment guardrails (must-have)
- dev/uat must never use prod DB or prod secrets.
- Add startup checks to fail fast if non-prod is configured with prod URLs.

## Suggested structure
- `src/app` (routes/controllers)
- `src/services` (business logic)
- `src/db` (db client + repositories)
- `src/middleware` (auth, validation, error handler)
- `src/config` (env schema + config loader)
