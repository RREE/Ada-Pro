# Ada Verification — Reference

_Deep-dive reference for the `Ada-Pro` skill. Loaded on demand. Covers static
and dynamic verification/analysis tooling (GNAT SAS, GNAT DAS, GNATcheck)._

> Sources: `AdaCore/gnat-foundry-intersection` and gnatsas documentation.

## GNAT SAS — static defect-finding (ex-CodePeer)

- **Availability:** GNAT SAS is an AdaCore commercial product, not part of
  the free GNAT FSF toolchain or the public Alire crate catalog. AdaCore
  directs new users to request a product evaluation and customers to GNAT
  Tracker for downloads. Do not suggest `alr install gnatsas`; confirm that
  the user has access to GNAT SAS before prescribing `gnatsas` commands.
- The product was renamed: CLI is **`gnatsas`**, not `codepeer`. Commands are
  `gnatsas analyze` then `gnatsas report`.
- Flags: `--mode=fast` (CI) / `--mode=deep` (thorough); results are files
  (`.sam`/`.sar`), not a SQL database; the old CodePeer `--level 0..4` switch is
  gone. Base result and diff to gate *only new* findings. SARIF output for CI.
- MISRA is enforced via **GNATcheck**; custom checks are written in **LKQL**.
  Invoke it against a project with a MISRA rule file, e.g.
  `gnatcheck -P <project>.gpr -rules <misra_rules_file>` — the rule files ship
  with the GNATcheck distribution; confirm exact rule-set names and flags with
  `gnatcheck --help` rather than guessing.

### Soundness — never conflate GNAT SAS with proof

- **GNAT SAS is unsound heuristic bug-finding on plain Ada** (no annotations):
  it produces false positives *and* false negatives, reporting "likely bugs" /
  CWE weakness classes. It does **not** prove absence of runtime errors.
- **GNATprove is sound formal proof** on the SPARK subset: within SPARK, no
  false negatives for absence-of-runtime-errors (AoRTE) at Silver+.
- "GNAT SAS / CodePeer proves no runtime errors" is **wrong** (correction-map
  rule). They are complementary tools, not substitutes.

## GNAT DAS — dynamic testing & coverage

A **separate** product family from GNAT SAS. Check tool availability in the
chosen toolchain before prescribing a command:

- **GNATcoverage (`gnatcov`):** Freely available as open-source source and as
  public Alire `gnatcov` and `gnatcov_bin` crates. It measures structural
  coverage, including statement and MC/DC coverage. The public Alire binary
  supports Ada only; the full tool also supports C/C++.
- **GNATtest (`gnattest`):** Freely available as open-source source and as
  public Alire `gnattest` and `gnattest_bin` crates. It generates AUnit test
  skeletons and a test harness.
- **GNATfuzz (`gnatfuzz`):** Part of AdaCore's commercial GNAT DAS offering.
  Its current guide requires GNAT Pro Ada x86_64, with GNAT Pro LLVM Ada also
  required for some fuzzing engines. Do not present it as a freely available
  GNAT FSF or Alire tool; confirm access before prescribing `gnatfuzz`.

GNATcheck is a separate coding-rules tool with public open-source source; it
is not supplied merely by installing the GNAT FSF compiler.

Do not conflate GNAT SAS (static defects) with GNAT DAS (dynamic
testing/coverage) — marketed and licensed separately. Confirmed via
[`AdaCore/gnat-foundry-intersection`](https://github.com/AdaCore/gnat-foundry-intersection)
and adacore.com/gnatpro.

## Explicitly NOT covered here

GNATprove (SPARK-only formal proof): assurance levels, loop invariants, ghost
code, ownership/borrow. This skill is Ada-focused — point SPARK-proof questions
to AdaCore's
[`gnatprove` skill](https://github.com/AdaCore/skills/tree/main/plugins/adacore/skills/gnatprove)
and the SPARK User's Guide.

## References
- [GNAT SAS User's Guide](https://docs.adacore.com/live/wave/gnatsas/html/user_guide/index.html)
- [AdaCore GNAT SAS product page](https://www.adacore.com/static-analysis-suite) and [download options](https://www.adacore.com/download)
- [Public Alire crate catalog](https://alire.ada.dev/crates.html)
- [GNAT SAS installation guide](https://docs.adacore.com/live/wave/gnatsas/html/user_guide/introduction.html)
- [GNATcoverage source](https://github.com/AdaCore/gnatcoverage), [Alire crate](https://alire.ada.dev/crates/gnatcov), and [Alire binary](https://alire.ada.dev/crates/gnatcov_bin)
- [GNATtest source](https://github.com/AdaCore/gnattest), [Alire crate](https://alire.ada.dev/crates/gnattest), and [Alire binary](https://alire.ada.dev/crates/gnattest_bin)
- [GNATfuzz User's Guide (toolchain requirements)](https://docs.adacore.com/live/wave/gnatdas/html/gnatdas_ug/gnatfuzz/gnatfuzz_part.html) and [GNAT DAS product page](https://www.adacore.com/dynamic-analysis-suite)
- [GNATcheck source](https://github.com/AdaCore/gnatcheck)
- AdaCore agent skills for the DAS tools — consult these for exact subcommand
  semantics instead of guessing:
  [`gnattest`](https://github.com/AdaCore/skills/tree/main/plugins/adacore/skills/gnattest),
  [`gnatfuzz`](https://github.com/AdaCore/skills/tree/main/plugins/adacore/skills/gnatfuzz),
  [`gnatdoc`](https://github.com/AdaCore/skills/tree/main/plugins/adacore/skills/gnatdoc)
- Note: AdaCore/skills (https://github.com/AdaCore/skills) has no dedicated
  `gnatsas` agent skill as of this writing — only `alire`, `gnatdoc`,
  `gnatfuzz`, `gnatprove`, `gnattest` (linked above). Do not assume `/gnatsas`
  exists as a slash command; consult `gnatsas --help` and the User's Guide
  instead.
