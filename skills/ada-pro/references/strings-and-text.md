# Ada Strings & Text — Reference

_Deep-dive reference for the `Ada-Pro` skill. Loaded on demand. Focus: the
string type families, UTF-8/Unicode handling, encoding conversions, and text
I/O. This is the #1 confusion area reported by the community — read it before
writing any non-ASCII string handling._

> Sources: AdaCore Gem #144 (linked below), learn.adacore.com, the Alire
> Unicode guidelines, the ada-lang.io UTF-8 how-to, and RM A.4.11.

## The string type zoo

| Type | Element | Width | Purpose |
|---|---|---|---|
| `String` | `Character` | 8-bit | **Latin-1 (ISO-8859-1), not UTF-8** |
| `Wide_String` | `Wide_Character` | 16-bit | Mostly legacy; avoid for new code |
| `Wide_Wide_String` | `Wide_Wide_Character` | 32-bit | Full Unicode code points (UTF-32) |
| `Unbounded_String` | `Character` | variable | Dynamic strings; `Ada.Strings.Unbounded` (plus `Wide_`/`Wide_Wide_Unbounded` variants) |
| `Bounded_String` | `Character` | capped | Stack-safe dynamic strings; `Ada.Strings.Bounded` |

- `String` is an array of `Character`; `Character` covers exactly 256
  Latin-1 values (RM 3.5.2). **Never assume `String` is UTF-8** — it is not.
- The 32-bit `Wide_Wide_String` family is the practical Unicode workhorse;
  the 16-bit `Wide_String` family is a middle ground that is rarely useful
  today.
- Conversions between families: `Ada.Characters.Conversions`
  (`To_Wide_String` / `To_Wide_Wide_String`), and
  `Ada.Strings.Unbounded.To_String` / `To_Unbounded_String` (and their
  `Wide_Wide_` counterparts).
- Character classification / case conversion: `Ada.Characters.Handling`
  (`Is_Letter`, `To_Upper`, …).

## UTF-8 basics

- UTF-8 is ASCII-compatible (ASCII characters are 1 byte); code points above
  127 are encoded as 2–4 bytes. Consequences:
  - `S'Length` counts **bytes**, not characters.
  - Slicing by character index is only valid at character boundaries —
    never cut in the middle of a multi-byte sequence.
  - Scanning for ASCII delimiters (space, newline) byte-by-byte is safe;
    iterating "characters" is not.
- GNAT does **not** assume UTF-8 by default (compiler defaults to Latin-1
  for source text and string literals):
  - `-gnatW8` — tell the compiler sources/string literals are UTF-8 (also
    enables UTF-8 identifiers); `-gnatiw` is the identifier-specific variant.
  - Binder: `-W8` (implied by `-gnatW8`).
  - `pragma Wide_Character_Encoding (UTF8);` is a per-file GNAT alternative
    to the switch.
  - New Alire crates get `-gnatW8` by default (`alr init`).
- With `-gnatW8`, a literal like `"Привет"` must be of `Wide_Wide_String`
  type — assigning it to `String` fails with
  `literal out of range of type Standard.Character`.

## `Ada.Strings.UTF_Encoding` — the standard conversion API

- `subtype UTF_8_String is String;` — a *byte* representation of UTF-8,
  mislabeled as `String` (a known design compromise, not a mistake to
  "fix" by inventing types).
- `type Encoding_Scheme is (UTF_8, UTF_16BE, UTF_16LE);`
- Child packages select the conversion pair:
  - `Ada.Strings.UTF_Encoding.Wide_Wide_Strings` — `Encode` /
    `Decode` between `Wide_Wide_String` and `UTF_8_String` / `UTF_String`
    (the most common pair).
  - `Ada.Strings.UTF_Encoding.Strings` / `.Wide_Strings` — same for the
    other string types.
  - `Ada.Strings.UTF_Encoding.Conversions` — `Convert` between encoding
    schemes, `Encoding` (BOM detection).
- Semantics: `Encode`/`Decode` raise `Encoding_Error` on invalid sequences
  (e.g. UTF-16 surrogates 16#D800#..16#DFFF#); an initial BOM matching the
  expected scheme is skipped on decode; `Output_BOM => True` adds one on
  encode. Results have lower bound 1.

```ada
with Ada.Strings.UTF_Encoding.Wide_Wide_Strings;
with Ada.Wide_Wide_Text_IO;
procedure Demo is
   use Ada.Strings.UTF_Encoding.Wide_Wide_Strings;
   Hello  : constant Wide_Wide_String := "Привет";       -- needs -gnatW8
   As_UTF8 : constant UTF_8_String := Encode (Hello);    -- byte representation
   Back    : constant Wide_Wide_String := Decode (As_UTF8);
begin
   Ada.Wide_Wide_Text_IO.Put_Line (Back);
end Demo;
```

## Text I/O and encodings

- `Ada.Text_IO` works on `String` and therefore expects **Latin-1** — you
  cannot print a UTF-8-encoded `String` variable through it.
- `Ada.Wide_Wide_Text_IO` works on `Wide_Wide_String` (UTF-32); with
  `-gnatW8` in effect its output is UTF-8 on most platforms. Known rough
  edge: `Wide_Wide_Text_IO` encoding behavior on Windows is reported
  inconsistent — verify actual output, don't paper over it confidently.
- To write **byte-exact UTF-8** (no runtime transformation), use
  `Ada.Streams.Stream_IO` or `GNAT.IO` on the encoded bytes.
- GNAT file-opening encoding via `Form`: `"WCEM=8"` (or
  `"ENCODING=UTF8"`) for wide text I/O without the compile-time switch.
- Environment variables `GNAT_CCS_ENCODING` / `GNAT_CODE_PAGE` can
  override runtime encoding on Windows — beware of surprises.

## Beyond the standard library

- **VSS** (`alr with vss`, AdaCore) — encoding-agnostic Unicode strings:
  character/grapheme-cluster boundaries, UTF-8/UTF-16 offsets, JSON;
  the modern choice for serious Unicode work (see AdaCore's "Introduction
  to VSS library", by Maxim Reznik — link below).
- **uxstrings** (`alr with uxstrings`) — Unicode-aware unbounded strings.
- **Matreshka League** (`alr with matreshka_league`) — code-point-level
  strings, transcoders, regex, JSON/XML.
- `'Image` output is implementation-defined — for stable text output use
  `T'Put_Image` or an explicit formatting function (see
  `common-pitfalls.md`).

## References

- [AdaCore Gem #144 — A Bit of Bytes: Characters and Encoding Schemes](https://www.adacore.com/blog/gem-144-a-bit-of-bytes-characters-and-encoding-schemes) — AdaCore article on character sets vs encoding schemes, UTF-8/16/32 in Ada
- [ada-lang.io — UTF-8 encoding in GNAT (how-to)](https://ada-lang.io/docs/learn/how-tos/gnat_and_utf_8/) — the walkthrough this file's `-gnatW8`/`-gnatiw`/`Form`/brackets-encoding details draw on
- [AdaCore blog (Maxim Reznik) — Introduction to VSS library](https://www.adacore.com/blog/introduction-to-vss-library)
- [learn.adacore.com — Advanced Ada: Strings](https://learn.adacore.com/courses/advanced-ada/parts/data_types/strings.html)
- [Alire — Unicode guidelines](https://alire.ada.dev/docs/unicode.html)
- [Ada 2022 RM — A.4.11 String Encoding](https://www.ada-auth.org/standards/22rm/html/RM-A-4-11.html)
