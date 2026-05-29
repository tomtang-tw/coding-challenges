# Coding Challenge #2 Review: thucpham

**Review Date:** 2026-05-21
**Submission Path / PR URL:** `/Users/stevelam/workspace/coding-challenges-thuc/coding-challenge-2/thucpham/`
**Reviewer:** AI Code Reviewer
**Tech Stack:** TypeScript / Node.js (tsx runtime), `@clack/prompts` CLI, Vitest

---

## 🏆 FINAL RECOMMENDATION

> **🟡 CONSIDER**

**Ranking Justification:** The implementation is genuinely the strongest part of this submission — a textbook centralised state machine, a clean adapter + registry seam, all 7 providers wired, and a reusable Strategy-based retry policy. But the design-first gate caps this hard: the README has no design narrative, no trade-offs, and no challenge-focused reasoning — only Quick start + a file-tree with role captions + a test list. Combined with a routing bug where Vietnam still uses User Story 1 mappings, the submission lands in the mid-🟡 band.

**For Final Selection Panel:** Strong implementation discipline (true state machine, true registry, true Strategy-based retry) — but the candidate has not demonstrated the thinking in writing. The VN US3 update was missed in code, and there is no design doc to discuss this with in a Phase 2 conversation. Compare unfavourably against any submission shipping even a short DESIGN.md with named trade-offs.

---

## Score Summary

| Category | Score | Max | Design-First Cap Applied |
|----------|-------|-----|--------------------------|
| Core Functionality | 10 | 15 | — |
| Code Quality & Structure | 6 | 10 | — |
| Provider Integration Design | 7 | 15 | **Cap = 7 (50% — Missing tier)** |
| Routing Behavior | 8 | 15 | Cap = 11 (Missing tier) — code-level deductions lower |
| State Management | 11 | 15 | Cap = 11 (Missing tier) |
| Adaptability to Change | 13 | 20 | — |
| Reusability Across Domains | 5 | 5 | — |
| Observability & Traceability | 3 | 5 | — |
| **Base Score** | **63** | **100** | — |
| Bonus (Architectural Thinking) | +0 | +5 | **Cap = 0 (Missing tier)** |
| **Total** | **63** | **105** | — |

**Design Tier:** ❌ Missing — see Design Documentation Audit below

> Note: 63 places the submission on the 🟡/🔴 boundary. The code is genuinely better than the bottom of 🟡, but the combined effect of the Missing-tier caps (≤7 Provider, ≤11 Routing, ≤11 State, 0 bonus) plus the VN routing defect produces this total. Re-banded as **low 🟡 CONSIDER** on the strength of the state machine and provider abstraction; the design-first gate is doing exactly what it is meant to do.

---

## 📐 Design Documentation Audit

**Design artifact location(s):** `README.md` only. No `DESIGN.md`, no `ARCHITECTURE.md`, no `docs/` folder, no ADRs, no diagrams. (Verified by `find … -name "*.md"` — exactly one .md file in the entire submission.)

### Doc-by-doc audit

| Doc | (a) Filename matches content? | (b) Advances SMS challenge solution? | (c) Reasoning or only description? |
|-----|-------------------------------|---------------------------------------|--------------------------------------|
| `README.md` | ✅ "README" honestly describes what's inside (Quick start + repo structure + test list) | ❌ None of the 6 challenge areas (provider integration, routing, state machine, callbacks, cross-domain reuse, spec ambiguities) is *discussed* — just file-tree captions like `StateMachine.ts ← Allowed state transitions` | ❌ Pure description — structure tree with one-line role captions, plus a bulleted test list. No alternatives, no trade-offs, no "why this shape" |

### Three-criteria audit

| Criterion | ✅/⚠️/❌ | Evidence |
|-----------|---------|----------|
| 1. README/design is clear (all 5 sub-checks pass) | ❌ | The README is discoverable, single-file, no placeholders, no filename mismatch, and there's only one doc — so sub-checks 1.1, 1.2, 1.3, 1.4, 1.5 all individually pass on hygiene grounds. **But "clear" requires that there *be* design content to be clear about.** The README contains zero design narrative — no problem framing, no rationale, no diagrams, no trade-offs. A clear file-tree is not a clear design. Marked ❌ because Criterion 1 measures the clarity of the *design*, not the clarity of the install steps. |
| 2. Trade-offs are identified (≥3 challenge-focused alternatives with reasoning) | ❌ | Zero trade-offs articulated anywhere. The README never names an alternative. There is no sentence of the form "we chose X because Y; alternative Z would have done W". |
| 3. Content is focused on the challenge problem (≥3 of the 6 areas, reasoned) | ❌ | Zero of the six challenge areas are discussed with reasoning. The file-tree captions name the modules (routing, state machine, providers, callbacks) but explain nothing about *why* the shape was chosen or what changes cost. |

### Criterion 1 sub-checks

| Sub-check | ✅/⚠️/❌ | Evidence |
|-----------|---------|----------|
| 1.1 Design discoverable in ≤2 hops from submission root | N/A | No design to discover; README is 1 hop, but it contains no design content. Hygiene-wise the file would have been findable. |
| 1.2 All design docs live inside the submission folder | N/A | Only README exists, and it's at the submission root. No docs outside the folder. |
| 1.3 No placeholder text (`- ...`, `TODO`, empty bullets) | ✅ | No placeholders observed in README. |
| 1.4 No filename-vs-content mismatches | ✅ | `README.md` honestly describes what's inside (install + structure + tests). No misleading filenames. |
| 1.5 Clear primacy / "start here" signal when multiple docs overlap | N/A | Only one doc. |

> The sub-checks technically pass on hygiene because there's only one doc and it's honest about what it contains. But the *overall* Criterion 1 verdict is ❌ because the SKILL's framing of Clarity is "can a reviewer find the *design narrative*" — and there is no design narrative to find. The sub-checks alone cannot lift Criterion 1 to ✅ when the content they're vetting doesn't exist.

**Assigned Tier:** ❌ **Missing**

**Caps applied:**
- Provider Integration Design: **Cap = 7 (50% cap)**
- Routing Behavior: Cap = 11
- State Management: Cap = 11
- Architectural Thinking bonus: **0 (zero, no exceptions)**

**Rationale:** The README is a setup-and-structure doc, not a design doc. Strongest signal of design discipline that exists is the code itself (centralised StateMachine, ProviderRegistry, RetryStrategy interface) — but per the SKILL's explicit rule, "Architecture that has to be inferred from code alone — without a written narrative, the candidate has not demonstrated the thinking." All three criteria fail; the tier is Missing.

---

## 🚨 Critical Issues / Disqualifiers

| Issue | Severity | Impact |
|-------|----------|--------|
| **Design-First gate: Missing tier** — README is setup + structure tree only, with zero design narrative, zero trade-offs, and zero challenge-focused reasoning | Critical | Provider Integration capped at 7/15, Routing capped at 11/15, State Management capped at 11/15, Architectural Thinking bonus forced to 0 |
| **Vietnam routing still uses User Story 1 (old) mappings, not User Story 3 (current)** — `main.ts:41-43` and `test/flows.test.ts:38-40` both encode the old rules (Viettel→Twilio primary, Mobifone→Vonage primary, Vinaphone→Vonage primary). US3 requires Viettel→Vonage, Mobifone→Infobip, Vinaphone→Twilio | Major | Direct deduction from Core Functionality, Routing, and Adaptability — the bug is *codified in tests*, so the test suite cannot catch it (the test asserts `provider === "Twilio"` for VN Viettel, locking the wrong mapping in) |

---

## Detailed Scoring

### 1️⃣ Core Functionality — 10/15

**Earned:**
- Send accepts all four required inputs (`id`, `country`, `phoneNumber`, `message`) — `application/SmsService.ts:30-33`
- Carrier derived deterministically by prefix — `routing/CarrierDetector.ts:65-83` (sorted longest-prefix-first to disambiguate PH 4-digit vs VN 3-digit)
- Happy-path lifecycle progresses through all 5 forward states — verified by `test/flows.test.ts:86-93`
- Callbacks drive state via `StateMachine.transition()` (not direct mutation) — `application/CallbackHandler.ts:32`
- TH, SG, PH routing rules all match spec — `main.ts:46-55`
- `Carrier-rejected → Send-to-provider` and `Send-failed → Send-to-provider` retry paths both work — `application/CallbackHandler.ts:34-37`

**Lost:**
- **Vietnam routing is wrong (US1 mappings)** — `main.ts:41-43`: `Viettel → [Twilio, Vonage]`, `Mobifone → [Vonage, Twilio, Infobip]`, `Vinaphone → [Vonage, Twilio]`. The spec's US3 rules are Viettel→Vonage, Mobifone→Infobip, Vinaphone→Twilio. The bug is locked in by `test/flows.test.ts:90` (`expect(sms.provider).toBe("Twilio")` for VN Viettel) — meaning a test-driven correction would fail the existing suite. −3
- Callback signature deviates from spec — spec says `messageId, provider, newState, actualCost`; implementation is `handle(messageId, newState, actualCost?, errorCode?)` at `application/CallbackHandler.ts:21`. Missing `provider`, adds non-spec `errorCode` (used to switch retry strategy, but undocumented). −1
- CLI conflates three separate callbacks into one user action — `cli.ts:225-235` automatically runs Queue → Send-to-carrier → Send-success in sequence on a single "Success" pick. The state machine accepts them individually, so this is presentation-only, but it obscures the asynchronous nature of callbacks. −1

**Verdict:** Core flow works end-to-end for TH/SG/PH but Vietnam — the one country the spec explicitly updated in US3 — is wrong. Tests codify the bug rather than catch it.

---

### 2️⃣ Code Quality & Structure — 6/10

**Earned:**
- Clean separation into `domain/`, `routing/`, `providers/`, `application/`, `infrastructure/` — `main.ts` is purely composition root
- No god classes; each module has a focused responsibility
- TypeScript types throughout; no `any` observed
- The Strategy pattern in `application/RetryStrategy.ts:8-67` is genuine — a small, real abstraction with two impls and a resolver, and it's actually used through the callback handler

**Lost:**
- **Public-mutable bag-of-fields domain entity** — `domain/SmsMessage.ts:9-19` declares every field as public-mutable (`id`, `country`, `provider`, `status`, `estimatedCost`, `actualCost`, `auditLog`). `SmsService.send` then mutates them directly at `application/SmsService.ts:38-40` (`sms.carrier = carrier; sms.provider = selectedProvider; sms.estimatedCost = ...`). The state machine is centralised but the entity is not encapsulated — anything in the application layer can rewrite any field. −1
- **Generic `Error` throughout** — no error taxonomy. Examples: `domain/StateMachine.ts:25` (invalid transition), `routing/CarrierDetector.ts:69,82` (carrier detection failures), `routing/RoutingEngine.ts:32-34` (no provider available), `providers/ProviderRegistry.ts:14` (provider not registered), `application/CallbackHandler.ts:24` (message not found), `application/RetryStrategy.ts:53` (no fallback). Callers cannot distinguish "no carrier" from "invalid transition" without string-matching. −1
- **DRY violation: routing rules duplicated** — `main.ts:40-55` and `test/flows.test.ts:38-47` both spell out the entire (incorrect) routing table. Any rule change requires editing two unrelated files. A `routing/DefaultRoutingRules.ts` exporting the seed data, imported by both, would eliminate this. −1
- **Debug-style logging in production code** — `providers/Providers.ts:13-15` uses raw `console.log` in `BaseAdapter.send()` ("Sending SMS..., Estimated cost: ..."). Adapters should either be silent (simulation) or take a logger via DI. The test file silences this with `vi.spyOn(console, "log").mockImplementation(() => {})` at `test/flows.test.ts:60` — proving the noise is being deliberately suppressed. −1

**Verdict:** Layering is right and concerns are not entangled — but at the seams (entity mutability, error types, duplicated seed data, in-adapter logging), the discipline slips.

#### 🧹 Clean Code & Principles Audit

##### SOLID Compliance

| Principle | Status | Evidence | Deduction |
|-----------|--------|----------|-----------|
| Single Responsibility | ✅ | Each module owns one concern; e.g. `RoutingTable` is just the lookup table, `RoutingEngine` is just the resolver, `CarrierDetector` is just the prefix matcher | 0 |
| Open/Closed | ⚠️ | Adding a new provider requires editing `main.ts:27-33` *and* `test/flows.test.ts:29-35` *and* an entry in `cli.ts:329` (`const providers = ["Vonage","Twilio",…]` hardcoded for cost-report iteration). The registry has a `getAll()` method (`providers/ProviderRegistry.ts:20-22`) but the CLI doesn't use it — duplicate provider list | −1 |
| Liskov Substitution | ✅ | `BaseAdapter` subclasses all honour the `SmsProvider` interface uniformly | 0 |
| Interface Segregation | ✅ | `SmsProvider` interface is minimal (3 members); `RetryStrategy` interface is 2 members | 0 |
| Dependency Inversion | ✅ | Application classes receive collaborators by constructor injection; `main.ts` is the composition root | 0 |

##### DRY / KISS / YAGNI

| Principle | Violation | Location | Fix / Alternative | Deduction |
|-----------|-----------|----------|-------------------|-----------|
| DRY | Routing rules duplicated; hardcoded provider list in CLI | `main.ts:40-55` vs `test/flows.test.ts:38-47`; `cli.ts:329` vs `providers/ProviderRegistry.ts:20-22` | Extract `DefaultRoutingRules` module; iterate `registry.getAll()` in CLI report | −1 |
| KISS | ✅ none flagged | — | — | 0 |
| YAGNI | None flagged — RetryStrategy + errorCode is *arguably* over-spec (the spec only requires callback drives state), but it's a small, justified seam | `application/RetryStrategy.ts` | Borderline acceptable; the strategy resolver is one file and addresses a real ambiguity (transient vs permanent retry) | 0 |

##### Additional Clean Code

| Check | Status | Evidence | Deduction |
|-------|--------|----------|-----------|
| Meaningful names | ✅ | `SmsMessage`, `StateMachine`, `RoutingEngine`, `CarrierDetector`, `RetryStrategyResolver` — all communicate intent | 0 |
| Small functions | ✅ | All functions under ~30 lines; `CarrierDetector.detect` is the longest at ~20 lines | 0 |
| No side effects | ⚠️ | `BaseAdapter.send` writes to stdout (`providers/Providers.ts:13-15`) | −1 |
| Error handling | ❌ | Generic `Error` everywhere — no custom error classes, no error codes (`StateMachine.ts:25`, `CarrierDetector.ts:69,82`, `RoutingEngine.ts:32`, `ProviderRegistry.ts:14`, `CallbackHandler.ts:24`, `RetryStrategy.ts:53`) | −1 |
| Consistent style | ✅ | Tabs throughout, consistent class/interface conventions | 0 |
| No debug artifacts | ⚠️ | `console.log` in `BaseAdapter.send` and `CallbackHandler.handle` (`CallbackHandler.ts:39`) — production logging, but uses raw `console.log` rather than a logger seam | −1 |
| Immutability | ❌ | `SmsMessage` fields are all public-mutable (`domain/SmsMessage.ts:9-19`); `SmsService.send` mutates them directly (`application/SmsService.ts:38-40`) | −1 |
| Type safety | ✅ | TypeScript strict; no `any` observed | 0 |

**Total Clean Code Deductions:** −4 (already folded into the −4 against Code Quality above; not double-counted)

---

### 3️⃣ Provider Integration Design — 7/15

**Cap applied:** **Cap = 7 (50% — Missing tier)**

**Design narrative (from artifact):** No written design. The README's only mention of providers is one structure-tree caption — `providers/ ← Send contract + simulated adapters` (README:35-39) — plus a list of adapter filenames. No discussion of *why* an Adapter + Registry, what the alternative would have been, or what change cost the design buys.

**Earned (code-level):**
- Provider interface defined at `providers/SmsProvider.ts:3-7` with three members (`name`, `getEstimatedCost`, `send`)
- `BaseAdapter` abstract class extracts the shared cost-lookup + log behaviour (`providers/Providers.ts:4-17`)
- All 7 providers implemented (`Providers.ts:18-71`) — Vonage, Twilio, Infobip, AwsSns, Telnyx, MessageBird, Sinch
- `ProviderRegistry` keyed by `provider.name` (`providers/ProviderRegistry.ts:7-23`) — adding a provider is exactly "new class + register entry" (`main.ts:27-33`)
- No `switch`/`if-else` on provider name anywhere in the orchestration path

**Lost:**
- Cap applied at 7 — design narrative absent, so the integration design is *inferred* from the code rather than *demonstrated* by the candidate. Per SKILL: "Strong adapter code with no written design — apply the design-first cap; the code may look right but the candidate has not demonstrated the design reasoning."
- Provider list hardcoded a second time in `cli.ts:329` rather than iterating `registry.getAll()` — small leak of provider knowledge into the presentation layer

**Verdict:** The code-level design here is genuinely strong (would earn ~13/15 with a design doc). The cap is doing its job — the candidate has *built* the seam but has not *articulated* it.

---

### 4️⃣ Routing Behavior — 8/15

**Cap applied:** Cap = 11 — code-level deductions take the result below the cap.

**Design narrative (from artifact):** No written design. README:31-34 only labels the routing folder ("Carrier detection + provider routing table") and `RoutingTable.ts ← Map (country, carrier) → ordered provider list`. No discussion of where rules live, why rules-as-data over a rule engine or inline conditionals, or how rule changes propagate.

**Earned (code-level):**
- Routing is rules-as-data — `RoutingTable` is a `Map<string, string[]>` populated by `addRule()` (`routing/RoutingTable.ts:13-18`)
- Single lookup path: `RoutingEngine.resolveProvider` returns `RoutingEngine.resolveProviders[0]` (`routing/RoutingEngine.ts:25-26`)
- `SmsService.send` consults `RoutingEngine`, not a hardcoded table inside orchestration (`application/SmsService.ts:34-35`)
- Updated TH/SG/PH rules — `TH AIS → Infobip`, `TH DTAC → AwsSns`, `SG Singtel → Twilio`, `SG StarHub → Telnyx`, `PH Globe → MessageBird`, `PH Smart → Sinch`, `PH DITO → MessageBird` (`main.ts:46-55`)
- Philippines (new market in US3) is supported

**Lost:**
- **Vietnam still uses User Story 1 mappings**, not User Story 3 — `main.ts:41-43`:
  - Viettel → `[Twilio, Vonage]` (US3: Vonage)
  - Mobifone → `[Vonage, Twilio, Infobip]` (US3: Infobip)
  - Vinaphone → `[Vonage, Twilio]` (US3: Twilio)

  This is the highest-signal defect in the routing category — US3 was explicitly the spec's *update*, and the candidate didn't pick it up. −3 within the cap.
- **Routing seed duplicated in tests** — `test/flows.test.ts:38-47` re-spells the entire (buggy) table, locking in the wrong VN mappings. A test like `expect(sms.provider).toBe("Twilio")` at `test/flows.test.ts:90` *codifies* the bug. −1
- **`addRule` not invoked from a routing module** — rules are added imperatively in `main.ts`, which means the routing layer doesn't ship default data. Defensible (composition root owns config), but means *changing a rule* requires touching the wiring file, not a config/data file. Minor.

**Verdict:** Structurally rules-as-data — the change cost for *adding* a new country is low. But the candidate missed the VN update, and the test suite cements the bug. The design is *adaptable*, but the candidate did not *adapt* it when the spec changed.

---

### 5️⃣ State Management — 11/15

**Cap applied:** Cap = 11 — and code is strong enough that the cap is the binding constraint.

**Design narrative (from artifact):** No written design. README:23-29 has only structure captions: `SmsStatus.ts ← Lifecycle status enum`, `StateMachine.ts ← Allowed state transitions`. No diagram. No discussion of why a transition table over scattered `if/else`, no commentary on the two retry paths.

**Earned (code-level):**
- All 7 spec states present, with **exact** spec names — `domain/SmsStatus.ts:4-12` (`New`, `Send-to-provider`, `Queue`, `Send-to-carrier`, `Send-success`, `Send-failed`, `Carrier-rejected`)
- All 6 spec transitions present, plus the 2 retry paths, expressed as a literal table — `domain/StateMachine.ts:9-19`:
  ```ts
  [NEW → SEND_TO_PROVIDER], [SEND_TO_PROVIDER → QUEUE], [QUEUE → SEND_TO_CARRIER],
  [QUEUE → CARRIER_REJECTED], [SEND_TO_CARRIER → SEND_SUCCESS], [SEND_TO_CARRIER → SEND_FAILED],
  [SEND_FAILED → SEND_TO_PROVIDER], [CARRIER_REJECTED → SEND_TO_PROVIDER]
  ```
- `transition()` is the *only* mutation path — `domain/StateMachine.ts:21-30` calls `canTransition` then delegates to `SmsMessage.transitionTo` (`domain/SmsMessage.ts:33-43`), which appends an `SmsAuditRecord`
- Invalid transitions throw an explicit error — `domain/StateMachine.ts:25` (`Invalid transition: ${from} → ${to}`)
- Transition history is traceable: every transition is appended to `auditLog` with `fromStatus`, `toStatus`, `provider`, `carrier`, `timestamp` (`domain/SmsAuditRecord.ts:4-10`)
- Callback path goes through `StateMachine.transition` (`application/CallbackHandler.ts:32`), not direct field mutation

**Lost:**
- Cap applied at 11 — perfect mechanics but no design narrative; the candidate has not demonstrated the *thinking* behind the centralised transition table
- `SmsMessage.transitionTo` is publicly callable (`domain/SmsMessage.ts:33`) — anything in the codebase could bypass the state machine by calling it directly. Convention enforces use, not visibility. Minor — none of the production code does so today.
- Generic `Error` on invalid transition with no error code; callers can't distinguish "invalid transition" from any other error

**Verdict:** Textbook state-machine code. The cap is doing its job — the same code paired with a `state-machine.md` explaining the choice of centralised validation and the retry semantics would unlock the full 14–15.

---

### 6️⃣ Adaptability to Change — 13/20

**Change-cost matrix:**

| Change Scenario | Cost in this submission | Verdict |
|-----------------|-------------------------|---------|
| Add a new provider | Implement `SmsProvider`, instantiate in `main.ts:27-33`, add hardcoded entry to `cli.ts:329` (cost-report list). 2-3 touch points; no orchestration edits | ⚠️ (one extra touch point in CLI) |
| Add a new country | Add `PREFIXES[country]` entries in `CarrierDetector.ts:6-66` + `addRule(country, ...)` calls in `main.ts` *and* `test/flows.test.ts`. 2-3 touch points | ⚠️ (test seed must be updated) |
| Update a routing rule | Edit `addRule` call in `main.ts` *and* edit the duplicated call in `test/flows.test.ts:38-47` *and* update the test assertion (`test/flows.test.ts:90`) | ❌ (rule changes require editing 2-3 files) |
| Add a new state or transition | Add to `SmsStatus` enum (`domain/SmsStatus.ts`) + entry in `VALID_TRANSITIONS` (`domain/StateMachine.ts:9-19`) | ✅ |
| New caller domain | SMS service's public API is `send(id, country, phoneNumber, message)` — domain-agnostic. New callers compose with `SmsService` | ✅ |
| Provider callback shape changes | Edit `CallbackHandler.handle` signature (`application/CallbackHandler.ts:21`); state-machine path stays unchanged | ✅ |

**Earned:**
- New provider, new state, new caller domain, callback shape — all genuinely localised
- `RetryStrategy` is a real seam: alternative retry behaviour can be added by implementing the interface and registering an error-code mapping (`application/RetryStrategy.ts:59-67`)

**Lost:**
- VN routing was *supposed* to be a low-cost change between US1 and US3 — the candidate didn't even make the change, signalling either a missed read of the spec or a fragility in noticing routing-rule updates
- DRY violation between `main.ts` routing table and `test/flows.test.ts` routing table means rule changes cost 2 edits, not 1 — the duplicated seed defeats the rules-as-data promise
- Hardcoded provider list in `cli.ts:329` defeats the registry abstraction for the cost report

**Verdict:** Structurally adaptable but operationally fragile — when the spec changed, the routing didn't follow. The duplicated routing seed in tests is the most concrete proof that the rules-as-data promise was only half-kept.

---

### 7️⃣ Reusability Across Domains — 5/5

**Earned:**
- `SmsService.send(id, country, phoneNumber, message)` — `application/SmsService.ts:30` — no domain-specific naming
- No `Customer`, `OTP`, `Booking`, or `Marketing` in any of the public API surfaces (verified across `SmsService`, `CallbackHandler`, `SmsMessage`)
- CLI's `DEFAULT_PHONE_BY_COUNTRY` (`cli.ts:13-18`) defaults to `"OTP: 1234"` as the placeholder message — but this is a CLI-only default, not a domain coupling

**Lost:**
- None

**Verdict:** API surface is genuinely domain-agnostic; full marks.

---

### 8️⃣ Observability & Traceability — 3/5

**Earned:**
- Transition history with timestamps, provider, and carrier — `domain/SmsAuditRecord.ts:4-10`, appended via `SmsMessage.transitionTo` (`domain/SmsMessage.ts:35-42`)
- `estimatedCost` captured at `Send-to-provider` (`application/SmsService.ts:40` before the transition call); `actualCost` captured on `Send-success` (`application/CallbackHandler.ts:29-31`)
- Cost aggregations exist — total cost per provider, total cost per country, volume per provider, success rate per provider (`application/CostReport.ts:8-36`)
- Lifecycle reconstructable from `SmsMessage.auditLog`

**Lost:**
- `CostReport.volumeByProvider` (`application/CostReport.ts:24-26`) counts *all* messages targeted at a provider, including failures still in flight — the CLI labels this "Volume" without qualification (`cli.ts:343-345`), which can mislead. Should distinguish total volume from successful volume. −1
- `CostReport` does not surface per-country *volume* or per-country *success rate* — only total cost (`application/CostReport.ts:18-22`). The spec calls for "volume per provider, success/failure rates" but country-level breakdowns of those don't exist. −1
- Provider selection rationale is *implicitly* visible via the audit log (provider is recorded per transition) but is not explicitly logged at routing time — a reviewer asking "why was this provider chosen" must infer from the rule table

**Verdict:** Audit log + basic cost report is in place; per-country and volume nuances are missing.

---

### 🌟 Bonus – Architectural Thinking — +0/5

**Cap applied:** **0 (zero — Missing tier)**

**Design narrative (from artifact):** No written design — therefore by SKILL rule, no bonus regardless of code quality.

**Earned:** None — capped at 0.

**Lost:**
- The `RetryStrategy` interface + `SameProviderStrategy` + `FallbackProviderStrategy` + `RetryStrategyResolver` (`application/RetryStrategy.ts`) is genuinely clean Strategy-pattern code addressing a real spec ambiguity (transient vs permanent retry). In a Strong-tier submission this would warrant +2 to +3. Lost entirely because there is no written narrative naming the ambiguity, the chosen position, or the trade-off. Per SKILL: "Architectural thinking that exists only in code, with no narrative, is not 'demonstrated' — it's inferred, and inference is the reviewer's work, not the candidate's claim."
- The centralised `StateMachine` + transition table + retry-path inclusion is itself a deliberate architectural choice — again, undocumented.

**Verdict:** The code shows real architectural thought; the README has none. The cap is doing exactly what it's meant to do.

---

## Key Strengths (Evidence-Backed)

1. **Textbook centralised state machine** — `domain/StateMachine.ts:9-30` declares all 8 valid transitions (6 spec + 2 retry) as data, and `transition()` is the only mutation path. Invalid transitions throw with a meaningful message. Every transition pushes an audit record (`domain/SmsMessage.ts:35-42`). This is the strongest part of the submission and Phase 2-ready.
2. **Genuine adapter + registry seam** — `providers/SmsProvider.ts:3-7` interface + `providers/ProviderRegistry.ts:7-23` keyed registry + 7 concrete adapters in `providers/Providers.ts:18-71`. Adding a provider is "new class + one `registry.register()` call in `main.ts`". No `switch` on provider name anywhere in the orchestration path.
3. **Strategy-based retry resolver** — `application/RetryStrategy.ts:8-67` defines a 2-method interface and two implementations resolved by error code (`RATE_LIMITED` → same-provider, otherwise → fallback). Addresses the spec ambiguity around transient vs permanent failure with a clean seam — but undocumented, so credit is forfeit under the Missing-tier cap.

---

## Key Risks (Phase 2 Failure Points)

1. **Routing-spec drift** — Vietnam already missed the US1 → US3 update (`main.ts:41-43`), and the test suite *codifies* the wrong mappings (`test/flows.test.ts:38-47`, assertion at line 90). When US4 lands with another rule update, the same workflow that missed US3 will likely miss it again unless rule changes are anchored to a single, test-validated source of truth.
2. **No design reference for Phase 2 conversations** — without a written design, there is nothing for a Phase 2 review meeting to anchor against. Discussions about "should `errorCode` drive retry?" or "is `CallbackHandler` the right shape?" become reverse-engineering exercises rather than evaluations of stated positions.
3. **Public-mutable domain entity** — `domain/SmsMessage.ts:9-19` allows any layer to overwrite any field. Today's discipline keeps this safe (only `SmsService.send` and `StateMachine.transition` mutate fields), but the type system does not enforce the invariant. A new caller bypassing the state machine by direct assignment would compile cleanly.
4. **Generic `Error` taxonomy** — every failure mode (invalid transition, no carrier, no provider, missing message, no fallback) throws a generic `Error` with a string message. Production observability and recovery code cannot pattern-match on error types — only on string content.
5. **Spec ambiguities not articulated** — cancel-vs-retry, idempotency of duplicate `messageId`, currency of `estimatedCost`, and the VN rule-versioning question are all left unstated. The candidate *picked positions* (e.g., retry resolves on error code) but never named them.

---

## Improvement Suggestions (5+ Required)

### 💡 Suggestion 1: Add a `DESIGN.md` covering the six challenge problem areas

**Current Implementation:** README.md is install + structure tree + test list, ~90 lines. No design narrative.

**Issue:** The design-first gate caps Provider Integration at 7/15, Routing at 11/15, State Management at 11/15, and the Architectural Thinking bonus at 0. The actual implementation could earn ~13–14 in each of those categories *with* a written design.

**Recommended Approach:** Add a short (~2 page) `DESIGN.md` next to `README.md`. Cover:
- **Provider integration**: "Adapter interface + name-keyed registry. Alternative: a `switch` in `SmsService.send` — would have required editing core orchestration on every provider add. Trade-off: registration is imperative in `main.ts` rather than self-registering, which keeps composition explicit."
- **Routing**: "Rules-as-data in `RoutingTable` as `Map<country|carrier, provider[]>`. Alternative: inline conditionals or a JSON config file. Trade-off: rules live in code so rule-engine extension is possible but currently a `Map` keeps it minimal."
- **State machine**: "Centralised transition table + `canTransition` + `transition` as the only mutation path. Include a small state diagram. Address the retry semantics: `Carrier-rejected → Send-to-provider` and `Send-failed → Send-to-provider` are intentional re-entries, not duplicates of `New`."
- **Callbacks**: "Single `CallbackHandler.handle(messageId, newState, actualCost?, errorCode?)`. Position on the spec: we added `errorCode` to drive retry strategy; the spec only requires `messageId, provider, newState, actualCost`. Justify."
- **Cross-domain reuse**: "Public API takes only generic SMS fields; OTP / marketing / booking flows are caller concerns."
- **Spec ambiguities**: name 2–3 (e.g., "cancel vs retry — we chose retry"; "VN rule versioning — we follow current US3 only; if both versions are needed, see future-work").

**Impact:** Removes all three caps. Would lift Provider Integration from 7 → ~13, Routing from 8 → ~11–12 (still bounded by the VN bug), State from 11 → ~14, and unlock +2–3 architectural bonus. Net: ~+10–14 points.

### 💡 Suggestion 2: Fix Vietnam routing to match User Story 3

**Current Implementation:** `main.ts:41-43` and `test/flows.test.ts:38-40` encode the old US1 mappings:
```ts
table.addRule("VN", "Viettel",   ["Twilio",  "Vonage"]);
table.addRule("VN", "Mobifone",  ["Vonage",  "Twilio", "Infobip"]);
table.addRule("VN", "Vinaphone", ["Vonage",  "Twilio"]);
```

**Issue:** US3 explicitly updates these. The test (`expect(sms.provider).toBe("Twilio")` at line 90) locks the wrong mapping in, so the bug is invisible to the test suite.

**Recommended Approach:**
```ts
table.addRule("VN", "Viettel",   ["Vonage",  "Twilio"]);   // US3 primary: Vonage
table.addRule("VN", "Mobifone",  ["Infobip", "Vonage"]);   // US3 primary: Infobip
table.addRule("VN", "Vinaphone", ["Twilio",  "Vonage"]);   // US3 primary: Twilio
```
And update the test assertion accordingly. Better: extract the seed routing to `routing/DefaultRoutingRules.ts` so production code and tests share the same source.

**Impact:** Routing: +2; Core Functionality: +2; Adaptability: +1.

### 💡 Suggestion 3: Extract the default routing seed to a single module

**Current Implementation:** The full routing table is spelled out in `main.ts:40-55` *and* `test/flows.test.ts:38-47` — two independent declarations of the same rules.

**Issue:** DRY violation. The test confirms the production-code rules by *re-declaring* them. A rule change requires editing both.

**Recommended Approach:** Create `routing/DefaultRoutingRules.ts`:
```ts
export const DEFAULT_ROUTING_RULES: Array<{country: string; carrier: string; providers: string[]}> = [
  { country: "VN", carrier: "Viettel",   providers: ["Vonage", "Twilio"] },
  ...
];

export function seedRoutingTable(table: RoutingTable): void {
  for (const r of DEFAULT_ROUTING_RULES) table.addRule(r.country, r.carrier, r.providers);
}
```
Both `main.ts` and `test/flows.test.ts` call `seedRoutingTable(table)`.

**Impact:** Code Quality +1; Adaptability +1. Rule changes become single-file edits.

### 💡 Suggestion 4: Encapsulate `SmsMessage` and introduce a small error taxonomy

**Current Implementation:**
- `domain/SmsMessage.ts:9-19` declares all fields as public-mutable
- `application/SmsService.ts:38-40` mutates them directly outside the state machine
- Every error site throws generic `new Error(...)` (`StateMachine.ts:25`, `CarrierDetector.ts:69,82`, `RoutingEngine.ts:32`, `ProviderRegistry.ts:14`, `CallbackHandler.ts:24`, `RetryStrategy.ts:53`)

**Issue:** The state machine is centralised but the entity isn't encapsulated; any layer can rewrite any field. Generic errors cannot be programmatically distinguished.

**Recommended Approach:**
- Mark fields `readonly` and expose setters that participate in audit (`assignProvider(name)`, `assignCarrier(name)`, `setActualCost(value)` with guards for state)
- Add a small error hierarchy: `InvalidTransitionError`, `CarrierDetectionError`, `RoutingError`, `ProviderNotRegisteredError`, `SmsNotFoundError`. Callers can `instanceof` for recovery decisions.

**Impact:** Code Quality +1; Provider Integration +1 (once cap is lifted); Phase 2 resilience.

### 💡 Suggestion 5: Align callback signature with the spec, then document the deviation

**Current Implementation:** `application/CallbackHandler.ts:21` — `handle(messageId, newState, actualCost?, errorCode?)`. The spec says `messageId, provider, newState, actualCost`.

**Issue:** Missing `provider` (spec); adding `errorCode` (non-spec) — *and* `errorCode` controls retry strategy, which is load-bearing behaviour the spec doesn't mandate. Currently undocumented.

**Recommended Approach:**
- Add `provider` to the parameter list for spec alignment (it's already available via the message's audit log, but accepting it explicitly matches the spec).
- Keep `errorCode` as an *optional* extension and explain it in the design doc: "We extended the callback contract with `errorCode` to differentiate transient (`RATE_LIMITED` → same-provider retry) from permanent (default → fallback retry) failures. Without this, the system cannot make an informed retry decision."

**Impact:** Core Functionality +1; Architectural Thinking bonus +1 (after cap lifted via design doc).

### 💡 Suggestion 6: Make adapter logging injectable (or silent) and use the registry for the CLI cost report

**Current Implementation:**
- `providers/Providers.ts:13-15` writes to `console.log` directly from `BaseAdapter.send`
- `cli.ts:329` hardcodes the provider list for the cost report instead of iterating `registry.getAll()` (`providers/ProviderRegistry.ts:20-22`)
- Tests silence the noise with `vi.spyOn(console, "log").mockImplementation(() => {})` (`test/flows.test.ts:60`)

**Issue:** Side effects in domain-adjacent code; presentation-layer code holds a duplicate of the registry's state.

**Recommended Approach:**
- Inject a logger or a `notify(msg)` callback into the adapter base; make tests assert on the captured calls
- In the CLI, iterate `registry.getAll()` to build the report rows

**Impact:** Code Quality +1; Adaptability +1.

---

## Knowledge Badges

| Badge | Earned | Evidence |
|-------|--------|----------|
| 🟢 Design-First Discipline | ❌ | No DESIGN.md / no trade-offs / no challenge-focused reasoning — README is structure-tree + install + tests only |
| 🟢 Provider Abstraction | ✅ | `providers/SmsProvider.ts:3-7` + `providers/ProviderRegistry.ts:7-23` + 7 concrete adapters at `providers/Providers.ts:18-71`; no `switch` on provider name |
| 🟢 Routing Policy Design | ⚠️ | Rules-as-data in `RoutingTable` (`routing/RoutingTable.ts:5-20`) ✅, but VN rules wrong (`main.ts:41-43`) and seed duplicated in tests (`test/flows.test.ts:38-47`) |
| 🟢 State Machine Design | ✅ | `domain/StateMachine.ts:9-30` — exact 7 states, 6 spec transitions + 2 retry paths, central `transition()` is the only mutation path, audit log on every transition (`domain/SmsMessage.ts:35-42`) |
| 🟢 Adaptability / OCP | ⚠️ | New provider / new state / new caller are localised, but rule changes touch 2-3 files (DRY violation between `main.ts` and `test/flows.test.ts`), and VN US3 update was missed |
| 🟢 Cross-Domain Reuse | ✅ | `SmsService.send(id, country, phoneNumber, message)` — no domain coupling; no OTP/Customer/Booking in module names |
| 🟢 Observability & Cost Tracking | ⚠️ | Audit log + `estimatedCost`/`actualCost` + cost report ✅, but volume metric is ambiguous (`CostReport.volumeByProvider:24-26` counts all not just success) and no per-country volume |
| 🟢 Clean Code Practices | ⚠️ | Good naming, small functions, clean injection — but public-mutable entity, generic `Error` taxonomy, `console.log` in adapters, DRY violation between `main.ts` and tests |
| 🟢 Test Quality (if present) | ⚠️ | Tests exist and cover happy path + retry paths + cost report, but test seed *duplicates* the routing rules (and codifies the VN bug at line 90), so the test suite cannot detect rule drift |

---

## Final Verdict

### Summary

This is a candidate who *can* implement clean, layered TypeScript: the state machine is textbook (exact spec names, central guarded transitions, audit log, two retry paths), the provider seam is a real adapter + registry with no `switch`-on-name, the retry policy is a small genuine Strategy pattern resolving on error code, and the SMS module's public API is domain-agnostic. On code-design discipline alone, this submission would land comfortably in the 🥈 RECOMMEND band.

The design-first gate caps the score hard, and the cap is the right verdict. The README is ~90 lines of install instructions + a file-tree with one-line role captions + a test list. There is no design narrative — no problem framing, no trade-offs, no diagrams, no challenge-focused reasoning. Per the SKILL: *"Architectural thinking that exists only in code, with no narrative, is not 'demonstrated' — it's inferred."* The caps fire: Provider Integration ≤ 7/15, Routing ≤ 11, State ≤ 11, Architectural Thinking bonus = 0.

On top of the design cap, a concrete defect compounds: Vietnam routing still uses User Story 1 mappings (Viettel→Twilio primary, Mobifone→Vonage, Vinaphone→Vonage), not the User Story 3 update (Viettel→Vonage, Mobifone→Infobip, Vinaphone→Twilio). The test suite *codifies* the wrong mapping (`test/flows.test.ts:90`), so the bug is invisible to CI. This is the single most concrete piece of evidence that "missed the spec update" is a real risk under Phase 2 — and the fact that no design doc exists to discuss VN rule-versioning makes the discussion harder.

The result — 63/105, low 🟡 CONSIDER — is the design-first gate doing its job. The code says "I know how to build this." The absence of a design doc says "I didn't show I thought about why."

### Selection Panel Notes
- **Strongest evidence of design instinct:** the StateMachine + audit log combination and the RetryStrategy Strategy/Resolver pair. Both would unlock the full bonus if narrated.
- **Single most concerning defect:** VN routing wrong, with a test that locks the bug in. Signals fragility in keeping rules aligned with spec updates.
- **Compared to other submissions:** Code discipline is high; documentation discipline is absent. Any peer shipping even a short DESIGN.md with named trade-offs lands a band higher even with weaker code.
- **Phase 2 conversation readiness:** Low — no shared design reference. Conversations would devolve into reverse-engineering the code.

---

## Appendix: Key Files Reviewed

- `/Users/stevelam/workspace/coding-challenges-thuc/coding-challenge-2/thucpham/README.md` — sole documentation
- `/Users/stevelam/workspace/coding-challenges-thuc/coding-challenge-2/thucpham/main.ts` — composition root; **VN routing bug at lines 41-43**
- `/Users/stevelam/workspace/coding-challenges-thuc/coding-challenge-2/thucpham/domain/SmsStatus.ts` — 7 states with exact spec names
- `/Users/stevelam/workspace/coding-challenges-thuc/coding-challenge-2/thucpham/domain/StateMachine.ts` — 8 transitions (6 spec + 2 retry); central `transition()`
- `/Users/stevelam/workspace/coding-challenges-thuc/coding-challenge-2/thucpham/domain/SmsMessage.ts` — public-mutable bag of fields
- `/Users/stevelam/workspace/coding-challenges-thuc/coding-challenge-2/thucpham/domain/SmsAuditRecord.ts` — audit row shape
- `/Users/stevelam/workspace/coding-challenges-thuc/coding-challenge-2/thucpham/providers/SmsProvider.ts` — interface
- `/Users/stevelam/workspace/coding-challenges-thuc/coding-challenge-2/thucpham/providers/Providers.ts` — 7 adapters + BaseAdapter with `console.log` side effect
- `/Users/stevelam/workspace/coding-challenges-thuc/coding-challenge-2/thucpham/providers/ProviderRegistry.ts` — name-keyed registry
- `/Users/stevelam/workspace/coding-challenges-thuc/coding-challenge-2/thucpham/routing/CarrierDetector.ts` — prefix-table detector
- `/Users/stevelam/workspace/coding-challenges-thuc/coding-challenge-2/thucpham/routing/RoutingTable.ts` — rules-as-data Map
- `/Users/stevelam/workspace/coding-challenges-thuc/coding-challenge-2/thucpham/routing/RoutingEngine.ts` — resolver
- `/Users/stevelam/workspace/coding-challenges-thuc/coding-challenge-2/thucpham/application/SmsService.ts` — orchestrator (send + retry)
- `/Users/stevelam/workspace/coding-challenges-thuc/coding-challenge-2/thucpham/application/CallbackHandler.ts` — non-spec `errorCode` parameter drives retry strategy
- `/Users/stevelam/workspace/coding-challenges-thuc/coding-challenge-2/thucpham/application/RetryStrategy.ts` — genuine Strategy pattern, undocumented
- `/Users/stevelam/workspace/coding-challenges-thuc/coding-challenge-2/thucpham/application/CostReport.ts` — aggregations
- `/Users/stevelam/workspace/coding-challenges-thuc/coding-challenge-2/thucpham/infrastructure/SmsRepository.ts` — in-memory store
- `/Users/stevelam/workspace/coding-challenges-thuc/coding-challenge-2/thucpham/cli.ts` — `@clack/prompts` UI; hardcoded provider list at line 329
- `/Users/stevelam/workspace/coding-challenges-thuc/coding-challenge-2/thucpham/test/flows.test.ts` — duplicated routing seed at 38-47, codifies VN bug at line 90
