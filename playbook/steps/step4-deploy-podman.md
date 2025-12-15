# Step 4 (SRV_1) — Deploy Baseline on VPS (Podman)

## Goal
Deploy FE + BE + DB/cache with:
- repeatable setup
- least privilege
- safe env separation

## Baseline model
- 1 VPS can run multiple small projects initially.
- Each project must be isolated by:
  - separate env vars
  - separate DB (preferred) or separate db user/schema
  - separate ingress routes

## Podman baseline
- rootless containers
- volumes for persistent data
- env files per environment (no shared `.env`)

## Reverse proxy / gateway baseline
- terminate TLS
- route by domain/subdomain to the correct FE/BE
- rate limit sensitive endpoints (auth/payments)

## Security baseline checklist
- firewall rules (only required ports open)
- SSH key-only + IP allowlist where possible
- fail2ban
- keep secrets out of git

## Notes
- Keep this runbook practical; prefer scripts/templates per project in the project repo when needed.
