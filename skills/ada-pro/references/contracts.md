# Ada Contracts — Reference

_Deep-dive reference for the `Ada-Pro` skill. Loaded on demand._

> Sources: the Ada 2022 RM, merged with `Ada-Pro`'s corrections.
> SPARK-proof follow-up (GNATprove, assurance levels) is cross-referenced, not
> covered — this skill is Ada-focused.

## Aspect syntax, not pragmas

Correction-map core rule: **`with Pre =>` / `with Post =>` aspects**, never
`pragma Precondition` / `pragma Postcondition`. Aspects (Ada 2012+) are the
idiomatic, portable form; the pragmas still compile but are stale.

```ada
function Sqrt (X : Float) return Float
  with Pre  => X >= 0.0,
       Post => Sqrt'Result >= 0.0;
```

- `'Result` names the return value in `Post`; `X'Old` captures the pre-state
  value. Do **not** invent `in`/`out` keywords inside contracts.
- Use `and then` / `or else` inside contracts whenever a guard must short-circuit
  (dereference, division, instance-above-manager).

## `Contract_Cases`

- Guards must be **disjoint and complete** (GNATprove checks this, and Ada
  semantics require an exhaustive case analysis of the precondition space).
  Do not write overlapping guards.
- Each case pairs a guard with the `Post` that holds when the guard holds.

## Contract aspects beyond Pre/Post

- `Type_Invariant`, `Default_Initial_Condition` (type-level; checked at
  conversion/membership, un-checkable for `'Old`).
- `Global => null` means "touches no global state". `Global` and `Depends` are
  Ada-legal flow contracts, but most valuable under SPARK proof — cross-reference
  only, this skill does not cover proof.

## Class-wide contracts and Liskov Substitution

This is the highest-value correction in the map, and the most common model
mistake:

- **Plain `Pre`/`Post` on a dispatching (`overriding`) primitive of a `tagged`
  type is not inherited and is not checked for dispatching calls.** Only the
  class-wide form governs dispatching. (A specific `Pre` on a tagged primitive is
  also illegal — RM 6.1.1.)
- **Use `Pre'Class` / `Post'Class`.** They are inherited by overrides and govern
  dispatching calls. A dispatching call is checked against the class-wide
  contract of the **static (declared) type** of the controlling operand.
- **LSP variance is enforced:** in an override, `Pre'Class` must be *weaker*
  (contravariant), `Post'Class` must be *stronger* (covariant). Strengthening
  `Pre'Class` or weakening `Post'Class` violates the Liskov verification
  condition.
- **Type extension initialization:** converting/extending a tagged value to a
  class-wide type can raise `high: extension of "X" is not initialized` — every
  component, including invisible (private-inherited) ones, must be initialized
  before the conversion.

## Where this skill stops and SPARK proof begins

`SPARK_Mode` (three-valued `On`/`Off`/`Auto`), GNATprove assurance levels
(Stone→Bronze→Silver→Gold→Platinum), manual loop invariants, ghost code, and
ownership/borrow are SPARK-proof territory. Point the user to AdaCore's
[`gnatprove` skill](https://github.com/AdaCore/skills/tree/main/plugins/adacore/skills/gnatprove)
and the SPARK User's Guide rather than attempting proof under this skill.

## References
- Ada 2022 RM — Aspect clauses: [RM 13.3.1](https://www.ada-auth.org/standards/22rm/html/RM-13-3-1.html)
- RM — Dispatching: [RM 6.1.1](https://www.ada-auth.org/standards/22rm/html/RM-6-1-1.html)
- SPARK UG — OOP & Liskov Substitution: https://docs.adacore.com/spark2014-docs/html/ug/en/source/object_oriented_programming.html