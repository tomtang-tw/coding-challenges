# Improvement Feedback: Thuc Pham (`thucpham`)

**Challenge:** Coding Challenge #2 — SMS Sending Capability
**Design Tier:** ❌ Missing

---

## What You Did Well

1. **Textbook centralized state machine** — `domain/StateMachine.ts:9-30` declares all 8 valid transitions as data, and `transition()` is the only mutation path. Invalid transitions throw with a meaningful message. Every transition pushes an audit record. This is the strongest part of your submission and genuinely Phase 2-ready.

2. **Genuine adapter + registry seam** — `providers/SmsProvider.ts` interface + `providers/ProviderRegistry.ts` keyed registry + 7 concrete adapters in `providers/Providers.ts`. Adding a provider is "new class + one `registry.register()` call." No `switch` on provider name anywhere.

3. **Strategy-based retry resolver** — `application/RetryStrategy.ts` defines a clean interface with two implementations resolved by error code (`RATE_LIMITED` → same-provider, otherwise → fallback). Addresses the transient vs permanent retry ambiguity with a real seam.

4. **Domain-agnostic public API** — `SmsService.send(id, country, phoneNumber, message)` has zero domain knowledge. Marketing, Notifications, OTP can all call it identically.

5. **Clean DDD-style layering** — `domain/`, `application/`, `infrastructure/`, `providers/`, `routing/` — each layer has a focused responsibility.

---

## What Held You Back (and How to Fix It)

### 1. Write a `DESIGN.md`

**The problem:** Your README is ~90 lines of install instructions + a file-tree with one-line role captions + a test list. There is no design narrative — no problem framing, no trade-offs, no diagrams, no challenge-focused reasoning. This triggers the Missing-tier design gate, which caps the three design-led categories and zeroes the Architectural Thinking bonus.

**Your code would earn significantly more without the caps.** The same implementation with a written design narrative would place in a higher band.

**The fix:** Add a ~2 page `DESIGN.md` next to `README.md` covering:
- **Provider integration:** "Adapter interface + name-keyed registry. Alternative: a switch in SmsService.send — would have required editing core on every provider add."
- **Routing:** "Rules-as-data in RoutingTable as Map. Alternative: inline conditionals or JSON config. Trade-off: rules in code, so rule-engine extension is possible but currently a Map keeps it minimal."
- **State machine:** "Centralised transition table + canTransition + transition as the only mutation path." Include a state diagram. Address retry semantics.
- **Callbacks:** Position on `errorCode` extension.
- **Cross-domain reuse:** "Public API takes only generic SMS fields."
- **Spec ambiguities:** "Cancel vs retry — chose retry"; "VN rule versioning — current US3 only"; "Idempotency on duplicate messageId — out of scope."

### 2. Fix Vietnam Routing to Match User Story 3

**The problem:** `main.ts:41-43` encodes the old US1 mappings:
```typescript
table.addRule("VN", "Viettel",   ["Twilio", "Vonage"]);     // should be Vonage
table.addRule("VN", "Mobifone",  ["Vonage", "Twilio", "Infobip"]); // should be Infobip
table.addRule("VN", "Vinaphone", ["Vonage", "Twilio"]);     // should be Twilio
```
The test at `test/flows.test.ts:90` locks the bug in: `expect(sms.provider).toBe("Twilio")` for Viettel.

**The fix:**
```typescript
table.addRule("VN", "Viettel",   ["Vonage", "Twilio"]);
table.addRule("VN", "Mobifone",  ["Infobip", "Vonage"]);
table.addRule("VN", "Vinaphone", ["Twilio", "Vonage"]);
```
And update the test assertion accordingly.

### 3. Extract the Default Routing Seed to a Single Module

**The problem:** `main.ts:40-55` and `test/flows.test.ts:38-47` both spell out the entire routing table. Any rule change requires editing two files. The test confirms the production rules by *re-declaring* them — and it locked the VN bug in.

**The fix:** Create `routing/DefaultRoutingRules.ts`:
```typescript
export function seedRoutingTable(table: RoutingTable): void {
  table.addRule("VN", "Viettel", ["Vonage", "Twilio"]);
  // ... all rules ...
}
```
Both `main.ts` and `flows.test.ts` call `seedRoutingTable(table)`.

### 4. Encapsulate `SmsMessage` and Introduce an Error Taxonomy

**The problem:**
- `domain/SmsMessage.ts:9-19` declares every field as public-mutable. `SmsService.send` mutates them directly. Anything can rewrite any field without going through the state machine.
- Every error site throws generic `new Error(...)`. Callers cannot distinguish "no carrier" from "invalid transition" without string-matching.

**The fix:**
- Mark fields `readonly` and expose setters that participate in audit (`assignProvider(name)`, `setActualCost(value)` with guards)
- Add a small error hierarchy: `InvalidTransitionError`, `CarrierDetectionError`, `RoutingError`, `ProviderNotRegisteredError`

### 5. Align Callback Signature with the Spec

**The problem:** `CallbackHandler.handle(messageId, newState, actualCost?, errorCode?)` — spec says `messageId, provider, newState, actualCost`. Missing `provider`, adds non-spec `errorCode`.

**The fix:** Add `provider` to the parameter list. Keep `errorCode` as an optional extension and explain it in the design doc.

### 6. Use the Registry in the CLI Cost Report

**The problem:** `cli.ts:329` hardcodes `providers = ["Vonage", "Twilio", ...]` instead of iterating `registry.getAll()`. Adding a provider requires editing 3 places.

**The fix:** Use `registry.getAll().map(p => p.name)` in the report. One less place to edit.

---

**The takeaway:** Your code shows you *can* design well — centralised state machine, clean registry, Strategy pattern for retry. You just didn't write down *why*. A 2-page `DESIGN.md` would have transformed the outcome. The VN routing data error compounded the problem — always verify your routing table against the latest spec version.
