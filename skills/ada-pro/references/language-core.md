# Ada Language Core — Reference

_Deep-dive reference for the `Ada-Pro` skill. Loaded on demand — not part of the
always-on `SKILL.md`. Focus: packages, types, generics, tagged types/OOP,
exceptions, tasking, visibility and child units._

> Sources: `agent-sh/ada-spark` advice, merged with `Ada-Pro`'s corrections.
> SPARK-specific items are cross-referenced, not covered (see end of file).

## Packages, visibility and child units

- File names must match unit names (`Hello_World` → `hello_world.adb`); GNAT and
  gprbuild resolve sources by unit name, so a mismatch is a `file not found`
  style error that has nothing to do with the code. Confirm the project layout
  (`.gpr` / `alire.toml`) before blaming the code.
- Visibility follows Ada's scope rules (RM Section 8), not file-scoped intuition
  from other languages: a child unit sees its parent's private part; siblings
  only via `with`. When in doubt, consult the RM's visibility rules rather than
  guessing.
- **Keep public package names distinct from public type and object names in the
  same API.** Use plural child package names for collections of related types,
  e.g. `Labels.Label`, `Buttons.Button`, `Displays.Display` —
  not a package and its primary type sharing one name.
- Ada loops have no `continue`: `exit when <cond>` always exits the loop — it
  never jumps to the next iteration. Restructure, or use a named-loop `exit`
  combined with an `if`, to get "skip this iteration" behavior.

## Strong typing

- Prefer strong, explicit types: derived types and subtypes with `range` /
  `digits` / `delta` constraints, rather than a single general-purpose numeric
  type. Type safety is Ada's core guarantee; express units and ranges in the
  type system.
- `'Size` applies to the object/type it is named on; on an access value it is
  the size of the pointer, not the pointee (a C-derived assumption that misleads).

## Generics

- Normal Ada generics: formal parameters, generic packages/subprograms,
  instantiation. The body compiles like any Ada code.
- **Generics and proof go through instantiations.** GNATprove does not analyze a
  generic body in isolation, only its instantiations; the same generic can prove
  on one instance and fail on another. (_Out of scope for this Ada-focused skill —
  see SPARK material._)

## Tagged types / OOP

- Tagged types provide single inheritance + multiple interface implementation;
  dispatching over class-wide types. `overriding` / `not overriding` markers
  catch signature drift at compile time — use them.
- **Dispatching contracts: `Pre'Class` / `Post'Class`, not plain `Pre`/`Post`.**
  See `contracts.md`. An override weakens `Pre'Class` and strengthens
  `Post'Class` (Liskov variance).

## Exceptions

- Declare, raise, and handle exceptions; structure handlers from most to least
  specific. `Ada.Exceptions` provides `Exception_Message`, `Exception_Information`.
- Remember the surface profile matters: whether exceptions can *propagate* is a
  runtime choice (`No_Exception_Propagation` under light/embedded runtimes) —
  see `embedded-and-runtimes.md`.

## Tasking

- `task` and `protected` types are **limited types**; a standard container
  cannot hold a task object directly. Use an access-to-task type or a task pool
  pattern instead of expecting `Ada.Containers.Vectors` to hold tasks.

## Boolean operators (short-circuit)

- Use `and then` / `or else` when evaluation order matters — guarding a
  dereference, or a division, or an array index before the operation that needs it:

  ```ada
  if Ptr /= null and then Ptr.all'Valid then ...
  if Denom /= 0 and then Num / Denom > 1 then ...
  ```

  Plain `and` / `or` evaluate both sides and will raise on a dangerous second
  operand. The failure mode is the same in contracts (`Pre`/`Post`), which is
  why the correction map bans plain `and`/`or` there too.

## SPARK cross-reference (out of scope here)

SPARK ownership/borrow on access types, `SPARK_Mode`, loop invariants, ghost
code, and the Stone→Platinum assurance ladder are beyond this Ada-focused skill.
Point SPARK-proof questions to `agent-sh/ada-spark` and the SPARK User's Guide
instead of guessing.

## References
- Ada 2022 RM — Visibility: [RM Section 8](https://www.ada-auth.org/standards/22rm/html/RM-8.html)
- SPARK User's Guide (ownership, proof): https://docs.adacore.com/spark2014-docs/html/ug/
