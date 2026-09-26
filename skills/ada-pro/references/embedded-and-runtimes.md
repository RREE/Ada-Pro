# Ada Embedded & Runtimes — Reference

_Deep-dive reference for the `Ada-Pro` skill. Loaded on demand._

> Sources: the Ada 2022 RM (profiles, D.13) and the GNAT UGX, merged with
> `Ada-Pro`'s corrections.

## Choose the runtime and tasking profile before designing the code

On bare-metal or restricted targets, choose the runtime first. If that
runtime supports tasking, select a suitable tasking profile such as
`pragma Profile (Ravenscar);` or `pragma Profile (Jorvik);`.

- **Ravenscar** is the classic hard-real-time profile.
- **Jorvik** (Ada 2022) is a strict **superset** of Ravenscar: it relaxes
  `Max_Entry_Queue_Length`, `Max_Protected_Entries`, allows relative `delay`,
  and richer `Pure_Barriers`.
- Both preserve hard-real-time guarantees.

## Light and Light-Tasking runtimes

GNAT Pro's Light runtime enforces `No_Exception_Propagation`,
`No_Finalization`, and `No_Tasking`. Light-Tasking keeps the first two
restrictions but supports Ravenscar and Jorvik tasking. Other bare-board
runtimes have their own restrictions. Check the selected runtime before
designing around a language feature.

Under Light and Light-Tasking:

- **Exceptions** can be raised and handled *locally* but **cannot propagate**
  across boundaries; an unhandled exception goes to the `Last_Chance_Handler`
  with no resume — no desktop-style unwind.
- **Controlled objects are unavailable** under `No_Finalization`. Use
  explicit initialization and cleanup instead of `Initialize` /
  `Finalize` / `Adjust`.
- Do not design with exceptions-as-control-flow or controlled types under a
  runtime with these restrictions.

## Fixed-point and representation clauses

- **Fixed-point (`delta`)**: arithmetic is pure-integer only when `small` is a
  ratio of suitably-sized integers; the range may stop at `'Last - Delta`;
  ordinary fixed-point arithmetic is not fully specified. Prefer integer for
  cross-hardware stability unless fixed-point is required.
- **Representation clauses** depend on machine endianness and storage unit —
  use them only for real hardware/ABI layout. They are not portable style.

## Elaboration order

When initialization order matters, control elaboration explicitly:

- Mark units `Pure` / `Preelaborate` / `Elaborate_Body` where applicable.
- Add `pragma Elaborate_All (X)` when elaboration code calls into a `with`ed
  non-pure unit; otherwise the binder can pick a legal-but-failing order.

## References
- GNAT UGX — cross-development / embedded targets: https://docs.adacore.com/live/wave/gnat_ugx/html/gnat_ugx/gnat_ugx.html
- [GNAT Pro Light and Light-Tasking runtimes](https://docs.adacore.com/gnat_ugx-docs/html/gnat_ugx/gnat_ugx/gnat_runtimes.html)
- Ada 2022 RM — Profiles: [RM D.13](https://www.ada-auth.org/standards/22rm/html/RM-D-13.html)
