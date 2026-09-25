# Ada-Pro

Skill file and references that teach AI coding agents to write idiomatic, correct,
current Ada — Ada 2022 on the modern toolchain (Alire + GNAT FSF, GNAT Pro).

**This is work in progress, in a very early stage. Use at your own risk.**

## Scope

- **Plain Ada only.** Writing, porting, reviewing, debugging `.ada` / `.ads` / `.adb` /
  `.gpr` files; packages, strong typing, generics, tagged types, exceptions,
  tasking, contracts (`Pre` / `Post` / `Contract_Cases`), Alire projects, GNAT /
  GNAT SAS tooling, embedded runtimes where they affect idiomatic Ada choices.
- **SPARK is cross-referenced, not covered.** Proof, assurance levels, and
  ownership/borrow reasoning belong to SPARK-specific material; this skill
  points to it rather than guessing.

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
  README.md                You are here
  LICENSE                  MIT
  skills/ada-pro/          The skill — copy or symlink this folder into an agent
    SKILL.md               Always-on skill: correction map + rules + workflow
    references/
      language-core.md     Packages, types, generics, OOP, tasking, visibility
      contracts.md         Pre/Post/Contract_Cases, class-wide contracts, LSP
      ada-2022-features.md @, declare expressions, 'Reduce, delta aggregates
      strings-and-text.md    String families, UTF-8/Unicode, encodings, VSS
      build-and-tooling.md Alire, gprbuild, .gpr files, GNAT Studio/ALS
      verification.md      GNAT SAS & GNAT DAS & GNATcheck
      embedded-and-runtimes.md  Ravenscar/Jorvik, light runtimes
      common-pitfalls.md   Stale idioms, hallucinated APIs, portability traps
```

`SKILL.md` stays short; the deep content lives in `references/`, loaded on
demand. Canonical material (RM, AARM, GNAT UG/RM/UGX) is linked, not copied, so
the skill does not drift as the toolchain evolves.

## Installation

Copy (or symlink) the `skills/ada-pro` folder into your agent's skills
directory:

| Agent | Directory |
|---|---|
| Claude Code | `~/.claude/skills/` |
| OpenCode | `~/.config/opencode/skills/` |
| OpenAI Codex | `~/.codex/skills/` |
| Cursor | `.cursor/skills/` |
| Any SKILL.md agent | `~/.agents/skills/` |

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
- The reference files are filled in incrementally; until one has the answer
  you need, fall back to the canonical sources linked in `SKILL.md`.

## Related work

- [`agent-sh/ada-spark`](https://github.com/agent-sh/ada-spark) — Ada + SPARK
  combined; prior art this one tries to improve on. Not used as a SPARK
  knowledge source by this skill — see the `gnatprove` skill below.
- [AdaCore's `gnatprove` skill](https://github.com/AdaCore/skills/tree/main/plugins/adacore/skills/gnatprove) — the SPARK proof tool skill; the redirect target for SPARK-proof questions, which Ada-Pro does not cover.
- [`AdaCore/skills`](https://github.com/AdaCore/skills) — official tool skills
  (`alire`, `gnatdoc`, `gnatfuzz`, `gnatprove`, `gnattest`), the authoritative
  `alr` command reference.

## License

MIT
