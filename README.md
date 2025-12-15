# Hunter Ho — Engineering Standard + Project Hub
 
 ## Start Here
 This README is the baseline reference for:
 - what stack I pick (and why)
 - how everything connects (FE/BE/cache/DB/infra)
 - why the architecture is designed this way (security, scale, operational discipline)
 - pointers to step-by-step runbooks (see `docs/playbook/`)
 
 Notes:
 - I keep all showcase links and demos at the bottom of this README.
 - This is a living standard; keep it practical and deployable.
 
 ---
 
 # Canonical Tech Stack v3 — Freelancer + Project Standard
 
 ## Purpose
 This is my default standard for architecture, security, and delivery.
 
 Goals:
 - security-first (least privilege, revocable auth, encrypted sensitive data)
 - high performance (multi-layer caching, gateways, backpressure)
 - operational stability (repeatable deploy, monitoring, backups, restore drills)
 - scales from MVP to enterprise (upgrade path without rewriting everything)
 
 ---
 
 ## Canonical Principles (Non-Negotiable)
 - separation of environments: dev / uat / prod are different trust levels and policies
 - defense-in-depth: every layer has caching + authZ + rate limiting + logging
 - explicit trust boundaries: FE, gateway, BE, DB are not assumed to be trusted
 - secrets never in code: secrets are injected at runtime and rotated
 - auditability: important actions are traceable (request-id, user-id, session-id)
 
 ---
 
 ## Canonical Architecture (Reference Flow)
 
 ### Web request flow
 ```
 Client (Browser/Mobile)
   -> CDN/Edge (Cloudflare) [optional]
   -> FE SSR Server (SvelteKit)
       -> FE SSR Cache (memory/Redis)
       -> Health checks (CDN/DNS/Gateway)
   -> API Gateway / Load Balancer
       -> Rate limit / WAF / bot control
   -> BE Cache (Redis)
   -> Backend Services
       -> Prod-only Crypto/Secrets Service (Private VPS) [optional]
   -> Databases (Postgres/Timescale/TigerBeetle) + Object Storage (MinIO/S3)
 ```
 
 ### Why FE SSR is a real security/performance layer
 - FE SSR cache reduces traffic before it reaches the gateway.
 - FE SSR can enforce routing rules:
   - only accept BE from FE / gateway private network
   - health-aware routing (graceful degradation)

 ---
 
 ## Runbooks (Step-by-step)
 All procedural steps live in `docs/playbook/` so this README stays clean.
 - `docs/playbook/README.md` (index)

 ---
 
 ## Prod-only Crypto/Secrets Service (Private VPS) [Optional]
 
 ### What it is
 A separate production-only service/VPS that can:
 - hold master keys
 - perform application-level encryption/decryption for sensitive DB fields
 - optionally sign/verify security tokens
 
 ### Why this is useful
 If the DB is leaked, sensitive fields are still encrypted and cannot be decrypted without access to the crypto service keys.
 
 ### Strict rule
 Do not encrypt fields that must be queried/filtered/sorted.
 
 ### Access policy baseline
 - reachable only via private network (WireGuard/Tailscale/private LAN)
 - SSH: IP allowlist, key-only auth, fail2ban, SELinux enforced
 
 ---
 
 ## Authentication Standard: Sessions + (Optional) JWT
 
 ### Canonical recommendation
 - session-based auth is the default for web apps
 - JWT is optional and treated as:
   - short-lived access token (minutes)
   - never stored in localStorage
   - transported via HTTP-only Secure cookies or server-to-server
 
 ### Why sessions are better for real products
 - revocation (logout all devices, ban sessions quickly)
 - device tracking (session per device)
 - rotation (refresh token rotation)
 - audit (per-session last_seen)
 
 ---
 
 ## Hashing vs Encryption (Clear Rules)
 
 ### Hashing is one-way (verification)
 Use hashing for:
 - passwords
 - API keys (store only a hash)
 - refresh tokens (store only a hash)
 
 ### Encryption is reversible (data recovery)
 Use encryption for:
 - sensitive PII fields (when required)
 - private notes
 - bank details (when required)
 
 ### Do not mix concepts
 - never encrypt passwords (passwords must be hashed)
 - never store API keys raw (store only a hash)
 
 ---
 
 ## Stack Picks (Components)
 
 ### Host VPS
 - AlmaLinux (SELinux on)
 - firewalld + fail2ban
 - automatic security updates

### Containers
- Podman (rootless)
- Alpine base images

### Runtime
- Bun (preferred)

Notes:
- Some Node ecosystem libraries still have runtime gaps under Bun.
- For queues specifically, validate compatibility before committing.

### Databases & storage
- Postgres (primary)
- TimescaleDB (add when time-series is real)
- TigerBeetle (ledger-grade financial invariants)
- Redis (cache + sessions + streams)
- Qdrant (vector DB)
- MinIO/S3 (objects + backups)

### AI
- DeepSeek (cloud)
- Ollama (local)
- HuggingFace
- LangChain (orchestration)

### CI/CD + quality
- GitHub Actions
- Husky + Commitlint
- Vitest
- k6
- Security scanning: Trivy / Snyk

### Monitoring
- Prometheus + Grafana
- Loki
- Uptime Kuma

### Mobile/Desktop
- Flutter / FlutterFlow
- Tauri

### Web3
- Solidity (EVM)
- Rust (Solana/Near/Substrate)
- Move/Sui optional

 ---
 
 ## Performance Standard (Web)

### Caching layers (canonical)
- CDN cache (static assets, optionally HTML)
- FE SSR cache (route-level caching where safe)
- BE cache (Redis)
- DB cache (indexes + query optimization)

### Rate limiting + DDoS posture
- Apply rate limiting at:
  - CDN/WAF (if available)
  - gateway
  - backend
- Prefer fail-closed for sensitive endpoints
- Use circuit breakers and bulkheads

### WAF baseline (edge/gateway)
- If you have Cloudflare/Fastly: enable WAF + bot controls before scaling backend.
- If self-hosted only: apply gateway rules + IP allow/deny + strict rate limits for auth/payment endpoints.

### Data access rules
- Index before scaling DB hardware
- Avoid N+1 queries
- Use connection pooling

---

## Observability Standard
- Logs, metrics, and (when microservices grow) tracing
- Every request carries:
  - `request_id`
  - `session_id`
  - `user_id` (if authenticated)

### Tracing (when microservices grow)
- Use OpenTelemetry for services; propagate trace context across gateway and services.
- Keep tracing payloads clean (no secrets/PII); sample intelligently to control cost.
- Store traces in a system that matches the Grafana stack (Tempo/Jaeger-compatible).

### Logging convention (JS/TS)
- All `console.log` must include: `🚀` (easier filtering)

---

## Monorepo vs Microservices (How I Decide)

### Default for freelancer MVP
- **Monorepo**
- Modular monolith (bounded contexts inside one deploy)

Why:
- faster delivery
- fewer operational failure modes
- simpler debugging

### When to split into microservices
Split only when at least one is true:
- independent scaling requirements
- strict isolation/security boundaries
- independent deployment cycles needed
- different languages/runtime are justified

### Microservices rules
- strict API contracts
- service-to-service auth (mTLS or signed tokens)
- shared libraries are versioned (avoid “shared folder spaghetti”)

---

## API Protocol Standard
- FE → BE: HTTP/JSON by default (simple, stable)
- Service → Service: gRPC is allowed internally
- Browser gRPC requires gRPC-Web / proxying, so it’s not the default

---

## AI Standard (Client Safety)
- classify what data is allowed to leave your infra
- redact PII before sending to cloud LLMs when required
- keep an offline path (Ollama) for sensitive clients

---

## Web3 Standard (Security-first)
- Never store user private keys
- Prefer “Sign-In with Ethereum” style flows (signature verification)
- Treat smart contracts as production-critical:
  - audits for serious projects
  - strict upgrade strategy (or no upgrade)
  - key management is the real risk

---

## Environment Policy

### Environment routing rules (hard guardrails)
- dev/uat must never use prod DB or prod secrets.
- prod must not run with dev keys.
- all sensitive operations (encrypt/decrypt, key rotation) are prod-only and must go through the crypto/secrets service.
- enforce this in 3 layers:
  - config: separate env files/vars per env (no shared .env)
  - code: startup checks that block “prod DB URL” in non-prod
  - network: prod DB + crypto service only reachable from prod private network

### dev
- fastest iteration
- can use mock crypto or local keys

### uat
- production-like
- stricter logging + monitoring

### prod
- crypto/secrets service enabled
- strict firewall rules
- backups + restore drills
- least privilege everywhere

---

## Links / Projects / Demos (Reference)
 
### Links
- **Portfolio**: https://hunterho07.github.io/Portfolio_1/
- **GitHub (personal)**: https://github.com/HunterHo07
- **GitHub (org)**: https://github.com/TrillionUnicorn
- **Website repo**: https://github.com/TrillionUnicorn/Website
 
### Featured Projects / Demos (Selected)
- **CoreDeskAiCMS** — Plug-and-play admin dashboard for any REST API (cron fetch + export). https://i6-d1-coredeskaicms.vercel.app/
- **ReportU** — Cross-border offense reporting platform (concept + demo). https://i1-d1-report-u.vercel.app/
- **NameCardAI** — AR-enhanced digital business cards (WebXR/Three.js concept). https://i2-d1-namecardai.vercel.app/
- **MessageYou** — Visual-first civic platform (concept + demo). https://i4-d1-messageyouai.vercel.app/
- **IOTA Recycling MVP (Hackathon)** — Move smart contracts + IOTA Identity + React. https://github.com/HunterHo07/h1-iota-recyclling
- **3Wallet** — Multi-chain wallet concept (security-first). https://github.com/TrillionUnicorn/3Wallet
- **OpenChance** — Global problem-solving hub concept. https://github.com/TrillionUnicorn/OpenChance
 
### More Showcase / UI Demos
- https://hunterho07.github.io/d21-i3-BestzDeal
- https://hunterho07.github.io/d4-fe-Travel
- https://d27-i7-sim-work.vercel.app/
- https://d34-i7-sim-work.vercel.app/
 
### Idea Demos (Multi-versions)
- **ReportU**: https://i1-d1-report-u.vercel.app/ | https://i1-d2-reportu.vercel.app/
- **NameCardAI**: https://i2-d1-namecardai.vercel.app/ | https://i2-d2-namecardai.vercel.app/
- **BestzDealAi**: https://i3-d1-bestzdealai.vercel.app/ | https://i3-d2-bestzdealai.vercel.app/ | https://i3-d3-bestzdealai.vercel.app/
- **MessageYou**: https://i4-d1-messageyouai.vercel.app/ | https://i4-d2-messageyouai.vercel.app/
- **WarrantyAi**: https://i5-d2-warrantyai.vercel.app/
 
### Resume
- `RESUME_2026.txt` (single master resume text -> paste to Docs -> export PDF)
 
### 2026 Interview Focus (Checklist)
- **Security**: session-based auth, revocation, key rotation, least privilege, OWASP.
- **Scale**: caching layers, queue/backpressure, DB indexing, API gateway rate limiting.
- **Observability**: logs + metrics baseline, tracing when microservices grow.
- **AI**: safe data policies (PII redaction), offline path (Ollama), cost/rate controls.
- **Delivery**: PR review discipline, CI/CD, testing strategy, release readiness.
 
### Workspace Overview (this repo)
This workspace contains multiple apps/services used across different products and client projects.
 
Top-level folders you will see here:
- `pitech-app1/`: Next.js web app
- `pitech-admin-5/`: Next.js admin/backoffice
- `pitech-api2/`, `pitech-api3/`, `pitech-api5/`: Node/Feathers APIs
