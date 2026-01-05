# 🧩 Coding Challenge #1 (Adjusted)

## **Build a Smart Team Rotator API – v1**

> A minimal service that returns fair rotations for a team using simple rules.
> 

---

## 🎯 Goal (Very Clear)

Build a **small API or library** that:

- Maintains a team of members
- Returns the **next person** (or next N people) for a duty
- Ensures **no immediate repetition**
- Is easy to understand, test, and explain

This is **not** about completeness — it’s about **clarity and correctness**.

---

## ⏱️ Timebox

- **4–6 hours total**
- Aim for **one clean solution**, not multiple strategies

---

## 🛠️ Simplified Functional Requirements

### 1️⃣ Team & Members (Minimal)

- A team has a list of members
- A member has:
    - `id`
    - `name`
    - `isActive` (boolean)

👉 No CRUD API required

👉 Hard-coded or in-memory setup is acceptable

---

### 2️⃣ Rotation Logic (Core Focus)

- API/function returns:
    - The **next member** (default)
    - Optionally the **next N members**
- Rules:
    - Must not return the **same member twice in a row**
    - Must **skip inactive members**
    - Rotation must be **fair over time** (simple round-robin is fine)

---

### 3️⃣ History (Lightweight)

- Track **last selected member only**
- Full history is **NOT required**

📌 This keeps state management trivial.

---

## 🚫 Explicitly Out of Scope

To avoid over-engineering:

- ❌ Database persistence
- ❌ Multiple rotation strategies
- ❌ Authentication
- ❌ UI
- ❌ Concurrency handling
- ❌ Distributed systems

---

## 🧪 Testing (Focused)

- Unit tests for:
    - No immediate repetition
    - Skipping inactive members
    - Rotation order correctness

👉 3–5 tests is enough.

---

## 📦 Deliverables

Each team submits:

- Git repository
- README (max 1 page) including:
    - How to run
    - Rotation approach
    - One trade-off they consciously made