# Zax binary floating-point scalars

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers choosing, storing, converting, inspecting, or calculating with binary floating-point values |
| Applies To | Ordinary IEEE-style, OFP8, profile-selected, and named legacy binary floating-point scalar formats; not a formal grammar or specification |
| Implementation State | Not established by this repository |
| Owns | Binary floating-point mental model; exact and profile-selected formats; sign, exponent, fraction, significand, special values, initialization, literals, type-owned special/mathematical/limit constants, arithmetic, comparison, conversion, representation adoption, validity and canonicalization, environment support, legacy formats, costs, diagnostics, and portability |
| Does Not Own | Fixed-point values ([fixed-point scalars](fixed-point-scalars.md)); cross-family endian semantics ([endianness](endianness.md)); aggregate compatibility and coercive conversion syntax ([structural shapes and compatibility](structural-shapes-and-compatibility.md)); exact generic/factory syntax; complete operator forms; CPU-provider file format; or real-literal source and raw-pattern grammar ([literal source and operators](literal-source-and-operators.md)) |
| Source / Provenance | Legacy floating input in [basics](../basics.md), refined against current integer, endian, structural, literal, and operator design |

## Choose intent or choose an exact format

Use `Float` when the selected environment's preferred general-purpose floating
format is appropriate:

```zax
myCalculation : Float = 1.5
```

Use an exact format when range, precision, representation, or cross-environment
meaning must be fixed:

```zax
myStoredValue : Binary32 = 0.1
```

`Float` is a profile-selected intent identity. `Binary32` is an exact format.
They remain distinct even when one target implements `Float` with Binary32:

```zax
myExact : Binary32 = 1.5
myPreferred : Float

myPreferred = myExact
// error: Float and Binary32 are distinct identities
```

Convert deliberately or choose the exact type at the declaration that requires
it.

Binary floating point and [fixed point](fixed-point-scalars.md) are separate
families. A floating value uses an exponent to move its binary point. A
fixed-point value uses one integer coefficient and one type-fixed scale.

## Format meaning is not target support

An exact floating type fixes:

- which values and special classes it represents;
- how sign, exponent, and significand are encoded;
- logical width and field placement;
- endianness and storage envelope;
- valid, noncanonical, and invalid patterns; and
- required arithmetic and conversion behavior when an operation is available.

The target separately determines:

- whether values can receive numeric operations;
- which operations are available;
- whether an operation uses hardware, software, runtime dispatch, or trap
  emulation; and
- its broad cost.

A software implementation with no floating-point instructions can fully support
Binary32. A hardware routine that omits Binary32 NaNs does not implement a
reduced form of Binary32; it either lacks the applicable operation or implements
another format.

Every language-permitted format identity remains nameable and reflectable even
when its numeric operations are unavailable.

## Fields and precision

Ordinary binary floating formats have:

- one sign bit;
- an exponent field;
- a stored fraction field; and
- an implicit leading integer bit for normal values.

Use the terms precisely:

- **fraction field** means the stored bits following the integer bit;
- **significand** means the integer bit together with the fraction; and
- **mantissa** is familiar informal vocabulary for the significand, although
  some ecosystems use it for only the stored fraction.

For an ordinary format with `E` exponent bits and `F` stored fraction bits:

```text
logical width    = 1 + E + F
normal precision = F + 1 bits
```

The leading integer bit is implicit for normal values and becomes zero for
subnormal values. The public generalized family does not expose an independently
combinable explicit-integer-bit parameter. The closed
[`X87Extended80`](#x87-extended-precision) legacy format reflects an explicit
integer bit as one of its derived properties.

## Ordinary IEEE-style interpretation

The initial generalized IEEE-style policy derives:

```text
exponent bias = 2^(E-1) - 1
```

It interprets exponent classes as follows:

- all-zero exponent with zero fraction represents signed zero;
- all-zero exponent with nonzero fraction represents a subnormal;
- an exponent between the reserved endpoints represents a normal value;
- all-one exponent with zero fraction represents signed infinity; and
- all-one exponent with nonzero fraction represents NaN.

Normal values use an implicit leading one. Subnormals use the smallest normal
exponent with an integer bit of zero. Ordinary all-zero logical bits represent
positive zero.

NaN quiet/signaling distinctions, payload behavior, signed zero, subnormal
normalization, and conversion behavior are fixed by each named exact format.
They are reflected format facts, not optional CPU capabilities.

Closed policies such as OCP E4M3 and the named legacy formats may deliberately
allocate exponent codes differently. A closed policy defines one coherent
format rather than exposing an arbitrary set of independent `Supports...`
booleans.

## Exact formats and conventional aliases

These are exact transparent alias pairs:

| Conventional name | Exact interchange name | Exponent bits | Fraction bits | Precision |
| --- | --- | --- | --- | --- |
| `Half` | `Binary16` | 5 | 10 | 11 bits |
| `Single` | `Binary32` | 8 | 23 | 24 bits |
| `Double` | `Binary64` | 11 | 52 | 53 bits |
| `Quad` | `Binary128` | 15 | 112 | 113 bits |

Each pair names one intrinsic format in the active environment:

```zax
myHalf : Half
mySameFormat : Binary16
```

The family root supplies canonical current-environment paths such as
`Scalars.Floating.Binary32` and `Scalars.Floating.Float`. Explicit
`Scalars.Floating.Target.Binary32` and
`Scalars.Floating.CompilerHost.Binary32` paths select another environment.

`BF16` is the established bfloat16 format: one sign bit, eight exponent bits,
seven fraction bits, and eight bits of normal precision.

### OFP8 formats

`E4M3` and `E5M2` use the Open Compute Project OFP8 definitions rather than an
underspecified field-width family.

`E4M3` has:

- one sign bit, four exponent bits, and three fraction bits;
- signed zero;
- subnormal values;
- NaN;
- no infinity; and
- the OCP exponent bias and finite range.

`E5M2` has:

- one sign bit, five exponent bits, and two fraction bits;
- signed zero;
- subnormal values;
- infinities; and
- NaNs.

Other encodings with the same field widths, including FNUZ variants, require
variant-specific names. A provider cannot silently redefine `E4M3` or `E5M2`.

## Endianness and storage

Endianness is part of concrete floating format identity:

```zax
Scalars.Floating.Little.Binary32
Scalars.Floating.Big.Binary32
```

Unqualified `Binary32` selects the active environment's concrete endianness.
Explicit `Target.Binary32` and `CompilerHost.Binary32` paths select their named
environments.

Numeric operations preserve the declared interpretation. A CPU whose native
byte order differs from the type may need byte rearrangement, endian-aware
instructions, or software support. Complete behavior is defined by
[Zax endianness](endianness.md).

Logical sign, exponent, and fraction fields are contiguous. For a logical width
that is not a multiple of eight:

- the used portion of the highest participating byte is its low-order part;
- unused high bits are non-value storage;
- their contents are unspecified; and
- defined floating behavior cannot consume them.

Required alignment remains a target layout fact. Foreign ABI padding around a
format is not padding inside the intrinsic scalar itself.

## Initialization and real-number source

Default initialization produces positive zero. All-zero logical format bits
represent that value, including a format with an explicit integer bit.
Initializing the complete envelope to zero is valid without promising that
non-value padding always remains zero.

A typed real number is parsed as an exact mathematical source value and realized
directly in the selected format:

```zax
myExact : Binary32 = 1.5
myRounded : Binary32 = 0.1
```

`1.5` is exact in Binary32. Decimal `0.1` is not, so ordinary realization rounds
it using the destination format's rule. Ordinary IEEE-style formats use nearest,
ties to even. Precision loss alone is not a diagnostic.

A tiny nonzero value may round to a subnormal or zero. A finite source literal
whose magnitude exceeds the destination's finite range is a compile-time error
rather than silently becoming infinity:

```zax
myTooLarge : Binary16 = 1e100 // error: outside the finite Binary16 range
```

The compiler computes the target format independently of compiler-host floating
arithmetic.

Decimal real source accepts `e` or `E` followed by an optionally signed decimal
exponent:

```zax
myA : Binary64 = 1e100
myB : Binary64 = 1E100
myC : Binary64 = 1.5e14
myD : Binary64 = 1.5e+14
myE : Binary64 = 1.5e-14
```

The exponent is a power of ten applied to the exact mathematical source before
destination realization. Its presence makes `1e100` real-number source even
without a fractional point. Bare `e100` is not a number literal.
Ordinary real source without another destination defaults to `Float`.
Uncommitted exact-real arithmetic calculates before floating commitment:

```zax
myResult : Binary32 = 0.1 + 0.2
// Exact rational 0.3 is realized once as Binary32.

myThird : Quad = 1.0 / 3.0
// Exact rational 1/3 is rounded once as Quad.

myConcrete : Binary32 =
  (: Binary32 = 0.1) + 0.2
// The inner declaration commits before +, so concrete Binary32 arithmetic occurs.
```

Binary and hexadecimal payloads may use a decimal `p`/`P` exponent denoting a
power of two:

```zax
myBinary : Binary32 = b'1.1p-3'
myHex : Binary64 = h'1.8p+10'
h'1.8' // equivalent to h'1.8p0'
```

Exact token, default, prefix, and exponent behavior is owned by
[literal source and operators](literal-source-and-operators.md#ordinary-numeric-source).

An exact floating type also supplies type-qualified raw logical-pattern
literals:

```zax
myOne := Binary32.h'3F80 0000'
```

The payload supplies exact format bits rather than a hexadecimal floating
value. Valid noncanonical patterns are preserved and invalid format patterns
are rejected. Complete raw-pattern rules are defined by
[literal source and operators](literal-source-and-operators.md#type-qualified-raw-scalar-patterns).

A future exact-realization request remains separate from raw-pattern source.

### Type-owned special and mathematical constants

Applicable floating formats expose concrete special values:

```zax
Float.inf
Float.ninf
Float.nan
Float.nzero
```

`nan` is the canonical quiet NaN. A canonical `snan` may exist where signaling
NaN is supported. Exact payload, sign, signaling state, or noncanonical
representation uses raw logical bits.

A format lacking a special class does not expose that constant:

```zax
E4M3.inf // error: E4M3 has no infinity
MBF40.nan // error: MBF40 has no NaN
```

Applicable floating types also expose `pi`, `tau`, `e`, `phi`, `sqrt2`,
`sqrt3`, `ln2`, `ln10`, `log2e`, `log10e`, `invPi`, `twoOverPi`,
`invSqrtPi`, `halfPi`, and `quarterPi`.

Each mathematical constant is rounded directly from its definition into the
receiver format rather than derived from another already rounded constant.

Floating representation limits remain separate: `maximumFinite`,
`minimumFinite`, `minimumPositiveNormal`, `minimumPositiveSubnormal`, and
`epsilon`.

Complete shared constant naming and source behavior is routed through
[literal source and operators](literal-source-and-operators.md#type-owned-scalar-constants).

## Profile-selected floating types

`Float` means:

> The selected provider's preferred general-purpose binary floating format with
> complete core numeric support.

It is a distinct profile identity rather than a transparent alias to whichever
exact format one profile chooses. There is no `DoubleFloat`; use an exact format
or a minimum-width selector.

Environment paths are:

```zax
Float
Target.Float
CompilerHost.Float
```

Shared code may be specialized for compiler-host and target execution. Use an
exact format when the identity must agree across that boundary.

### Width-selected families

`FastF<N>` selects the provider-preferred supported format having at least `N`
logical storage bits. `LeastF<N>` selects the smallest native format satisfying
that storage-width requirement.

These are width and provider-preference contracts, not claims that all floating
formats form one precision/range order. Two formats of equal width can trade
precision against exponent range.

`NativeMaxF`, `OptimizedMaxF`, and `SupportedMaxF` are provider-selected
identities:

- `NativeMaxF` is the widest format classified as natively represented.
- `OptimizedMaxF` is the widest format for which the provider supplies its
  preferred general-purpose optimized implementation.
- `SupportedMaxF` is the widest format with the provider's complete supported
  operation contract.

When several formats share that width, the provider selects and exposes one
exact format under its versioned profile.

There is no unique `LanguageMaxF` type because many exponent/fraction
partitions can share the maximum width.

### Language limits

Language-version limits are immutable compile-time metadata:

```zax
Scalars.Floating.LanguageLimits.maximumWidth
```

`LanguageLimits` is conceptually a type-owned descriptor available during type
formation with no required per-instance runtime storage. Future generic work
selects the numeric maximum and may expose separate exponent, fraction, storage,
or alignment limits. Changing a limit is a language-version compatibility
event.

Exact declaration and `once` syntax remain future work; `once` by itself does
not establish compile-time availability.

## Environment support

Every format has a baseline representation surface where target resource limits
permit an instance:

- known type identity, size, alignment, and layout;
- positive-zero initialization;
- byte-preserving copy, move, assignment, and destruction;
- storage in aggregates and internal Zax calls; and
- representation access subject to validity rules.

Numeric behavior is separately supported.

### Core numeric support

A format has complete core numeric support when the environment supplies:

- unary sign;
- addition, subtraction, multiplication, and division;
- equality and ordering;
- classification; and
- the format's required rounding and exceptional-result behavior.

An exact type may expose individually available operations without having the
complete bundle. Profile `Float` selects only a type with the complete bundle.

Conversions, square root, fused multiply-add, remainders, reports,
transcendentals, and environmental controls may be independently available.
Future reflection asks about an exact operation instead of requiring one
permanently exhaustive capability bitmap.

### Support classification

The compact environment classification has at least:

```text
Unsupported
Software
HardwareGuaranteed
HardwareOptional
TrapEmulated
```

The classification is an extensible enum-like type. `HardwareBacked`, or the
applicable classification, is intentionally coarse: at least one relevant
floating operation uses direct hardware. It does not promise that multiplication,
division, conversion, or another particular operation has one instruction.

Optional hardware discovered at runtime cannot change static type identity. It
may select another implementation of the same already chosen format.

Unsupported numeric source produces a compiler diagnostic. A provider cannot
claim support while returning an incomplete result for some valid operand.

## Ordinary operations

Ordinary arithmetic uses operands of one exact identity. Zax does not implicitly
promote or convert mixed formats:

```zax
mySingle : Binary32 = 1.5
myDouble : Binary64 = 2.0

myResult := mySingle + myDouble
// error: choose the intended result format explicitly
```

IEEE-style exact formats follow their defined nearest-ties-to-even arithmetic,
signed-zero, subnormal, infinity, NaN, overflow, underflow, invalid-operation,
and zero-divisor behavior when the operation is available.

OFP8 and legacy types follow their own closed format contracts. A type lacking
an infinity does not silently acquire IEEE infinity, and a type lacking a
particular operation reports that operation as unavailable.

### Comparison and classification

Floating equality and relational comparison follow the selected format's NaN
and signed-zero behavior. IEEE-style NaN is unordered under ordinary relational
comparison. Positive and negative zero compare numerically equal while remaining
distinguishable through representation and sign inspection.

The aligned operation surface includes:

- classification as zero, finite, normal, subnormal, infinity, or NaN where
  those classes exist;
- a total ordering operation suitable for deterministic sorting including NaNs;
- adjacent representable values through next-up and next-down concepts; and
- sign and magnitude operations.

Exact source words for classification, total ordering, and adjacent-value
operations remain operator work. Promoted examples do not present illustrative
wording as settled syntax.

### Additional operations

Square root and fused multiply-add are useful individually supported operations.
Remainder variants, transcendental functions, exception flags, mutable rounding
environments, atomics, and exhaustive minimum/maximum NaN policies remain later
focused operator or environment work.

An implementation may use hardware instructions, wider internal calculation,
software routines, constant folding, or runtime dispatch only when the selected
format's observable result and failures remain unchanged.

Ordinary floating bitwise operations remain unavailable. Use representation
access or a recognized coercive integer view when the encoding is the intended
subject.

## Numeric conversion

Numeric conversion preserves mathematical value unless an explicit rounding
policy says otherwise.

Plain `as Destination` is available only when type facts or a
compile-time-known value prove exact representability. It introduces no runtime
panic. `as Destination?` reports value-dependent exact nonrepresentability
through absence:

```zax
myWide : Binary64 = 1.5
myExact := myWide as Binary32

myRuntime : Binary64 = readMeasurement()
myPossible := myRuntime as Binary32?
```

Rounded conversion explicitly selects precision loss when source and destination
formats differ. Precision loss and a tiny nonzero value becoming subnormal or
zero do not panic. On range overflow, an IEEE floating destination may produce
its defined infinity. A destination without infinity uses the selected
required, optional, or saturating policy: a required form may panic and an
optional form reports absence.

Its exact source phrase remains operator work.

Important boundaries include:

- infinity and NaN have no exact integer or fixed-point destination;
- finite float-to-integer/fixed conversion handles fractional loss and range
  independently;
- integer/fixed-to-float exactness depends on both exponent range and
  significand precision;
- widening Binary32 to Binary64 preserves every finite numeric value exactly;
- NaN payload/signaling preservation is a separate representation promise; and
- conversion of negative zero to integer zero preserves mathematical value but
  not zero sign.

Conversion among endian specializations preserves numeric meaning and may
rearrange bytes. Equal-format endian conversion is total and cannot overflow or
underflow. Raw reinterpretation does not preserve numeric meaning.

## Representation extraction and adoption

Numeric conversion, representation access, and same-storage coercion answer
different questions:

1. Which destination value preserves or rounds numeric meaning?
2. Which logical bits encode this value?
3. Which recognized second type can view the same bytes without translation?

The aligned representation surface requires concepts equivalent to:

```zax
// Illustrative only; exact operator words remain unsettled.
myBits := representation bits of myValue
myOptional := X87Extended80 optional from representation myBits
myTrusted := X87Extended80 unsafe from representation myBits
myCanonical := myValue canonical representation
```

These distinguish:

- exact logical-bit extraction;
- validation;
- optional reporting of an invalid pattern;
- unsafe assertion that a pattern is valid; and
- canonicalization.

Unsafe adoption asserts validity; it cannot turn an invalid bit pattern into a
floating value.

For an unusual padded width, logical representation excludes storage-envelope
padding. Direct pointer access to complete object bytes is a separate
target-layout operation and may observe unspecified non-value bits.

## Structural compatibility and coercion

Safe floating scalar compatibility requires equality of:

- total logical format width;
- sign encoding;
- exponent width, bias, encoding, and class allocation;
- fraction width and significand interpretation;
- implicit or explicit integer-bit mode;
- normal, subnormal, zero, infinity, NaN, and invalid-pattern rules;
- payload and canonicalization behavior;
- concrete endianness and logical field placement; and
- storage extent, alignment, and non-value-bit rules.

Hardware support and cost do not participate.

Different floating formats do not receive a float-to-float coercive relationship
merely because total width matches. Repartitioning exponent and fraction fields
provides no useful format-preserving lens.

Coercion exists when another recognized lens provides more insight than raw
bytes. In precise terms, it performs a compiler-checked zero-translation view
while retaining known field correspondence.

A floating format may instead have a directional relation with its equal-width,
same-endian unsigned integer representation:

```zax
myValue : Binary32 = -1.5

myBitsView :=
  myValue as coercive layout U32 &
```

For Binary32, the compiler still knows that U32 bit 31 is the sign, bits 30
through 23 are the exponent, and bits 22 through 0 are the fraction. This is
more information than arbitrary raw casting while performing zero translation.

The reverse direction is safely available when every unsigned source pattern is
a valid destination encoding. Binary16/32/64/128 satisfy that requirement.
Their unsigned representation views may remain writable because every write
also leaves a valid floating representation.

Extended80 safely produces a readonly U80 view. A writable U80 view uses
`unsafe as coercive` because a write may invalidate the original Extended80
value. U80-to-Extended80 requires validation or an unsafe validity assertion.

Coercion is always local and reference-shaped; it does not construct a by-value
copy or become a declaration posture. Neither safe nor unsafe coercion grants
access authority. Existing mutable, writable, or varying authority may be
preserved. After an unsafe write, the programmer must restore a valid original
representation before using that original type again.

Complete aggregate, anchor, qualification, and same-storage view behavior is
defined by
[Zax structural shapes and compatibility](structural-shapes-and-compatibility.md).
`unsafe cast` is defined by [Zax conversions and casts](casting.md).

## Legacy formats

Legacy formats are historically established non-core encodings. `Legacy` does
not mean deprecated, unavailable, or software-only.

### x87 extended precision

The canonical type and transparent aliases are:

```text
Scalars.Floating.Legacy.X87Extended80
Scalars.Floating.Legacy.Extended80
Scalars.Floating.Legacy.X80M64
```

The format has:

- one sign bit;
- fifteen exponent bits with bias 16383;
- one explicit integer bit;
- sixty-three fraction bits;
- sixty-four bits of significand precision; and
- eighty logical format bits in a ten-byte intrinsic storage envelope.

Foreign ABIs may place those eighty bits in twelve or sixteen bytes. That is
container/ABI padding, not the intrinsic format envelope.

#### Validity

Extended80 classifies encodings as follows:

- exponent zero with integer bit zero is canonical zero or subnormal;
- an ordinary nonzero exponent with integer bit one is a canonical normal;
- maximum exponent with integer bit one is canonical infinity or NaN;
- exponent zero with integer bit one is a valid noncanonical pseudo-denormal
  representing the corresponding normal value; and
- nonzero exponent with integer bit zero is invalid/unsupported.

Ordinary construction produces canonical encodings. Copying and representation
extraction preserve existing bits. Numeric use may normalize a pseudo-denormal.
Validated adoption accepts canonical and pseudo-denormal patterns; optional
adoption reports invalid patterns.

This makes representation coercion and its safety directional:

```text
X87Extended80 -> same-endian readonly U80 view: safe
X87Extended80 -> same-endian writable U80 view: unsafe
same-endian U80 -> X87Extended80: validated or unsafe assertion
```

#### Provider-relative operations

There is no one universal historical x87 operation-result contract. Individual
CPU providers expose their hardware's rounding, precision-control, exception,
and instruction behavior rather than software-emulating one selected x87
generation everywhere.

Source and reflection can determine that narrower portability contract. Source
requiring reproducible arithmetic selects a standard exact format or another
fixed operation contract instead.

### Microsoft Binary Format 40

`Scalars.Floating.Legacy.MBF40` is a closed historical format. It fixes:

- an eight-bit exponent;
- one sign bit in the historical MBF position;
- a thirty-one-bit stored fraction and implicit leading bit;
- historical exponent interpretation and byte arrangement;
- no NaN, infinity, signed zero, or subnormal classes; and
- forty logical bits in five bytes.

Every nonzero-exponent pattern represents a finite value. Exponent zero means
zero regardless of the other sign/fraction bits:

- all-zero bits are canonical zero; and
- exponent zero with other bits nonzero is a valid noncanonical zero.

Historical arithmetic may leave residual non-value bits in an exponent-zero
result. Numeric comparison treats all such encodings as zero. Copying and raw
extraction may preserve the bits; canonicalization produces all-zero MBF40.

Every U40 pattern therefore has a valid MBF40 interpretation, permitting
matching-endian bidirectional representation coercion:

```text
MBF40 <-> U40
```

#### Historical operation contract

MBF40 arithmetic reproduces Microsoft BASIC for 6502 Version 1.1 with
`ADDPRC=1`:

- a discarded high guard bit rounds the retained magnitude upward, including
  the halfway case;
- underflow becomes zero;
- overflow fails; and
- division by zero fails.

A provider may support storage and conversion without arithmetic. A declared
MBF40 operation covers every valid MBF40 input under this contract. The absence
of NaN and infinity is part of MBF40 rather than incomplete implementation.

Future bit-for-bit compatibility vectors derive from that named source and
configuration. They are conformance evidence, not another conceptual arithmetic
model.

#### Modern conversion bridge

Binary64 can exactly represent every finite MBF40 value:

```zax
myModern := myLegacy as Binary64
```

Conversion back may require exact reporting or explicit rounding:

```zax
myExactLegacy := myModern as MBF40?

// Illustrative rounded-conversion wording; exact syntax remains unsettled.
myRoundedLegacy := myModern rounding as MBF40
```

Raw representation access preserves the historical five-byte form for files,
emulators, and 6502 tooling. Stable file or wire contracts remain separate from
ordinary target layout.

## Costs

Programmers and tooling need to expose:

- public identity and selected exact format;
- range, precision, exponent and fraction widths, and special classes;
- concrete endianness, storage extent, alignment, and non-value bits;
- exact, rounded, optional, representation, coercive, or unsafe conversion;
- core support and individual operation availability;
- software, guaranteed hardware, optional hardware, or trap-emulated support;
- rounding, overflow, underflow, invalid-operation, and zero-divisor behavior;
- provider identity/version for profile and X87 behavior; and
- ABI or serialization work as a separate contract.

Native representation and direct instructions can remove runtime work.
Software emulation, endian adaptation, checks, classification, canonicalization,
and runtime dispatch can add cost. Representation access may be cheap while
still adding portability and unsafe-responsibility cost.

## Diagnostics

Diagnostics should distinguish:

- exact format mismatch from profile identity mismatch;
- invalid or unsupported format parameters;
- unavailable type operation from unavailable hardware acceleration;
- range overflow from ordinary precision rounding;
- finite, zero, subnormal, infinity, NaN, and invalid-pattern cases;
- exact conversion failure from rounded conversion;
- invalid or noncanonical representation adoption;
- safe format incompatibility from directional coercion failure;
- endianness or field-placement mismatch;
- unavailable writable coercion authority; and
- provider-relative X87 behavior from fixed IEEE behavior.

Ordinary diagnostics lead with public names:

```text
cannot exactly convert Binary64 value to Binary32
the value is outside Binary32's exact representable set
```

Expanded tooling may show canonical path, field widths, support classification,
provider version, and operation cost.

## Portability and maturity

- Exact ordinary formats retain their value sets and operation contracts across
  providers.
- Unqualified names select active-environment endianness; absolute-endian paths
  fix stored byte order.
- `Float`, `FastF<N>`, `LeastF<N>`, and maximum roles follow versioned profile
  contracts and remain distinct identities.
- Runtime-optional hardware changes implementation, not type identity.
- X87 operation behavior is intentionally provider-relative; portable source
  must not assume one historical CPU contract.
- MBF40 operation behavior follows its named historical source.
- Changing field allocation, validity, canonicalization, support facts, or
  profile selection can change source, values, layout, or cost.

This document is current conceptual design, not formal grammar, an IEEE
conformance claim, a CPU-provider schema, an ABI/wire contract, or an
implementation mapping. Exact generic declarations, reflection APIs, most
representation-operation words, provider files, and exhaustive protected
operator forms remain future work and must preserve the programmer-visible
behavior established here.
