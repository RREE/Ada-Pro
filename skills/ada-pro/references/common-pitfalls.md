# Ada Common Pitfalls — Reference

_Deep-dive reference for the `Ada-Pro` skill. Loaded on demand. "What models get
wrong" style catalogue — stale idioms, hallucinated APIs, portability traps._

> Sources: the correction map plus community threads on forum.ada-lang.io.

## Stale toolchain advice

- **"Download GNAT Community Edition"** — discontinued, last release 2021. Use
  Alire (`alr`) + GNAT FSF (see `build-and-tooling.md`).
- **`pragma Precondition` / `pragma Postcondition`** — use `with Pre =>` /
  `with Post =>` aspects (see `contracts.md`).
- **"GPS"** as the current IDE — it is GNAT Studio; ALS + VS Code is the
  strategic direction (see `build-and-tooling.md`).
- **"The latest Ada is Ada 2012"** — Ada 2022 is finalized (see
  `ada-2022-features.md`).

## Hallucinated names

- Standard-library units: `Ada.IO`, `Ada.Strings.Format`, `Ada.Collections` do
  **not exist**. The real names are precise: `Ada.Text_IO`,
  `Ada.Strings.Fixed` / `Ada.Strings.Unbounded`, `Ada.Containers.Vectors`. Never
  invent a unit name; verify against the RM index.
- Third-party libraries: `aws`, `gtkada`, `gnatcoll`, `vss`, `libadalang` are
  real crates — anything else should be confirmed with `alr search <topic>`
  before being referenced.
- Alire commands: `alr with` = project dependency; `alr install` = binary-tool
  crate to a shared prefix. Don't invent cargo/apt semantics.

## Boolean operators

- Plain `and` / `or` evaluate both sides; use `and then` / `or else` to guard a
  dereference, division, or index. In contracts the same rule applies — a
  guarded second operand is a runtime check or proof failure waiting to happen
  (see `language-core.md`).

## `'Image` is not stable output

- `'Image` spacing/formatting (and record field order, access values) is
  **implementation-defined** — do not rely on it for stable/portable output.
  Define an explicit formatting function, or `T'Put_Image`, for portable text.

## Dispatching contracts

- Plain `Pre`/`Post` on a dispatching (`overriding`) primitive is not inherited
  and not checked on dispatching calls — use `Pre'Class`/`Post'Class`. Also
  don't strengthen a `Pre'Class` / weaken a `Post'Class` in an override (LSP).
  Full detail in `contracts.md`.

## Name collisions ("SPARK" disambiguation)

- "SPARK" is also an Apache web framework, a cluster engine, and an unrelated
  coding-agent methodology. If the task is Ada SPARK specifically, say so —
  don't merge it with same-named products.
- **SPARK proof is out of scope for Ada-Pro**: ownership/borrow, `SPARK_Mode`,
  assurance levels, loop invariants, ghost code. Point to AdaCore's
  [`gnatprove` skill](https://github.com/AdaCore/skills/tree/main/plugins/adacore/skills/gnatprove)
  and the SPARK User's Guide instead of guessing.

## Formatting & style policy

- **There is no single "correct" Ada style.** `-gnaty...` (GNAT style checks),
  `gnatformat`'s output, and the RM's own examples disagree in places (the RM
  omits a space before `(` in subprogram calls; line-length conventions vary
  by editor). Style is a **project-level policy choice** — never assert one
  true style when reviewing code.
- Hints for handling style in a project:
  - Pick **one** formatting tool per project (`gnatformat`, see
    `build-and-tooling.md`) and/or a `-gnaty` switch set, document it in the
    `.gpr` or README, and pin the tool version.
  - Default to the formatter's output when reviewing; flag genuine style
    *policy* questions back to the user instead of silently "fixing" code to
    your own preference.
  - When a style rule is contested (spacing, casing, line length), treat the
    existing project convention as authoritative, not your memory of "Ada
    style".

## Platform rough edges

- Alire-installed tools can behave differently cross-platform (e.g. `gnatformat`
  silently doing nothing on Windows for some users). Verify a tool actually ran
  before debugging your code.
- Ada's `String` is an array of `Character` (8-bit, Latin-1), **not UTF-8**.
  For real UTF-8 use `Ada.Strings.UTF_Encoding`; `Wide_Wide_Text_IO` encoding
  has known rough edges — don't paper over them confidently. Full detail,
  including the type-conversion API and third-party options (VSS, uxstrings,
  Matreshka): `strings-and-text.md`.

## References
- Ada 2022 Reference Manual (unit index): https://www.ada-auth.org/standards/22rm/html/RM-TOC.html
- GNAT RM — Implementation of Ada 2022 features: https://gcc.gnu.org/onlinedocs/gnat_rm/Implementation-of-Ada-2022-Features.html
- Forum research this file draws on: forum.ada-lang.io threads (beginner, string/UTF-8, style and formatter topics)