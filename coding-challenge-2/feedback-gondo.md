# Improvement Feedback: Go & Do (`gondo`)

**Challenge:** Coding Challenge #2 — SMS Sending Capability
**Design Tier:** ⚠️ Partial

---

## What You Did Well

Your submission was the **top-ranked** in the cohort. Here's what stood out:

1. **Time-versioned routing rules** — Maintaining both US1 (v1) and US3 (v2) with `effective_from`/`effective_to` in `alembic/versions/0002_seed_data.py` was the best single design choice in the entire cohort. This directly addresses the "how does routing evolve?" question.

2. **All 10 spec routes correct** — You were one of only two teams to deliver the full User Story 3 Vietnam update AND the Philippines new market. This is the most concrete evidence of reading and integrating the spec carefully.

3. **Real design reasoning exists** — `diagrams/notification-state-machine.md` argues for your interpretation of `Carrier-rejected`; `docs/backlog/challenge-vs-repo-gap-scan.md` addresses three spec ambiguities with reasoned positions. These are the cleanest examples of design-first work in the cohort.

4. **Domain-neutral API with `cost_story`** — The `cost_story` field makes lifecycle-stage cost provenance explicit to consumers. Zero domain coupling; `X-Calling-Domain` header for per-domain KPIs.

5. **Idempotent, validated callbacks** with 30+ test files across services.

---

## What Held You Back (and How to Fix It)

### 1. Fix Design Packaging — Would Lift Partial → Strong

**The problem:** Your design *content* is strong, but the *packaging* is sloppy. Four of five Clarity sub-checks failed:
- Diagrams live two levels above the submission folder (`/coding-challenges/diagrams/`)
- README contains placeholder text in `## Challenges Faced` / `## What We Learned`
- `docs/routing-vs-challenge-brief.md` promises analysis but delivers a verification table
- No clear "start here" signal across 25 docs

**The fix (concrete checklist):**
1. Move `diagrams/` inside the submission folder
2. Remove or fill placeholder sections in README
3. Rename `routing-vs-challenge-brief.md` → `routing-seed-verification.md` and add a separate `routing-design.md` with the temporal-versioning rationale
4. Add a `docs/README.md` designating ONE entry point ("Start here: SUBMISSION.md")

### 2. Enforce the State Machine Guard Centrally

**The problem:** `is_valid_transition()` is documented as the central guard, but `main.py:380, 405, 508, 533` assign `n.state` directly, bypassing the guard. The design diagrams imply central enforcement; the code doesn't deliver it.

**The fix:** Create a single `transition(n, target, source, reason)` function that validates, mutates, and records — then use it as the *only* code path that touches `n.state`.

### 3. Move Per-Country Phone Validation to Data

**The problem:** `phone_validation.py:51-189` hardcodes per-country if-elif blocks. Adding a new country requires a Python edit, contradicting the "data-driven" promise.

**The fix:** A `country_phone_rules` table/YAML with `country_code, calling_code, length_min, length_max, subscriber_prefixes`. The validator reads the rule instead of branching.

### 4. Introduce a Real `ProviderAdapter` Contract

**The problem:** Providers are pure config (YAML + DB row). No per-provider code. First provider needing signed payloads or custom callback parsing forces the pattern to be retrofitted.

**The fix:** A `Protocol` (or ABC) `ProviderAdapter` with `prepare_send(message)` and `parse_callback(payload)`. Document the seam with named alternatives.

### 5. Practice the Discipline You Named

**The observation:** `README.md:44-46` acknowledges that the system could ship as one process — you named the leaner alternative. But you shipped the elaborate version anyway (Postgres, NATS, gRPC, admin UI). The brief's evaluation criteria favour "domain design, abstraction, and adaptability — NOT infrastructure."

**The advice:** Either lean into simplicity (drop the infra), or justify each component in `SUBMISSION.md` tied to a specific spec-level extension point. Drop anything that doesn't justify.

---

**The takeaway:** Your design content is already close to Strong tier; it's the packaging that's holding it back. This is the most fixable gap in the cohort.
