# Preflight Verification: Database Migration Deployment

## Goal

Perform a confidence check on a major database migration that restructures user tables affecting 10M+ users with no rollback script.

## Current Context

- **Operation**: Database migration — restructuring user tables
- **Scale**: 10M+ user records affected
- **Rollback capability**: None (no rollback script exists)
- **Risk tier**: HIGH — this involves data, large scale, and is destructive/irreversible

## Verification

### Assumptions

1. The migration has been tested in a staging or pre-production environment that mirrors production data volume and schema.
2. There is a backup strategy in place (even if no rollback script exists, full database backups should be available).
3. The application layer has been updated to work with the new schema, or a feature flag / dual-write strategy is in place.
4. Downtime window has been communicated to stakeholders and users.
5. The team has estimated migration duration based on realistic data volumes.

### Risks

| Risk | Severity | Likelihood | Mitigation |
|------|----------|------------|------------|
| **No rollback script** — if migration fails mid-way, data could be left in an inconsistent state | CRITICAL | Medium | Create a rollback script before deploying. At minimum, take a full backup immediately before execution. |
| **10M+ rows** — migration could take much longer than expected, exceeding maintenance window | HIGH | Medium | Test on production-scale data in staging. Benchmark timing. Consider batched migration. |
| **Schema mismatch** — application code may not handle transitional states during migration | HIGH | Medium | Deploy with feature flags. Test application against both old and new schemas. |
| **Data loss or corruption** — restructuring tables could lose data if transformations are incorrect | CRITICAL | Low-Medium | Validate row counts and checksums before/after. Run migration on a copy first. |
| **Cascading failures** — foreign key constraints, triggers, or dependent services may break | HIGH | Medium | Map all dependencies. Test with referential integrity checks. |
| **Locking and performance** — large ALTER TABLE or data moves can lock tables for extended periods | HIGH | High | Use online DDL tools (e.g., pt-online-schema-change, gh-ost) if applicable. |

### Pre-Deployment Checks

- [ ] **Full database backup** completed and verified (restore tested)
- [ ] **Rollback script** written and tested — deploying without one on 10M+ rows is unacceptable
- [ ] **Staging test** completed on production-scale data with timing benchmarks
- [ ] **Row count verification** — expected counts documented for before/after comparison
- [ ] **Data integrity checksums** prepared for critical columns
- [ ] **Dependent services** identified and notified (APIs, caches, queues, read replicas)
- [ ] **Application compatibility** verified — app works with new schema OR dual-schema strategy in place
- [ ] **Monitoring & alerting** configured for migration progress, error rates, and performance
- [ ] **Maintenance window** scheduled and communicated
- [ ] **Runbook** prepared with step-by-step execution plan, success criteria, and abort criteria
- [ ] **DBA review** completed on migration SQL
- [ ] **Point-of-no-return** identified — at what step can you no longer abort safely?

### Confidence Level

**[CONFIDENCE: UNCERTAIN]** — Cannot approve this deployment as described.

The absence of a rollback script for an irreversible operation affecting 10M+ users is a **blocking concern**. This violates fundamental deployment safety principles:

1. **No rollback = no safety net.** If the migration corrupts data, you have no recovery path other than restoring from backup, which could mean significant data loss for any writes that occurred after the backup.
2. **Scale amplifies risk.** At 10M+ rows, even small bugs in transformation logic can cause widespread damage.
3. **Irreversibility demands extra caution.** The higher the stakes, the more safeguards are required — not fewer.

**Recommendation: DO NOT DEPLOY** until:
1. A rollback script is written and tested
2. A full backup is taken and restore is verified
3. The migration is benchmarked on production-scale staging data
4. A runbook with abort criteria is prepared

## Lessons Learned

- Never deploy a destructive migration without a tested rollback path — backups are a last resort, not a strategy.
- Large-scale migrations should be tested with production-volume data, not just schema correctness.
- The confidence framework applies: if you are UNCERTAIN about a deployment, you stop and address the gaps before proceeding.
- "No rollback script" is a red flag that should block any deployment review, regardless of how well-tested the forward path appears.

## Persistable Updates

- **PROGRESS.md**: Record that a deployment preflight check was conducted and the deployment was flagged as NOT READY due to missing rollback script and insufficient safety measures.
- **LESSONS.md**: Log the rule — "Never approve a large-scale destructive migration without a tested rollback script and verified backup restore."

---

```json
{
  "artifact": "preflight-verification",
  "task": "database-migration-confidence-check",
  "timestamp": "2026-03-05T00:00:00Z",
  "goal": "Confidence check on major database migration affecting 10M+ users with no rollback script",
  "context": {
    "operation": "database table restructuring",
    "scale": "10M+ users",
    "rollback_available": false,
    "risk_tier": "HIGH"
  },
  "verification": {
    "confidence_level": "UNCERTAIN",
    "recommendation": "DO_NOT_DEPLOY",
    "blocking_issues": [
      "No rollback script exists for an irreversible operation",
      "No evidence of production-scale staging test",
      "No documented abort criteria or runbook"
    ],
    "assumptions": [
      "Migration tested in staging (unverified)",
      "Backup strategy exists (unverified)",
      "Application layer updated for new schema (unverified)",
      "Downtime window communicated (unverified)",
      "Migration duration estimated (unverified)"
    ],
    "risks": [
      {
        "risk": "No rollback script — inconsistent state on failure",
        "severity": "CRITICAL",
        "likelihood": "Medium"
      },
      {
        "risk": "Migration exceeds maintenance window at scale",
        "severity": "HIGH",
        "likelihood": "Medium"
      },
      {
        "risk": "Data loss or corruption during restructuring",
        "severity": "CRITICAL",
        "likelihood": "Low-Medium"
      },
      {
        "risk": "Table locking causing extended downtime",
        "severity": "HIGH",
        "likelihood": "High"
      },
      {
        "risk": "Cascading failures in dependent services",
        "severity": "HIGH",
        "likelihood": "Medium"
      }
    ],
    "required_before_deploy": [
      "Write and test rollback script",
      "Take full backup and verify restore",
      "Benchmark migration on production-scale staging data",
      "Prepare runbook with abort criteria",
      "DBA review of migration SQL",
      "Verify application compatibility with new schema",
      "Configure monitoring and alerting"
    ]
  },
  "lessons": [
    "Never deploy destructive migrations without tested rollback path",
    "Large-scale migrations require production-volume testing",
    "UNCERTAIN confidence level = stop and address gaps before proceeding"
  ],
  "persistable_updates": {
    "progress": "Deployment preflight check conducted — flagged as NOT READY",
    "lessons": "Never approve large-scale destructive migration without tested rollback script and verified backup restore"
  }
}
```
