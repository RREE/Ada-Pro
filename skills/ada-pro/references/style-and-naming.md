# Ada Style & Naming — Reference

_Deep-dive reference for the `Ada-Pro` skill. Loaded on demand. Focus: naming
conventions and code style — always as **project-level policy**, never as a
single universally "correct" style (see `common-pitfalls.md`)._

> Sources: the Ada Quality and Style Guide (ada-lang.io, linked below), GNAT's
> default style, and `Ada-Pro`'s corrections.

## Casing

_Content pending._

## Indentation

_Content pending._ (GNAT default: 3 spaces.)

## Type name suffix (`_T`)

_Content pending._

## Access type suffix (`_Access`)

_Content pending._

## Package and type names: singular vs plural

- **Keep public package names distinct from public type and object names in the
  same API.** Use plural child package names for collections of related types,
  e.g. `Labels.Label`, `Buttons.Button`, `Displays.Display` —
  not a package and its primary type sharing one name.
- Related style-guide rule: avoid using the same identifier for different kinds
  of declarations (an object and a child package) — visibility clashes with
  subunits are the classic failure (Style Guide §3.2.1).

## References

- [Ada Quality and Style Guide](https://ada-lang.io/docs/style-guide/Ada_Style_Guide/) — the community-maintained update of the Ada 95 Quality & Style guide to Ada 2012 (adapted from the [Ada Style Guide on Wikibooks](https://en.wikibooks.org/wiki/Ada_Style_Guide))
- [Style Guide §3.2 — Naming Conventions](https://ada-lang.io/docs/style-guide/s3/02/)
