---
name: infra-infra-audit
description: Every-4h health check and weekly deep audit of infrastructure — services, database, DNS, SSL, and resource utilization.
---

# infra-audit — Infrastructure Health Audit

## When to Use
DevOps uses this when running the every-4h health check or during weekly deep audit.

## Inputs
- Service status via the `infra.deploy` adapter — see `.claude/hive/adapters/infra-deploy.md`
- Database status via the `infra.db` adapter — see `.claude/hive/adapters/infra-db.md`
- DNS and SSL configuration
- Resource utilization metrics (CPU, memory, disk) via the `observe.metrics` adapter

## Procedure

1. Check service status via the `infra.deploy` adapter (all services healthy)
2. Check database status via the `infra.db` adapter (database accessible, auth functional)
3. Verify DNS resolution for all configured domains
4. Check SSL certificate expiry dates — warn if < 30 days
5. Check resource utilization (CPU, memory, disk) for each service
6. Check for unused services or resources (cost waste)
7. Post report to #ops:

```markdown
---
agent: devops
type: report
severity: {info | warning | critical}
tags: [infra-audit]
requires: {ack | action}
---

## Infrastructure Audit

### Services: {healthy | degraded | down}
### Database: {healthy | degraded | down}
### DNS: {all resolving | issues found}
### SSL Expiry: {nearest expiry date and domain}
### Resource Utilization:
- CPU: {%}
- Memory: {%}
- Disk: {%}
### Unused Resources: {list or "none"}
### Status: {healthy | attention needed | critical}
```

## Output Format
Infrastructure audit report posted to #ops (see template above).

## Rules
- SSL cert expiry < 14 days is critical — escalate to CTO
- SSL cert expiry < 30 days is a warning
- Resource utilization > 80% is a warning
- Resource utilization > 90% is critical — recommend scaling action
- Weekly deep audit includes all checks. 4h check focuses on service status and resource utilization
