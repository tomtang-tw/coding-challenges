# Coding Challenge 2 — Demo Day Kick-off Introduction

For this round, our participants stepped into the shoes of the **Customer Domain team at a regional Cinema Ticket Booking Platform** — already live in Vietnam, Thailand, and Singapore, and now expanding into the **Philippines**.

The mission: build a **shared SMS Sending Capability**. The first user is OTP verification for membership registration, but very quickly it has to serve **Booking, ERP, Accounting, Operations, and Data** — all calling the same module.

Concretely, each team had to design a system that:

- Accepts a request of `(messageId, country, phoneNumber, message)` from **any** domain;
- Picks **exactly one** SMS provider based on a `(country + carrier) → provider` routing table — and lets that table change when we add the Philippines or update Vietnam's rules, **without rewriting code**;
- Walks each message through a full lifecycle — `New → Send-to-provider → Queue → Send-to-carrier → Send-success`, plus failure and retry paths;
- Captures **estimated cost** when it hands off to a provider, and **actual cost** when delivery succeeds;
- Handles **async provider callbacks** for state and cost updates;
- And makes all of this **traceable** — so Accounting and Operations can answer real questions about volume, cost, and success rate.

All provider, queue, and database interactions are **simulated** — the goal isn't infrastructure, it's **design**.

**What to Watch For**

1. **Problem understanding & design approach** — How did they read the problem, and what shape did their solution take?
2. **Key trade-offs** — What did they intentionally choose _not_ to do, and why?
3. **Scalability, maintainability, and performance** — How does their design absorb new countries, new providers, new domains, and higher volume — without becoming the next "big ball of mud"?

**Close**

Remember — Demo Day is a **celebration**, not a job interview. Cheer the bold ideas, ask the curious questions, and have fun.