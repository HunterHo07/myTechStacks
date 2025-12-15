# Step 0 — Environment Routing + Guardrails

## Goal
Prevent the #1 production disaster:
- dev/uat accidentally pointing to prod DB or prod secrets

## Guardrails (3 layers)
- Config:
  - separate env vars per environment
  - no shared `.env` between dev/uat/prod
- Code:
  - startup checks: block prod DB URL in non-prod
  - startup checks: block dev keys in prod
- Network:
  - prod DB and crypto/secrets service only reachable from prod private network

## Routing rule
- dev -> dev backend -> dev db
- uat -> uat backend -> uat db
- prod -> prod backend -> prod db

Sensitive operations
- encryption/decryption and key rotation are prod-only and must go through crypto/secrets service
