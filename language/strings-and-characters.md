# Zax strings and characters

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers choosing, constructing, joining, validating, or exchanging byte strings, text, characters, and legacy encoded strings |
| Applies To | Programmer-facing string and character identities and their literal-time behavior; not a complete runtime string library or formal specification |
| Implementation State | Not established by this repository |
| Owns | Explicit string code-unit backing; `Byte`, `U8`, `AsciiChar`, `Ucs2`, and `Rune` distinctions as used by text; `String`, ASCII, UCS-2, UTF-8/16/32, MBCS, and legacy native string identities; direct, escaped, raw-unit, RFC, XML, and code-page literal catalogs; Unicode scalar/grapheme boundaries; policy-selected legacy termination; left-owned compile-time joining; literal-time validation, costs, diagnostics, and source stability |
| Does Not Own | General literal source/declarations ([literal source and operators](literal-source-and-operators.md)); intrinsic array and slice behavior ([arrays and slices](arrays-and-slices.md)); runtime string storage representation, allocation, mutation, slicing, conversion, normalization, grapheme/presentation APIs, locale, formatting, foreign ABI, exact code-page tables, contiguous ordinary-string access, or exact generic MBCS/termination-policy syntax |
| Source / Provenance | Legacy string material in [basics](../basics.md) and the retired root casting page, refined against current integer, identity, literal, source, operator, and scalar design |

## Choose bytes, characters, or validated text

```zax
myBytes := "hello ☃"
// String: unrestricted bytes; this literal contributes valid UTF-8 bytes.

myAscii := ascii'hello'
// AsciiString: every element is an AsciiChar.

myRune := rune'☃'
// Rune: exactly one Unicode scalar.

myUtf8 := utf8'hello ☃'
myUtf16 := utf16'hello ☃'
myUtf32 := utf32'hello ☃'
// Validated Unicode text with an explicit code-unit encoding.
```

These identities are not interchangeable merely because one source phrase can
construct each of them.

`String` is the general byte string. It can hold every eight-bit sequence,
including NUL and malformed UTF-8. Ordinary source happens to populate it with
well-formed UTF-8 bytes; that does not become a permanent type invariant.

`AsciiString` and the UTF types are validated text identities. Their invariants
survive construction and mutation.

## Underlying code-unit types

The logical unit type is programmer-visible even though ordinary string storage
may later be flat, segmented, rope-like, inline, or another representation:

| Identity | Logical underlying units | Physical scalar representation |
| --- | --- | --- |
| `String` | `U8` | Unrestricted eight-bit values |
| `AsciiString` | `AsciiChar` | Strict identity over `U8`, valid `0x00..0x7F` |
| `Ucs2String` | `Ucs2` | Strict identity over `U16`, BMP nonsurrogate values |
| `Utf8String` | `U8` | Well-formed UTF-8 code units |
| `Utf16String` | `U16` | Well-formed UTF-16 code units |
| `Utf32String` | `U32` | Unicode scalar code units |
| `Rune` | One scalar | Strict identity over `U32` |
| `MbcsString<Encoding>` | `U8` | Valid sequence under the selected encoding |
| `Legacy.CharString` | `Legacy.Char` | Length-tracked native narrow code units |
| Optional `Legacy.WideString` | `Legacy.WChar` | Length-tracked native wide code units |
| `Legacy.TerminatedCharString<Policy>` | `Legacy.Char` | Concrete policy-selected terminated narrow representation |
| `Legacy.TerminatedWideString<Policy>` | `Legacy.WChar` | Concrete policy-selected terminated wide representation |

`MbcsString<Encoding>` expresses the relationship; exact generic syntax remains
future work.

Concrete `U16`/`U32` code-unit endianness is part of string type identity.
Unqualified forms follow the active environment. Exact target, compiler-host,
and absolute-endian paths remain future type-family syntax.

## Character identities

### `Byte`

`Byte` is a strict identity over `U8` with values `0x00..0xFF`. It is not a
transparent alias and says nothing about character encoding:

```zax
myByte := byte'A'
myRaw := Byte.h'FF'
```

A high byte is valid `Byte`. It is not automatically a Unicode code point or a
partial UTF-8 sequence.

`Byte` and singular `U8` never admit directly into validated text, even when
their numeric value is below `0x80`:

```zax
utf8'' <+> Byte.h'41'   // error: Byte has no character semantics
utf8'' <+> (: U8 = 65) // error: U8 has numeric semantics
```

Use `AsciiChar` for ASCII meaning, or validate a complete `String` or borrowed
`U8[]` sequence as UTF-8. `Byte` may append raw data to `String` or a raw
`U8` array/slice.

### `AsciiChar`

`AsciiChar` is a distinct semantic identity with underlying `U8` storage and
valid values `U+0000..U+007F`:

```zax
myAscii := AsciiChar.s'A'
myControl := AsciiChar.h'09'
myInvalid := AsciiChar.h'80' // error: outside ASCII
```

The unused high-bit patterns are invalid `AsciiChar` representations. This
restriction provides one stable mapping into every Unicode encoding.

There is no universal “extended ASCII.” Values `0x80..0xFF` receive different
meanings in ISO-8859-1, Windows-1252, CP437, and other encodings. Use `Byte` for
raw data or an exact legacy encoded identity.

### `Ucs2`

`Ucs2` represents one Unicode scalar in the Basic Multilingual Plane:

```text
U+0000..U+FFFF, excluding U+D800..U+DFFF
```

It cannot represent supplementary scalars. Unicode noncharacters remain valid
scalars.

### `Rune`

`Rune` represents exactly one Unicode scalar:

```text
U+0000..U+10FFFF, excluding U+D800..U+DFFF
```

A `Rune` is not a UTF-8 byte, UTF-16 code unit, or user-perceived grapheme:

```text
U+00E9                 one Rune
U+0065 U+0301          two Runes, often displayed like U+00E9
family emoji sequence  several Runes, often displayed as one grapheme
```

Zax does not normalize scalar sequences implicitly. Combining order, grapheme
segmentation, rendering support, and visually suspicious combinations are
presentation or higher-level text-validation concerns.

## String identities

### `String`

`String` is a sequence of unrestricted `U8` values. It does not use `Byte` as
its element identity:

```zax
myText := "hello"
mySame := s'hello'
myBytes := String.h'00 FF'
```

The first two start with valid UTF-8 because source text is valid UTF-8. The raw
hexadecimal form may create any bytes.

Admission to a validated text identity checks the complete value:

```zax
myUtf8 := utf8'' <+> myStringLiteral
// Compile-time join succeeds only when the String bytes form valid UTF-8.
```

Runtime admission syntax remains future string design.

### `AsciiString`

`AsciiString` is a sequence of `AsciiChar`. Its values may include ASCII control
characters produced by a literal parser even though C0 controls cannot occur
physically inside Zax quoted source.

### `Ucs2String`

`Ucs2String` is a sequence of BMP Unicode scalars excluding surrogates. It
cannot represent supplementary Unicode values.

### UTF strings

`Utf8String`, `Utf16String`, and `Utf32String` represent the same domain of
Unicode scalar sequences with different code units:

- `Utf8String` contains well-formed UTF-8 `U8` units.
- `Utf16String` contains well-formed UTF-16 `U16` units.
- `Utf32String` contains `U32` units that are each Unicode scalars.

UTF-16 uses a high/low surrogate pair to encode one supplementary scalar. A
valid pair is part of the encoding; an isolated surrogate is invalid. Conversion
decodes the pair to one scalar before encoding the destination.

Controls and Unicode noncharacters are valid values. Assigned-character status
does not participate in type validity, so a Unicode database update cannot
silently invalidate a string.

## Direct literal forms

General attachment, payload, merging, declaration, and required execution rules
are defined by [literal source and operators](literal-source-and-operators.md).

The common main aliases are:

```text
s      -> String.s
c      -> String.c
byte   -> Byte.s
rune   -> Rune.s
ascii  -> AsciiString.s
utf8   -> Utf8String.s
utf16  -> Utf16String.s
utf32  -> Utf32String.s
```

Ordinary `""` is shorthand for the direct `String.s` literal:

```zax
myA := "ordinary bytes"
myB := s'ordinary bytes with a " quote'
```

Type-owned forms make result identity and transformation explicit:

```zax
Byte.s'A'
Byte.c'\n'
Byte.h'0A'

AsciiChar.s'A'
AsciiChar.c'\t'
AsciiChar.h'7F'

Ucs2.s'☃'
Ucs2.c'\u2603'
Ucs2.h'2603'

Rune.s'☃'
Rune.c'\u2603'
Rune.h'2603'

Utf8String.s'valid Unicode text'
Utf16String.s'valid Unicode text'
Utf32String.s'valid Unicode text'
```

The source scanner verifies admitted UTF-8. The selected literal operator
performs element-count, encoding, and result-domain validation:

```zax
rune'é'
// error when written as U+0065 followed by U+0301: two scalars
```

### Direct character rules

| Form | Result | Required payload result |
| --- | --- | --- |
| `byte''` / `Byte.s''` | `Byte` | Exactly one resulting source byte |
| `Byte.c''` | `Byte` | Exactly one decoded byte |
| `Byte.h''` | `Byte` | Numeric value `0..255` |
| `AsciiChar.s''` | `AsciiChar` | Exactly one ASCII scalar |
| `AsciiChar.c''` | `AsciiChar` | Exactly one decoded ASCII scalar |
| `AsciiChar.h''` | `AsciiChar` | Numeric value `0..0x7F` |
| `Ucs2.s/c/h` | `Ucs2` | Exactly one BMP nonsurrogate scalar |
| `rune''` / `Rune.s/c/h` | `Rune` | Exactly one Unicode scalar |

`byte'é'` is invalid because the source scalar encodes as more than one source
byte. `rune'é'` is valid because it contains one scalar.

### Direct string rules

| Form | Result | Behavior |
| --- | --- | --- |
| `""`, `s''`, `String.s''` | `String` | Encode admitted source scalars as UTF-8 bytes |
| `c''`, `String.c''` | `String` | Apply the C-style parser below |
| `String.h''` | `String` | Parse hexadecimal byte pairs; permit every byte sequence |
| `ascii''`, `AsciiString.s''` | `AsciiString` | Require every scalar to be ASCII |
| `utf8''`, `Utf8String.s''` | `Utf8String` | Encode source scalars as UTF-8 |
| `utf16''`, `Utf16String.s''` | `Utf16String` | Encode source scalars as UTF-16 |
| `utf32''`, `Utf32String.s''` | `Utf32String` | Encode source scalars as UTF-32 |
| `Text.ucs2''`, `Ucs2String.s''` | `Ucs2String` | Reject supplementary scalars |

Raw validated-code-unit forms check the complete sequence:

```zax
myUtf8 := Utf8String.h'E2 98 83'
myUtf16 := Utf16String.h'D83D DE00'
myUtf32 := Utf32String.h'0001F600'
```

Code-unit digits are most-significant first and independent of target byte
endianness.

## C-style literal parsing

`c` is a literal parser, not Zax lexical escaping. It recognizes:

```text
\\  \'  \"
\0  \a  \b  \t  \n  \v  \f  \r
\ooo
\xHH
\uHHHH
\UHHHHHHHH
```

`\ooo` consumes one through three octal digits greedily and must produce a byte
no greater than `0xFF`. `\0` is its one-digit NUL form. After three octal digits,
another digit is ordinary payload:

```zax
c'\1234'
// Octal 123 followed by literal 4.

c'\400' // error: octal value exceeds one byte
```

`\xHH` requires exactly two hexadecimal digits and emits one byte.
`\uHHHH` and `\UHHHHHHHH` name one Unicode scalar and emit its UTF-8 bytes.
Surrogate and out-of-range scalar escapes are errors.

Unknown, incomplete, and overlong escapes are errors. Alternate source
delimiters often avoid quote escapes, but the parser still accepts them:

```zax
c'\'' // error: the single quote closes at the Zax source layer
c"\'"
c"'" // direct quote; no escape needed
c'\"'
```

The `c` parser recognizes `\'`; the Zax scanner does not treat it as a source
escape.

## Encoded-data transformations

Specialized/versioned transformations use semantic namespaces rather than
occupying the main literal namespace.

RFC 4648 decoding returns binary data:

| Path | Payload |
| --- | --- |
| `Rfc4648.Encoding.base16` | Base16 encoding |
| `Rfc4648.Encoding.base32` | Base32 encoding |
| `Rfc4648.Encoding.base32hex` | Extended-hex Base32 encoding |
| `Rfc4648.Encoding.base64` | Base64 encoding |
| `Rfc4648.Encoding.base64url` | URL/file-safe Base64 encoding |

```zax
myBytes :=
  Rfc4648.Encoding.base64'VGhlIHF1aWNrIGJyb3duIGZveC4='
```

These declarations produce an owning fixed `U8[N]`, not `String`: their source
payload is text, and compile-time decoding determines exact output count `N`.
Exact padding and accepted-alias policy remain future encoding-contract work.

XML entity processing is likewise explicit:

```zax
myText := Xml.entities'John&#39;s Fish &amp; Chips'
// Utf8String
```

Its exact XML version and entity contract belong to the declaration/library
version.

## Legacy native and encoded characters

Legacy character identities remain supported without being the ordinary text
choice:

```text
Legacy.Char
Legacy.WChar
Legacy.Latin1Char
Legacy.CharString
Legacy.WideString
Legacy.TerminatedCharString<TerminatorPolicy>
Legacy.TerminatedWideString<TerminatorPolicy>
```

`Legacy.Char` is always defined. The active profile chooses its native narrow
character width, signedness, encoding, and representation. An ordinary
eight-bit profile may represent it with `I8` or `U8` while preserving the
`Legacy.Char` identity.

`Legacy.WChar` and `Legacy.WideString` are platform-optional. When present, the
profile selects their native wide width, signedness, encoding, and
representation. A platform without a wide-character model does not synthesize
one from `I32`, `U32`, or `Rune`.

No profile is silently assumed to use ASCII or Unicode. Exact signed/unsigned C
and C++ character ABI types remain future interoperability work.

Future compile-time/reflection design must permit a static enclosed-source or
declaration-resolution query whose payload may mention unavailable
`Legacy.WChar` without producing an ordinary error. Until then, using an absent
platform type is a compile error.

Representative literals:

```zax
Legacy.Char.s'A'
Legacy.CharString.s'native string'

// Available only when supplied by the platform profile.
Legacy.WChar.s'W'
Legacy.WideString.s'native wide string'
```

### Latin-1

`Legacy.Latin1Char` has underlying `U8`. Every byte maps under ISO-8859-1 to
`U+0000..U+00FF`:

```zax
Legacy.Latin1Char.s'ÿ'
Legacy.Latin1Char.h'FF'
```

It is one character, not a string. A corresponding string is the ISO-8859-1
specialization of MBCS:

```text
Legacy.Latin1Char
  = MbcsChar<Legacy.CodePages.iso88591>

MbcsString<Legacy.CodePages.iso88591>
  = length-tracked ISO-8859-1 U8 sequence
```

The notation is conceptual until exact generic syntax is established.

### MBCS strings

MBCS is the general code-page encoded-byte family. An SBCS encoding is a
specialization whose maximum encoded-character sequence length is one.

`MbcsString<Encoding>`:

- stores `U8` units;
- validates the selected encoding;
- tracks logical byte length;
- permits embedded NUL;
- requires no trailing sentinel; and
- has no `.c_str()` guarantee.

### Terminated legacy string policies

`Legacy.CharString` and optional `Legacy.WideString` are ordinary
length-tracked native-code-unit strings. They permit NUL and promise no
terminator or contiguous storage.

A terminated representation has a distinct concrete type:

```text
Legacy.TerminatedCharString<TerminatorPolicy>
Legacy.TerminatedWideString<TerminatorPolicy>
```

The policy statically owns:

- the terminating code unit or sequence;
- whether that sequence is excluded from logical contents or represented
  through an escape/translation;
- required contiguous storage and physical suffix;
- admission and conversion rules;
- safe mutation/reallocation restoration;
- logical length treatment; and
- any additional platform/API meaning.

The policy is part of concrete type identity; termination is never selected
dynamically from one `CharString`.

The common NUL policies may have convenient aliases:

```text
Legacy.NulTerminatedCharString
Legacy.NulTerminatedWideString
```

They reject embedded NUL, maintain one trailing NUL outside logical length, and
enable a `.c_str()`-style view with an applicable lifetime. Other platform
policies may use another terminator, no in-band terminator, escaping, or another
admission contract.

Writable raw access that could break a termination or encoding invariant
requires a constrained or unsafe boundary. Ordinary Zax, MBCS, and baseline
legacy native strings have no trailing-NUL requirement.

### Admission to native legacy strings

Every available native legacy string validates construction from an array of
its exact code-unit type:

```text
Legacy.Char[]  -> Legacy.CharString
Legacy.WChar[] -> Legacy.WideString, when defined
```

These `[]` inputs are borrowed complete-sequence slices. Construction produces
an independently owned string under its selected transfer contract.

Construction preserves the supplied logical units after validating any
platform encoding/code-unit invariants. NUL is permitted.

Additional text admission is profile-dependent. A profile with a stable Unicode
mapping may transcode ASCII, UCS-2, Rune, or UTF text and reject unrepresentable
scalars. `String` must first validate as UTF-8. A truly opaque native character
set may expose only raw-code-unit construction.

Construction of a `TerminatedCharString<Policy>` or
`TerminatedWideString<Policy>` additionally applies that policy's terminator,
in-band-value, contiguity, and admission rules.

### Named code pages

A code-page literal is qualified under `Legacy.CodePages`:

```zax
myLegacy := Legacy.CodePages.cp1252'price: €10'
```

It returns a string carrying that exact code-page identity, rejects
unrepresentable scalars, permits embedded NUL, and has no trailing sentinel.

The initial name catalog is:

```text
Windows:
  cp1250 cp1251 cp1252 cp1253 cp1254 cp1255 cp1256 cp1257 cp1258
  cp932 cp936 cp949 cp950

DOS/OEM:
  cp437 cp850 cp852 cp855 cp857 cp858 cp866

ISO:
  iso88591 iso88592 iso88595 iso88597 iso88598 iso88599 iso885915

Separately named:
  shiftjis gb2312 gbk big5 euckr
```

The separately named encodings are not assumed aliases. CP932 is not identical
to base Shift_JIS; CP949/UHC extends ordinary EUC-KR; CP936/GBK and CP950/Big5
require exact mapping comparison before any alias is declared.

The concrete string is an `MbcsString` specialization with `U8` backing. Exact
generic source syntax remains future string/generic design.

## Compile-time joining

Every join is explicit:

```zax
left <+> right
```

The left sequence identity owns admission and remains the result identity:

```text
left sequence
<+> right value
-> admit or transcode right under left's rules
-> append
-> return left's identity
```

Literal joins execute from left to right during compilation. Invalid admission,
unrepresentable characters, broken encoding, embedded legacy NUL, and capacity
failure are compile-time diagnostics. The complete result must fit the
destination sequence's `IndexSize` limit.

### `String` on the left

`String <+> rhs` returns `String`:

- `String`, `Byte`, and `U8[]` append raw bytes.
- `AsciiChar`/`AsciiString` append ASCII bytes.
- `Legacy.Latin1Char`, `Ucs2`, `Rune`, and validated Unicode strings encode
  scalars as UTF-8 bytes.
- A legacy encoded string decodes under its exact profile/code page and then
  encodes as UTF-8.

Appending text does not grant the resulting `String` a permanent UTF-8
invariant.

### `AsciiString` on the left

`AsciiString <+> rhs` returns `AsciiString`:

- `AsciiChar`/`AsciiString` append directly.
- `Legacy.Latin1Char` requires a value at most `0x7F`.
- `Byte` and singular `U8` are rejected because they carry no character
  semantics.
- `String` must be valid UTF-8 whose every scalar is ASCII.
- UCS-2, Rune, UTF, and decoded legacy text require every scalar to be ASCII.
- Raw `U8[]` has no implicit text admission.

### `Ucs2String` on the left

`Ucs2String <+> rhs` returns `Ucs2String`:

- ASCII, Latin-1, `Ucs2`, and `Ucs2String` append scalar values directly.
- `Byte` and singular `U8` are rejected.
- `String` must be valid UTF-8 containing only BMP nonsurrogate scalars.
- Rune, UTF, and decoded legacy text reject supplementary scalars.
- Raw `U8[]` has no implicit text admission.

### UTF strings on the left

`Utf8String`, `Utf16String`, and `Utf32String` retain their left identity:

- ASCII, Latin-1, UCS-2, Rune, and validated UTF values transcode through
  Unicode scalars.
- `String` must decode as complete valid UTF-8.
- `Byte` and singular `U8` are rejected; neither supplies a character or
  partial UTF sequence.
- A complete `String` or `U8[]` may use a whole-sequence UTF-8 validation
  operation.
- Legacy strings decode through their selected profile or code page.

Once source validity is established, transcoding a scalar sequence among
UTF-8/16/32 is total except for result capacity/resource failure.

### MBCS and native legacy strings on the left

A code-page-specific MBCS string retains that exact identity:

- the same encoding appends directly;
- another legacy encoding decodes and re-encodes;
- `String` must decode as UTF-8;
- ASCII, Latin-1, UCS-2, Rune, and UTF values transcode through scalars;
- `Byte` and singular `U8` are rejected as characters;
- a scalar unavailable in the destination encoding is an error.

`Legacy.CharString` and `Legacy.WideString` apply the same rule under their
selected native profile. Baseline native strings and MBCS permit embedded NUL
and maintain no sentinel.

A `TerminatedCharString<Policy>` or `TerminatedWideString<Policy>` keeps its
concrete policy identity. Every join applies that policy's in-band terminator,
encoding, contiguity, suffix, and failure rules before returning the same
terminated type.

### Raw byte arrays and slices on the left

`U8[] <+> rhs` uses a borrowed byte slice as the left sequence and constructs a
new owning array result. It initially accepts `U8`, `Byte`, another raw
`U8` array/slice, and raw `String` bytes. An owning `U8[N]` or ranged `U8`
array provides the corresponding left-owned array join directly.

Appending a UTF or legacy string's storage representation requires an explicit
representation operation so semantic transcoding cannot be mistaken for byte
extraction. Complete owning/slice result bounds, layout, and transfer behavior
belong to [Zax arrays and slices](arrays-and-slices.md#joining-arrays).

### Character values are right operands

Characters join into a sequence:

```zax
utf8'temperature: ' <+> rune'℃'
```

Two characters do not infer a string container:

```zax
rune'a' <+> rune'b' // error: no character-to-character join
```

Joining independently parsed uses of the same literal declaration requires the
[same-prefix acknowledgement](literal-source-and-operators.md#same-declaration-requires-intent-acknowledgement).

## Storage continuity and raw access

Ordinary strings expose their logical code-unit type but do not promise one
contiguous storage region. A future representation may be flat, segmented,
rope-like, inline, or another form.

Future string APIs must distinguish:

1. querying whether the current value already has one contiguous region;
2. explicitly forcing or coalescing it into one region, with visible allocation
   and copying; and
3. obtaining an unsafe raw array pointer only from that proven or forced
   contiguous region.

The raw pointer exposes the exact underlying unit type and its concrete
endianness. Mutation may violate encoding invariants; lifetime and invalidation
remain caller responsibility. A safe readonly contiguous view is useful future
pressure but is not promised here.

Concrete `TerminatedCharString<Policy>` and
`TerminatedWideString<Policy>` specializations are exceptions when their policy
requires contiguous storage. NUL-terminated policies provide the applicable
`.c_str()` contract.

## Costs and diagnostics

Programmers and tooling need to expose:

- result identity and storage units;
- raw byte append versus scalar transcoding;
- validation, encoding-table, and capacity work;
- temporary compile-time allocation;
- termination-policy validation and suffix maintenance;
- selected native/code-page profile;
- exact source parser and join declaration; and
- compiler resource cost for large constants.

Diagnostics distinguish:

- invalid physical source from a literal parser's domain rejection;
- a `Byte` payload producing more than one byte;
- an `AsciiChar` high-bit representation;
- zero or several scalars where one character is required;
- isolated UTF-16 surrogates or invalid UTF-8/UTF-32;
- supplementary scalar supplied to UCS-2;
- unrepresentable legacy/code-page scalar;
- invalid RFC/XML payload;
- a terminator-policy admission or suffix violation;
- unavailable compile-time join;
- raw-byte versus text-admission mismatch; and
- result capacity/resource failure.

## Source stability and maturity

Changing a character valid set, source alias, escape grammar, encoding mapping,
termination policy, join direction, result identity, or validation policy is a
source-compatibility event. Formatters preserve literal prefix, delimiter,
payload spelling, merge/join, and explicit acknowledgement.

This document is current conceptual design, not a runtime library contract,
Unicode conformance specification, code-page database, ABI, generic declaration,
or implementation mapping.

Still future are runtime construction/admission/conversion, mutation and
allocation APIs, normalization, grapheme and presentation services, exact
generic MBCS syntax, code-page versions, ordinary-string contiguity/coalescing
and raw access, generic terminator-policy syntax, RFC canonical-padding policy,
locale, formatting, serialization, and foreign calling conventions.
