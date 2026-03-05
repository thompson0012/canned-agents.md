# Postmortem: Auth Service Outage During Product Launch

**Date**: 2026-03-05
**Duration**: ~2 hours
**Severity**: Critical (P1)
**Author**: AI Agent (postmortem facilitation)
**Status**: Draft — pending team review

---

## 1. Executive Summary

During the product launch, a traffic spike overwhelmed the auth service. The cache layer (likely Redis or similar) failed under load, causing auth tokens to expire prematurely. This resulted in a ~2-hour production outage where users could not authenticate, effectively blocking access to the product during a high-visibility launch event.

---

## 2. What Happened (Timeline)

> **Note**: Fill in exact timestamps from logs/monitoring. Below is a reconstructed sequence.

| Time (approx.) | Event |
|----------------|-------|
| T+0 | Product launch announced; traffic begins ramping |
| T+10min | Auth service request rate exceeds baseline by ~Nx |
| T+15min | Cache layer begins showing elevated error rates / evictions |
| T+20min | Cache layer fails (crash, connection exhaustion, or OOM) |
| T+20min | Auth service falls back to direct DB queries; DB saturates |
| T+25min | Token validation failures spike; users see auth errors |
| T+25min | **Outage begins** — users cannot log in or access product |
| T+~2h | Incident resolved (cache restored / service scaled / hotfix) |
| T+~2h | **Outage ends** — service restored to normal |

---

## 3. Root Cause Analysis

### Primary Root Cause
**The cache layer was not provisioned to handle launch-day traffic volumes.** When the cache failed, the auth service had no graceful degradation path and fell back to a pattern that overwhelmed downstream dependencies (likely the database).

### Contributing Factors

| Factor | Detail |
|--------|--------|
| **Insufficient cache capacity** | Cache instance(s) were sized for steady-state traffic, not launch-spike traffic. No auto-scaling or overflow mechanism was in place. |
| **Premature token expiration** | When the cache went down, cached tokens were lost. Short TTLs on tokens meant they couldn't survive even a brief cache outage. Token validation then required full re-auth against the DB. |
| **No circuit breaker / fallback** | The auth service had no circuit breaker to protect the database when cache was unavailable. No fallback strategy (e.g., serving stale tokens, extending TTL during degradation). |
| **Lack of load testing at launch scale** | The system was not load-tested at expected launch-day traffic volumes. Capacity planning did not account for the spike pattern of a product launch. |
| **No pre-scaling for known event** | The launch was a planned event, but infrastructure was not pre-scaled. No runbook existed for launch-day capacity preparation. |

### 5 Whys

1. **Why did users lose access?** — Auth tokens expired and couldn't be refreshed.
2. **Why couldn't tokens be refreshed?** — The cache layer holding token data failed.
3. **Why did the cache fail?** — It wasn't sized for the traffic spike and had no overflow protection.
4. **Why wasn't it sized correctly?** — No load testing was done at launch-scale volumes.
5. **Why was there no load testing?** — No established process for pre-launch capacity validation.

---

## 4. Impact

### User Impact
- **Duration**: ~2 hours of complete or partial auth failure
- **Scope**: All users attempting to log in or perform authenticated actions
- **Experience**: Login failures, session drops, inability to access the newly launched product

### Business Impact
- **Revenue**: Lost conversions during the launch window (highest-intent traffic)
- **Reputation**: Negative first impression for new users arriving for launch
- **Trust**: Existing users experienced unexpected session termination
- **Support**: Likely spike in support tickets and social media complaints

### Technical Impact
- **Database**: Likely experienced elevated load / connection exhaustion during the incident
- **Downstream services**: Any service depending on auth was affected (cascading failure)
- **Data**: Potential for inconsistent session state if recovery was partial

---

## 5. Lessons Learned

### 5.1 Technical Lessons

| # | Lesson | Detail |
|---|--------|--------|
| T1 | **Cache is a critical dependency, not just an optimization** | When cache failure causes an outage, the cache is in the critical path. It must be treated with the same rigor as the primary database — HA, monitoring, failover. |
| T2 | **Token TTL strategy matters under failure conditions** | Short TTLs are good for security but catastrophic during cache outages. Need a strategy for graceful TTL extension during degradation. |
| T3 | **Circuit breakers are non-negotiable for critical paths** | The auth service needed a circuit breaker to prevent cascading failure to the database when cache was unavailable. |
| T4 | **Graceful degradation > hard failure** | The system should have a degradation mode (e.g., serve slightly stale tokens, allow extended sessions) rather than complete auth failure. |
| T5 | **Cache resilience needs its own design** | Redis Cluster/Sentinel, connection pooling, retry with backoff, local in-process cache as L1 — these patterns should be standard for critical cache dependencies. |

### 5.2 Process Lessons

| # | Lesson | Detail |
|---|--------|--------|
| P1 | **Planned events need capacity planning rituals** | A product launch is a known event. There should be a checklist that includes load testing, pre-scaling, and go/no-go criteria. |
| P2 | **Load testing must simulate realistic traffic patterns** | Steady-state load tests miss spike patterns. Launch-day testing should simulate the ramp-up curve and sustained peak. |
| P3 | **Runbooks for known scenarios prevent scrambling** | A "launch day" runbook with pre-scaling steps, monitoring dashboards, and rollback procedures would have reduced response time. |
| P4 | **Incident detection was likely too slow** | If the outage lasted 2 hours, detection and response may have been delayed. Alerting thresholds need review. |

### 5.3 Coordination Lessons

| # | Lesson | Detail |
|---|--------|--------|
| C1 | **Cross-team launch readiness review** | Product, engineering, SRE/infra, and support should have a joint launch readiness meeting with explicit sign-off. |
| C2 | **War room for high-risk launches** | A dedicated incident channel/war room during launch windows enables faster detection and coordination. |
| C3 | **Communication plan for outages during launches** | Pre-drafted user-facing messaging for auth issues during launches would reduce reputational damage. |
| C4 | **Post-launch monitoring ownership** | Clear ownership of who is watching dashboards during the launch window and empowered to trigger scaling. |

---

## 6. Action Items

### Immediate (This Week)

| # | Action | Owner | Due |
|---|--------|-------|-----|
| A1 | Conduct detailed timeline reconstruction from logs and monitoring data | Incident Lead | +2 days |
| A2 | Review and increase cache instance sizing for current traffic + 3x headroom | Infra/SRE | +3 days |
| A3 | Implement cache health alerting with aggressive thresholds (connection count, memory, eviction rate) | SRE | +5 days |
| A4 | Add circuit breaker to auth service for cache dependency | Auth Team | +5 days |

### Short-Term (Next 2 Weeks)

| # | Action | Owner | Due |
|---|--------|-------|-----|
| A5 | Implement multi-tier caching (L1 in-process + L2 distributed) for auth tokens | Auth Team | +2 weeks |
| A6 | Design and implement graceful degradation mode for auth (extended TTL during cache failure) | Auth Team | +2 weeks |
| A7 | Create "launch day" runbook with pre-scaling checklist and go/no-go criteria | SRE + Product | +2 weeks |
| A8 | Set up load testing pipeline that can simulate launch-day traffic spikes | QA/Perf Team | +2 weeks |

### Medium-Term (Next 30 Days)

| # | Action | Owner | Due |
|---|--------|-------|-----|
| A9 | Deploy Redis Cluster or Sentinel for cache HA (if not already in place) | Infra | +30 days |
| A10 | Establish capacity planning process for all planned high-traffic events | SRE + Product | +30 days |
| A11 | Conduct chaos engineering exercise: simulate cache failure in staging and validate graceful degradation | SRE + Auth Team | +30 days |
| A12 | Review token TTL strategy holistically — balance security vs. resilience | Security + Auth Team | +30 days |

---

## 7. Detection & Response Review

| Metric | Value | Target |
|--------|-------|--------|
| Time to detect | ___ min | < 5 min |
| Time to acknowledge | ___ min | < 10 min |
| Time to mitigate | ~120 min | < 30 min |
| Time to resolve | ~120 min | < 60 min |

> **Note**: Fill in actual values. The 2-hour duration suggests significant gaps in detection and/or mitigation speed.

---

## 8. Recurring Patterns Check

Ask the team:
- Has the cache layer caused issues before? If so, this is a recurring pattern that needs systemic investment, not point fixes.
- Have we had other "planned event" outages? If so, the capacity planning process gap is the real root cause.
- Is the auth service a known bottleneck? If so, consider dedicated investment in auth resilience (dedicated team sprint, architecture review).

---

## 9. Follow-Up Schedule

| Date | Activity |
|------|----------|
| +1 week | Review immediate action items completion |
| +2 weeks | Review short-term action items; run first load test |
| +30 days | Review all action items; validate chaos test results |
| +60 days | Retrospective on whether fixes were effective |

---

## 10. Sign-Off

| Role | Name | Approved |
|------|------|----------|
| Incident Lead | | ☐ |
| Auth Team Lead | | ☐ |
| SRE/Infra Lead | | ☐ |
| Engineering Manager | | ☐ |
| Product Manager | | ☐ |

---

*This postmortem is blameless. The goal is to improve systems and processes, not assign fault to individuals.*
