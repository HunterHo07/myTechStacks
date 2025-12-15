# Upgrade 4 — Split Services (Only When Needed)

## Trigger
Split only when at least one is true:
- independent scaling requirements
- strict isolation/security boundaries
- independent deployment cycles needed
- different language/runtime is justified

## Rules
- strict API contracts
- service-to-service auth (mTLS or signed tokens)
- propagate request_id across services
- avoid "shared folder" coupling; version shared libs properly
