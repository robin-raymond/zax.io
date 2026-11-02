# Raw input: numeric type families

| Field | Value |
| --- | --- |
| Status | Raw future-work input / non-authoritative |
| Audience | Future work defining custom, unbounded, unusual-width, generic, or advanced numeric families and operations |
| Applies To | Numeric type relationships and operator mechanics still deferred after integer, fixed-point, and floating scalar review |
| Owns | Custom-family participation, signedness-counterpart mechanics, finite-bit-extent constraints, count-tier implementation, delta/distance mechanics, multiword operations, reversal, extraction/deposit, representation-operation words, and remaining policy applicability questions |
| Does Not Own | Current [finite integers](../../language/integers.md), [fixed-point scalars](../../language/fixed-point-scalars.md), [binary floating-point scalars](../../language/floating-point-scalars.md), or [enum behavior](../../language/enums.md) |
| Source / Provenance | Legacy intrinsic-type, casting, and operator evidence; work item `012` nested optional conversion pressure |

## Current finite-integer disposition

Current exact and role-selected finite integers, ranges, storage, conversion,
count tiers, signedness pairs, `delta`, `distance`, and concrete endianness are
owned by [Zax integers](../../language/integers.md). Complete protected integer
operation behavior is owned by the
[integer operator catalog](../../language/integer-operator-catalog.md); exact
forms and precedence remain in the
[general operator catalog](../../language/operator-catalog.md).

Current fixed-point formats, names, arithmetic, conversion, and representation
are owned by [Zax fixed-point scalars](../../language/fixed-point-scalars.md).
Current ordinary and legacy binary floating formats are owned by
[Zax binary floating-point scalars](../../language/floating-point-scalars.md).

This raw input retains generic/associated-type mechanics, custom and unbounded
numeric families, multiword arrangement, advanced bit operations, and exact
representation-operation wording. It is not a competing definition of current
scalar semantics.

## Custom integer-family participation

Compare a Zax integer with a separate identity that uses an integer underneath:

```zax
myRaw : U32 = 5

MyInteger :: identity admit expose type Integer
myUnderlying : Integer = 5
myIdentity := MyInteger from myUnderlying
myValue := 0 + myIdentity
// error: MyInteger is not an integer-family type
```

`U32` and `Integer` belong to the language's integer family. `MyInteger` is a
different type whose construction, admission, and exposed operations preserve
that identity boundary. Numeric source therefore applies directly to
integer-family types, not to every type with integer storage.

Future numeric/generic work may define a mechanism for declaring a genuine
custom integer family or direct-realization contract. That work must decide:

- which mathematical values and sign intents the type accepts;
- which operations are width-invariant for its mathematical model;
- whether it participates in contextual peer construction or protected
  same-identity operand anchoring;
- which contextual/explicit construction and operator modes it exposes;
- how range, width, storage, and associated types are exposed;
- what authority may declare the relationship; and
- how tooling distinguishes genuine numeric participation from an identity
  using integer representation.

Such a mechanism must not authorize speculative temporary construction, global
operator discovery, or implicit admission across an ordinary identity boundary.

## Signedness counterparts

A concretely typed integer may request its equal-width opposite-signedness
counterpart:

```zax
unsignedValue : U8 = 100
signedValue := +unsignedValue // I8(100)
```

Future numeric work must decide how a custom type advertises:

- signedness;
- an equal-width counterpart;
- supported panic/optional/wrapping/saturating/reporting policies;
- generic constraints and reflection;
- layout relationships; and
- conversion versus counterpart operation.

This is one type with a signedness property or relationship, not a requirement
for separate mutable/immutable-style implementations.

## Finite and unbounded families

An arbitrary but finite type such as conceptual `I137`/`U137` can define modular
counterpart operations. A truly unbounded signed integer has no wrapping
conversion to an unbounded nonnegative counterpart without a supplied modulus.

Bit count, shift, rotate, reversal, reduction, set-bit mask, set-bit position,
multiword, and masked extraction/deposit operations all require a finite defined
logical bit extent. A runtime-sized bit vector may provide one. Hidden allocation
capacity and padding must not change results.

## Associated bit- and byte-count types

The zero-count forms and optional set-bit-position phrases share the associated
unsigned bit-count type of their operand:

```zax
leadingZeros := #<bits
position := bits trailing set bit position

if ?position
  shifted := x << position.
```

`T bit count type` must represent zero through `T`'s logical width and be directly
viable as a protected shift count for `T`. Ordinary widths select `BitCount`;
extreme widths select `LargeBitCount`. Generic code asks for the associated type
instead of branching between tiers.

`T byte count type` similarly selects `ByteCount` or `LargeByteCount` according
to the complete storage-envelope byte count.

The CPU profile chooses ordinary representations subject to language capacity
rules. Large tiers cover the language maximum:

```text
LargeBitCount: 0 through LanguageMaximumIntegerWidth
LargeByteCount: 0 through ceil(LanguageMaximumIntegerWidth / 8)
```

The initial language maximum is `2^23` logical bits, requiring at least 24
unsigned logical bits for `LargeBitCount` and at least 21 for `LargeByteCount`.
Exact tier syntax, reflection, and future larger language versions remain
numeric/generic work.

An ordinary `BitCount` has at least eight logical bits and therefore represents
at least zero through 255.

Associated-type teaching must lead with its purpose: ordinary widths use compact
counts, extreme widths use larger counts, and generic code asks `T bit count
type` rather than branching on width.

Bit-count tier selection uses logical width. Byte-count tier selection uses the
storage-envelope byte size.

## Exact, optional, and narrowing conversion

Integer conversions use declared range facts:

```zax
myWide := myU16 as U32
myOptional := myU16 as U8?
myNarrow := myU16 narrowing as U8
```

- `as Destination` is available when the type contract or a compile-time-known
  value proves exact representability;
- `as Destination?` returns absent instead of losing information;
- `narrowing as Destination` performs defined modular conversion;
- `unsafe as` is reserved for actual guarantee weakening rather than ordinary
  integer truncation.

Admission into an exposed numeric identity uses `from`, `optional from`, or
`narrowing from`. A restricted identity may instead declare validating
`optional from`, defined-but-unvalidated `unchecked from`, truly unsafe
`unsafe from`, or no representation admission.

Equal representation never creates an identity conversion. A direct bridge
requires a declared semantic relationship and a proof that every source value
fits.

Nested optional type formation does not generate a corresponding numeric
conversion:

```zax
single := myWide as U8?
nested := myWide as U8? ?
```

The second form is available only if the source numeric family explicitly
declares that exact `U8? ?` result. Its contract must distinguish outer absence
from outer-present/inner-absent and both-present states. Type inference merely
receives the declared result; it does not select the state policy.

Future numeric review must decide whether any nested optional conversion is
useful. Optional existence alone supplies neither availability nor recursive
conversion.

Future work must provide enough range and relationship metadata for programmers
and tooling to derive the applicable surface without listing every named pair.

## Delta and distance

The aligned operation concepts are:

```zax
myDelta := myLeft delta myRight
myDistance := myLeft distance myRight
```

Both are eager, left-associative language-defined binary phrases at additive
precedence and require the same integer identity on both sides.

`delta` returns `T delta type` and represents exact `left - right`. Its selected
signed result covers `-(2^W - 1)..+(2^W - 1)`. A CPU profile may select the next
appropriate native or byte-oriented width rather than exact `W + 1`. The
operation is unavailable when no language-supported result type covers the
range, including at the maximum integer width.

`distance` returns `T distance type` and represents exact `abs(left - right)`.
Its unsigned result covers `0..2^W - 1`, so it remains available at the language
maximum.

Neither operation has wrapping, saturating, optional, reporting, compound, or
mutation forms. Pointer comparability remains pointer/lifetime work.

## Storage carrier and unusual widths

`T storage type` is the smallest byte-sized unsigned integer carrier large enough
for `T`'s complete storage envelope:

```text
I32 storage type -> U32
I57 storage type -> U64
```

It names capacity, not a safe raw conversion. A non-byte-multiple integer has
unspecified non-value storage bits that cannot affect defined numeric behavior.
Widening uses the logical sign or zero extension; narrowing uses logical bits.

An arbitrary exact width may back an ordinary enum. Every intrinsic scalar has
concrete endianness, including unusual widths; used logical bits occupy the
low-order portion of the highest participating byte and padding remains
non-value storage. No language-provided operation exposes that padding as
stable value data for an unusual width such as `I57`.

## Identity and counterpart relationships

Exact intrinsic counterparts are derived by changing signedness at equal logical
width. Named role identities are paired relationally by the integer factory or
another validated generic mechanism; the compiler never infers a counterpart
from spelling.

One-sided roles return an unnamed exact intrinsic counterpart and intentionally
leave role identity. Restricted or opaque semantic identities receive no
counterpart merely because they use integer storage.

## Optimized maximum family

`OptimizedMaxI`/`OptimizedMaxU` preserve the widest provider-recommended
general-purpose implementation, including deliberately optimized software
emulation beyond native width. Exact profile selection, cost metadata, and
relational pair generation remain CPU-provider/generic work.

## Multiword operations

The operator catalog records these as accepted concepts with exact words
deferred:

```zax
// Illustrative wording; exact multiword phrases are not established.
high shift left through low by count
high rotate left through low by count
result funnel right from high and low by count
```

Exact spellings stay deferred because arithmetic versus logical right shift,
receiver orientation, high/low ordering, result shape, multi-place mutation,
signedness, and aliasing all affect the theoretical prototypes. Future numeric
work must settle:

- whether components must share one logical bit extent;
- whether a writable form mutates one place or several;
- what a funnel shift's fixed-width result window is; and
- how aliasing among component places behaves.

## Reversal and masked extraction/deposit

Bit reversal and byte reversal are distinct finite-extent concepts: byte reversal
preserves bit order within each byte, while bit reversal does not.

Masked extraction packs every source bit selected by a mask into contiguous
low-order positions. Masked deposit scatters contiguous low-order input bits into
positions selected by a mask.

Future numeric work must decide exact words, mask-width behavior, zero fill,
signedness interaction, and unsupported-target costs.

## Aliases

Transparent aliases and explicit identity types are now distinguished by
[Zax identity types](../../language/identity-types.md). Future numeric work
retains only generic alias generation and identity-aware associated-type
questions.

Legacy intrinsic input also proposed `UUID` as an alias of `U128` and `Rune` as
an alias of a wide character integer. Current design does not retain those
transparent integer aliases. Future UUID and Unicode-scalar work must decide
their independent identity, validity, formatting, conversion, and operation
surfaces without inheriting arithmetic merely from storage.

## Numeric interaction with enums

An enum is an independent identity backed by an eligible exact intrinsic or
language-provided integer role. Strict enums receive no automatic integer
arithmetic, relaxed enums reuse eligible exposed-identity behavior, and flags
receive their mask-preserving bitwise surface. Complete behavior belongs to
[Zax enums](../../language/enums.md).

This file retains numeric-family questions only. Future custom numeric types do
not become enum-eligible merely because they use integer storage; eligibility
requires an explicit, separately reviewed capability.

## Remaining representation-operation syntax

Current floating design distinguishes numeric conversion, logical
representation access, validated/unsafe adoption, canonicalization, coercive
views, and raw casting. Complete semantics belong to
[Zax binary floating-point scalars](../../language/floating-point-scalars.md#representation-extraction-and-adoption).

Exact source words remain unsettled:

```zax
// Illustrative future concept.
bits of floatValue
```

Future operator/reflection work must choose forms for representation extraction,
validation, optional and unsafe adoption, and canonicalization without
reopening those semantic distinctions. Ordinary floating bitwise operators
remain unavailable.

## Exact uncommitted real evaluation limits

[Literal source](../../language/literal-source-and-operators.md#ordinary-numeric-source)
evaluates the uncommitted real operation family as exact finite rationals and
rounds once at commitment; exceeding the compiler's practical capacity is a
resource diagnostic, never hidden rounding. That compiler-arithmetic limit is
distinct from user compile-time code, which has no language time limit.

Future work must decide:

- practical capacity guarantees programmers can rely on;
- resource diagnostics that do not depend on one compiler's algorithm;
- caching of exact evaluations; and
- how a programmer establishes an earlier concrete boundary to control cost,
  for example by committing to a concrete type before a long chain of
  operations.

It must preserve exact grouping, sign, `+`, `-`, `*`, rational `/`, equality,
and ordering; zero-divisor diagnostics; exact scaled source without expanding
repeating decimals; destination-aware algorithms only when they prove the same
correctly rounded result; and a required concrete commitment before square
root, transcendental, or representation-dependent operations.

## Activation and retirement

Activate this input for custom/unbounded numeric types, generic numeric
contracts, signedness mechanics, unusual width, count and associated-type
implementation, delta/distance mechanics, multiword operations, reversal,
masked extraction/deposit, or representation-operation wording. Move accepted
behavior into numeric, type, cast, generic, reflection, or operator owners and
retire this file after every remaining item is dispositioned. Current enum
behavior remains with [Zax enums](../../language/enums.md).
