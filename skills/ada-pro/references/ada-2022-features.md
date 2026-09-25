# Ada 2022 Features — Reference

_Deep-dive reference for the `Ada-Pro` skill. Loaded on demand._

> Sources: the Ada 2022 RM, learn.adacore.com, and `Ada-Pro`'s corrections.

## The language version

- **Ada 2022 is the current, finalized standard.** The claim "the latest Ada is
  Ada 2012" is stale (correction-map core rule).
- GNAT does **not** default to Ada 2022.
  Enable Ada 2022 explicitly with
  `-gnat2022` or `pragma Ada_2022`.
- With Alire, select Ada 2022 in `alire.toml` via build switches —
  `alr init` does not do this for you:

  ```toml
  [build-switches]
  "*".ada_version = "Ada2022"
  ```

- When a project or toolchain targets Ada 2012 (the GNAT default, still common
  for certified/commercial compilers), say so and consult the Ada 2012 RM
  instead of assuming 2022.

## What changed vs Ada 2012

> The Ada 2022 feature set is detailed in the RM and in learn.adacore.com's
> "What's New in Ada 2022"; the Ada-Pro correction map only *points* there rather
> than duplicating the RM. Key features to know by name:

- **Target name `@`** in assignments (`A := A + 1` ⇔ `A := @ + 1`, incl. indexed
  and selected components).
- **Declare expressions** — `declare` inside a larger expression.
- **`'Reduce` attribute** — on `Ada.Containers` vectors/maps (and the iterable
  `'Reduce` on arrays), fold over elements.
- **Delta aggregates** — `(X with Delta => ...)` copies a record/array and
  overrides the listed components.
- **String interpolation** (`Ada.Strings` `Interpolation`) and other 2022
  conveniences (`'Result` in more places, parallel block).
- **Jorvik profile** — a spike over Ravenscar tasking (details in
  `embedded-and-runtimes.md`).

## Migration notes: Ada 2012 → Ada 2022

- Build with `-gnat2022` and fix what breaks; the language is conservative, so
  most 2012 code compiles unchanged.
- Version-qualify any claim ("as of Ada 2022 / GNAT FSF <version>"); do not
  present to-new-feature behavior as timeless.

## References
- Ada 2022 Reference Manual: https://www.ada-auth.org/standards/22rm/html/RM-TOC.html
- GNAT RM — Implementation of Ada 2022 features: https://gcc.gnu.org/onlinedocs/gnat_rm/Implementation-of-Ada-2022-Features.html
- What's New in Ada 2022 (learn.adacore.com): https://learn.adacore.com/courses/whats-new-in-ada-2022/
