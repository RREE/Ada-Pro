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

## GNAT-defined `Contract_Cases`

- `Contract_Cases` is a GNAT-defined aspect, not part of standard Ada.
  Use it only when the project accepts GNAT-specific source code.
- When checks are enabled, GNAT requires exactly one guard to hold on entry.
  GNATprove can prove that guards are disjoint and complete.
- Each case pairs a guard with a condition checked on return.
- For the full worked example and the exact guard-checking rules (`others` ⇒
  only disjointness is checked; without it, completeness too), see AdaCore's
  [`gnatprove` skill — contracts.md](https://github.com/AdaCore/skills/blob/main/plugins/adacore/skills/gnatprove/references/spark/contracts.md)
  rather than duplicating it here.

## Contract aspects beyond Pre/Post

- `Type_Invariant` is language-defined. Its checks include initialization,
  conversion, and return from boundary operations when enabled.
- `Global` is language-defined in Ada 2022. `Global => null` declares that
  the operation reads or writes no global variables.
- `Depends` and `Default_Initial_Condition` are GNAT-defined aspects.
  They are useful in SPARK work, but are not portable Ada contracts.
  Consult the GNAT and SPARK manuals for their exact rules.

## Class-wide contracts and Liskov Substitution

Specific and class-wide contracts have different inheritance and call rules
(Ada 2022 RM 6.1.1):

- A concrete tagged primitive, including an override, can declare a specific
  `Pre` or `Post`. A dispatching call checks the specific contracts of the
  operation actually invoked. These contracts do not propagate to overrides.
  An abstract subprogram or null procedure cannot declare a specific `Pre` or
  `Post` (RM 6.1.1(9/3)).
- Use `Pre'Class` / `Post'Class` for conditions that apply to corresponding
  operations of descendant types. For a dispatching call, the class-wide
  preconditions come from the operation denoted by the call. The class-wide
  postconditions include those applicable to the operation invoked.
- Inherited class-wide preconditions are combined with logical OR; applicable
  class-wide postconditions are combined with logical AND. This preserves the
  inherited contract across overrides. Ada does not require each new
  expression to be individually weaker or stronger.
- If a specific `Pre` seems to be skipped, check which override was invoked
  and whether the assertion policy enables the check. Dispatching alone does
  not disable a specific precondition.
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
- [Ada Programming Wikibook — Aspects](https://en.wikibooks.org/wiki/Ada_Programming/Aspects) — contract and other aspects, with examples
- Ada 2022 RM — Aspect clauses: [RM 13.3.1](https://www.ada-auth.org/standards/22rm/html/RM-13-3-1.html)
- RM — Dispatching: [RM 6.1.1](https://www.ada-auth.org/standards/22rm/html/RM-6-1-1.html)
- [Ada 2022 RM — Global](https://www.adaic.org/resources/add_content/standards/22rm/html/RM-6-1-2.html)
- [GNAT RM — Implementation-defined aspects](https://docs.adacore.com/gnat_rm-docs/html/gnat_rm/gnat_rm/implementation_defined_aspects.html)
- SPARK UG — OOP & Liskov Substitution: https://docs.adacore.com/spark2014-docs/html/ug/en/source/object_oriented_programming.html
