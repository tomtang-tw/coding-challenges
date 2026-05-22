# Final Leaderboard: Coding Challenge #2 — All 5 PRs Reviewed

---

## Summary Table

| Rank | Team | Score |
|------|------|-------|
| 1 | Go & Do (`gondo`) | 85/105 |
| 2 | Harry Nguyen (`harrynguyen`) | 66/105 |
| 3 | Thuc Pham (`thucpham`) | 63/105 |
| 4 | Susan & Anh Le (`susan-anhle`) | 45/105 |
| 5 | Anh Phan, Hoa Nguyen, Henry (`anhphan-hoanguyen-henry`) | 43/105 |

---

## Score Breakdown by Category

| Category (Max) | gondo | harrynguyen | thucpham | susan-anhle | anhphan-hoanguyen-henry |
|----------------|-------|-------------|----------|-------------|-------------------------|
| Core Functionality (15) | 14 | 12 | 10 | 6 | 7 |
| Code Quality & Structure (10) | 6 | 3 | 6 | 5 | 5 |
| Provider Integration Design (15) | 12 | 7 | 7 | 5 | 5 |
| Routing Behavior (15) | **13** | 11 | 8 | 8 | 6 |
| State Management (15) | 12 | 11 | 11 | 5 | 9 |
| Adaptability to Change (20) | 15 | 13 | 13 | 7 | 6 |
| Reusability Across Domains (5) | 5 | 4 | 5 | 4 | 3 |
| Observability & Traceability (5) | 5 | 5 | 3 | 4 | 2 |
| **Base Score (100)** | **82** | **66** | **63** | **44** | **43** |
| Bonus: Architectural Thinking (+5) | +3 | +0 | +0 | +1 | +0 |
| **Total (105)** | **85** | **66** | **63** | **45** | **43** |

---

## Design-First Gate Results

Coding Challenge #2 is **design-led**: the challenge primarily evaluates problem understanding, design approach, and key trade-offs. Submissions without a written design artifact are capped on Provider Integration, Routing, State Management, and the Architectural Thinking bonus.

| Team | Design Tier | Caps Applied |
|------|-------------|--------------|
| gondo | ⚠️ Partial | Provider ≤13, Routing ≤13, State ≤13, Bonus ≤+3 |
| harrynguyen | ❌ Missing | Provider ≤7, Routing ≤11, State ≤11, Bonus = 0 |
| thucpham | ❌ Missing | Provider ≤7, Routing ≤11, State ≤11, Bonus = 0 |
| susan-anhle | ⚠️ Partial | Provider ≤13, Routing ≤13, State ≤13, Bonus ≤+3 |
| anhphan-hoanguyen-henry | ❌ Missing | Provider ≤7, Routing ≤11, State ≤11, Bonus = 0 |

---

## 1. Go & Do (`gondo`) — 85/105

**Tech Stack:** Python 3.13 / FastAPI, gRPC, PostgreSQL + Alembic, NATS, React + Vite + MUI, pytest / Vitest, Docker Compose, WireMock

**Ranking Justification:** gondo ships the most substantial design content (25 design docs / diagrams) and code that delivers all 10 spec routes — including the User Story 3 Vietnam update and the Philippines new market — with cost capture at the correct lifecycle stages and idempotent, validated callbacks. The Design-First gate drops to Partial tier because design *packaging* is sloppy (diagrams live outside the submission folder, placeholder text in README sections, a misleading filename), even though design *content* is genuinely strong. Code-level issues remain: state-machine guard bypassed in 4 mutation sites, and significant scope drift (Postgres + Alembic + NATS + gRPC + admin UI).

**Key Strengths:**
- Time-versioned routing rules (`effective_from`/`effective_to`) — best single design choice in the cohort
- All 10 spec routes correct including VN US3 update and PH new market
- Genuine design reasoning in `diagrams/notification-state-machine.md` and `docs/backlog/challenge-vs-repo-gap-scan.md`
- Domain-neutral API with `X-Calling-Domain` attribution; `cost_story` API field
- Idempotent, validated callbacks; 30+ test files

**Key Risks:**
- State-machine guard bypassed in dispatch/retry paths (direct `n.state` assignment)
- Design packaging fails 4 of 5 clarity sub-checks
- Scope drift: Postgres, NATS, gRPC, admin UI — documented as deliberate but misaligned with "Implementation Simplicity" guidance
- Per-country phone validation hardcoded as Python if-elif blocks

**Score Summary:**

| Category | Score | Max |
|----------|-------|-----|
| Core Functionality | 14 | 15 |
| Code Quality & Structure | 6 | 10 |
| Provider Integration Design | 12 | 15 |
| Routing Behavior | 13 | 15 |
| State Management | 12 | 15 |
| Adaptability to Change | 15 | 20 |
| Reusability Across Domains | 5 | 5 |
| Observability & Traceability | 5 | 5 |
| **Base Score** | **82** | **100** |
| Bonus (Architectural Thinking) | +3 | +5 |
| **Total** | **85** | **105** |

---

## 2. Harry Nguyen (`harrynguyen`) — 66/105

**Tech Stack:** Go 1.25, Echo v4, PostgreSQL/GORM, Redis/BullMQ, React/Vite, Prometheus/Grafana/Loki, Playwright

**Ranking Justification:** Solid Go implementation with correct US3 routing rules for all 10 spec routes, a centralized 7-state machine, callback-driven state changes, and impressive resilience/race-tested code. However, the submission skips the design-led deliverable entirely: README is setup/run-only, no rationale or trade-offs in writing, and the SPECS_KIT docs are spec restatements plus test matrices rather than design narratives. Substantial out-of-scope infrastructure (Postgres, Redis/BullMQ, Prometheus/Grafana/Loki, React SPA, Playwright E2E) is a textbook scope-drift signal.

**Key Strengths:**
- Correct US3 routing rules and PH new market
- Centralized state machine with explicit transition map + recovery taxonomy for out-of-order callbacks
- Clean provider abstraction + registry (no switch in core)
- Rich observability: per-provider AND per-country cost tracking, observer pattern, `/api/sms/stats` endpoint
- Resilience tested with race detector, concurrent send, duplicate-callback convergence

**Key Risks:**
- No written design — Phase 2 conversations have no anchor
- Hidden recursive transition inside `UpdateStatus` (auto-fallback as side-effect)
- Scope drift increases change cost (Postgres, Redis, Prometheus, React SPA, Playwright, Nginx, bcrypt auth)
- Carrier resolution as hardcoded switch
- Duplicate SPECS_KIT docs at root and under `specs-kit/`

**Score Summary:**

| Category | Score | Max |
|----------|-------|-----|
| Core Functionality | 12 | 15 |
| Code Quality & Structure | 3 | 10 |
| Provider Integration Design | 7 | 15 |
| Routing Behavior | 11 | 15 |
| State Management | 11 | 15 |
| Adaptability to Change | 13 | 20 |
| Reusability Across Domains | 4 | 5 |
| Observability & Traceability | 5 | 5 |
| **Base Score** | **66** | **100** |
| Bonus (Architectural Thinking) | +0 | +5 |
| **Total** | **66** | **105** |

---

## 3. Thuc Pham (`thucpham`) — 63/105

**Tech Stack:** TypeScript / Node.js (tsx runtime), `@clack/prompts` CLI, Vitest

**Ranking Justification:** The implementation is genuinely the strongest part — a textbook centralised state machine, a clean adapter + registry seam, all 7 providers wired, and a reusable Strategy-based retry policy. But the design-first gate caps this hard: the README has no design narrative, no trade-offs, no challenge-focused reasoning. Combined with Vietnam still using User Story 1 mappings (not US3), the submission lands in the mid-🟡 band.

**Key Strengths:**
- Textbook centralized state machine with exact spec names, 8 transitions, central guarded `transition()`
- Genuine adapter + registry seam (no switch on provider name)
- Strategy-based retry resolver distinguishing transient vs permanent failure
- Domain-agnostic public API (`SmsService.send(id, country, phoneNumber, message)`)
- Full audit log with timestamps per transition

**Key Risks:**
- Vietnam routing uses User Story 1 mappings, locked into tests (`test/flows.test.ts:90`)
- No design reference for Phase 2 conversations
- Public-mutable domain entity (`SmsMessage` fields all public)
- Generic `Error` taxonomy throughout (no custom error classes)
- Spec ambiguities not articulated anywhere

**Score Summary:**

| Category | Score | Max |
|----------|-------|-----|
| Core Functionality | 10 | 15 |
| Code Quality & Structure | 6 | 10 |
| Provider Integration Design | 7 | 15 |
| Routing Behavior | 8 | 15 |
| State Management | 11 | 15 |
| Adaptability to Change | 13 | 20 |
| Reusability Across Domains | 5 | 5 |
| Observability & Traceability | 3 | 5 |
| **Base Score** | **63** | **100** |
| Bonus (Architectural Thinking) | +0 | +5 |
| **Total** | **63** | **105** |

---

## 4. Susan & Anh Le (`susan-anhle`) — 45/105

**Tech Stack:** Java 25, Spring Boot, Gradle, Jackson, JUnit. Event Sourcing + CQRS, in-memory repositories.

**Ranking Justification:** Visible architectural ambition (event sourcing, CQRS, projectors) is undermined by spec non-conformance. The lifecycle is missing the `Send-failed` state entirely, the retry path back to `Send-to-provider` is impossible from both retry exits, the `Carrier-rejected` guard is on the wrong source state, and 7 of 10 spec-route providers are unwired in the dispatcher — silently NPE-ing through a catch-all. A working demo for VN-Vonage and SG/VN-Twilio masks a system that fails 70% of spec routes and 2 of 6 spec transitions.

**Key Strengths:**
- Event-sourced lifecycle with real history and 4 working reporting projectors
- Routing data shape is correct (VN US3 updates + PH new market in `agreements.json`)
- Centralized state guards in aggregate with idempotency for happy-path replays
- Genuine ADR (`0001-event-as-a-source-of-truth.md`) with a real X-vs-Y trade-off

**Key Risks:**
- Lifecycle missing `Send-failed` state; `Carrier-rejected` source state wrong; retry paths blocked
- 7 of 10 spec routes silently NPE (only Twilio and Vonage adapters wired)
- Storage coupled to cost-min semantics (out of scope)
- Catch-all error handling hides defects (bare HTTP 500)
- Design doc contradicts code (README Mermaid shows `AIProviderRouting` as live; it's a null-returning stub)

**Score Summary:**

| Category | Score | Max |
|----------|-------|-----|
| Core Functionality | 6 | 15 |
| Code Quality & Structure | 5 | 10 |
| Provider Integration Design | 5 | 15 |
| Routing Behavior | 8 | 15 |
| State Management | 5 | 15 |
| Adaptability to Change | 7 | 20 |
| Reusability Across Domains | 4 | 5 |
| Observability & Traceability | 4 | 5 |
| **Base Score** | **44** | **100** |
| Bonus (Architectural Thinking) | +1 | +5 |
| **Total** | **45** | **105** |

---

## 5. Anh Phan, Hoa Nguyen, Henry (`anhphan-hoanguyen-henry`) — 43/105

**Tech Stack:** Go 1.26, Gin, Testify, in-memory repos, `godotenv`, `gorm` imported (unused)

**Ranking Justification:** The submission compiles and exercises a basic end-to-end flow, but it ships User Story 1 routing rules only — Viettel → Twilio — and skips User Story 3 entirely. Only VN/Viettel is seeded; no Thailand, Singapore, or Philippines. Only Twilio and Vonage clients exist; 5 providers are not implemented. Provider client resolution is a `switch` on a `ProviderEnum` string. The README is large but trade-offs are about Go tooling choices, not the SMS challenge — Design-First gate lands at Missing.

**Key Strengths:**
- State machine has the right shape — `CanTransitionTo` correctly implements all 7 states and 6+2 transitions
- Routing-as-data via `ProviderAgreement` — a clean seam; a rule is a row
- High unit-test coverage (86–100% per package)
- Domain types are neutral — no domain-specific names

**Key Risks:**
- User Story 3 not integrated — only VN/Viettel/Twilio seeded
- Provider onboarding is multi-file (switch on enum, not a registry)
- No transition history — only current state stored; cannot reconstruct lifecycle
- State guard not enforced — 4 sites mutate `message.Status` directly
- Trade-off thinking not demonstrated at the challenge level

**Score Summary:**

| Category | Score | Max |
|----------|-------|-----|
| Core Functionality | 7 | 15 |
| Code Quality & Structure | 5 | 10 |
| Provider Integration Design | 5 | 15 |
| Routing Behavior | 6 | 15 |
| State Management | 9 | 15 |
| Adaptability to Change | 6 | 20 |
| Reusability Across Domains | 3 | 5 |
| Observability & Traceability | 2 | 5 |
| **Base Score** | **43** | **100** |
| Bonus (Architectural Thinking) | +0 | +5 |
| **Total** | **43** | **105** |

---

## Cohort-Wide Observations

### What separated the top from the bottom

1. **Design documentation matters.** The single biggest discriminator was the Design-First gate. `gondo` (Partial tier) was the only team that shipped meaningful design rationale. Three teams earned Missing tier; one earned Partial but with broken spec compliance. **No team achieved Strong tier.**

2. **User Story 3 compliance.** The VN routing update and PH new market were the explicit "adaptability under change" test. Only `gondo` and `harrynguyen` delivered all 10 spec routes correctly; `thucpham` and `anhphan-hoanguyen-henry` shipped User Story 1 values.

3. **Scope discipline.** Two submissions (`gondo`, `harrynguyen`) added significant out-of-scope infrastructure (real databases, message queues, monitoring stacks, admin UIs). The challenge brief explicitly says in-memory + CLI is sufficient. Over-engineering was penalized under YAGNI / Code Quality.

4. **State machine quality.** Every team attempted a state machine, but enforcement varied widely — from textbook transition tables (`thucpham`, `gondo`) to guards bypassed by direct field assignment (`anhphan-hoanguyen-henry`, `harrynguyen`).

### Advice for future challenges

- **Write the design before the code.** A 2-page `DESIGN.md` with named trade-offs can be worth 10–15 points.
- **Read the spec updates.** User Story 3 explicitly changed Vietnam routing; missing this is the single most visible defect.
- **Stay in scope.** Real databases, message queues, and monitoring dashboards are impressive engineering but are not what the challenge measures.
- **Test your routing table against the spec.** Several teams' tests codified wrong routing mappings, making bugs invisible to CI.
