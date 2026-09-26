# Ada Language Core — Reference

_Deep-dive reference for the `Ada-Pro` skill. Loaded on demand — not part of the
always-on `SKILL.md`. Focus: packages, types, generics, tagged types/OOP,
exceptions, tasking, visibility and child units._

> Sources: the Ada 2022 RM, merged with `Ada-Pro`'s corrections.
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
- Ada loops have no `continue`: `exit when <cond>` always exits the loop — it
  never jumps to the next iteration. Restructure, or use a named-loop `exit`
  combined with an `if`, to get "skip this iteration" behavior.

## I/O basics

- `Ada.Text_IO` is the standard console I/O package: `Put`, `Put_Line`,
  `Get_Line` (returns the whole line, without the terminator), `New_Line`.
- For numbers, use `Ada.Integer_Text_IO` / `Ada.Float_Text_IO` (or the
  `Ada.Text_IO.Integer_IO` / `Float_IO` instantiations for user-defined
  numeric types): `Get (N)`, `Put (N)`.
- For quick tests or debugging, use `X'Image` to display the value of `X`.
- **The classic `Get` trap:** numeric `Get` reads exactly the number and
  leaves the rest of the line (usually the line terminator) in the input
  buffer — a following `Get_Line` returns an empty string. Fix: call
  `Skip_Line` after numeric input before reading text.
- For Unicode text I/O see `strings-and-text.md` (`Wide_Wide_Text_IO`,
  `-gnatW8`, UTF-8 output caveats).
- See also the [Ada Programming Wikibook — Input Output](https://en.wikibooks.org/wiki/Ada_Programming/Input_Output) chapter.

## Strong typing

- Prefer strong, explicit types: derived types and subtypes with `range` /
  `digits` / `delta` constraints, rather than a single general-purpose numeric
  type. Type safety is Ada's core guarantee; express units and ranges in the
  type system.
- `'Size` applies to the object/type it is named on; on an access value it is
  the size of the pointer, not the pointee (a C-derived assumption that misleads).

### Private types

_Content pending._

## Numeric type selection

_When this section is needed, read the [Ada Programming Wikibook — Type System](https://en.wikibooks.org/wiki/Ada_Programming/Type_System) chapter and follow it rather than writing examples from memory._

## Arrays

_When this section is needed, read the [Ada Programming Wikibook — Arrays](https://en.wikibooks.org/wiki/Ada_Programming/Types/array) chapter and follow it rather than writing examples from memory._

## Records

_When this section is needed, read the [Ada Programming Wikibook — Records](https://en.wikibooks.org/wiki/Ada_Programming/Types/record) chapter and follow it rather than writing examples from memory._

## Access types

_Content pending._

## Subprogram parameter modes

_When this section is needed, read the [Ada Programming Wikibook — Subprograms](https://en.wikibooks.org/wiki/Ada_Programming/Subprograms) chapter and follow it rather than writing examples from memory._

## Return-by-value and build-in-place

- **Returning a local variable from a function is safe in Ada** — there is
  no C-style dangling reference to a stack frame. The result is copied out
  (or built directly in the caller's context); do not introduce pointer
  gymnastics out of C habits.
- Since Ada 2005, functions can return **immutably limited types** directly (older
  versions needed an `in out` parameter workaround). The result is
  **built in place** in the caller's context — no copy. This is the
  idiomatic constructor pattern for such types (tasks, protected
  objects, `Ada.Finalization.Limited_Controlled` derivatives).
- For indefinite return types (e.g. `return String`), the result's bounds
  are those of the returned expression; the caller gets a correctly
  constrained object.
- `'Size` vs `'Length`: `'Size` is the size in bits of the object/type;
  `'Length` is the element count (arrays only). On an access value, `'Size`
  is the size of the pointer, not the pointee (see "Strong typing").
- See also the [Ada Programming Wikibook — Subprograms](https://en.wikibooks.org/wiki/Ada_Programming/Subprograms) chapter.

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
- **Dispatching contracts:** specific `Pre` / `Post` checks follow the
  operation invoked; `Pre'Class` / `Post'Class` conditions apply across
  descendants. See `contracts.md` for the call and inheritance rules.

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

## Containers (`Ada.Containers`)

_When this section is needed, read the [Ada Programming Wikibook — Containers](https://en.wikibooks.org/wiki/Ada_Programming/Containers) chapter and follow it rather than writing examples from memory._

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
Point SPARK-proof questions to AdaCore's
[`gnatprove` skill](https://github.com/AdaCore/skills/tree/main/plugins/adacore/skills/gnatprove)
and the SPARK User's Guide instead of guessing.

## References

- [Ada Programming (Wikibook)](https://en.wikibooks.org/wiki/Ada_Programming) — the featured tutorial covering Ada 2005/2012/2022; read the matching chapter (linked from the sections above) before writing examples. Chapters for the filled sections: [Packages](https://en.wikibooks.org/wiki/Ada_Programming/Packages), [Generics](https://en.wikibooks.org/wiki/Ada_Programming/Generics), [Object Orientation](https://en.wikibooks.org/wiki/Ada_Programming/Object_Orientation), [Exceptions](https://en.wikibooks.org/wiki/Ada_Programming/Exceptions), [Tasking](https://en.wikibooks.org/wiki/Ada_Programming/Tasking)
- Ada 2022 RM — Visibility: [RM Section 8](https://www.ada-auth.org/standards/22rm/html/RM-8.html)
- SPARK User's Guide (ownership, proof): https://docs.adacore.com/spark2014-docs/html/ug/
