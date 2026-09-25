---
name: ada-pro
description: "Use when writing, reviewing, porting, or debugging Ada code — .ada/.adb/.ads/.gpr files, Ada packages, generics, tagged types, exceptions, tasking, contracts (Pre/Post/Contract_Cases), Alire (alr) projects, or GNAT/GNAT SAS tooling. Targets current Ada (Ada 2022) and the current toolchain (Alire + GNAT FSF, GNAT Pro). Focus is plain Ada; SPARK is only referenced in passing where relevant, not covered in depth. Not for unrelated languages. This skill exists specifically to correct outdated or hallucinated Ada knowledge — read it fully before writing or judging any Ada code."
allowed-tools: Read, Edit, Write, Grep, Glob, Bash(alr:*), Bash(gnatmake:*), Bash(gprbuild:*), Bash(gnat:*), Bash(gnatsas:*), Bash(gnatformat:*), Bash(gnatcheck:*), Bash(gnatcov:*)
license: MIT
metadata:
  version: "0.1.0"
  domain: language
  triggers: Ada, .ada, .adb, .ads, .gpr, Alire, alr, GNAT, GNAT FSF, GNAT Pro, GNAT SAS, gprbuild, gnatmake, gnatsas, gnatcheck, tagged types, generics, tasking, Pre, Post, Contract_Cases
  role: specialist
  scope: implementation-and-review
  output-format: code
  related-skills: gnatprove, alire
---

# Ada-Pro

Senior Ada specialist for writing, porting, reviewing, and debugging
professional, idiomatic Ada code on the current open source toolchain (Ada 2022, Alire +
GNAT FSF / GNAT Pro).

**Read this entire file before writing, editing, or reviewing any Ada code.**
This skill exists because some AI models carry outdated or partially incorrect Ada knowledge.

**Scope of this skill: plain Ada.** SPARK (the formally verifiable Ada
subset) is mentioned only where directly relevant. Do not attempt SPARK proof,
assurance levels, or ownership/borrow reasoning based on this skill alone —
if the user asks about SPARK proof specifically, say so and point them to
SPARK-specific documentation (or a dedicated SPARK skill such as AdaCore's
[`gnatprove` skill](https://github.com/AdaCore/skills/tree/main/plugins/adacore/skills/gnatprove))
instead of guessing.

## Core Workflow

1. **Confirm context** — Look for an `alire.toml` (Alire project) and/or a
   `.gpr` project file; if tools are available, `alr --version` and/or
   `gnat --version` confirm what is actually installed rather than assuming.
2. **Apply the correction map** (below) and the constraints (`MUST DO` /
   `MUST NOT DO`) to everything you write or review — on every task, not
   just some.
3. **Consult references for anything version-sensitive or non-obvious** —
   an aspect's exact semantics, a GNAT SAS flag, a runtime profile — before
   committing to an answer (see Reference Guide below).
4. **Implement** — new code: aspect-based contracts, strong explicit types, Ada 2022
   (`-gnat2022`) by default unless told otherwise.
5. **Port/review existing code** — first translate stale constructs via the
   correction map, then check idiomatic style, then check against
   `MUST NOT DO`.
6. **Verify** — `gprbuild -P <project>.gpr` or `alr build` to confirm it
   compiles. For static/dynamic verification (not SPARK proof), see
   `references/verification.md`.

## Correction Map — apply first, on every task

If you find yourself about to write or say anything in the **Stale** column
below, stop and use the **Current** column instead — even if the stale form
"feels" more familiar. Ordered: language-level corrections first, then
tooling/ecosystem corrections.

### Language corrections

| Stale (do NOT use/say) | Current (use this instead) | Why |
|---|---|---|
| `pragma Precondition (...)` / `pragma Postcondition (...)` | `with Pre => ...` / `with Post => ...` aspects | Aspects (Ada 2012+) are the idiomatic, modern form. |
| Plain `Pre` / `Post` on a dispatching (`overriding`) primitive of a `tagged` type | `Pre'Class` / `Post'Class` | A specific `Pre`/`Post` is not checked for dispatching calls and is not properly inherited. Only the class-wide form governs dispatching. |
| "The latest Ada is Ada 2012" | Ada 2022 is finalized and current | Use `-gnat2022` or `pragma Ada_2022` when relevant. |
| `Ada.IO`, `Ada.Strings.Format`, `Ada.Collections` (as package names) | `Ada.Text_IO`, `Ada.Strings.Fixed` / `Ada.Strings.Unbounded`, `Ada.Containers.Vectors` (etc.) | These exact names do not exist. Never invent a package name — say you're unsure and look it up rather than guessing. |

### Tooling & ecosystem corrections

| Stale (do NOT use/say) | Current (use this instead) | Why |
|---|---|---|
| "Download GNAT Community Edition" | Alire (`alr`) + GNAT FSF | GNAT Community was discontinued (last release 2021). |
| `alr install <package>` used to mean "add this as a project dependency" | `alr with <crate>` adds a dependency to the *current* project; `alr install <crate>` is a *different* command that installs a binary-tool crate (e.g. `gnatformat`, `gnatsas`) to a shared prefix, available on `PATH` outside any project | Alire is not a system package manager in the apt/npm sense, but `alr install` does exist — for global tool installs, not project dependencies. Don't conflate the two. |
| "GPS" (GNAT Programming Studio) as the current IDE | GNAT Studio, or the Ada & SPARK VS Code extension + Ada Language Server (ALS) | GPS was renamed to GNAT Studio; VS Code + ALS is an increasingly common modern setup. |

When you are not sure whether something is version-sensitive (a tool flag, a
package name, an aspect name), say so explicitly and, if possible, verify
against the current toolchain or the canonical references below rather than
asserting from memory.

## When to Use / Skip

**Use this skill when the task involves:** writing, porting, reviewing, or
debugging `.ada` / `.adb` / `.ads` files; Ada packages, strong typing,
generics, tagged types, exceptions, tasking; contracts (`Pre`, `Post`,
`Contract_Cases`, `Global`, `Depends`); `.gpr` GNAT project files; Alire
(`alr`) project setup or dependency management; GNAT / GNAT SAS / GNATcheck
tooling; embedded/bare-metal Ada (Ravenscar/Jorvik, light runtimes) — only as
far as it affects idiomatic plain-Ada code, not deep RTOS design.

**Skip (or apply only lightly) when the task is:** about SPARK **proof**
specifically (GNATprove, assurance levels Stone–Platinum, ownership/borrow
checking) — redirect to SPARK-specific documentation instead of guessing;
about an unrelated programming language; about "SPARK" as an unrelated
product name.

## Constraints

### MUST DO

- Default to Alire (`alr`) for project setup and dependency management:
  `alr init --bin myproj` / `alr init --lib myproj`, `alr with <crate>` to
  add a project dependency, `alr build`, `alr run`, `alr toolchain --select`,
  `alr search <crate>`, `alr get <crate>` to fetch a crate's sources,
  `alr install <crate>` to install a binary-tool crate to a shared prefix
  (distinct from `alr with`), `alr pin` to pin a dependency to a local path
  or Git repo, `alr exec` / `alr printenv` to run in or inspect the project
  environment.
- Use `gprbuild -P <project>.gpr` (supports scenario variables like
  `-XMODE=release`) for anything with a `.gpr` file; use `gnatmake` only for
  a single file/closure without a project file.
- Write contracts as aspects on the declaration: `function F (X : T) return
  U with Pre => <condition>, Post => F'Result = <expr>;`. Use `'Result` for
  the return value in `Post`, and `X'Old` for the entry-time value.
- Use `Pre'Class` / `Post'Class` (not plain `Pre`/`Post`) on a `tagged`
  type's dispatching (`overriding`) primitives.
- Use `and then` / `or else` instead of `and`/`or` whenever short-circuit
  evaluation matters (dereference guards, division
  guards).
- Prefer strong, explicit types (derived types, subtypes with range/digits/
  delta constraints) over a single general-purpose numeric type.
- Qualify anything version-sensitive explicitly, e.g. "as of GNAT FSF
  &lt;version&gt; / Ada 2022" — never present version-sensitive facts as
  timeless.
- Search Alire (`alr search <keyword>`) or ask the user before assuming a
  crate name exists.

### MUST NOT DO

- Do NOT recommend GNAT Community Edition, `pragma Precondition`/
  `pragma Postcondition`, or the name "CodePeer" for the current static
  analysis tool.
- Do NOT claim Ada 2012 is the current/latest standard.
- Do NOT write a plain (non-class-wide) `Pre`/`Post` on a dispatching
  primitive and assume it is inherited or checked for dispatching calls.
- Do NOT use plain `and`/`or` where short-circuit evaluation is required for
  correctness.
- Do NOT rely on the default `'Image` attribute for output that must be
  stable/portable (its formatting is implementation-defined); define an
  explicit formatting function (or `'Put_Image`) instead.
- Do NOT invent standard-library package or subprogram names, or Alire
  commands that don't exist.
- Do NOT attempt deep SPARK proof reasoning (assurance levels, ownership/
  borrow, GNATprove internals) under this skill — say so and redirect to
  AdaCore's [`gnatprove` skill](https://github.com/AdaCore/skills/tree/main/plugins/adacore/skills/gnatprove)
  and the SPARK User's Guide instead of guessing.

## Reference Guide

Load detailed guidance based on context — read the matching file when a task
needs more depth than this file provides. Do not load all of them "just in
case"; pick the one matching the task at hand.

| Topic | Reference | Load When |
|---|---|---|
| Language core | `references/language-core.md` | Packages, private types, generics, tagged types/OOP, exceptions, tasking, access types, visibility/child units |
| Contracts | `references/contracts.md` | `Pre`/`Post`/`Contract_Cases`, `Global`/`Depends`, class-wide contracts, Liskov substitution |
| Ada 2022 features | `references/ada-2022-features.md` | Target name `@`, declare expressions, `'Reduce`, delta aggregates, string interpolation |
| Strings & text | `references/strings-and-text.md` | String/Wide_String/Unbounded families, UTF-8/Unicode, `Ada.Strings.UTF_Encoding`, encodings, `Wide_Wide_Text_IO`, VSS |
| Build & tooling | `references/build-and-tooling.md` | Alire details, `.gpr` project structure, gprbuild/gnatmake specifics, GNAT Studio / ALS / VS Code setup |
| Verification | `references/verification.md` | GNAT SAS (`gnatsas`, static) vs GNAT DAS (`gnattest`/`gnatfuzz`/coverage, dynamic) usage, GNATcheck/MISRA, distinguishing static analysis from SPARK proof |
| Embedded & runtimes | `references/embedded-and-runtimes.md` | Ravenscar/Jorvik profiles, light/embedded runtimes, bare-metal constraints |
| Common pitfalls | `references/common-pitfalls.md` | Broader "what models get wrong" catalogue — stale idioms, hallucinated APIs, portability traps |

> **Status:** the files in `references/` have initial content (drawn from
> community forum research on forum.ada-lang.io) and
> are extended incrementally. If a reference file doesn't yet contain the answer
> you need, fall back to the canonical sources below rather than guessing.

## Knowledge Reference

Ada 2022, GNAT FSF, GNAT Pro, Alire (`alr`), gprbuild, gnatmake, GNAT SAS
(`gnatsas`), GNATcheck, GNAT Studio, Ada Language Server (ALS), Ravenscar,
Jorvik, tagged types, generics, tasking, contracts (Pre/Post/Contract_Cases).

Canonical sources — verify against these, do not rely on memory alone:

- [Ada 2022 Reference Manual](http://www.ada-auth.org/standards/22rm/html/RM-TOC.html)
- [Ada 2022 Annotated Reference Manual (AARM)](https://www.adaic.org/resources/add_content/standards/22aarm/html/AA-TTL.html)
- [Ada 2012 Reference Manual](http://www.ada-auth.org/standards/12rm/html/RM-TOC.html) — many commercial/certified compilers and projects still target Ada 2012, not Ada 2022; consult this when the user requires Ada 2012.
- [Ada 2012 Annotated Reference Manual (AARM)](http://www.ada-auth.org/standards/12aarm/html/AA-TTL.html)
- [GNAT Reference Manual](https://docs.adacore.com/live/wave/gnat_rm/html/gnat_rm/gnat_rm.html)
- [GNAT User's Guide](https://docs.adacore.com/live/wave/gnat_ugn/html/gnat_ugn/gnat_ugn.html)
- [GNAT_UGX — cross-development / embedded targets](https://docs.adacore.com/live/wave/gnat_ugx/html/gnat_ugx/gnat_ugx.html)
- [Alire documentation](https://alire.ada.dev/docs/)
- [AdaCore's `alire` skill](https://github.com/AdaCore/skills/tree/main/plugins/adacore/skills/alire) — authoritative, agent-oriented Alire command reference (search, toolchain, get, with, pin, install, build, run, exec, printenv); prefer it over this file for exact `alr` subcommand semantics
- [AdaCore's `gnatprove` skill](https://github.com/AdaCore/skills/tree/main/plugins/adacore/skills/gnatprove) — authoritative `gnatprove`/SPARK-proof tool usage; the redirect target when a task turns out to need SPARK proof (out of scope here)
- [GNAT SAS (Static Analysis Suite) User's Guide](https://docs.adacore.com/live/wave/gnatsas/html/gnatsas_ug/gnatsas_ug.html) — `gnatsas analyze` / `gnatsas report` usage and flags; rely on `gnatsas --help` and this guide.
- [What's New in Ada 2022 (learn.adacore.com)](https://learn.adacore.com/courses/whats-new-in-ada-2022/)
