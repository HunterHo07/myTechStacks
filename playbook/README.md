# Playbook (Step-by-step)

This folder contains the procedural runbooks.

Goal:
- Keep the root `README.md` clean (why + architecture + stack picks)
- Keep all operational steps here (step1..stepN, upgrade1..upgradeN)

## Baseline (Step 1..)
- `steps/step0-environment-routing.md`
- `steps/step1-fe-sveltekit.md`
- `steps/step2-be-baseline-api.md`
- `steps/step3-cache-redis.md`
- `steps/step4-deploy-podman.md`
- `steps/step5-observability-baseline.md`

## Upgrade path (Upgrade 1..)
- `upgrades/upgrade1-dedicated-redis-rate-limit.md`
- `upgrades/upgrade2-dedicated-db-backups.md`
- `upgrades/upgrade3-queue-backpressure.md`
- `upgrades/upgrade4-split-services.md`
