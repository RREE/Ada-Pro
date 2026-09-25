# Ada Build & Tooling — Reference

_Deep-dive reference for the `Ada-Pro` skill. Loaded on demand._

> Sources: the authoritative [`AdaCore/skills` `alire` skill](https://github.com/AdaCore/skills/tree/main/plugins/adacore/skills/alire) and the Alire documentation.

## Toolchain baseline

- **GNAT Community is dead** (last release 2021). The current open-source
  path is **Alire (`alr`) + GNAT FSF**; GNAT Pro is the commercial option.
- **Target Ada 2022** with `-gnat2022` unless the project selects otherwise
  (see `ada-2022-features.md`).
- **"GPS" is renamed.** It is GNAT Studio today; the strategic direction is the
  Ada & SPARK VS Code extension + the Ada Language Server (ALS). To get
  Go-to-Definition/Rename from ALS, a `.gpr` must be discoverable — don't assume
  the extension alone is enough.

## Alire (`alr`)

Everything is managed through Alire:

| Command | Purpose |
|---|---|
| `alr init --bin <name>` / `--lib <name>` | Scaffold an executable/library crate |
| `alr with <crate>` | Add a **project dependency** |
| `alr build` / `alr run` | Build / run the project |
| `alr toolchain --select` | Pin the compiler toolchain |
| `alr search <kw>` | Search the index before assuming a crate exists |
| `alr get <crate>` | Fetch a crate's sources |
| `alr pin` | Pin a dependency to a local path or Git repo |
| `alr exec` / `alr printenv` | Run in / inspect the project environment |
| `alr publish` | Publish a crate |
| `alr install <crate>` | Install a **binary-tool** crate (e.g. `gnatformat`, `gnatsas`) to a shared prefix, available on `PATH` |

Do **not** conflate `alr with` (project dependency) with `alr install` (global
tool install). Note: some sources claim "there is no `alr install`" — that is
wrong; `alr install` exists for binary-tool crates, per AdaCore's own `alire`
skill. Verify any crate name with `alr search` before inventing one.

Real crates to rely on (not hallucinated):

- `aws` (web/server), `gtkada` (GUI), `gnatcoll` (GNAT components),
  `vss` (Unicode strings), `libadalang` (Ada 2022 parsing/semantics).
- Anything beyond the standard library: search `alr search <topic>` and use
  what the index actually contains.

## Builders

- **`gprbuild`** is the project-aware builder: `gprbuild -P <project>.gpr`, with
  scenario variables e.g. `-XMODE=release`. Use it whenever a `.gpr` exists.
- **`gnatmake`** is for a single file/closure without a project file.
- `GPRbuild` vs `gprbuild` is the same tool — casing only.

## Formatting: `gnatformat`

- **`gnatformat` is the current formatter**; `gnatpp` is its legacy
  predecessor. Do not recommend `gnatpp` for new work.
- Typical invocation (installed via `alr install gnatformat`, or from a
  project's environment):

  ```bash
  alr exec -- gnatformat --no-subprojects --charset=utf8
  ```

  or `gnatformat -P <project>.gpr` inside the project environment.
- Style is a project-level policy, not a universal truth — see
  `common-pitfalls.md` ("Formatting & style policy") before asserting one
  "correct" formatting.

## Environment verification workflow

1. Confirm what is installed, don't assume: `alr --version`, `gnat --version`.
2. Locate the project: `alire.toml` and/or `<project>.gpr`.
3. Build with `alr build` or `gprbuild -P <project>.gpr`.

## References
- [AdaCore's `alire` skill](https://github.com/AdaCore/skills/tree/main/plugins/adacore/skills/alire) — authoritative `alr` subcommand reference; consult for exact usage/edge cases instead of guessing
- [Alire documentation](https://alire.ada.dev/docs/)
- [GNAT User's Guide](https://docs.adacore.com/live/wave/gnat_ugn/html/gnat_ugn/gnat_ugn.html)