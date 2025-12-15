# Step 1 (FE_1) — Frontend Bootstrap (SvelteKit / Svelte 5)

## Goal
Create a frontend that is:
- fast to ship
- safe (no secrets in browser)
- production-ready (SSR + caching as a real layer)

## Baseline rules
- dev/uat/prod must have separate env vars and separate endpoints.
- FE must not talk directly to prod DB.
- FE can route requests to the correct BE by environment.

## Create project
- Create app:
  - `npm create svelte@latest my-fe`
- Install:
  - `npm install`
- Run:
  - `npm run dev`

## FE architecture reminders
- FE SSR is not just UI:
  - it can reduce load via route-level caching
  - it can enforce environment routing rules (dev -> dev BE, uat -> uat BE, prod -> prod BE)

## Lint/format (baseline)
- Use Prettier for formatting `.svelte`.
- Use ESLint for code rules.
- Keep versions consistent with the project `package.json`.

## Deployment reminder
- FE should only receive public config (example: public base URLs).
- Any signing/encryption or sensitive logic must stay server-side.
