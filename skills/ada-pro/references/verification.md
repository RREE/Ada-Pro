# Ada Verification — Reference

_Deep-dive reference for the `Ada-Pro` skill. Loaded on demand. Covers static
and dynamic verification/analysis tooling (GNAT SAS, GNAT DAS, GNATcheck)._

> Sources: `AdaCore/gnat-foundry-intersection` and gnatsas documentation.

## GNAT SAS — static defect-finding (ex-CodePeer)

- The product was renamed: CLI is **`gnatsas`**, not `codepeer`. Commands are
  `gnatsas analyze` then `gnatsas report`.
- Flags: `--mode=fast` (CI) / `--mode=deep` (thorough); results are files
  (`.sam`/`.sar`), not a SQL database; the old CodePeer `--level 0..4` switch is
  gone. Base result and diff to gate *only new* findings. SARIF output for CI.
- MISRA is enforced via **GNATcheck**; custom checks are written in **LKQL**.

### Soundness — never conflate GNAT SAS with proof

- **GNAT SAS is unsound heuristic bug-finding on plain Ada** (no annotations):
  it produces false positives *and* false negatives, reporting "likely bugs" /
  CWE weakness classes. It does **not** prove absence of runtime errors.
- **GNATprove is sound formal proof** on the SPARK subset: within SPARK, no
  false negatives for absence-of-runtime-errors (AoRTE) at Silver+.
- "GNAT SAS / CodePeer proves no runtime errors" is **wrong** (correction-map
  rule). They are complementary tools, not substitutes.

## GNAT DAS — dynamic testing & coverage

A **separate** product family from GNAT SAS:

- Unit testing: `gnattest` (AUnit harness generation).
- Fuzz testing: `gnatfuzz` (coverage-guided).
- Structural coverage: MC/DC, statement coverage (Ada and C/C++).

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
- [GNAT SAS User's Guide](https://docs.adacore.com/live/wave/gnatsas/html/gnatsas_ug/gnatsas_ug.html)
- Note: AdaCore/skills (https://github.com/AdaCore/skills) has no dedicated
  `gnatsas` agent skill as of this writing — only `alire`, `gnatdoc`,
  `gnatfuzz`, `gnatprove`, `gnattest`. Do not assume `/gnatsas` exists as a
  slash command; consult `gnatsas --help` and the User's Guide instead.