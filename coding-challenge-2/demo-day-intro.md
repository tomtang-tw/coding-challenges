# 🎬 Coding Challenge 2 — Demo Day Kick-off

Welcome, teams! 👋 Here's the stage you've been building for.

---

## 🎯 The Mission, in One Breath

For this round, you stepped into the shoes of the **Customer Domain team at a regional Cinema Ticket Booking Platform** — already live in 🇻🇳 Vietnam, 🇹🇭 Thailand, and 🇸🇬 Singapore, and now expanding into 🇵🇭 **the Philippines**.

Your mission: build a **shared SMS Sending Capability**. 📩
The first user is **OTP verification** for membership registration — but very quickly it has to serve **Booking, ERP, Accounting, Operations, and Data**, all calling the same module.

---

## 🧩 What You Built

Concretely, each team designed a system that:

- 📨 **Accepts a request** of `(messageId, country, phoneNumber, message)` from **any** domain.
- 🧭 **Picks exactly one provider** via a `(country + carrier) → provider` routing table — and lets that table change when we add the Philippines or update Vietnam's rules, **without rewriting code**.
- 🔄 **Walks each message through the full lifecycle** — `New → Send-to-provider → Queue → Send-to-carrier → Send-success`, plus failure and retry paths.
- 💰 **Tracks cost** — _estimated_ when it hands off to a provider, _actual_ when delivery succeeds.
- 📡 **Handles async provider callbacks** for state and cost updates.
- 🔍 **Stays traceable** — so Accounting and Operations can answer real questions about volume, cost, and success rate.

> 💡 All provider, queue, and database interactions are **simulated** — the goal isn't infrastructure, it's **design**.

---

## 🔭 What We're Watching For

As you present, walk us through these three things — and audience, listen for them too:

| #   | Focus Area                                       | The Question We Want You to Answer                                                                                            |
| --- | ------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------- |
| 1️⃣  | 🧠 **Problem understanding & design approach**   | How did you read the problem, and what shape did your solution take?                                                          |
| 2️⃣  | ⚖️ **Key trade-offs**                            | What did you intentionally choose _not_ to do, and why?                                                                       |
| 3️⃣  | 🏗️ **Scalability, maintainability & performance** | How does your design absorb new countries, new providers, new domains, and higher volume — without becoming a big ball of mud? |

---

## 🎉 One Last Thing

Demo Day is a **celebration**, not a job interview.

- 🙌 Cheer the bold ideas.
- 🤔 Ask the curious questions.
- 😄 Have fun.

**First team — the stage is yours.** 🎤
