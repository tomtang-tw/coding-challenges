# Improvement Feedback: Susan Henriquez & Anh Le (`susan-anhle`)

**Challenge:** Coding Challenge #2 — SMS Sending Capability
**Design Tier:** ⚠️ Partial

---

## What You Did Well

1. **Event-sourced lifecycle with real history** — `InmemoryEventRepository` + `Message.java` rehydration + 4 working projectors (cost, volume, delivery) give genuine reconstructable lifecycle. The event-sourcing investment pays off in the reporting endpoints.

2. **Routing data shape is correct** — `agreements.json` reflects VN US3 updates and the new PH market exactly per spec. Carrier resolution uses longest-prefix matching — deterministic.

3. **Centralized state guards in aggregate** — `Message.java:70-101` keeps state mutations behind guarded methods with idempotency for happy-path replays. The pattern shape is correct, even where the transition set has gaps.

4. **Genuine ADR** — `0001-event-as-a-source-of-truth.md` makes a real X-vs-Y trade-off (event log vs. CRUD update). This is the only doc in your submission with a genuine "we chose X because Y" structure.

---

## What Held You Back (and How to Fix It)

### 1. Add the Missing `SendFailed` State + Fix `Carrier-rejected` Source + Open Retry Guards

**The problem — this is the biggest issue:** Your lifecycle is missing the spec's `Send-failed` state entirely. `Carrier-rejected` is guarded on the wrong source state (`Queued` instead of `SentToCarrier`). Both retry transitions back to `Send-to-provider` are impossible because the guards block them.

Concretely:
- `Message.java:103-105` enum has `RoutePlanCalculated` instead of `SendFailed`
- `Message.java:90-94` allows only `Queued → CarrierRejected` (spec: `SentToCarrier → CarrierRejected`)
- `Message.java:84-86` allows only `RoutePlanCalculated → SentToProvider` (retry needs `CarrierRejected → SentToProvider` and `SendFailed → SentToProvider`)

**The fix:**
1. Add `SendFailed` to the state enum
2. Add `markAsSendFailed` with source state `SentToCarrier`
3. Change `markAsCarrierRejected`'s allowed source to `SentToCarrier`
4. Loosen `markAsSentToProvider`'s guard to accept `RoutePlanCalculated OR CarrierRejected OR SendFailed`
5. Add a transition-table data structure (`Map<State, Set<State>>`) so the 6 transitions are visible in one place

### 2. Wire All 10 Spec Providers in the Dispatcher

**The problem:** `AppConfig.java:65-70` registers only Twilio and Vonage. Your `agreements.json` references 7 other providers. `ProviderMessageDispatcher.dispatch` doesn't null-check the map lookup — so 70% of spec routes silently NPE, caught by the swallowing `try/catch` in `SendSmsHandler.java:37-39` and returned as bare HTTP 500.

**The fix:** Either:
- (a) Implement the 5 missing adapters as no-op stubs analogous to your existing Twilio/Vonage ones, or
- (b) At startup, walk `agreements.json` and assert every distinct `providerId` has a registered adapter — throw `IllegalStateException` at boot if not

Option (a) is the more honest spec compliance.

### 3. Decouple Routing Strategy from Cost-Optimisation

**The problem:** `AgreementRepository.getCheapestAgreement` lives in the repo interface. The spec explicitly says cost optimisation is out of scope. Your "Strategy pattern" claim in the README is undermined: swapping the strategy requires changing the repo interface. `AIProviderRouting.java` is a dead stub returning nulls — false pattern theatre.

**The fix:**
- Add a `findAgreement(country, carrierId): Agreement` to the repo (single deterministic lookup)
- Make the default routing strategy `DeterministicRouting`
- Remove `AIProviderRouting` (dead code)
- Keep `CheapestProviderRouting` only if you demonstrate strategy swap and document the trade-off

### 4. Add `provider` to `CallbackMessage`

**The problem:** Your `CallbackSimulation.CallbackMessage` is `(messageId, state, actualCost)`. The spec requires `messageId, provider, newState, actualCost`. This means observability cannot record which provider actually fired the callback.

**The fix:** Add `provider` to the record. Populate it in relevant events. Update the dev guide curl examples.

### 5. Rewrite "Design Decisions" as Challenge-Area Trade-offs

**The problem:** `README.md:42-54` lists patterns ("Event Sourcing", "CQRS", "Strategy pattern") as descriptions. The Mermaid class diagram annotates components with `<<Strategy>>` / `<<Adapter>>` stereotypes — but pattern labels are not reasoning.

**The fix:** Replace the pattern list with 5 short sections, one per challenge area:
- **Provider seam:** "We chose `Map<Provider, Gateway>` over switch on provider name because..."
- **Routing:** "Rules live in `agreements.json` because... alternative: code-side Map which loses hot-edit"
- **State machine:** 7 states + 6 transitions, state diagram, why centralised, how retry works
- **Callbacks:** How the dispatcher maps event class → handler; why this over a chain
- **Spec ambiguities:** Explicit positions on idempotency, VN rule versioning, cost currency

### 6. Remove the Swallowing Catch-All

**The problem:** `SendSmsHandler.java:37-39` catches all exceptions and returns `Result.failure(e)`. `PostSMS.java:22-28` collapses everything to bare HTTP 500. Validation errors (400), illegal transitions (409), missing adapters (500), and infra failures are all identical.

**The fix:** Throw custom exceptions (`InvalidRequestException`, `IllegalTransitionException`, `ProviderNotConfiguredException`). Map them to 400/409/500 with structured error bodies.

### 7. Fix the Broken Doc Reference

`README.md:347` cites `doc/e2e-sms-test-scenario.md` which doesn't exist on disk — only `e2e-sms-developer-guide.md` is present. And `README.md:230` reads "AI is not that smart" — an unresolved candid admission that hurts design trust.

---

**The takeaway:** Your architectural ambition (event sourcing, CQRS, projectors) was the right instinct — but the spec's 7-state lifecycle is non-negotiable. The event-sourcing chassis underneath is sound; it's the spec-conformance layer on top that needs rework. A working demo that covers 100% of spec routes with the correct state machine would have changed the outcome significantly.
