# Ada-Pro

Skill file and references that teach AI coding agents to write idiomatic, correct,
current Ada — Ada 2022 on the modern toolchain (Alire + GNAT FSF, GNAT Pro).

**This is work in progress, in a very early stage. Use at your own risk.**

## Scope

- **Plain Ada only.** Writing, porting, reviewing, debugging `.ada` / `.ads` / `.adb` /
  `.gpr` files; packages, strong typing, generics, tagged types, exceptions,
  tasking, Ada contracts (`Pre` / `Post`);
  Alire projects, GNAT / GNAT tooling, and embedded runtimes.
- **SPARK is cross-referenced, not covered.** Proof, assurance levels, and
  ownership/borrow reasoning belong to SPARK-specific material; this skill
  points to it.

## What it does

- Embeds a stale-to-current **correction map** the agent applies on every Ada
  task.
- States **MUST DO / MUST NOT DO** rules for idiomatic packages, contracts,
  and strong typing.
- Links to a set of **on-demand references** (loaded only when the task needs
  them) for language core, contracts, Ada 2022 features, build & tooling,
  verification, embedded/runtimes, and common pitfalls.

## Repository layout

```text
Ada-Pro/
  README.md
  LICENSE
  skills/ada-pro/
    SKILL.md
    references/
      language-core.md
      contracts.md
      ada-2022-features.md
      strings-and-text.md
      style-and-naming.md
      build-and-tooling.md
      verification.md
      embedded-and-runtimes.md
      common-pitfalls.md
```

`SKILL.md` stays short; the deep content lives in `references/`, loaded on
demand. Canonical material (RM, AARM, GNAT UG/RM/UGX) is linked, not copied, so
the skill does not drift as the toolchain evolves.

## Installation

Copy (or symlink) the `skills/ada-pro` folder into your agent's skills
directory:

| Agent              | Directory                    |
| ------------------ | ---------------------------- |
| Claude Code        | `~/.claude/skills/`          |
| OpenCode           | `~/.config/opencode/skills/` |
| OpenAI Codex       | `~/.codex/skills/`           |
| Cursor             | `.cursor/skills/`            |
| Any SKILL.md agent | `~/.agents/skills/`          |

```bash
git clone https://github.com/<you>/Ada-Pro.git
mkdir -p ~/.claude/skills ~/.config/opencode/skills ~/.codex/skills
cp -r Ada-Pro/skills/ada-pro ~/.claude/skills/ada-pro
cp -r Ada-Pro/skills/ada-pro ~/.config/opencode/skills/ada-pro
cp -r Ada-Pro/skills/ada-pro ~/.codex/skills/ada-pro
```

Start a fresh session so the agent rescans its skills.

## Usage

The skill activates automatically on Ada work. Example prompts:

```
write an Ada package for a bounded stack with contracts
is this idiomatic Ada 2022?
set up an Alire project and add a dependency
why is my Pre contract not checked on a dispatching call?
review this .gpr project file
```

## Development

- `SKILL.md` is always-on: keep it compact. Add depth to `references/`, one
  topic per file, and update the reference table in `SKILL.md` when you add one.
- Qualify anything version-sensitive ("as of GNAT FSF <version>") rather than
  stating it as timeless fact, and re-verify against the installed toolchain.
- The reference files are filled in incrementally and work in progress; until one has the answer
  you need, fall back to the canonical sources linked in `SKILL.md`.

## Related work

- [`agent-sh/ada-spark`](https://github.com/agent-sh/ada-spark) — Ada + SPARK
  combined; prior art this one tries to improve on. Not used as a SPARK
  knowledge source by this skill — see the `gnatprove` skill below.
- [`AdaCore/skills`](https://github.com/AdaCore/skills) — official tool skills
  (`alire`, `gnatdoc`, `gnatfuzz`, `gnatprove`, `gnattest`) and the authoritative
  `alr` command reference. Its [`gnatprove` skill](https://github.com/AdaCore/skills/tree/main/plugins/adacore/skills/gnatprove)
  covers SPARK proof questions outside Ada-Pro's scope.
- [`AdaCore/gnat-foundry-intersection`](https://github.com/AdaCore/gnat-foundry-intersection)
  — a demonstration of agent-driven Ada development with tool-generated
  verification evidence.

## License

MIT

## Open issues

### Language content

1. Fill the private types section in `language-core.md`: private part and full
   view, limited private types, and how they enforce abstraction.
2. Add a small `Static_Predicate` and `Dynamic_Predicate` example to
   `contracts.md`. Label `Default_Initial_Condition` as a GNAT extension if the
   example uses it. `Contract_Cases` remains covered by the AdaCore `gnatprove`
   skill.
3. Add a container selection guide to `language-core.md`, including indefinite
   holders, cursors, and iteration.
4. Explain `limited with`, `private with`, `use type`, and `use all type` in
   `language-core.md`.
5. List the real `Ada.Numerics` packages in `language-core.md`:
   `Ada.Numerics.Elementary_Functions`, `Float_Random`, `Discrete_Random`, and
   `Generic_Elementary_Functions`.
6. Cover stream attributes (`'Read`, `'Write`, `'Input`, `'Output`) and
   `Ada.Streams.Stream_IO`. Explain that `'Image` is not a serialization format.
7. Expand `embedded-and-runtimes.md` with `Volatile`, `Atomic`,
   `Interrupt_Priority`, `Attach_Handler` for Ravenscar static attachment,
   secondary stacks, indefinite types, `System.BB` through Alire, and the
   `Restrictions` and `No_Implementation_*` pragma families.
8. Fill the casing, indentation, `_T`, and `_Access` sections in
   `style-and-naming.md`, including GNAT's default three-space indentation.
9. Correct the file naming advice in `language-core.md`: matching source file
   and unit names is GNAT's default, not an Ada language rule. Explain how to
   inspect a GPR project's `package Naming` before diagnosing a mismatch. See
   the [GPR source naming guide](https://docs.adacore.com/live/wave/gprbuild/html/gpr_user_guide/managing_sources.html).

### Structure and process

10. Add a canonical idiomatic example with a package spec and body, contracts,
    strong types, and generics in `references/examples.md` or `examples/`. The
    bounded stack prompt above has no model answer yet.
11. Add three to six lines of concrete examples to the link-only sections on
    numeric types, arrays, records, and subprogram parameter modes in
    `language-core.md`.
12. Add reasons to the `MUST NOT` rows in `SKILL.md` that lack them.
13. Add common pitfalls for `'Access` versus `'Unchecked_Access`,
    `Unchecked_Conversion` and endianness, `delay until` versus `delay`, modular
    wraparound, and `Get_Line` buffering.
