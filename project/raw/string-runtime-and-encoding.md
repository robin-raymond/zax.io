# Raw input: string runtime and encoding

| Field | Value |
| --- | --- |
| Status | Raw future-work input / non-authoritative |
| Audience | Future work defining runtime string APIs, admission, conversion, mutation, allocation, encoding data, normalization, graphemes, or presentation |
| Applies To | Unresolved runtime and data-contract consequences of current string, character, literal, and compile-time join design |
| Owns | Runtime admission/conversion pressure; runtime joining; encoded-string family naming; code-page and RFC data contracts; allocation, mutation, capacity, normalization, grapheme, locale, presentation, and foreign-view pressure; representative source; activation and retirement |
| Does Not Own | Current literal source ([literal source and operators](../../language/literal-source-and-operators.md)); current character/string identities and compile-time joins ([strings and characters](../../language/strings-and-characters.md)); intrinsic arrays and slices ([arrays and slices](../../language/arrays-and-slices.md)); or accepted runtime APIs |
| Source / Provenance | Legacy string and casting input after literal-focused review established current source identities and compile-time behavior |

## Why this input exists

Current string design establishes:

- unrestricted byte `String`;
- validated ASCII, UCS-2, and UTF identities;
- `Byte`, `AsciiChar`, `Ucs2`, and `Rune`;
- legacy native and code-page strings;
- literal-time transformations; and
- explicit left-owned compile-time `<+>`.

Those decisions create runtime pressure without deciding a complete string
library.

## Runtime admission and conversion

Future work must define explicit runtime forms for:

- `String` to UTF validation;
- UTF-to-UTF transcoding;
- ASCII and UCS-2 narrowing;
- Unicode to named code-page encoding;
- code-page decoding and cross-page transcoding;
- exact, optional, replacing, lossy, and reporting policies;
- error location and partial-progress reporting; and
- allocation and destination-capacity handling.

Singular `Byte` and `U8` values carry no character semantics and do not admit
directly into validated text. Whole `String` or `U8[]` values may undergo
explicit complete-sequence UTF-8 validation so multibyte sequences are checked
together.

Representative pressure:

```zax
runtimeBytes : String = readBytes()

// Illustrative only: exact runtime conversion syntax is not established.
myUtf8 := runtimeBytes as Utf8String?
```

Compile-time literal joining diagnoses known invalid admission. A runtime
operation cannot change its static result from `T` to `T?` merely because its
inputs stopped being constant. Required, optional, reporting, and lossy forms
need distinct contracts.

## Runtime joining and concatenation

Literal `<+>` is required at compile time. Future string work must decide
whether runtime `<+>` exists at all.

If it does, it must preserve:

- left-owned result identity;
- visible raw-byte versus scalar-transcoding behavior;
- one static result shape;
- explicit failure policy;
- source-order evaluation;
- allocation and capacity costs; and
- no fallback from an unavailable selected operation.

Ordinary `+`, named append/concatenate functions, builders, streams, and
in-place mutation remain alternatives. Current compile-time syntax does not
choose among them.

## MBCS family and Latin-1

```text
MbcsString<Encoding>
```

MBCS is the general code-page encoded-byte family. An SBCS is a specialization
whose maximum encoded-character sequence length is one. Every MBCS string uses
`U8`, tracks byte length, permits embedded NUL, and has no trailing sentinel.
Exact generic syntax remains future work.

`Legacy.Latin1Char` remains one ISO-8859-1 character with underlying `U8`; it is
not a string. Conceptually it is the one-character specialization associated
with `MbcsString<Legacy.CodePages.iso88591>`.

## Encoding contracts and data

Future code-page work must fix:

- exact byte-to-scalar mappings and version identifiers;
- invalid, incomplete, lead, and trail byte behavior;
- stateful encoding reset/state rules;
- canonical versus accepted alternate byte sequences;
- replacement and lossy conversion policy;
- whether a mapping is language-, library-, or provider-versioned;
- host/target independence; and
- reflection and diagnostic names.

Names that look related are not assumed aliases:

- CP932/Windows-31J is not base Shift_JIS;
- CP949/UHC extends ordinary EUC-KR;
- CP936/GBK and CP950/Big5 require exact mapping comparison;
- ISO-8859-1 differs from Windows-1252 in the high control range.

The retained initial catalog is:

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

## RFC and radix input policy

Current names distinguish:

```zax
Radix.b32'32Y9'
Radix.b64'9+/'
Rfc4648.Encoding.base64'VGhlIHF1aWNrIGJyb3duIGZveC4='
```

Future encoding contracts must decide:

- required, optional, or forbidden RFC padding;
- canonical case;
- accepted whitespace;
- Base64url padding;
- Crockford `O`/`I`/`L` permissive aliases and checksum symbols; and
- whether lenient parsing uses separate names from canonical parsing.

Leniency must not silently change the canonical positional digit set or binary
result.

## Allocation, mutation, and capacity

Current [arrays and slices](../../language/arrays-and-slices.md) now supplies
fixed/ranged owning arrays, borrowed slices, logical length, usable and
suggested capacity, storage-provider selection, joining, transfer, relocation,
invalidation, and iterable construction. Future string work consumes those
concepts rather than redefining them.

String-specific work must still define:

- builders and amortized growth;
- whether string values share array-compatible storage providers and unique
  backing handles;
- which provider types/capabilities preserve each string invariant;
- mutation while preserving UTF, ASCII, code-page, and selected terminator-policy
  invariants;
- querying whether an ordinary string is already contiguous;
- explicit coalescing/forcing into one region with visible allocation/copying;
- unsafe raw array-pointer access after contiguity is established;
- writable code-unit access;
- invalidation of references and views; and
- compile-time constants materialized into runtime storage.

An ordinary string may use a linked, rope, inline, contiguous, or another
storage provider without exposing itself as an intrinsic array. A required
contiguous foreign view must force or prove one applicable provider region and
preserve its array/slice lifetime contract. Unsafe access to an erased provider
object is not raw access to string code units.

Baseline `Legacy.CharString`, optional `Legacy.WideString`, and MBCS are
length-tracked and have no sentinel requirement.

Future generic policy syntax must support concrete terminated specializations
whose policy defines:

- one or more terminator units;
- whether an in-band occurrence is rejected, escaped, or translated;
- contiguous storage and physical suffix;
- logical length and iteration treatment;
- admission/conversion behavior; and
- mutation/reallocation restoration.

NUL-terminated aliases are the common case, not the universal model. A raw
writable view may require unsafe restoration responsibility.

Current string design exposes each identity's logical underlying unit type
without promising contiguous ordinary storage. Future raw access must expose
that exact type and concrete endianness. A safe readonly contiguous view remains
a useful optional facility only after lifetime and invalidation are defined.

## Unicode services beyond scalar validity

Current `Rune` and UTF strings validate Unicode scalar sequences but do not
normalize or validate presentation.

Future work may define:

- normalization forms and versioning;
- grapheme-cluster iteration;
- segmentation;
- collation and case operations;
- display width;
- bidirectional and confusable/security analysis;
- locale-sensitive behavior; and
- presentation-layer rejection or replacement.

These services must not turn one grapheme into one `Rune` or make an ordinary
string's validity depend silently on a new Unicode database version.

## Foreign string views

Current baseline native and MBCS strings make no terminator promise. A concrete
`TerminatedCharString<Policy>` or `TerminatedWideString<Policy>` may impose one.
Future interop work must define:

- `.c_str()`-style pointer/reference shape;
- lifetime and pinning;
- mutability and writable foreign calls;
- encoding and ABI identity;
- host versus target profiles;
- ownership and deallocation;
- returned foreign strings; and
- validation after unsafe mutation.

The same storage guarantee is not itself a calling-convention or foreign
ownership contract.

## Activation and retirement

Activate this input for runtime string construction, conversion, concatenation,
mutation, allocation, encoding data, normalization, grapheme/presentation
services, locale, or foreign string APIs.

Move accepted behavior into string, container, Unicode/text-service, interop,
operator, and runtime-library owners. Retire this file when every preserved
question has an accepted owner or explicit narrower destination.
