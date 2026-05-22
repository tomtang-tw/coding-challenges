# Improvement Feedback: Harry Nguyen (`harrynguyen`)

**Challenge:** Coding Challenge #2 — SMS Sending Capability
**Design Tier:** ❌ Missing

---

## What You Did Well

1. **Correct US3 routing rules and PH new market** — `internal/providers/router.go:60-72` implements all 10 spec routes correctly (Viettel→Vonage, Mobifone→Infobip, Vinaphone→Twilio, full PH triple). You were one of only two teams to get this right.

2. **Centralized state machine with recovery taxonomy** — `IsValidTransition` + `UpdateStatus` + `applyStatusWithRecovery` handle out-of-order webhook scenarios with a named recovery taxonomy. This shows real engineering thoughtfulness.

3. **Clean provider abstraction** — `SMSProvider` interface + `SimpleProviderRouter` keep provider selection data-driven and free of switch statements in core orchestration.

4. **Rich observability** — Per-message status logs, observer pattern with global + per-source scopes, cost tracker aggregating by provider AND country, `/api/sms/stats` endpoint.

5. **Resilience testing with race detector** — `resilience_test.go` + `prove-resilience.sh` cover concurrent send, duplicate-callback convergence, rate-limit precision, and recovery invariants.

---

## What Held You Back (and How to Fix It)

### 1. Write a `DESIGN.md`

**The problem:** README is install/run/test only. The SPECS_KIT docs are spec restatements + test matrices. No file contains *"we chose X over Y because Z"*. This triggers the Missing-tier design gate, which caps three design-led categories and zeroes the Architectural Thinking bonus.

**Your implementation earned a lot more than the caps allow.** The same code with a written design narrative would place significantly higher.

**The fix:** Add a single `DESIGN.md` covering:
- **Provider integration:** interface + registry; why not a switch; how to onboard a new provider in 2 file edits
- **Routing:** rules as a flat in-code map; why not config file or rule engine; how a rule change propagates
- **State machine:** why centralised guard; why the recursive auto-fallback in `UpdateStatus`; cancel-vs-retry position
- **Callbacks:** recovery taxonomy + invariants from SPECS_KIT
- **Cross-domain reuse:** domain-agnostic API + send-source observer
- **Spec ambiguities:** name 2-3 positions explicitly

### 2. Remove Out-of-Scope Infrastructure

**The problem:** Postgres + GORM, Redis + BullMQ, Prometheus/Grafana/Loki/Promtail, React SPA + Nginx, Playwright E2E, bcrypt auth — all explicitly listed as out of scope in the challenge brief. The brief says "in-memory storage + CLI is sufficient."

**The fix:** Replace with `InMemRepository` (already exists!) and a CLI/scripted trigger or minimal HTTP handler. Move telemetry to in-process slog only. The SMS domain is the deliverable, not the deployment infrastructure.

### 3. Make Auto-Fallback an Explicit Pipeline Step

**The problem:** `UpdateStatus` at `internal/sms/status.go:25-29` recursively calls itself when it encounters `Carrier-rejected` or `Send-failed`:
```go
if newStatus == StatusCarrierRejected || newStatus == StatusSendFailed {
    return s.UpdateStatus(ctx, msg, StatusSendToProvider, "Automatic fallback triggered")
}
```
A function named `UpdateStatus` silently also retries. Phase 2 retry-policy changes have to edit the state mutator.

**The fix:** Move the fallback to a dedicated `applyRetryPolicy(msg, reason)` step called *after* the basic state write returns. Document the rule in DESIGN.md.

### 4. Promote `CarrierResolver` to a Registry Like the Router

**The problem:** `internal/carrier/resolver.go:24-79` is a single nested switch over phone prefixes. Adding a new country requires editing the resolver function.

**The fix:** Define `PrefixRule{country, prefix, carrier}` rows in a map or slice. `Resolve` does a longest-prefix lookup. Then "new market" is uniformly a data edit.

### 5. Consolidate SPECS_KIT Docs and Add a "Start Here" Pointer

**The problem:** Three SPECS_KIT files at root, three duplicates under `specs-kit/`, an architecture image in `docs/`, no README pointer to any of them. Reviewers can't tell which is canonical.

**The fix:** Delete the `specs-kit/` duplicates. Add a "## Design" section in README linking to `DESIGN.md` (primary) + the recovery spec + the architecture diagram.

### 6. Extract Duplicated Cost-Simulation Logic

**The problem:** `internal/providers/twilio.go:32-72` and `provider.go:36-72` share a character-class scan with only rate values differing.

**The fix:** Extract `simulateCost(content, normalRate, specialRate)` into a helper inside `internal/providers/`.

---

**The takeaway:** Your implementation engineering is already strong; the gap is entirely in design communication and scope discipline. The same code with a 2-page `DESIGN.md` naming trade-offs — and without the out-of-scope infrastructure — would place in a significantly higher band.
