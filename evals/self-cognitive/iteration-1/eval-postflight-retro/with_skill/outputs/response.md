# Self-Cognitive — Postflight Retro

## Goal
Conduct a postmortem on the 2-hour production outage caused by auth service cache failure during a product launch traffic spike, and capture actionable lessons learned.

## Current context
- Production outage lasted 2 hours during a product launch.
- The auth service could not handle the traffic spike.
- The cache layer failed under load.
- Auth tokens expired prematurely as a result of the cache failure.
- The incident is resolved; this is a retrospective analysis.

## Verification
### Assumptions
- The cache layer is the primary mechanism for serving auth tokens (i.e., without cache, every token validation hits the backing store directly).
- Token expiration was premature because cached TTLs were lost when the cache failed, forcing re-authentication or token regeneration under degraded conditions.
- The traffic spike was foreseeable given a planned product launch.
- There was no auto-scaling or circuit-breaker mechanism that activated to protect the auth service.
- The auth service is a shared dependency — other services were likely affected downstream.

### Risks and failure modes
- **Recurrence on next launch**: Without fixes, any future traffic spike will trigger the same cascade.
- **Silent cache degradation**: Cache may fail partially (elevated latency, partial evictions) before full failure, and this may go undetected without specific monitoring.
- **Token TTL mismatch**: If cache TTL and token TTL are configured independently, a cache restart could serve stale or missing tokens, causing valid sessions to drop.
- **Thundering herd on recovery**: When the cache comes back online, all clients retry simultaneously, potentially causing a second outage.
- **Single point of failure**: If the cache layer has no replication or failover, it is an SPOF in the auth path.
- **Downstream blast radius unknown**: Other services depending on auth may have experienced cascading failures not yet fully cataloged.

### Checks
- [ ] Review cache infrastructure for replication/clustering — is there a failover node?
- [ ] Verify whether cache eviction policies contributed (e.g., LRU evictions under memory pressure before full failure).
- [ ] Check auth service behavior when cache is unavailable — does it fall back to direct DB/token-store reads, or does it fail open/closed?
- [ ] Confirm whether auto-scaling was configured and whether it triggered (or why it didn't).
- [ ] Audit token TTL configuration — are TTLs set in cache independently from the token issuer?
- [ ] Review monitoring and alerting — was there an alert before the outage, or only after users reported issues?
- [ ] Load-test the auth service at 2x–5x expected launch traffic with cache disabled to measure degraded-mode capacity.
- [ ] Check for thundering-herd protection (e.g., request coalescing, jittered retries, staggered reconnects).

### Confidence
- Level: medium
- Why: The user provided clear facts about the failure chain (traffic spike → cache failure → premature token expiration → outage). However, root cause details are incomplete — we don't know *why* the cache failed (OOM, connection limits, misconfiguration, network partition) or the exact auth service fallback behavior.
- To raise confidence:
  - Obtain cache service logs and metrics from the outage window (memory, connections, eviction rate, error rate).
  - Obtain auth service logs showing fallback behavior when cache was unavailable.
  - Confirm the timeline: did cache fail first, or did the auth service overload first and saturate cache connections?
  - Review the infrastructure topology (cache cluster size, auth service replica count, load balancer config).

## Lessons learned
### Technical
- **Cache is not optional infrastructure for auth** — if the auth path depends on cache for performance, the cache must be treated as critical infrastructure with HA (replication, failover, persistence).
- **Graceful degradation is required** — the auth service should have a fallback mode (e.g., direct token-store reads with rate limiting) when cache is unavailable, rather than failing or issuing premature expirations.
- **Token TTL and cache TTL must be coupled** — if cache entries expire or are evicted, the system should not force token re-issuance; it should re-validate from the source of truth.
- **Thundering herd protection is essential** — request coalescing, jittered backoff, and staggered cache warming prevent recovery from causing a second outage.
- **Load testing must include failure scenarios** — testing at expected peak load with cache disabled would have revealed this failure mode before launch.

### Process
- **Product launches need capacity planning** — a launch-day traffic estimate should trigger a capacity review of all critical-path services, including auth and its dependencies.
- **Pre-launch runbooks should exist** — a checklist covering: scale-up auth replicas, warm cache, verify monitoring thresholds, designate incident commander.
- **Incident detection was too slow** — 2 hours suggests monitoring/alerting gaps. Alert on cache error rate and auth latency p99 with aggressive thresholds during launch windows.
- **Post-incident review should produce a timeline** — minute-by-minute reconstruction of what happened, who was notified, and what actions were taken.
- **Define an SLO for auth availability** — e.g., 99.99% uptime, with an error budget that triggers automatic review when breached.

### Prompting and coordination
- **Gather the full incident timeline before analysis** — ask for logs, metrics, and the sequence of events before proposing fixes.
- **Separate "what happened" from "what to fix"** — the postmortem should clearly delineate the factual timeline from the remediation plan.
- **Assign owners to each action item** — lessons without owners become shelf-ware.

## Persistable updates
### Memory summary
- User preferences:
  - Wants structured postmortems with actionable lessons.
  - Values separation of facts from assumptions.
- Project facts:
  - Auth service depends on a cache layer for token serving.
  - Cache failure caused a 2-hour production outage during a product launch.
  - Auth tokens expired prematurely when cache was unavailable.
  - The system lacked sufficient resilience for launch-day traffic spikes.
- Open questions:
  - What cache technology is used (Redis, Memcached, other)?
  - What caused the cache to fail (OOM, connection exhaustion, network, misconfiguration)?
  - Does the auth service have a fallback mode when cache is down?
  - Was auto-scaling configured for the auth service?
  - What is the current monitoring/alerting setup for cache and auth?
  - How many downstream services were affected?

### Skill update proposal
No target skill exists for incident postmortems. Propose a new skill:

- **Skill name**: `incident-postmortem`
- **Trigger**: User asks for postmortem, incident review, outage analysis, or root cause analysis.
- **Frontmatter**:
  ```yaml
  name: incident-postmortem
  description: Use when conducting a postmortem, incident review, or root cause analysis after a production incident.
  ```
- **Sections**:
  1. Incident summary (what, when, duration, impact)
  2. Timeline reconstruction (minute-by-minute facts)
  3. Root cause analysis (5 Whys or fishbone)
  4. Contributing factors (process, tooling, monitoring gaps)
  5. Action items (owner, priority, deadline)
  6. Recurrence prevention (architectural changes, runbooks, alerts)

### Artifacts JSON
```json
{
  "self_cognitive_meta": {
    "goal": "Conduct a postmortem on the 2-hour production outage caused by auth service cache failure during a product launch traffic spike, and capture actionable lessons learned.",
    "context_facts": [
      "Production outage lasted 2 hours during a product launch.",
      "The auth service could not handle the traffic spike.",
      "The cache layer failed under load.",
      "Auth tokens expired prematurely as a result of the cache failure.",
      "The incident is resolved; this is a retrospective analysis."
    ],
    "verification": {
      "assumptions": [
        "The cache layer is the primary mechanism for serving auth tokens.",
        "Token expiration was premature because cached TTLs were lost when the cache failed.",
        "The traffic spike was foreseeable given a planned product launch.",
        "There was no auto-scaling or circuit-breaker mechanism that activated.",
        "The auth service is a shared dependency affecting downstream services."
      ],
      "risks": [
        "Recurrence on next traffic spike without remediation.",
        "Silent cache degradation may go undetected without specific monitoring.",
        "Token TTL mismatch between cache and token issuer.",
        "Thundering herd on cache recovery causing secondary outage.",
        "Cache is a single point of failure in the auth path.",
        "Downstream blast radius not fully cataloged."
      ],
      "checks": [
        "Review cache infrastructure for replication and failover.",
        "Verify cache eviction policies under memory pressure.",
        "Check auth service fallback behavior when cache is unavailable.",
        "Confirm auto-scaling configuration and whether it triggered.",
        "Audit token TTL configuration coupling between cache and issuer.",
        "Review monitoring and alerting coverage for cache and auth.",
        "Load-test auth service at 2x-5x expected traffic with cache disabled.",
        "Check for thundering-herd protection mechanisms."
      ],
      "confidence": {
        "level": "medium",
        "why": "Clear failure chain provided (traffic spike → cache failure → premature token expiration → outage), but root cause details and infrastructure specifics are incomplete.",
        "to_raise": [
          "Obtain cache service logs and metrics from the outage window.",
          "Obtain auth service logs showing fallback behavior.",
          "Confirm the failure sequence timeline with metrics.",
          "Review infrastructure topology (cache cluster size, auth replicas, LB config)."
        ]
      }
    },
    "lessons_learned": {
      "technical": [
        "Cache must be treated as critical HA infrastructure when in the auth path.",
        "Auth service needs graceful degradation when cache is unavailable.",
        "Token TTL and cache TTL must be coupled to prevent premature expiration.",
        "Thundering herd protection is essential for cache recovery scenarios.",
        "Load testing must include failure scenarios (cache disabled, degraded mode)."
      ],
      "process": [
        "Product launches need explicit capacity planning for all critical-path services.",
        "Pre-launch runbooks should cover scale-up, cache warming, monitoring, and incident roles.",
        "Incident detection took too long — alerting thresholds need to be more aggressive during launch windows.",
        "Post-incident review should produce a minute-by-minute timeline.",
        "Define an SLO for auth availability with automatic review triggers."
      ],
      "prompting_and_coordination": [
        "Gather the full incident timeline before proposing fixes.",
        "Separate factual timeline from remediation plan in the postmortem.",
        "Assign owners to each action item to prevent lessons from becoming shelf-ware."
      ]
    },
    "memory_summary": {
      "user_preferences": [
        "Wants structured postmortems with actionable lessons.",
        "Values separation of facts from assumptions."
      ],
      "project_facts": [
        "Auth service depends on a cache layer for token serving.",
        "Cache failure caused a 2-hour production outage during a product launch.",
        "Auth tokens expired prematurely when cache was unavailable.",
        "System lacked sufficient resilience for launch-day traffic spikes."
      ],
      "open_questions": [
        "What cache technology is used (Redis, Memcached, other)?",
        "What caused the cache to fail (OOM, connection exhaustion, network, misconfiguration)?",
        "Does the auth service have a fallback mode when cache is down?",
        "Was auto-scaling configured for the auth service?",
        "What is the current monitoring/alerting setup for cache and auth?",
        "How many downstream services were affected?"
      ]
    },
    "skill_updates": {
      "target_skill_name": null,
      "proposed_patch": null,
      "new_skill_drafts": [
        "incident-postmortem"
      ]
    }
  }
}
```
