# Upgrade 2 — Dedicated Database + Backups/Restore Drills

## Trigger
Do this when:
- one project becomes critical
- DB size/IO grows
- you need stronger isolation

## Changes
- move project to dedicated DB (or dedicated DB host)
- enforce least privilege:
  - separate DB users per service
  - read/write separation when needed

## Backups
- automate backups
- test restore (restore drills)
- document RPO/RTO targets
