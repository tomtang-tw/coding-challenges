# Improvement Feedback: Anh Phan, Hoa Nguyen, Henry (`anhphan-hoanguyen-henry`)

**Challenge:** Coding Challenge #2 — SMS Sending Capability
**Design Tier:** ❌ Missing

---

## What You Did Well

1. **State machine has the right shape** — `SMSMessage.CanTransitionTo` correctly implements all 7 states and all 6+2 transitions. This is the strongest single artifact in your submission. The transition table is the spec's transition table — you read it correctly.

2. **Routing-as-data via `ProviderAgreement`** — `internal/models/provider_agreement.go` + `internal/repositories/provider_agreement_repository.go` give a clean seam: a rule is a row. The shape is right, even though only 1 of 10 rows is seeded.

3. **High unit-test coverage** — 86–100% per package per your README, and the test files exercise edge cases (missing agreements, message-not-found, invalid transition from NEW to SEND_SUCCESS).

4. **Domain-neutral types** — no `CustomerOtpSMSService` or domain-specific names. `SMSMessage`, `Sender`, `Recipient`, `SendSMSRequest` — the API surface is clean and reusable.

---

## What Held You Back (and How to Fix It)

### 1. Implement User Story 3 — Vietnam Updated + PH + TH + SG

**The problem — the biggest issue:** `main.go:74-99` seeds only Vietnam/Viettel/Twilio — still on User Story 1. The brief is explicit: 4 countries, ~10 carriers, with the VN mapping *updated* in US3. No Thailand, Singapore, or Philippines carriers exist. This makes the entire submission read like a Story 1 prototype.

**The fix:**
1. Externalize the seed into `config/routing.yaml` (or a Go fixture) with all 10 carrier→provider rows from the brief
2. Implement Infobip, AWS SNS, Telnyx, MessageBird, Sinch clients (stubs are fine — return a hardcoded cost like your existing two)
3. Wire them in `main.go`

### 2. Replace the Provider `switch` with a Registry

**The problem:** `provider_client.go:34-43` is:
```go
func GetProviderAndClient(enum ProviderEnum) (ProviderAPIClient, error) {
    switch enum {
    case Twilio: return &TwilioAPIClient{}, nil
    case Vonage: return &VonageAPIClient{}, nil
    default: return nil, fmt.Errorf("provider not supported: %s", enum)
    }
}
```
Adding a provider means editing this function. The "open/closed" property promised in the README is undermined.

**The fix:** A `map[ProviderID]ProviderAPIClient` populated at startup in `main.go`:
```go
type ProviderClientRegistry map[ProviderID]ProviderAPIClient

func (r ProviderClientRegistry) Get(id ProviderID) (ProviderAPIClient, error) {
    c, ok := r[id]
    if !ok { return nil, fmt.Errorf("no client for provider %s", id) }
    return c, nil
}
```
New provider = new client + one line in `main.go`. No edits to the registry type.

### 3. Centralize State Mutation via a `Transition` Helper

**The problem:** `message.Status = newStatus` in 4 places (`sms_message_handler.go:121, 213, 222, 232`). The guard `CanTransitionTo` is called before but is bypassable — a future contributor can skip the guard with one line.

**The fix:**
```go
func (m *SMSMessage) Transition(to SMSStatus, at time.Time) error {
    if !m.CanTransitionTo(to) {
        return fmt.Errorf("invalid transition %s → %s", m.Status, to)
    }
    m.Status = to
    m.UpdatedAt = at
    m.History = append(m.History, StatusEvent{From: m.Status, To: to, At: at})
    return nil
}
```
Make `Status` private so direct assignment is impossible.

### 4. Add a Status-History Table and Aggregation Queries

**The problem:** `SMSMessage` stores only the latest `Status`. No history = cannot reconstruct lifecycle. No aggregation endpoints = cannot satisfy Story 5 ("Total cost per provider", "Total cost per country", "volume per provider", "success/failure rates").

**The fix:**
- `StatusEvent{From, To, At, Reason}` slice on the message (free with Suggestion 3 above)
- A `MessageRepository.Aggregate(filter) (Stats, error)` method that scans in-memory and returns the four aggregations
- Add `GET /api/v1/sms/stats`

### 5. Move `SendSMS` Orchestration into a Service

**The problem:** `sms_message_handler.go:39-150` does parsing, country lookup, carrier lookup, message creation, save, provider selection, decision save, status mutation, second save, response shaping — 8 concerns in one ~110-line method.

**The fix:** Extract `SendSMSService.Handle(ctx, req)` returning a result. The HTTP handler becomes: parse → service.Handle → render.

### 6. Write a Real DESIGN.md with Challenge-Level Trade-offs

**The problem:** Your README's "Trade-offs" section lists 5 trade-offs — all about Go tooling:
- Procedural vs OOP
- Composition vs Inheritance
- In-Memory vs DB
- Manual DI vs IoC
- Single Selector Strategy

None of these are about the SMS challenge problem space. The Design-First gate measures *challenge-problem* trade-offs.

**The fix:** Add `docs/DESIGN.md` with at least 4 reasoned trade-offs:
1. **Routing storage:** agreements-as-data vs. config map vs. rule engine — chose X because Y
2. **State machine location:** aggregate-owned guard vs. service-tier orchestrator vs. library — chose X because Y
3. **Provider adapter resolution:** registry vs. switch vs. plugin discovery — chose X because Y
4. **Callback handling:** webhook → handler → message vs. callback → event bus — chose X because Y
5. **Spec ambiguity stance:** cancel-vs-retry, idempotency, VN rule versioning

### 7. Remove Scope Drift

**The problem:** `gorm.io/gorm` in `go.mod` (unused), `godotenv.Load()` mandatory in `main.go:26-28`. The brief says "CLI or simple triggers", "in-memory only."

**The fix:** Remove `gorm` from `go.mod`. Make `.env` optional.

---

**The takeaway:** The foundation is there — your state machine table is correct, your routing-as-data shape is clean, your domain types are neutral. But the challenge asked for 4 countries and 10 routes, and you delivered 1 country and 1 route. The single most impactful improvement is finishing the spec: seed all routes, implement all provider stubs, and write down *why* you chose this shape. The code structure can carry it; the data just needs to be there.
