# Coding Challenge #1 Review: Hoa Nguyen

**Review Date:** 2026-01-27
**PR URL:** <https://github.com/twvn-dev-community-coding-challenge/coding-challenges/pull/1>
**Reviewer:** AI Code Reviewer
**Tech Stack:** Python 3.14, uv, pytest, hatchling

---

## 🏆 FINAL RECOMMENDATION

> **🔴 DO NOT RECOMMEND**

**Ranking Justification:** While the core rotation algorithm (modulo arithmetic) works for the happy path, the submission has a critical bug (`add_member` always increments `active_count` regardless of actual status, causing an infinite loop when inactive members are added), debug artifacts in production code (`print()` on line 23), commented-out code blocks, dead code (`prev_id` is set but never read), no formal design pattern, and only 4 test functions with minimal edge case coverage. The engineering maturity is insufficient for the final round.

**For Final Selection Panel:** This submission demonstrates a working prototype for the simplest scenario but falls significantly short on code quality, testing rigor, and design pattern application. The `active_count` bug alone is a fundamental reliability issue. Not recommended for advancement.

---

## Score Summary

| Category | Score | Max | Grade |
|----------|-------|-----|-------|
| Core Logic | 30/45 | 45 | C |
| Code Quality | 10/25 | 25 | D |
| Tests | 6/15 | 15 | D |
| Communication | 7/15 | 15 | C- |
| **Subtotal** | **53/100** | **100** | |
| Bonus | +0/20 | +20 | |
| **Total** | **53/120** | **120** | |

**Score Grade**: D (<75)

---

## 🚨 Critical Issues

| Issue | Severity | Impact |
|-------|----------|--------|
| `add_member` always increments `active_count` regardless of member status — causes infinite loop when inactive members are added then `get_next()` is called | Critical | -3 (Skips Inactive), -3 (Fair Rotation), -3 (Rotation Order) |
| `print("Current members: ", self.members)` debug artifact in production code (`member_manager.py:23`) | Major | -1 (Clean Code) |
| `prev_id` set but never read — dead code indicating unfinished no-repetition implementation | Major | -3 (No Immediate Repetition) |
| Commented-out code blocks in `get_next()` (`member_manager.py:64-68`) | Major | -1 (YAGNI) |
| No formal design pattern implemented | Major | -7 (Design Pattern) |

---

## Detailed Scoring (STRICT)

### 1️⃣ Core Logic Correctness — 30/45

| Criteria | Max | Score | Justification |
|----------|-----|-------|---------------|
| No immediate repetition | 10 | 7/10 | Works **implicitly** via counter advancement (counter moves past each selected member). However, `prev_id` is set (`member_manager.py:77`) but **never read** anywhere — dead code suggesting developer intended explicit check but never implemented it. No test specifically verifies no-repetition. |
| Skips inactive members | 10 | 6/10 | Basic filtering works in `get_next()` (checks `member.status == Status.isActive`). **BUG:** `add_member()` always does `self.active_count += 1` (`member_manager.py:29`) regardless of member's actual status. Adding an inactive member inflates `active_count`, causing `get_next()` to infinite-loop when no active members exist. `change_status()` correctly adjusts count. All-inactive returns `[]` silently (no error). |
| Fair rotation over time | 15 | 10/15 | Counter-based modulo (`counter % member_count + 1`) gives fair round-robin for active members. Each active member selected before any repeats. But `min(n, active_count)` caps batch returns (no cycling). The `active_count` bug undermines confidence in fairness guarantees. |
| Correct rotation order | 10 | 7/10 | Deterministic via counter. Order is Alice → Bob → Charlie → Diana → Alice. Consistent across sequential calls as verified in tests. Loses points for `active_count` bug creating incorrect behavior when inactive members are added. |

**Section Assessment:** The core rotation works for the tested happy path (all members added as active, then some deactivated via `change_status`). The modulo arithmetic approach is sound and gives correct round-robin behavior. However, the `active_count` bug is a critical reliability issue — adding a member with inactive status breaks the entire system. The `prev_id` dead code indicates the developer recognized the no-repetition requirement but relied on the counter's implicit behavior rather than implementing it deliberately.

### 2️⃣ Code Quality & Design — 10/25

| Criteria | Max | Score | Justification |
|----------|-----|-------|---------------|
| Design pattern implementation | 10 | 3/10 | No formal design pattern named or implemented. `MemberManager` is a monolithic class handling member storage, status tracking, and rotation logic. No Iterator, Strategy, or State pattern. The README describes classes but never identifies a pattern. |
| Readability & naming | 5 | 2/5 | `ans` variable name (`member_manager.py:72`), `i` as while-loop counter (`member_manager.py:71`), `counter` is vague. `print()` debug artifact in production code. Commented-out code blocks. Some decent naming: `member_count`, `active_count`, `change_status`. |
| Separation of concerns | 5 | 2/5 | `Member` and `Status` are separate files (good). But `MemberManager` is a monolith mixing: member storage (dict operations), state tracking (counters), rotation logic (modulo), and validation (KeyError checks). No separation between storage and rotation. |
| Simplicity (no over-engineering) | 5 | 3/5 | The solution is simple — perhaps too simple (under-engineering). Modulo arithmetic is straightforward. But managing 4 manual counters (`counter`, `member_count`, `active_count`, `prev_id`) is error-prone and more complex than necessary. Could use `len()` and `sum()` instead of manual tracking. |

**Design Pattern Analysis:**

- **Score 3/10:** No formal pattern named in README. No interface, no separation of iteration logic, no strategy abstraction. The "Classes" section in the README describes `MemberManager`, `Member`, `Status` but doesn't reference any design pattern. The modulo arithmetic is an algorithm, not a pattern. This is a significant gap for the challenge requirements.

### 🧹 Clean Code & Principles Audit

> Every deduction MUST have evidence (file path, line number, code snippet).

#### SOLID Compliance

| Principle | Status | Evidence | Deduction |
|-----------|--------|----------|-----------|
| Single Responsibility | ❌ | `member_manager.py` — `MemberManager` handles member CRUD, status management, rotation logic, counter tracking, and validation all in one class | -1 |
| Open/Closed | ❌ | No extensibility mechanism. Cannot add new rotation strategies without modifying `MemberManager`. No interfaces or abstract classes. | -1 |
| Liskov Substitution | N/A | No inheritance hierarchy | 0 |
| Interface Segregation | N/A | No interfaces defined | 0 |
| Dependency Inversion | N/A | No abstractions to invert; single concrete class | 0 |

#### DRY / KISS / YAGNI

| Principle | Violation | Location | Fix / Alternative | Deduction |
|-----------|-----------|----------|-------------------|-----------|
| DRY | None | — | — | 0 |
| KISS | Minor: manual counter tracking | `member_manager.py:9-13` — 4 manual counters (`counter`, `member_count`, `active_count`, `prev_id`) | Use `len(self.members)` and `sum(1 for m in self.members.values() if m.status == Status.isActive)` | 0 |
| YAGNI | Commented-out code blocks | `member_manager.py:64-68` — commented-out `active_count == 0` and `active_count == 1` handlers | Remove or implement; don't leave in final submission | -1 |
| YAGNI | Dead variable `prev_id` | `member_manager.py:13,77` — set but never read | Remove or implement no-repetition check | -1 |

#### Additional Clean Code

| Check | Status | Evidence | Deduction |
|-------|--------|----------|-----------|
| Meaningful names | ❌ | `ans` (`member_manager.py:72`), `i` as while-loop decrementor (`member_manager.py:71`), `counter` (vague — counter of what?) | -1 |
| Small functions | ✅ | All functions under 20 lines | 0 |
| No side effects | ❌ | `add_member()` mutates input `member_info.id = self.member_count + 1` (`member_manager.py:27`) — caller's object is modified | -1 |
| Error handling | ⚠️ | Uses generic `KeyError` with string messages ("User not exist", "Member already exists") instead of custom error classes | -1 |
| Consistent style | ⚠️ | Enum values use camelCase (`isActive`, `isNotActive`) — non-Pythonic, should be `ACTIVE`/`INACTIVE` or `UPPER_SNAKE_CASE` | 0 |
| No debug artifacts | ❌ | `print("Current members: ", self.members)` in production code (`member_manager.py:23`). Commented `# print(f"Getting member id: {member_id}")` (`member_manager.py:74`). Commented `# @pytest.mark.skip` in test files. Commented `# import sys` / `# print(f"Sys path: {sys.path}")` in `fixtures.py:8-9`. | -1 |
| Immutability | ❌ | `member_info.id = self.member_count + 1` (`member_manager.py:27`) — mutates caller's Member object | -1 |
| Type safety | ⚠️ | `member.py:9` — `id: int | None = None` allows None IDs in Member dataclass. Test `test_member_manager.py:60` passes positional args in wrong order: `Member(1, "Alice", Status.isActive)` → name=1, status="Alice", id=Status.isActive. Python dataclass doesn't enforce types at runtime. | -1 |

**Total Clean Code Deductions:** -9 (already reflected in Code Quality scores above)

### 3️⃣ Tests & Reliability — 6/15

| Criteria | Max | Score | Justification |
|----------|-----|-------|---------------|
| Core logic tests | 10 | 5/10 | 4 test functions total. `test_returns_members_in_rotation_order` is a comprehensive multi-step test verifying rotation order, capping, and continuity — but it's one giant function instead of focused individual tests. `test_add_member_with_dup_id` tests duplicate rejection. `test_changes_status` tests status changes. `test_get_member` tests member retrieval. No test for no-repetition rule specifically. |
| Edge cases | 5 | 1/5 | Only 1 edge case covered: getNext(n) > active members (returns max active). Missing: single active member, all inactive, empty team, n=0/n<0/n=1 boundaries, dynamic status change mid-rotation, inactive member added directly. |

**Test Coverage Checklist:**

- [ ] No-repetition tests — NOT tested
- [ ] Skip inactive tests — NOT tested (no test with mixed active/inactive during rotation)
- [x] Rotation order tests — `test_returns_members_in_rotation_order` verifies order across 5 sequential get_next calls
- [x] getNext(n) tests — Tested with n=6, n=3, n=2, n=1 (but all in one test function)
- [ ] Single active member — NOT tested
- [ ] All inactive (error) — NOT tested
- [ ] Empty initialization — NOT tested
- [ ] Boundary conditions — NOT tested (n=0, n<0)

**Section Assessment:** The test suite is minimal. The rotation order test is actually decent — it verifies continuity across multiple get_next calls with different batch sizes and confirms proper wrapping. But it's a single function doing too much. The `module`-scoped fixture (`member_manager_object`) is an anti-pattern — shared mutable state between tests creates order-dependent execution. Critical edge cases (single active, all inactive, empty team, boundaries) are entirely missing. The `test_add_member_with_dup_id` test has incorrect positional argument ordering (type mismatch) that only works because Python doesn't enforce dataclass types at runtime.

### 4️⃣ Communication & Demo — 7/15

| Criteria | Max | Score | Justification |
|----------|-----|-------|---------------|
| Clear explanation | 5 | 3/5 | Algorithm section explains modulo arithmetic with example. "Member storing design" explains dict approach with O(1) complexity. Adequate but has typos ("effecient", "Writting", "froma", "dictonary", "effecient"). |
| Design pattern justification | 5 | 1/5 | No formal design pattern named anywhere. "Classes" section describes `MemberManager`, `Member`, `Status` but never identifies a GoF pattern. No comparison with alternatives. No justification. |
| README clarity | 5 | 3/5 | Has sections: How to Run, Approach, Trade-offs, Challenges, What We Learned, AI Tools Used. Missing: design pattern name (-1), pattern justification (-1). Some typos but generally readable. Jupyter notebook sample is a nice touch. |

**README Checklist:**

- [x] How to run/build — 6-step setup guide with uv, pytest instructions
- [x] Algorithm/approach explained — Modulo arithmetic with example, O(1) complexity analysis
- [ ] Design pattern used — NOT mentioned (no pattern named)
- [ ] Why that pattern — NOT mentioned
- [x] Trade-offs mentioned — Pros/cons of dict approach, cyclic algorithm limitations

**Section Assessment:** The README is the strongest part of the submission. The algorithm explanation with concrete examples (showing modulo wrap-around) demonstrates understanding. The trade-off analysis (incremental ID dependency, O(n) worst case for get_next with inactive members, in-memory limitations) shows honest self-assessment. The "What We Learned" section (uv, pytest, packaging) shows growth mindset. The "AI Tools Used" transparency is appreciated. However, the complete absence of design pattern discussion is a significant gap for the challenge requirements. PR body is just "Hoa Nguyen submission" — no content.

### 🌟 Bonus Points — +0/20

| Bonus | Max | Score | Evidence |
|-------|-----|-------|----------|
| TDD approach | +10 | +0/10 | "First commit + logic + test" — all code and tests committed together. Subsequent 7 commits are README/config updates. No test-first evidence. |
| Junior-led demo | +5 | +0/5 | No evidence in commits or PR notes |
| Elegant simplicity | +5 | +0/5 | Debug `print()` in production, commented-out code, dead `prev_id` variable, `active_count` bug, poor naming (`ans`, `i`) — disqualifies from elegance bonus |

**TDD Commit Analysis:**

| # | Date | Commit | TDD Signal |
|---|------|--------|------------|
| 1 | Jan 28, 09:33 | `First commit + logic + test` | ❌ Big-bang: all code + tests in one commit |
| 2 | Jan 28, 09:48 | `Update gitignore` | — Config |
| 3 | Jan 28, 17:33 | `Update readme, add samples` | — Docs + samples |
| 4 | Jan 28, 18:16 | `Update readme` | — Docs |
| 5 | Jan 29, 02:01 | `Update readme, and member manage init` | — Docs + init |
| 6 | Jan 29, 02:07 | `Update readme to show how to run tests` | — Docs |
| 7 | Jan 29, 02:09 | `Update readme` | — Docs |
| 8 | Jan 29, 02:15 | `Update alog explanation readme` | — Docs |

**Verdict:** No TDD evidence whatsoever. One big-bang commit with all code and tests, followed by 7 documentation-only commits within a day.

---

## Strengths (Be Specific)

1. **Working Happy-Path Rotation:** The modulo arithmetic approach (`counter % member_count + 1`) correctly implements round-robin for active members. The test verifies continuity across 5 sequential `get_next()` calls with different batch sizes and wrapping behavior.

2. **Adequate README Algorithm Explanation:** The "Cyclic algorithm" section provides a concrete mathematical example showing modulo wrap-around, and the "Member storing design" section explains the O(1) dict approach with justification.

3. **Honest Trade-Off Analysis:** The README acknowledges real limitations: incremental ID dependency, O(n) worst case for inactive traversal, in-memory scalability, and sacrificed user flexibility. This self-awareness is valuable.

4. **Modern Python Tooling:** Uses `uv` for project management, `hatchling` for builds, Python 3.14, `@dataclass`, `Enum`, and `pyproject.toml` — modern Python stack.

5. **Jupyter Notebook Sample:** Provides `samples/samples.ipynb` as an interactive demo — a creative touch for a library.

6. **`change_status` Logic:** The `change_status` method correctly handles same-status no-op, adjusts `active_count` on transitions, and validates member existence.

---

## Weaknesses (Be Thorough)

1. **Critical: `active_count` Always Incremented in `add_member`** (`member_manager.py:29`): `self.active_count += 1` runs regardless of the member's actual status. Adding an inactive member inflates `active_count`, and `get_next()` enters an **infinite loop** when no truly active members exist because `min(n, active_count)` produces a nonzero loop bound with no active members to find.

2. **Debug `print()` in Production Code** (`member_manager.py:23`): `print("Current members: ", self.members)` is left in `add_member`. This pollutes stdout on every member addition and indicates unfinished cleanup.

3. **Dead Code: `prev_id` Variable** (`member_manager.py:13,77`): `self.prev_id` is set in `get_next()` but never read anywhere in the codebase. Suggests the developer intended an explicit no-repetition check but forgot to implement it. The no-repetition behavior works only incidentally via counter advancement.

4. **Commented-Out Code in Final Submission** (`member_manager.py:64-68`): Three commented-out lines for handling `active_count == 0` and `active_count == 1` are left in the final code. These should have been either implemented or removed.

5. **No Design Pattern:** No Iterator, Strategy, State, or any formal pattern. The entire rotation logic lives in a single monolithic class without abstraction or extensibility.

6. **Input Mutation** (`member_manager.py:27`): `member_info.id = self.member_count + 1` directly mutates the caller's `Member` object. The caller's object is silently modified, which can cause unexpected side effects.

7. **Only 4 Test Functions with Major Gaps:** No tests for: single active member, all inactive, empty team, no-repetition verification, rotation with mixed active/inactive, boundary conditions (n=0, n<0). The `module`-scoped shared fixture is an anti-pattern.

8. **Wrong Positional Args in Test** (`test_member_manager.py:60`): `Member(1, "Alice", Status.isActive)` passes `name=1, status="Alice", id=Status.isActive` due to dataclass field order (`name`, `status`, `id`). Works only because Python doesn't enforce dataclass types at runtime.

9. **Poor Variable Naming:** `ans` for results list, `i` as a while-loop decrementor, `counter` without qualification (counter of what?). These violate meaningful naming principles.

10. **Generic Error Handling:** Uses `KeyError` with string messages ("User not exist") instead of custom exception classes. No `NoActiveMembersError` or similar domain-specific errors.

11. **File Placement:** Code is placed directly under `coding-challenge-1/` instead of a team subfolder (e.g., `coding-challenge-1/hoanguyen/`), conflicting with the repository structure where each team should have their own directory.

---

## Detailed Category Feedback

### Core Logic Analysis

The rotation algorithm uses a global counter and modulo arithmetic: `member_id = self.counter % self.member_count + 1`. This maps sequential counter values to member IDs (1-based). Members are stored in a dict keyed by incremental IDs, so the counter wraps around naturally.

The approach is fundamentally sound for the happy path — sequential `get_next()` calls cycle through members deterministically. The `min(n, active_count)` cap prevents over-returning when requesting more than available active members (returns max active instead of cycling).

The critical flaw is `add_member`'s unconditional `active_count += 1`. This creates a state inconsistency: if inactive members are added, `active_count` is inflated, and `get_next()` loops endlessly searching for active members that don't exist. The fix is trivial (`if member_info.status == Status.isActive: self.active_count += 1`) but the bug's existence raises concerns about testing discipline.

The `prev_id` dead code is puzzling — it's assigned on every successful selection but never checked. The developer may have recognized the no-repetition requirement but didn't realize their counter approach already handles it implicitly (or intended to add an explicit check and forgot).

### Code Quality Analysis

The codebase is a straightforward single-class implementation with no design patterns, no abstractions, and no extensibility mechanism. `MemberManager` handles everything: member CRUD, status management, counter tracking, and rotation logic. This violates SRP and makes the code harder to test and extend.

The use of `@dataclass` for `Member` and `Enum` for `Status` shows awareness of Python's modern features. But the implementation doesn't leverage them well — the `Status` enum uses non-Pythonic camelCase values (`isActive` instead of `ACTIVE`), and the dataclass's `id: int | None = None` allows a confusing API where IDs are always overwritten internally.

Manual counter management (`counter`, `member_count`, `active_count`, `prev_id`) is error-prone. Using `len(self.members)` and computing active count on-demand would be simpler and bug-free:

```python
@property
def active_count(self) -> int:
    return sum(1 for m in self.members.values() if m.status == Status.isActive)
```

### Testing Analysis

The test suite has only 4 functions across 3 files. The main rotation test (`test_returns_members_in_rotation_order`) is actually a decent integration test — it verifies 5 sequential `get_next()` calls with different batch sizes and confirms correct ordering and wrapping. But it's one monolithic function testing multiple behaviors.

Critical gaps: no test for the no-repetition rule, no test for skipping inactive during rotation, no test for edge cases (single active, all inactive, empty team, boundary inputs). The `active_count` bug would have been caught by a simple test adding an inactive member and calling `get_next()`.

The `module`-scoped `member_manager_object` fixture creates a shared mutable instance — tests that use it accumulate state, making tests order-dependent. The rotation test works around this by creating a new `MemberManager()` inside the test, effectively ignoring the fixture.

### Documentation Analysis

The README is the submission's strongest area. The "Cyclic algorithm" section with modulo arithmetic examples is clear and demonstrates understanding. The trade-off analysis (incremental ID dependency, O(n) inactive traversal, in-memory limitations) shows honest engineering assessment.

However, the complete absence of design pattern discussion is a significant gap. The "Classes" section describes `MemberManager`, `Member`, and `Status` but never identifies them as implementing any pattern. The README would benefit from naming the approach (e.g., "God Object anti-pattern" or proposing an Iterator refactoring).

The PR body — "Hoa Nguyen submission" — provides no context, no highlights, no summary of the approach. This is a missed communication opportunity.

---

## Improvement Suggestions (6 Required)

### 💡 Suggestion 1: Fix `active_count` Bug in `add_member`

**Current Implementation:**

```python
# member_manager.py:22-30
def add_member(self, member_info: Member) -> None:
    print("Current members: ", self.members)  # Debug artifact!
    if member_info.id in self.members:
        raise KeyError("Member already exists")
    else:
        member_info.id = self.member_count + 1
        self.members[member_info.id] = member_info
        self.member_count += 1
        self.active_count += 1  # BUG: Always increments!
```

**Issue:**
If a member is added with `Status.isNotActive`, `active_count` is still incremented. This causes `get_next()` to infinite-loop when searching for active members that don't exist, because `min(n, active_count)` produces a nonzero loop bound.

**Recommended Approach:**

```python
def add_member(self, member: Member) -> None:
    if member.id is not None and member.id in self.members:
        raise DuplicateMemberError(f"Member with id {member.id} already exists")
    member_id = self.member_count + 1
    new_member = Member(name=member.name, status=member.status, id=member_id)
    self.members[member_id] = new_member
    self.member_count += 1
    if member.status == Status.isActive:
        self.active_count += 1
```

**Impact:** Critical bug fix. Prevents infinite loop. Also fixes input mutation and removes debug print.

### 💡 Suggestion 2: Implement a Formal Design Pattern (Iterator)

**Current Implementation:**

```python
# All rotation logic embedded in MemberManager.get_next()
def get_next(self, n: int = 1) -> List[Member]:
    i = min(n, self.active_count)
    ans = []
    while i:
        member_id = self.counter % self.member_count + 1
        member = self.members[member_id]
        if member.status == Status.isActive:
            ans.append(member)
            self.prev_id = member_id
            i -= 1
        self.counter += 1
    return ans
```

**Issue:**
No separation of traversal logic from member management. Cannot extend with different rotation strategies.

**Recommended Approach:**

```python
from abc import ABC, abstractmethod

class RotationIterator(ABC):
    @abstractmethod
    def next(self, exclude_id: int | None = None) -> Member | None:
        pass

class RoundRobinIterator(RotationIterator):
    def __init__(self, members: dict[int, Member]):
        self._members = members
        self._counter = 0

    def next(self, exclude_id: int | None = None) -> Member | None:
        active = [m for m in self._members.values() if m.status == Status.ACTIVE]
        if not active:
            raise NoActiveMembersError()
        start = self._counter % len(active)
        for i in range(len(active)):
            candidate = active[(start + i) % len(active)]
            if candidate.id != exclude_id or len(active) == 1:
                self._counter = (start + i + 1) % len(active)
                return candidate
        return active[0]  # Fallback
```

**Impact:** -7 from Design Pattern score. Would also improve SRP and OCP compliance.

### 💡 Suggestion 3: Remove All Debug Artifacts

**Current Implementation:**

```python
# member_manager.py:23
print("Current members: ", self.members)

# member_manager.py:74
# print(f"Getting member id: {member_id}")

# member_manager.py:64-68
# if self.active_count == 0:
#     return []
# if self.active_count == 1:
#     return [self.members[self.counter % self.member_count + 1]]

# fixtures.py:8-9
# import sys
# print(f"Sys path: {sys.path}")

# test files:
# @pytest.mark.skip
```

**Issue:**
Debug artifacts in production code indicate unfinished cleanup. `print()` pollutes stdout. Commented-out code adds visual noise and signals incomplete work.

**Recommended Approach:**
Remove all `print()` statements, commented-out code blocks, and `@pytest.mark.skip` markers. Use Python's `logging` module if runtime debug output is needed:

```python
import logging
logger = logging.getLogger(__name__)

def add_member(self, member: Member) -> None:
    logger.debug("Current members: %s", self.members)
    # ... rest of logic
```

**Impact:** -1 for debug artifacts, -1 for YAGNI (commented code). Immediate clean code improvement.

### 💡 Suggestion 4: Add Custom Error Classes

**Current Implementation:**

```python
# member_manager.py:17-18
raise KeyError("User not exist")

# member_manager.py:24
raise KeyError("Member already exists")
```

**Issue:**
Generic `KeyError` with string messages. No domain-specific errors. "User not exist" is grammatically incorrect.

**Recommended Approach:**

```python
class MemberNotFoundError(Exception):
    def __init__(self, member_id: int):
        super().__init__(f"Member with id {member_id} not found")
        self.member_id = member_id

class DuplicateMemberError(Exception):
    def __init__(self, member_id: int):
        super().__init__(f"Member with id {member_id} already exists")
        self.member_id = member_id

class NoActiveMembersError(Exception):
    def __init__(self):
        super().__init__("No active members available for rotation")
```

**Impact:** -1 for error handling quality. Professional error handling with domain-specific context.

### 💡 Suggestion 5: Add Critical Edge Case Tests

**Current Implementation:**
No tests for: single active member, all inactive, empty team, boundary inputs, no-repetition verification, inactive members during rotation.

**Issue:**
Missing tests leave critical bugs undetected (e.g., the `active_count` bug, infinite loop scenario).

**Recommended Approach:**

```python
def test_single_active_member_allows_repetition():
    mm = MemberManager()
    mm.add_member(Member("Alice", Status.isActive))
    mm.add_member(Member("Bob", Status.isNotActive))
    result1 = mm.get_next(1)
    result2 = mm.get_next(1)
    assert result1[0].name == "Alice"
    assert result2[0].name == "Alice"  # Repetition OK

def test_all_inactive_raises_error():
    mm = MemberManager()
    mm.add_member(Member("Alice", Status.isActive))
    mm.change_status(1, Status.isNotActive)
    with pytest.raises(NoActiveMembersError):
        mm.get_next(1)

def test_no_immediate_repetition():
    mm = MemberManager()
    mm.add_member(Member("Alice", Status.isActive))
    mm.add_member(Member("Bob", Status.isActive))
    results = [mm.get_next(1)[0].name for _ in range(6)]
    for i in range(1, len(results)):
        assert results[i] != results[i - 1]

def test_get_next_zero_raises_error():
    mm = MemberManager()
    mm.add_member(Member("Alice", Status.isActive))
    with pytest.raises(ValueError):
        mm.get_next(0)
```

**Impact:** Would have caught the `active_count` bug and provided confidence in edge case behavior.

### 💡 Suggestion 6: Use Pythonic Enum and Naming Conventions

**Current Implementation:**

```python
# status.py
class Status(Enum):
    isActive = "isActive"
    isNotActive = "isNotActive"
```

**Issue:**
`isActive` / `isNotActive` use camelCase — non-Pythonic. Python conventions use `UPPER_SNAKE_CASE` for enum values. `isNotActive` is a double-negative name.

**Recommended Approach:**

```python
from enum import Enum

class MemberStatus(Enum):
    ACTIVE = "active"
    INACTIVE = "inactive"
```

Also rename `ans` → `selected_members`, `i` → `remaining`, `counter` → `rotation_index`:

```python
def get_next(self, count: int = 1) -> list[Member]:
    remaining = min(count, self.active_count)
    selected_members = []
    while remaining > 0:
        member_id = self.rotation_index % self.member_count + 1
        member = self.members[member_id]
        if member.status == MemberStatus.ACTIVE:
            selected_members.append(member)
            remaining -= 1
        self.rotation_index += 1
    return selected_members
```

**Impact:** -1 for naming quality. Follows Python community conventions and improves readability.

---

## Knowledge Badges

| Badge | Earned | Evidence |
|-------|--------|----------|
| 🟢 API/Library Design Fundamentals | ⚠️ Partial | Library packaged with hatchling/pyproject.toml, but API design has bugs and no clean interface |
| 🟢 State Management Basics | ⚠️ Partial | Tracks counters for state, but manual counter management is error-prone (`active_count` bug) |
| 🟢 Algorithm Design | ✅ | Modulo arithmetic for round-robin is correct approach, explained in README |
| 🟢 Design Pattern Application | ❌ | No design pattern named, implemented, or justified |
| 🟢 Unit Testing & Edge Cases | ❌ | Only 4 tests, minimal edge case coverage, shared mutable fixture |
| 🟢 Test-Driven Development (TDD) | ❌ | Big-bang commit, no test-first evidence |
| 🟢 Clean Code Practices | ❌ | Debug `print()`, dead code, commented-out blocks, poor naming, input mutation |
| 🟢 Requirement Analysis | ⚠️ Partial | Core requirements partially met, but missed no-repetition explicit handling and design pattern requirement |

---

## Final Verdict

### Summary

Hoa Nguyen's submission demonstrates a working prototype for the simplest scenario — all members added as active with sequential `get_next()` calls. The modulo arithmetic approach is sound and the README provides decent algorithm explanation with honest trade-off analysis.

However, the submission falls significantly below the competitive bar. The critical `active_count` bug (infinite loop when inactive members are added), debug `print()` in production code, commented-out code blocks, dead `prev_id` variable, absence of any design pattern, poor variable naming, and minimal test coverage (4 tests with no edge cases) collectively indicate insufficient engineering maturity for the final round.

The work shows a developer early in their engineering journey — learning new tools (uv, pytest), building something functional, and documenting their approach. These are positive signals for growth, but the submission needs significantly more polish, testing rigor, and design pattern awareness to be competitive.

### Comparison to Ideal Solution

An ideal solution would:

- Implement a formal design pattern (Iterator, Strategy) with proper abstraction
- Have 20+ tests covering all edge cases with focused, descriptive test names
- Use custom error classes instead of generic KeyError
- Have zero debug artifacts in production code
- Show TDD discipline in commit history
- Validate all inputs (n <= 0, empty team, all inactive)
- Not mutate input arguments
- Use Pythonic naming conventions throughout

Hoa's solution achieves approximately 35% of the ideal. The main gap is the absence of software engineering practices (patterns, testing, clean code) around the functional core algorithm.

### Red Flags

- Critical `active_count` bug causing infinite loop — shows lack of testing discipline
- Debug `print()` left in production code — shows incomplete cleanup
- Dead `prev_id` variable — suggests unfinished implementation
- Commented-out code in final submission — signals work-in-progress
- Only 4 test functions with no edge cases — insufficient for competitive evaluation
- No design pattern — challenge explicitly requires one
- Input mutation — surprising side effect
- Code placed directly in `coding-challenge-1/` instead of team subfolder

### Green Flags

- Working round-robin algorithm for happy path
- Honest trade-off analysis in README
- Uses modern Python tooling (uv, dataclass, Enum, pyproject.toml)
- Jupyter notebook demo sample
- Transparent about AI tool usage
- `change_status` method correctly manages state transitions

### Selection Panel Notes

This submission is not competitive for the final round. The score of 53/120 is well below the 75-point threshold for consideration. While the developer shows willingness to learn (uv, pytest, packaging), the code quality, testing rigor, and design pattern gaps are too significant. Recommend encouraging the developer to study design patterns, TDD, and clean code practices for future challenges.

---

## Appendix: Code Snippets Reviewed

### member_manager.py — Critical Bug: `active_count` Always Incremented

```python
def add_member(self, member_info: Member) -> None:
    print("Current members: ", self.members)  # ❌ Debug artifact
    if member_info.id in self.members:
        raise KeyError("Member already exists")
    else:
        member_info.id = self.member_count + 1  # ❌ Mutates input
        self.members[member_info.id] = member_info
        self.member_count += 1
        self.active_count += 1  # ❌ BUG: Always increments regardless of status
```

### member_manager.py — `get_next` with Dead Code and Poor Naming

```python
def get_next(self, n: int = 1) -> List[Member]:
    # ❌ Commented-out code blocks (YAGNI)
    # if self.active_count == 0:
    #     return []
    # if self.active_count == 1:
    #     return [self.members[self.counter % self.member_count + 1]]

    i = min(n, self.active_count)  # ❌ 'i' — poor name
    ans = []  # ❌ 'ans' — poor name

    while i:
        member_id = self.counter % self.member_count + 1
        # print(f"Getting member id: {member_id}")  # ❌ Commented debug
        member = self.members[member_id]

        if member.status == Status.isActive:
            ans.append(member)
            self.prev_id = member_id  # ❌ Dead code: set but never read
            i -= 1
        self.counter += 1

    return ans
```

### status.py — Non-Pythonic Enum Naming

```python
class Status(Enum):
    isActive = "isActive"      # ❌ Should be ACTIVE = "active"
    isNotActive = "isNotActive"  # ❌ Should be INACTIVE = "inactive"
```

### member.py — Optional ID with Input Mutation Risk

```python
@dataclass
class Member:
    name: str
    status: Status
    id: int | None = None  # ⚠️ Allows None, always overwritten by add_member
```

### test_member_manager.py — Wrong Positional Argument Order

```python
# Line 60 — passes name=1, status="Alice", id=Status.isActive due to field order!
member_manager_object.add_member(Member(1, "Alice", Status.isActive))
```
