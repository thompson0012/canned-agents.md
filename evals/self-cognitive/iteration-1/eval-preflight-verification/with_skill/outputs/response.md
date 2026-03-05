## Goal
Verify whether the major database migration that restructures user tables is safe to deploy, given it affects 10M+ users and has no rollback script.

## Current context
- Migration restructures user tables (schema-level change, not just data).
- Affects 10M+ user records.
- No rollback script exists.
- User is at the pre-deployment decision point (preflight).

## Verification
### Assumptions
- The migration has been tested in a non-production environment (staging or equivalent) — **unconfirmed, must validate**.
- Application code compatible with the new schema is already deployed or will be deployed atomically with the migration — **unconfirmed**.
- There is a maintenance window or zero-downtime strategy in place — **unconfirmed**.
- Backups of the current database exist and have been verified as restorable — **unconfirmed**.
- The migration is idempotent or at least safe to re-run partially — **unconfirmed**.

### Risks and failure modes
- **No rollback path**: If the migration fails midway or introduces data corruption, there is no scripted way to reverse it. Recovery depends entirely on backups, which adds hours of downtime for 10M+ rows.
- **Partial migration failure**: A crash or timeout mid-migration could leave tables in an inconsistent state (e.g., some rows migrated, some not), breaking application queries.
- **Application incompatibility**: If the app expects the old schema during or after migration, queries will fail, causing user-facing outages.
- **Lock contention / downtime**: Restructuring large tables often requires table locks (ALTER TABLE, etc.), which can block reads/writes for extended periods on 10M+ row tables.
- **Data loss**: Without a tested rollback and without verified backups, any data corruption during migration is potentially unrecoverable.
- **Performance degradation**: New schema may have different index characteristics, causing slow queries post-migration until indexes are rebuilt/warmed.

### Checks
- [ ] **Backup verified**: Confirm a full backup exists AND has been test-restored to a separate environment successfully.
- [ ] **Staging dry-run**: Run the exact migration on a staging database with production-scale data (or a representative subset of 10M+ rows). Record execution time.
- [ ] **Rollback script created**: Write and test a rollback migration before proceeding. This is non-negotiable for a destructive schema change at this scale.
- [ ] **Application compatibility**: Confirm application code handles both old and new schemas during the transition window (dual-write or feature flag).
- [ ] **Lock strategy validated**: Confirm whether the migration uses online DDL (e.g., `pt-online-schema-change`, `gh-ost`, or native online ALTER) to avoid blocking reads/writes.
- [ ] **Monitoring in place**: Alerting on query errors, replication lag, lock wait timeouts, and migration progress.
- [ ] **Communication plan**: Stakeholders and on-call are aware of the migration window and potential impact.
- [ ] **Execution time estimate**: Migration time has been measured on production-scale data; the window is sufficient.

### Confidence
- Level: **low**
- Why: The absence of a rollback script for a destructive schema change affecting 10M+ users is a critical gap. No information was provided about backup verification, staging dry-runs, lock strategy, or application compatibility — all of which are essential for a safe deployment.
- To raise confidence:
  - Write and test a rollback migration script.
  - Perform a full dry-run on staging with production-scale data.
  - Verify backups are restorable (actually restore one and validate row counts / checksums).
  - Confirm the application handles the schema transition gracefully (dual-read or blue-green deploy).
  - Validate online DDL or maintenance window strategy to avoid extended table locks.

## Lessons learned
### Technical
- Irreversible schema migrations at scale require a tested rollback script as a hard prerequisite, not an afterthought.
- Table restructures on 10M+ row tables need online DDL tooling benchmarked against production-scale data volumes.

### Process
- Preflight confidence checks should be run before scheduling a deployment window, not at the last minute.
- "No rollback script" should be treated as a blocking finding, not a risk to accept.

### Prompting and coordination
- When requesting a confidence check, include: backup status, staging test results, rollback plan, and expected downtime. This enables a more precise assessment.

## Persistable updates
### Memory summary
- User preferences:
  - Requests structured confidence checks before major deployments.
- Project facts:
  - Database contains 10M+ user records.
  - User table restructure is planned (schema-level migration).
  - No rollback script currently exists.
- Open questions:
  - What database engine is in use (MySQL, PostgreSQL, etc.)? This affects online DDL options.
  - Is there a maintenance window or must this be zero-downtime?
  - Has a staging dry-run been performed?
  - Are backups verified as restorable?
  - What is the application deployment strategy during migration (blue-green, rolling, etc.)?

### Skill update proposal
No target skill exists for database migration preflight. Propose a new skill:
- **Name**: `database-migration-preflight`
- **Trigger**: User mentions database migration, schema change, or table restructure for large datasets.
- **Sections**: Backup verification checklist, rollback script requirement, online DDL strategy, staging dry-run protocol, application compatibility check, monitoring and alerting setup.

### Artifacts JSON
```json
{
  "self_cognitive_meta": {
    "goal": "Verify whether the major database migration that restructures user tables is safe to deploy, given it affects 10M+ users and has no rollback script.",
    "context_facts": [
      "Migration restructures user tables (schema-level change).",
      "Affects 10M+ user records.",
      "No rollback script exists.",
      "User is at the pre-deployment decision point."
    ],
    "verification": {
      "assumptions": [
        "The migration has been tested in a non-production environment — unconfirmed.",
        "Application code compatible with the new schema is deployed or will be deployed atomically — unconfirmed.",
        "A maintenance window or zero-downtime strategy is in place — unconfirmed.",
        "Backups exist and have been verified as restorable — unconfirmed.",
        "The migration is idempotent or safe to re-run partially — unconfirmed."
      ],
      "risks": [
        "No rollback path: recovery depends entirely on backups, adding hours of potential downtime.",
        "Partial migration failure could leave tables in an inconsistent state.",
        "Application incompatibility if old-schema queries run against new schema.",
        "Lock contention on 10M+ row tables can block reads/writes for extended periods.",
        "Data loss risk if backups are untested and migration corrupts data.",
        "Performance degradation from changed index characteristics post-migration."
      ],
      "checks": [
        "Verify backup exists AND has been test-restored successfully.",
        "Run exact migration on staging with production-scale data; record execution time.",
        "Write and test a rollback migration script before proceeding.",
        "Confirm application handles both old and new schemas during transition.",
        "Validate online DDL or lock strategy to avoid blocking production traffic.",
        "Confirm monitoring and alerting are in place for migration progress and errors.",
        "Ensure stakeholders and on-call are aware of migration window.",
        "Measure migration execution time on production-scale data."
      ],
      "confidence": {
        "level": "low",
        "why": "No rollback script for a destructive schema change on 10M+ users. No information provided on backup verification, staging dry-runs, lock strategy, or application compatibility.",
        "to_raise": [
          "Write and test a rollback migration script.",
          "Perform full dry-run on staging with production-scale data.",
          "Verify backups are restorable (restore and validate).",
          "Confirm application handles schema transition gracefully.",
          "Validate online DDL or maintenance window strategy."
        ]
      }
    },
    "lessons_learned": {
      "technical": [
        "Irreversible schema migrations at scale require a tested rollback script as a hard prerequisite.",
        "Table restructures on 10M+ rows need online DDL tooling benchmarked at production scale."
      ],
      "process": [
        "Preflight confidence checks should run before scheduling a deployment window.",
        "No rollback script should be treated as a blocking finding."
      ],
      "prompting_and_coordination": [
        "When requesting a confidence check, include: backup status, staging results, rollback plan, and expected downtime."
      ]
    },
    "memory_summary": {
      "user_preferences": [
        "Requests structured confidence checks before major deployments."
      ],
      "project_facts": [
        "Database contains 10M+ user records.",
        "User table restructure is planned (schema-level migration).",
        "No rollback script currently exists."
      ],
      "open_questions": [
        "What database engine is in use?",
        "Is there a maintenance window or must this be zero-downtime?",
        "Has a staging dry-run been performed?",
        "Are backups verified as restorable?",
        "What is the application deployment strategy during migration?"
      ]
    },
    "skill_updates": {
      "target_skill_name": null,
      "proposed_patch": null,
      "new_skill_drafts": [
        "database-migration-preflight"
      ]
    }
  }
}
```
