# Architecture Decision: Microservices vs. Monolith

## Recommendation: Start with a Modular Monolith

**TL;DR** — Given your constraints (5 engineers, 10K initial users, 3-month launch window), a **modular monolith** is the right starting architecture. It gives you the speed of a monolith with the future optionality of microservices.

---

## 1. Context Analysis

| Factor | Your Situation | Implication |
|--------|---------------|-------------|
| **Team size** | 5 engineers | Too small to sustain multiple service boundaries, on-call rotations, and independent deployment pipelines |
| **Initial scale** | ~10K users | A single well-architected server handles this trivially; no need for independent scaling of components |
| **Timeline** | 3 months to launch | Microservices add significant infrastructure overhead that compresses feature development time |
| **Stage** | Startup (early) | Domain boundaries are not yet well-understood; premature decomposition leads to expensive re-draws |

---

## 2. Detailed Tradeoff Analysis

### 2.1 Monolith (Modular)

| Dimension | Assessment |
|-----------|-----------|
| **Development velocity** | ✅ **High.** Single codebase, single deployment, shared types/models. Engineers move fast with no cross-service coordination overhead. |
| **Operational complexity** | ✅ **Low.** One deployment artifact, one CI/CD pipeline, one set of logs, one database. A 5-person team can manage this without dedicated DevOps. |
| **Debugging & testing** | ✅ **Simple.** End-to-end request tracing is straightforward. Integration tests run in-process. No distributed system failure modes. |
| **Domain discovery** | ✅ **Flexible.** Module boundaries can be refactored cheaply. When you discover your real domain boundaries through production usage, restructuring is a code refactor, not a system migration. |
| **Scaling ceiling** | ⚠️ **Moderate.** Horizontal scaling via multiple instances behind a load balancer handles 10K–500K+ users comfortably. You hit real limits only at significant scale. |
| **Future decomposition** | ⚠️ **Requires discipline.** Must enforce module boundaries in code (separate packages/modules, clear interfaces) to preserve the option to extract services later. |

### 2.2 Microservices

| Dimension | Assessment |
|-----------|-----------|
| **Development velocity** | ❌ **Lower initially.** Must build service scaffolding, inter-service communication (REST/gRPC/messaging), API contracts, and handle distributed data consistency from day one. |
| **Operational complexity** | ❌ **High.** Requires container orchestration (Kubernetes or equivalent), service discovery, distributed tracing (Jaeger/Zipkin), centralized logging (ELK/Datadog), and per-service CI/CD pipelines. This is a full-time job for 1–2 engineers—40% of your team. |
| **Debugging & testing** | ❌ **Complex.** Distributed failures (network partitions, partial outages, eventual consistency bugs) are fundamentally harder to diagnose. Integration testing requires service mocking or full environment spin-up. |
| **Domain discovery** | ❌ **Risky.** Wrong service boundaries are extremely expensive to fix (data migration, API redesign, deployment pipeline changes). At a startup, you don't yet know where the real boundaries are. |
| **Scaling ceiling** | ✅ **High.** Independent scaling of hot services is the core advantage—but this only matters when you have identified and measured actual bottlenecks. |
| **Team autonomy** | ⚠️ **Irrelevant at your size.** Team autonomy benefits apply when you have 30+ engineers across multiple squads. With 5 people, coordination overhead exceeds the autonomy benefit. |

---

## 3. Risk Assessment

### 3.1 Risks of Choosing Microservices Now

| Risk | Severity | Likelihood | Mitigation |
|------|----------|------------|------------|
| **Missed launch deadline** | 🔴 Critical | High | Infrastructure work (K8s, service mesh, CI/CD per service) consumes 30–50% of your 3-month runway |
| **Wrong service boundaries** | 🔴 Critical | High | Early-stage domains shift rapidly; redrawing boundaries across services requires data migration and API rework |
| **Operational burden** | 🟠 High | High | 5 engineers cannot sustain on-call for multiple services plus feature development; burnout risk |
| **Distributed system complexity** | 🟠 High | Medium | Network failures, data consistency issues, and partial outages are new failure classes your team may lack experience managing |

### 3.2 Risks of Choosing a Monolith

| Risk | Severity | Likelihood | Mitigation |
|------|----------|------------|------------|
| **Big ball of mud** | 🟠 High | Medium | **Mitigation:** Enforce module boundaries from day one—separate packages, explicit public APIs between modules, no cross-module database queries |
| **Scaling bottleneck** | 🟡 Low | Low | At 10K users this is a non-issue. Horizontal scaling (multiple instances + load balancer) handles 100x growth. Revisit when you have real metrics. |
| **Future extraction cost** | 🟡 Low | Medium | **Mitigation:** Modular design with clean interfaces means extraction is a bounded, plannable project—not an emergency rewrite |

---

## 4. Recommended Architecture

### 4.1 Modular Monolith Pattern

```
┌─────────────────────────────────────────────┐
│                 API Gateway / Router         │
├──────────┬──────────┬───────────┬───────────┤
│  Auth    │  Users   │  Billing  │  Core     │
│  Module  │  Module  │  Module   │  Feature  │
│          │          │           │  Module   │
├──────────┴──────────┴───────────┴───────────┤
│          Shared Infrastructure              │
│   (DB access, logging, config, events)      │
├─────────────────────────────────────────────┤
│              Single Database                │
└─────────────────────────────────────────────┘
```

**Key principles:**
1. **Module isolation** — Each module owns its domain models and exposes a clean interface (service layer). No module directly accesses another module's database tables.
2. **Internal event bus** — Use an in-process event system (e.g., an EventEmitter or mediator pattern) for cross-module communication. This mirrors the messaging pattern of microservices and makes future extraction straightforward.
3. **Schema separation** — Use database schemas or table prefixes per module. This makes future database splitting easier.
4. **Single deployment** — One CI/CD pipeline, one artifact, one runtime process.

### 4.2 Technology Suggestions

| Concern | Recommendation | Rationale |
|---------|---------------|-----------|
| **Runtime** | Node.js (NestJS) or Python (FastAPI/Django) or Go | All support modular monolith patterns well; choose based on team expertise |
| **Database** | PostgreSQL | Battle-tested, handles 10K–1M+ users, excellent ecosystem |
| **Caching** | Redis (add when needed) | Don't add until you measure a need |
| **Deployment** | Single container on a managed platform (Railway, Render, Fly.io, or AWS ECS) | Minimizes DevOps burden for a small team |
| **CI/CD** | GitHub Actions (single pipeline) | Simple, well-supported, free tier likely sufficient |

### 4.3 Extraction Triggers — When to Revisit

Plan a microservices migration **only** when you observe concrete signals, not hypothetical future needs:

| Signal | Threshold | Action |
|--------|-----------|--------|
| **Team size** | 15+ engineers, 3+ squads | Teams are stepping on each other; consider extracting the highest-contention module |
| **Deployment coupling** | Releases blocked by unrelated changes >3x/month | Extract the fast-changing module into its own service |
| **Scaling bottleneck** | Specific module consumes >70% of compute and scales differently from the rest | Extract and scale independently |
| **Availability requirements** | One module needs 99.99% uptime while others can tolerate 99.9% | Isolate the critical-path module |

---

## 5. Implementation Roadmap (3-Month Timeline)

| Phase | Timeline | Focus |
|-------|----------|-------|
| **Phase 1: Foundation** | Weeks 1–2 | Project scaffolding, module structure, database schema, CI/CD pipeline, auth module |
| **Phase 2: Core Features** | Weeks 3–8 | Build core product features within module boundaries. Ship internal alpha by week 6. |
| **Phase 3: Harden & Launch** | Weeks 9–12 | Load testing, monitoring setup (basic APM), error tracking, security review, soft launch |

---

## 6. Common Objections Addressed

### "But we'll just have to rewrite later"

No. A modular monolith is **not** a rewrite-requiring dead end. With enforced module boundaries and an internal event bus, extracting a module to a service is a **bounded, plannable migration** — not a rewrite. Companies like Shopify, GitHub, and Basecamp run massive-scale monoliths. The "monolith → rewrite" narrative comes from **unstructured** monoliths (big balls of mud), not modular ones.

### "Microservices are industry best practice"

Microservices solve **organizational scaling** problems (many teams, independent deployment cadences). They are not a technical best practice for small teams. Martin Fowler's "Monolith First" guidance and Sam Newman's *Building Microservices* both explicitly recommend starting monolithic and extracting services when you have evidence-based reasons.

### "What if we go viral and need to scale fast?"

Horizontal scaling of a monolith (multiple instances behind a load balancer) handles orders-of-magnitude traffic increases. Shopify serves millions of merchants on a modular monolith. If you go from 10K to 1M users, your bottleneck will be database queries and caching strategy—problems that exist identically in a microservices architecture. Scale the database (read replicas, connection pooling, query optimization) and add caching before decomposing services.

---

## 7. Summary

| Criterion | Monolith (Modular) | Microservices |
|-----------|-------------------|---------------|
| Time to launch | ✅ 3 months feasible | ❌ High risk of overrun |
| Team fit (5 engineers) | ✅ Right-sized | ❌ Overstretched |
| Operational burden | ✅ Manageable | ❌ Requires dedicated DevOps |
| 10K user scale | ✅ Trivial | ✅ Overkill |
| Future optionality | ✅ Extractable with discipline | ✅ Already decomposed |
| Domain discovery | ✅ Cheap to refactor | ❌ Expensive to redraw |

**Final recommendation:** Build a modular monolith. Launch in 3 months. Instrument and measure. Extract services only when real signals demand it. This is the lowest-risk, highest-velocity path for a 5-person startup.
