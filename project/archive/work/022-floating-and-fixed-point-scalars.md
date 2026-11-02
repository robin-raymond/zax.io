# 022: Floating and fixed-point scalars

| Field | Value |
| --- | --- |
| Status | Historical working record / non-normative / audit-only |
| Work Item | `022` |
| Created | 2026-09-14 |
| Closed | 2026-09-15 |
| Owns | The bounded review defined by the fixed initiating input |
| Does Not Own | Complete generics, literal parsing, exhaustive numeric operators, CPU-provider implementation, ABI/FFI, vectors/SIMD, arbitrary decimal arithmetic, or compiler lowering |

## Non-authority notice

This file is a historical collaborative record. Its observations, candidates,
discussion, and aligned findings are non-authoritative; promoted language
owners contain the current conceptual design.

## Fixed initiating input

This section records the information aligned when work item `022` was created.
It is intentionally incomplete and must not be rewritten as work develops.

### Initiating concern

Establish a coherent programmer-facing model for Zax fixed-point and binary
floating scalar families, including concrete format identity, representation,
availability, conversion, costs, and safe versus coercive structural
compatibility.

The [floating-point maintainer notes](../raw/floating-point-maintainer-notes.md)
are the primary input. Treat their complete contents as maintainer-supplied
design evidence to understand and test, not as automatically accepted language
design. The [raw numeric-family input](../raw/numeric-type-families.md)
preserves supplemental generic, fixed-point, float-representation, conversion,
and operation pressure.

### Motivating pressure

Current integer design establishes exact logical widths, two's-complement signed
representation, storage envelopes, profile-selected identities, and explicit
conversion. Current structural design requires each scalar family to define:

1. the complete meaning-bearing properties that must match for safe shape/layout
   compatibility; and
2. the reduced properties that must still match for local coercive conversion.

Those lists remain provisional for fixed-point and binary floating values.
Floating review must finalize them and recheck the integer list rather than
letting equal byte size stand in for compatible numeric format.

Zax also needs a usable explanation of which formats exist, which values they
represent, when target or compiler-host support exists, what operations may
cost, and how programmers choose exact versus profile-selected types without
prematurely committing to one generic syntax or backend implementation.

### Known assumptions

- Type identity, scalar semantic format, storage layout, safe compatibility,
  coercive compatibility, numeric conversion, and ABI compatibility remain
  distinct.
- Integer-backed fixed-point values conceptually add a fixed binary fractional
  position to finite integer representation; exact generic syntax is not yet
  established.
- Binary floating formats conceptually depend on fraction or mantissa width,
  exponent width, explicit or implicit integer bit, endianness, and applicable
  special-value/normalization rules.
- A concrete scalar type has statically known format properties even when its
  implementation uses software support.
- Hardware support is optional and operation-specific; “native” must not imply
  that every operation has one direct instruction.
- Target, compiler-host, and active execution environment remain distinct.
- No compiler implementation exists in this repository.

### Known inclusions

- Human-facing terminology for fixed-point and binary floating scalar families.
- Concrete type identity and the dimensions that determine one format.
- Logical value interpretation, range, precision, storage envelope, alignment,
  padding/non-value bits, encoding, and endianness.
- Zero, signed zero, infinities, NaNs, subnormals, invalid patterns, and other
  format-dependent domains at the depth needed to define concrete types.
- Exact, conventional, target-selected, compiler-host-selected, fastest,
  smallest, widest, and supported aliases where justified.
- Hardware-backed versus software-backed availability and visible cost.
- Default initialization and ordinary value construction.
- Numeric conversion, raw representation access, checked/reporting boundaries,
  and compatibility with integer/fixed-point or other floating formats.
- Final safe-compatibility and coercive-compatibility property lists for
  fixed-point and binary floating values, plus a deliberate recheck of the
  current integer list.
- Literal, operator, generic, reflection, build-profile, and interop
  consequences far enough to give every material deferral a live owner.
- Lasting documentation ownership and complete disposition of the floating
  maintainer notes and applicable numeric-family raw input.

### Known starting boundaries

- Complete generic declaration, factory, inference, specialization, and
  associated-type syntax.
- Exact decimal/binary literal tokenization, parsing, suffix, and commitment
  grammar beyond immediate scalar consequences.
- An exhaustive floating/fixed-point operator catalog when the scalar model can
  remain coherent without it.
- Complete rounding-mode, exception-flag, environment-control, and
  reproducibility APIs.
- CPU-provider file formats, backend capability discovery, instruction
  selection, software-library algorithms, and compiler lowering.
- SIMD/vector/matrix formats and packed-lane operations.
- Arbitrary-precision or decimal floating families.
- ABI, FFI, calling convention, serialization, and wire-format guarantees.
- Formal grammar, conformance, and specification language.

These boundaries do not erase consequences. Preserve every material pressure in
a current owner or indexed raw destination rather than leaving it only in the
eventual archive of this work item.

### Intentionally unresolved framing

- Whether fixed-point values are concrete integer-family specializations, a
  related scalar family, or another relationship.
- The exact concrete parameters and derived properties for each fixed-point and
  binary floating format.
- Which conventional aliases are language-defined, CPU-provider-defined, or
  deliberately absent.
- Which formats and operations every complete toolchain must support.
- The exact safe and coercive property lists, especially when two values share
  total storage but differ in sign, fractional position, exponent partition,
  explicit integer bit, endianness, or special-value policy.
- Whether and how unsupported formats remain valid type identities.
- Which raw representation operations exist and how they differ from numeric
  conversion, coercive conversion, and `unsafe cast`.
- The minimum ordinary arithmetic, comparison, reporting, and conversion
  surface needed to make each scalar family useful.

### Initial stopping guidance

Stop when the work has:

- established a teachable scalar mental model for fixed-point and binary
  floating values;
- defined which properties determine concrete identity, value meaning, storage,
  safe compatibility, and coercive compatibility;
- described ordinary exact/profile-selected type use and availability;
- explained representative construction, conversion, failure, raw access, and
  cost behavior with valid and invalid source;
- dispositioned special values, endianness, padding, support, and portability;
- rechecked and either confirmed or revised the current integer compatibility
  property lists;
- assigned every generic, literal, operator, reflection, CPU-provider, interop,
  and implementation consequence to a live owner or indexed raw destination;
- identified exact lasting owners and raw/legacy dispositions; and
- completed the required documentation-fit dry run.

Do not promote findings, archive this work item, begin work item `023`, or
design complete generics, literal grammar, exhaustive operators, CPU-provider
implementation, ABI/FFI, or compiler lowering without the separately required
discussion, alignment, and authorization.

## Reading scope

### Required

- [Documentation architecture](../documentation.md) - governs focused reading,
  decision maturity, promotion, deferral, and closure.
- [Floating-point maintainer notes](../raw/floating-point-maintainer-notes.md) -
  supplies the maintainer's primary fixed-point, binary floating, aliases,
  capabilities, support, and integer-recheck input.
- [Language vision](../../language/vision.md) - supplies explicit-cost,
  data-representation, low-level access, portability, and toolchain direction.
- [Integers](../../language/integers.md) - owns the finite integer model,
  storage envelopes, exact/profile-selected identities, conversions, and the
  current scalar compatibility list that this work must recheck.
- [Structural shapes and compatibility](../../language/structural-shapes-and-compatibility.md)
  - owns scalar format versus identity/layout, safe and coercive compatibility,
  and raw-view boundaries.
- [Endianness](../../language/endianness.md) - owns absolute/native byte-order
  meaning and semantic conversion versus raw storage.
- [Raw numeric type families](../raw/numeric-type-families.md) - preserves
  generic numeric-family, fixed-point, floating representation, conversion, and
  operation pressure that this work must disposition.

### Consequence-driven

- [Integer literals and realization](../../language/integer-literals.md),
  [raw literals maintainer notes](../raw/literals-maintainer-notes.md), and
  [raw literal operators](../raw/literal-operators.md) when fixed/floating
  literal source, commitment, suffixes, or compile-time value formation becomes
  material.
- [Operators](../../language/operators.md), the
  [operator catalog](../../language/operator-catalog.md), and the
  [integer operator catalog](../../language/integer-operator-catalog.md) when
  arithmetic, comparison, reporting, conversion, raw-bit, or protected form
  availability becomes necessary.
- [Raw CPU-provider model](../raw/cpu-provider-model.md) when native/software
  support, aliases, target/compiler-host choice, or capability stability becomes
  concrete.
- [Raw type parameters and generics](../raw/type-parameters-and-generics.md)
  when concrete family parameters, factories, associated types, or
  specialization identity become material.
- [Raw reflection](../raw/reflection.md) when format properties, support,
  aliases, hidden specializations, or operation availability need metadata.
- [Raw compile-time execution](../raw/compile-time-execution.md) when
  compiler-host/target evaluation, constant folding, or unavailable-format
  behavior crosses the scalar boundary.
- [Raw interop](../raw/interop.md) when foreign format, ABI, calling convention,
  serialization, or wire representation becomes concrete.
- [Enums](../../language/enums.md) only when scalar backing eligibility or
  semantic enum interaction becomes material.
- Legacy floating, numeric, casting, or literal material only when a concrete
  question cannot be resolved from the primary notes, current owners, and
  indexed raw input.

### Audit-only

- Archived work only when a concrete provenance or regression question cannot
  be answered from current owners and live raw input.

## Initial working posture

Begin from concrete programmer needs: selecting one supported format, storing a
known value, predicting precision/range, converting deliberately, and
understanding whether work uses hardware or software support. Reconstruct the
maintainer notes into a candidate model without treating generic placeholders,
alias lists, or capability structures as accepted merely because they are
detailed.

Use current integer, structural, and endian owners as constraints. Follow
literal, operator, provider, generic, reflection, and interop consequences only
when a concrete scalar decision crosses those boundaries.

## Working record

The initial reconstruction supplied the evidence and questions retained later in
this working record. The following findings are aligned for the current review
scope, but remain non-authoritative until separately promoted into their lasting
owners. Where the retained initial reconstruction conflicts with these findings,
the aligned findings control the continuing work.

### Aligned working findings

#### Programmer model and family names

Zax has two separate fractional scalar families:

- A **fixed-point scalar** is a finite signed or unsigned integer coefficient
  interpreted at a statically fixed fractional-bit position.
- A **binary floating-point scalar** has sign, exponent, and significand fields
  interpreted by one statically known format.

Do not call the first family "binary fixed floating point" or present it as a
variation of binary floating point. Its scale is base two, but its coefficient
and fixed binary point give programmers a different model from an IEEE-style
moving exponent.

The aligned namespace structure is:

```text
Scalars.Integers
Scalars.Fixed
Scalars.Floating
Scalars.Floating.Legacy
```

The family root is the canonical current-execution-environment route:

```zax
Scalars.Integers.I32
Scalars.Integers.Integer
Scalars.Integers.Near.TypeSize
Scalars.Fixed.I16F8
Scalars.Floating.Binary32
Scalars.Floating.Float
```

Explicit environment, absolute-endian, and support routes use the existing
distinctions:

```zax
Scalars.Integers.Target.I32
Scalars.Integers.CompilerHost.I32
Scalars.Integers.Little.I32
Scalars.Integers.Big.I32
Scalars.Floating.Target.Float
Scalars.Floating.CompilerHost.Float
Scalars.Floating.Legacy.MBF40
Scalars.Floating.Legacy.X87Extended80
```

Future decimal floating formats can use mechanically distinct names such as
`Decimal64` without placing current binary formats under a redundant extra
`Binary` namespace.

#### Fixed-point representation and identity

Fixed-point is part of the intrinsic integer family rather than a sibling
family that happens to use integer storage. A concrete specialization fixes:

- total coefficient width `W`;
- signedness;
- fractional-bit count `F`;
- two's-complement encoding for signed coefficients; and
- concrete endianness.

Illustrative generic spelling remains non-current:

```zax
MyI16F8 :: alias type Integer$(
  LogicalBits = 16,
  Signedness = Sign.Signed,
  FractionalBits = 8,
  Endianness = Endian.Little
)
```

The represented value is:

```text
coefficient * 2^-F
```

The useful bounds keep the binary point within the represented coefficient:

```text
signed:   0 <= F <= W - 1
unsigned: 0 <= F <= W
```

For a signed coefficient:

```text
minimum = -2^(W-1) * 2^-F
maximum = (2^(W-1) - 1) * 2^-F
```

For an unsigned coefficient:

```text
minimum = 0
maximum = (2^W - 1) * 2^-F
```

Every coefficient bit pattern is valid. Default initialization produces
coefficient zero and numeric zero. `F = 0` is exactly the existing integer
specialization; it does not create another intrinsic identity:

```text
I16F0 = I16
U16F0 = U16
```

Allowing negative `F` or `F` beyond these bounds would create a more general
scaled-integer family, introduce larger scale factors and shifts, and weaken the
simple bit-partition model. It is deliberately absent from the current family.

#### Fixed-point names

Canonical Zax fixed-point names state total coefficient width symmetrically:

```text
I<W>F<FractionBits>
U<W>F<FractionBits>
```

The initially useful exact pairs are:

```text
I16F8  / U16F8
I32F16 / U32F16
I32F8  / U32F8
I64F32 / U64F32
I16F14 / U16F14
I16F12 / U16F12
```

Q names remain transparent industry-facing alternatives under an explicitly
Zax-defined convention that excludes the sign bit from the integer count:

```text
QI<i>F<f> => signed width   = 1 + i + f
QU<i>F<f> => unsigned width = i + f
```

The corresponding aliases are:

```text
QI7F8   / QU8F8
QI15F16 / QU16F16
QI23F8  / QU24F8
QI31F32 / QU32F32
QI1F14  / QU2F14
QI3F12  / QU4F12
```

This preserves familiar Q spelling without inheriting the incompatible
sign-inclusive convention used by some other sources.

Research found real use of several mechanical formats but no ecosystem-wide
meaning for the proposed `MicroFixed16`, `ControlFixed32`, `AudioFixed32`,
`MLFixed16`, or similar domain names. In particular, ARM control libraries use
several Q15/Q31 formats and established audio systems commonly retain much more
than eight fractional bits. These descriptive names are not language aliases.
Domain libraries and applications may define identities after applying their
own range and precision requirements.

#### Fixed-point operations

Ordinary arithmetic requires operands of one exact fixed-point identity. Zax
does not implicitly combine independently scaled values.

Addition and subtraction retain coefficient width and `F`. Ordinary
multiplication and division also return the same concrete type and round an
inexact result to that type's grid using nearest, ties-to-even.

For same-`F` coefficients `A` and `B`, the mathematical coefficient results are:

```text
multiply = roundToNearestEven((A * B) / 2^F)
divide   = roundToNearestEven((A * 2^F) / B)
```

These equations define results rather than required lowering. An implementation
may use wider registers, native fractional instructions, fused operations, or
automatically supplied software while preserving the result.

A nonzero result that rounds to zero is permitted and does not inherently
panic. A reporting form may expose inexactness or rounding to zero. A rounded
result outside the coefficient range follows the required, optional, wrapping,
saturating, or reporting overflow policy selected by the operation. Required
division by zero fails.

The separate `full precision product` operation preserves the exact product in
a wider associated fixed-point type. For two like `W`, `F` operands, its natural
result has sufficient coefficient width for the full product and `2F`
fractional bits, conceptually `2W`, `2F`:

```zax
myOrdinary := myLeft * myRight
// same type as the operands; rounded to their existing grid

myFull := myLeft full precision product myRight
// wider exact product; exact final declaration details remain generic work
```

Division has no corresponding universally exact finite fixed-point result
because values such as one third require unbounded fractional precision.

Fixed-point bitwise operations remain available deliberately rather than by
accidental generic inheritance. Complement, AND, OR, XOR, AND-NOT, shifts,
rotations, counts, masks, and positions act on or inspect the coefficient
representation. Shifts also retain their numeric power-of-two consequences and
must state discarded-bit and overflow behavior.

An uncommitted integer used beside a concrete fixed-point operand realizes as
that same fixed-point identity. In `myBits : U32F8` followed by
`myBits ^ 1`, the `1` means fixed-point `1.0`, coefficient `256`; it does not
silently become a raw `U32F0` mask. Use one quantum or an explicit unsigned
representation view to address the lowest coefficient bit.

Remainder is available for one fixed-point identity. It uses a quotient
truncated toward zero, returns the same fixed-point type, and fails for a zero
divisor. Because both coefficients share `F`, coefficient remainder directly
produces the result. Thus typed `I16F8` value `10.5 % 4` treats the right literal
as `I16F8` value `4.0` and returns `2.5`. A wholly uncommitted `10.5 % 4`
expression still depends on future default real-type selection.

Scale-changing numeric conversion operates on value:

- increasing `F` is exact when the shifted coefficient fits;
- decreasing `F` is exact only when discarded bits are zero;
- fixed-to-integer conversion is the `F = 0` case;
- integer-to-fixed conversion is exact when the scaled coefficient fits; and
- rounded conversion is separate from modular integer `narrowing as`.

#### Endianness is intrinsic scalar format

Endianness is a built-in generic property of integer, fixed-point, and floating
formats. A concrete scalar is always little- or big-endian.

Native, "agnostic," or a comparable profile input is a selector, not a
surviving type property. Type realization settles it for the active execution
environment. Explicit target and compiler-host paths may select different
concrete types.

A CPU that can switch endian mode at runtime does not change an existing value's
type or interpretation. The selected target contract either fixes CPU mode or
supplies operations that preserve the declared concrete endianness despite
runtime mode. Opposite-endian arithmetic may therefore require byte swaps,
special instructions, or software work.

Absolute-endian specializations use an endian namespace segment:

```text
Scalars.Integers.Little.U32
Scalars.Integers.Big.U32
Scalars.Fixed.Little.I16F8
Scalars.Fixed.Big.I16F8
Scalars.Floating.Little.Binary32
Scalars.Floating.Big.Binary32
```

Unqualified `U32`, `I16F8`, and `Binary32` are active-environment aliases.
`Target.U32` and `CompilerHost.U32`, with corresponding fixed and floating
paths, select their named environments. Existing endian semantic enums become
intrinsic endian specializations or transparent aliases rather than wrapper
identities. They receive the complete operation surface of their scalar family.
Mixed-endian operands require explicit conversion: numeric conversion preserves
value and may rearrange bytes, while coercion preserves bytes and changes the
interpreted value.

This finding revises the current endian-semantic-enum model and must be followed
through integers, endianness, structural compatibility, environment aliases,
and operators as a direct consequence of work item `022`. It is not an
implementation-only concern.

For a logical width that is not a multiple of eight:

- storage uses the smallest whole-byte envelope;
- the used part of the highest participating byte is its low-order portion;
- unused high bits are non-value bits with unspecified contents;
- bit significance within a byte is fixed rather than target-defined; and
- endianness controls byte order.

Although defined numeric behavior cannot access non-value bits, their placement
must be stable because Zax permits direct memory inspection and structural
layout reasoning.

#### Ordinary binary floating formats

The ordinary generalized family follows IEEE practices with explicit,
language-defined deviations for named formats. An ordinary specialization fixes
exponent width, stored fraction width, encoding policy, and endianness. It has
one sign bit.

The initial generalized IEEE-style policy derives:

- exponent bias `2^(E-1) - 1`;
- zero and subnormal interpretation from an all-zero exponent field;
- normal biased-exponent interpretation;
- infinity and NaN interpretation from the all-one exponent field;
- signed zero;
- an implicit leading integer bit for normal values; and
- all-zero logical bits as positive zero.

The public generalized family does not initially expose an independently
combinable explicit-integer-bit parameter. All current ordinary formats use the
implicit model. Explicit integer-bit mode remains a reflected, derived property
of the closed `X87Extended80` legacy format. This avoids incoherent combinations
without preventing another closed policy if future evidence requires one.

Use these terms precisely:

- **fraction field**: stored bits following the integer bit;
- **significand**: the integer bit together with the fraction;
- **mantissa**: familiar informal vocabulary for the significand, accompanied
  by the precise term because some ecosystems use it for only the stored
  fraction.

The generic parameter remains `FractionBits`, not `MantissaBits`.

#### Exact and profile-selected floating names

These pairs are exact transparent aliases:

```text
Half   / Binary16
Single / Binary32
Double / Binary64
Quad   / Binary128
```

Each `BinaryN` name has the complete corresponding IEEE encoding and
special-value behavior. `BF16` is the established bfloat16 format.

`E4M3` and `E5M2` use the Open Compute Project OFP8 definitions:

- E4M3 has signed zero, subnormals, NaN, and no infinity.
- E5M2 has signed zero, subnormals, NaNs, and infinities.

Other variants such as FNUZ require variant-specific names. A target provider
does not redefine these exact formats.

`Float` is a distinct profile-selected identity for the provider's preferred
general-purpose binary floating format with the complete core numeric support
bundle. It may select Binary32 on one target and Binary64 on another.
`DoubleFloat` is not defined.

`FastF<N>` selects the provider-preferred supported format having at least `N`
logical storage bits. This is deliberately a storage-width and provider-choice
contract, not a claim that all floating formats form one precision/range order.
`LeastF<N>` similarly selects the smallest native format satisfying the stated
storage-width requirement.

`NativeMaxF`, `OptimizedMaxF`, and `SupportedMaxF` may remain
provider-selected identities with width-oriented meanings. There is no unique
`LanguageMaxF` type because many exponent/fraction partitions can share one
maximum width.

Language limits are instead immutable compile-time metadata under:

```zax
Scalars.Floating.LanguageLimits.maximumWidth
```

`LanguageLimits` is conceptually a type-owned descriptor whose values are
nonreplaceable, immutable, readonly, available during type formation, and need
no per-instance runtime storage. Exact `once` and compile-time-constant syntax
remain future declaration and compile-time work; `once` alone must not be
assumed to guarantee compile-time availability. The descriptor may later expose
separate exponent, fraction, storage, or alignment limits when required.

#### Legacy floating formats

Legacy formats are historically established, non-core encodings. Membership in
`Legacy` does not mean deprecated, unavailable, or software-only.

The x87 format is:

```text
Scalars.Floating.Legacy.X87Extended80 // canonical exact format
Scalars.Floating.Legacy.Extended80    // transparent alias
Scalars.Floating.Legacy.X80M64        // transparent alias
```

It has one sign bit, fifteen exponent bits with bias 16383, one explicit integer
bit, sixty-three fraction bits, and sixty-four bits of significand precision.
Its exact encoding and validity classes are fixed.

There is no one universal historical x87 arithmetic result contract. Individual
CPU providers may expose their hardware's rounding, precision-control,
exception, and instruction behavior rather than software-emulating one selected
x87 generation on every target. The provider must describe enough of that
behavior for source and reflection to determine support and portability.
Source requiring cross-target reproducibility uses a standard exact format or a
separately fixed operation contract rather than relying on unspecified
`X87Extended80` provider behavior.

`MBF40` is a separate closed legacy format rather than a variation created by
combining ordinary IEEE generic dimensions:

```text
Scalars.Floating.Legacy.MBF40
```

Its policy fixes exponent/sign/fraction placement, exponent interpretation,
implicit leading bit, zero domain, lack of IEEE NaNs/infinities/subnormals,
byte order, and historical operation behavior.

Microsoft BASIC for 6502 Version 1.1 with `ADDPRC=1` is the intended MBF40
arithmetic reference. Its source rounds a retained magnitude upward when the
high discarded guard bit is set, including the halfway case; underflow becomes
zero, overflow raises an error, and division by zero raises its dedicated
error. Zax MBF40 mode reproduces that historical behavior rather than imposing
IEEE ties-to-even.

Future compatibility vectors derive from that named source and configuration;
they do not need a separate historical arithmetic oracle.

A provider may support MBF40 storage and conversion without supporting its
arithmetic. A provider that declares one MBF40 operation supports that operation
for the format's complete valid domain and historical result contract. It does
not need to manufacture NaN or infinity because those are not MBF40 values.

Binary64 can exactly represent every finite MBF40 value and is the preferred
modern calculation bridge:

```zax
myModern := myLegacy as Binary64
// exact for every MBF40 value

myExactLegacy := myModern as MBF40?
// present only when the Binary64 value is exactly representable as MBF40

// Illustrative rounded-conversion wording; exact syntax remains operator work.
myRoundedLegacy := myModern rounding as MBF40
```

Raw representation access separately preserves the historical five-byte form
for files, emulators, and 6502 tools.

#### Valid, noncanonical, and invalid encodings

A **canonical** encoding is one ordinary construction and arithmetic may
produce. A **noncanonical but valid** encoding represents a value but is not an
ordinary result. An **invalid** encoding represents no value of the floating
type.

For `X87Extended80`:

- exponent zero with explicit integer bit zero is canonical zero or subnormal;
- an ordinary exponent with integer bit one is a canonical normal;
- maximum exponent with integer bit one is canonical infinity or NaN;
- exponent zero with integer bit one is a valid noncanonical pseudo-denormal
  for the corresponding normal value; and
- nonzero exponent with integer bit zero is invalid/unsupported.

Ordinary construction and arithmetic produce canonical encodings. Copying and
raw extraction preserve existing bits. Numeric use may normalize a
pseudo-denormal. Validated adoption accepts canonical and pseudo-denormal
patterns; optional adoption reports invalid patterns. Unsafe adoption asserts
validity but cannot turn an invalid pattern into a value.

For MBF40, every nonzero-exponent pattern represents a finite value. Exponent
zero represents zero regardless of remaining sign/fraction bits:

- all logical bits zero is canonical zero; and
- exponent zero with other bits nonzero is a valid noncanonical zero.

Historical arithmetic may leave residual non-value bits in an exponent-zero
result. Numeric comparison treats every such encoding as zero; copying and raw
extraction may preserve the bits; explicit canonicalization produces all-zero
MBF40. No MBF40 bit pattern inherently denotes NaN, infinity, or an invalid
value.

#### Type existence, storage support, and numeric support

Every language-permitted format identity can be named and reflected even when
the selected environment supplies no numeric implementation.

Every such instance has a minimum representation surface where target resource
limits permit the object:

- known size, alignment, and layout;
- positive-zero initialization;
- byte-preserving copy, move, assignment, and destruction;
- storage in aggregates and transfer through internal Zax calls; and
- raw representation access subject to the format's validity rules.

Arithmetic, comparison, classification, literal realization, and numeric
conversion require their declared support. Unsupported source produces a
compiler diagnostic. A declared operation must be correct for every valid input
in its contract; a weak target cannot silently return incomplete or incorrect
answers.

The compact environment support classification is an extensible enum-like type
with at least:

```text
Unsupported
Software
HardwareGuaranteed
HardwareOptional
TrapEmulated
```

`HardwareBacked` or the applicable enum classification is intentionally a
coarse selection fact: at least one relevant floating operation uses direct
hardware. It is not a promise that every operation is one instruction.
Runtime-optional hardware cannot change static type identity; it changes the
implementation path for the already selected format.

A format has complete core numeric support when the environment supplies:

- unary sign;
- addition, subtraction, multiplication, and division;
- equality and ordering;
- classification; and
- the format's required rounding and exceptional-result behavior.

Representation-only behavior is baseline rather than part of this numeric
bundle. Conversions, square root, fused multiply-add, remainders,
transcendentals, reports, and environmental controls may be individually
available. Reflection can query exact operation availability without one
permanently exhaustive capability bitmap.

An explicitly named type may use an individually supported operation without
having the complete bundle. `Float` and other profile roles that promise
ordinary numeric usefulness select only a type with the complete bundle.

Format semantics and environment support remain separate. A software
implementation that omits Binary32 NaNs is not a reduced Binary32 format; it
either fails to implement the applicable operation or implements a different
format. MBF40 legitimately has no NaN because that is its complete format.

#### Construction and real-number source

All intrinsic fixed and named floating formats use positive zero as the default
value. All-zero logical bits represent that zero, including formats with an
explicit integer bit. Writing zero to the complete storage envelope is valid;
doing so does not promise that non-value padding always remains zero.

Unprefixed real source is conceptually parsed into an exact mathematical value
during compilation and realized directly into an already selected fixed or
floating destination. It is not first constructed as `Float`.

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
without a fractional point. Bare `e100` is not a number literal. Binary and
hexadecimal exponent forms remain future literal work.

Ordinary precision loss is not an error. Realization correctly rounds to the
destination's policy:

- ordinary fixed-point and IEEE-style destinations use nearest, ties-to-even;
- MBF40 uses its historical rounding contract;
- a tiny nonzero value may round to zero; and
- ordinary inexact values such as decimal `0.1` do not require a diagnostic.

A finite source literal whose magnitude exceeds the destination's finite range
is a compile-time diagnostic rather than silently becoming infinity. A future
exact-realization form may let source require zero precision loss. Exact token,
exponent, suffix, radix, and prefixed-literal grammar remains literal work.

#### Numeric conversion, representation, and adoption

Numeric conversion answers which destination value represents the source's
numeric meaning. Representation access answers which bits encode the source.
Coercive recasting changes the typed point of view over the same bits. These are
different operations.

Numeric conversion remains explicit:

- `as Destination` is available only when type facts or a compile-time-known
  value prove exact representability; it does not introduce a runtime panic;
- `as Destination?` reports any value-dependent exact nonrepresentability
  through absence rather than panic;
- rounded conversion uses an explicit rounding policy when ordinary literal
  realization is not the operation;
- infinity and NaN have no exact integer/fixed destination;
- finite float-to-integer/fixed conversion handles fractional loss and range
  independently;
- integer/fixed-to-float exactness depends on significand precision and
  exponent range; and
- NaN payload, signaling state, and signed zero are representation concerns
  beyond mathematical-value equality.

Rounded conversion may lose precision or round a tiny nonzero value to zero
without panicking. When the rounded magnitude is outside the destination range,
an IEEE floating destination may produce its defined infinity; a destination
without infinity uses an explicit required/optional/saturating policy. The
required form may panic and the optional form reports absence. Equal-format
endian conversion is total and cannot overflow or underflow.

The representation-adoption surface needs concepts equivalent to:

```zax
// Illustrative only; exact operator phrases remain unsettled.
myBits := representation bits of myValue
myOptional := Extended80 optional from representation myBits
myTrusted := Extended80 unsafe from representation myBits
myCanonical := myValue canonical representation
```

The final surface distinguishes exact extraction, validation, optional invalid
reporting, unsafe validity assertion, and canonicalization. Unsafe adoption
asserts the precondition that a pattern is valid; it does not grant meaning to
an invalid pattern.

For a padded unusual format, logical representation extraction excludes
storage-envelope padding. Direct pointer access to all object bytes remains a
separate target-layout operation that may observe unspecified non-value bits.

#### Safe scalar compatibility

Fixed-point safe compatibility requires equality of:

- coefficient logical width;
- signedness and coefficient encoding;
- fractional-bit position;
- concrete endian role;
- logical-bit placement;
- storage extent and required alignment;
- non-value storage placement and behavior; and
- normalization/valid-pattern rules.

Floating safe compatibility requires equality of:

- total logical format width;
- sign encoding;
- exponent width, bias, encoding, and class-code allocation;
- stored fraction width and significand interpretation;
- implicit or explicit integer-bit mode;
- normal, subnormal, zero, infinity, NaN, and invalid-pattern rules;
- payload and canonicalization rules where applicable;
- concrete endian role and logical field placement; and
- storage extent, alignment, and non-value-bit rules.

Hardware support and operation cost do not participate in scalar format
compatibility. Public and programmer identities remain outside scalar shape.

#### Coercion is a checked representation lens

In everyday terms, coercion gives the same stored bits another recognized lens
when that lens provides more insight than raw byte access. Viewing Binary32
through U32, for example, exposes known sign, exponent, and fraction positions
in an integer format.

Precisely, coercion is a compiler-checked, zero-translation reinterpretation
through a recognized second meaning of the same stored bits. It need not
preserve numeric value, but it preserves more known structure than an arbitrary
raw cast.

Examples of retained information include:

```text
I32 -> U32
```

Every bit retains its integer position and the values have a defined
modulo-`2^32` relationship.

```text
I16F8 -> U16F4
```

The coefficient geometry remains known while signedness and scale change.

```text
Binary32 -> same-endian U32
```

The destination's bit 31 is known to expose the sign, bits 30 through 23 the
exponent, and bits 22 through 0 the fraction.

A different floating exponent/fraction partition supplies no corresponding
useful relationship. Use `unsafe cast` when only raw extent remains known.

Coercion remains a local reference-producing operation rather than a carried
posture or a by-value copy:

```zax
source as coercive layout Destination &
source unsafe as coercive layout Destination &
```

There is no declarable `compatible representation` or coercive posture.
Coercive permission does not propagate implicitly through parameters, results,
or inferred declarations.

Changing numeric interpretation is not by itself unsafe. Safe coercion is
available when the relation provides the required insight, every valid source
representation is valid in the destination, and any requested writable access
cannot create an invalid source representation. Unsafe coercion is required
when initial validity is asserted rather than proved or writes may invalidate
the source scalar.

Neither form grants access authority. A readonly, immutable, or final source
cannot become writable, mutable, or varying. Existing writable, mutable, and
varying authority may be preserved when the ordinary whole-root, anchored
region, lifetime, construction, replacement, and destruction conditions permit
it. Unsafe writes that can create an invalid source representation carry a
restoration obligation before the original scalar type is used again. Coercion
cannot bypass bounds, lifetime, resource, construction, or destruction
invariants; those remain `unsafe cast` territory.

The directional scalar relations are:

- equal-width integers may coerce when storage geometry is compatible;
- equal-width fixed/integer formats may coerce when coefficient geometry is
  compatible, including local reinterpretation of signedness and `F`;
- integer/fixed cross-endian coercion may expose the precisely known byte-order
  reinterpretation;
- one binary floating format does not coerce to a different floating format
  unless their complete formats already match safely;
- a binary float may coerce to an equal-width, same-endian unsigned integer when
  logical field placement, extent, alignment, and padding agree;
- an unsigned integer may coerce to that floating format only when every source
  bit pattern is a valid destination encoding; and
- unrelated layouts require `unsafe cast`.

Consequently, matching-endian:

```text
Binary32 <-> U32
```

is safely coercible, including writable access, because every bit pattern is
valid in both directions. For Extended80:

```text
X87Extended80 -> U80
U80 -> X87Extended80
```

readonly extraction is safe. Writable extraction is unsafe because it can
create an invalid original encoding. The reverse requires validation or an
unsafe validity assertion because some U80 patterns are not Extended80 values.

Every MBF40 pattern is a finite value or an exponent-zero representation of
zero, so matching-endian:

```text
MBF40 <-> U40
```

may be bidirectionally coercible.

#### Initial operation surface

Supported fixed-point types provide:

- ordinary construction, copy/move, assignment, and swap;
- unary sign and magnitude;
- same-identity addition, subtraction, multiplication, and division;
- same-identity remainder with a quotient truncated toward zero;
- equality and ordering;
- compound arithmetic;
- applicable required, optional, wrapping, saturating, and reporting policies;
- bitwise operations and finite-width inspection;
- exact, optional, and rounded conversion;
- coefficient, quantum, and format inspection; and
- full-precision product.

Supported binary floating types provide:

- ordinary construction, copy/move, assignment, and swap;
- unary sign and magnitude;
- same-identity addition, subtraction, multiplication, and division;
- the format's equality, unordered comparison, and ordering behavior;
- classification of zero, finite, normal, subnormal, infinity, and NaN where
  those classes exist;
- exact, optional, and rounded numeric conversion;
- adjacent-value operations such as next-up and next-down;
- total ordering suitable for deterministic ordering including NaNs;
- representation extraction and adoption; and
- reflected support and cost information.

Square root and fused multiply-add are useful individually supported
operations. Remainder variants, transcendental functions, exception flags,
mutable rounding environments, atomics, exhaustive min/max NaN policies, and
the complete protected operator catalog remain later operator work.

Operations should follow established IEEE, historical-format, library, and
hardware semantics when those choices are coherent with Zax. Hardware
availability does not let providers silently choose another result. The language
selects stable semantics for ordinary formats, while explicitly
provider-relative legacy modes such as `X87Extended80` expose their narrower
portability contract.

#### Integer compatibility recheck

The current integer safe list remains sound after these refinements when:

- zero fractional bits identifies the intrinsic `F = 0` specialization;
- endianness is the concrete generic property described above;
- logical-bit placement is included; and
- destination value behavior cannot consume source padding.

The coercive list should replace "sufficient compatible target storage
envelope" with exact same-region requirements:

- equal logical coefficient width;
- equal selected storage extent;
- source placement satisfying destination alignment;
- matching logical-bit placement for the declared relation; and
- padding/validity behavior compatible with the destination view.

A larger destination may not overread a smaller source, and a smaller
destination may not silently leave bytes outside a corresponding aggregate
leaf. Numeric widening remains conversion rather than coercion.

### Remaining formalization and live deferrals

No unresolved semantic alternative currently blocks the aligned scalar model.
The following exact mechanisms remain deliberately assigned rather than
silently completed here:

- generic declaration, validation, associated full-product type, selector, and
  type-result syntax remain in
  [type-parameters and generics](../raw/type-parameters-and-generics.md);
- remaining real token grammar, binary/hexadecimal exponent spelling, prefixes,
  suffixes, default type selection, and exact-source realization syntax remain in the
  [literal maintainer notes](../raw/literals-maintainer-notes.md) and
  [literal-operator input](../raw/literal-operators.md);
- exact new operation spellings and the exhaustive protected surface remain in
  the [operator catalog](../../language/operator-catalog.md) after promotion
  contributes the aligned semantics;
- profile files, versioning, support enum realization, runtime hardware
  dispatch, individual operation availability, and cost metadata remain in the
  [CPU-provider input](../raw/cpu-provider-model.md);
- metadata source forms for scalar format, `LanguageLimits`, support,
  operation availability, aliases, and representation validity remain in
  [reflection](../raw/reflection.md);
- host execution, target-context evaluation, capability-dependent static
  branches, and reproducible evaluation remain in
  [compile-time execution](../raw/compile-time-execution.md);
- foreign ABI, calling convention, x87 ABI padding, serialization, and stable
  wire contracts remain in [interop](../raw/interop.md);
- decimal floating, arbitrary precision, and vectors remain deferred until
  concrete use creates their own work; and
- a general freely combinable explicit-integer-bit floating policy remains
  deferred until evidence beyond the closed `X87Extended80` format requires
  one.

MBF40 compatibility vectors remain future formal-contract and implementation
evidence derived from the named Microsoft Version 1.1 `ADDPRC=1` source. They
do not reopen the aligned format or historical operation model.

Fixed-point and binary floating-point teaching use separate concept owners.
Fixed-point readers should not need to traverse exponents, NaNs, target floating
support, or legacy floating formats to understand coefficient-and-scale
arithmetic. Their shared endian and compatibility mechanisms remain in the
existing cross-cutting owners rather than a third scalar umbrella document.

### Initial reconstruction retained as superseded evidence

The remainder of this working record is the initial evidence reconstruction.
It remains useful for provenance, examples, and rejected alternatives, but its
candidate labels and open questions are superseded where the aligned findings
above provide an answer.

#### Current candidate programmer model

Zax would have two related but distinct binary-scaled scalar kinds:

- A **binary fixed-point value** is a finite signed or unsigned integer
  coefficient interpreted at a statically fixed binary scale. If the concrete
  type has coefficient width `W` and `F` fractional bits, its represented value
  is `coefficient * 2^-F`.
- A **binary floating-point value** has a statically fixed sign, exponent, and
  significand encoding. Its binary point moves according to the encoded
  exponent. The complete concrete format also says how exponent codes,
  normalization, zero, infinities, NaNs, subnormals, and invalid patterns work.

In both cases, the concrete type fixes numeric meaning. Target support decides
whether and how operations on that already-defined type are available; it must
not silently change the type's value set or encoding. Hardware execution,
software execution, storage layout, public role identity, numeric conversion,
and raw representation access remain separate facts.

This suggests three programmer-visible layers:

1. an exact intrinsic specialization that fixes the complete numeric format;
2. transparent conventional names for exact formats whose meaning is globally
   fixed, such as a fully defined `Binary32`; and
3. distinct profile-selected intent identities for target-dependent choices,
   comparable to current `Integer`, `FastI32`, and `NativeMaxI`.

The third layer may select the first, but representation coincidence must not
erase the profile role or create implicit conversion.

#### Most important contradiction

The primary notes currently mix **format semantics** with **environment
capability**. `SupportsNaN`, `SupportsInfinity`, `SupportsSubnormals`,
`SupportsSignedZero`, exponent bias, and implicit-versus-explicit integer-bit
mode are properties of a concrete format. Hardware/software availability and
per-operation acceleration are environment capabilities.

For example, a software implementation on a 6502 that omits Binary32 NaNs is
not reduced-capability `Binary32`; it implements a different numeric format.
Conversely, a target may fully support Binary32 semantics in software despite
having no floating-point instruction.

This separation is prerequisite to trustworthy aliases, structural
compatibility, source stability, and host/target reasoning.

#### First decisions for maintainer review

The reconstruction recommends resolving these in order:

1. Confirm the coefficient-and-scale fixed-point model and whether `F = 0`
   is exactly the existing integer specialization rather than a second scalar
   identity.
2. Confirm that a binary floating specialization includes a complete encoding
   policy, not only exponent/fraction widths and integer-bit mode.
3. Separate format facts from per-environment type/operation support.
4. Decide the representability and rounding contract for fixed-point
   multiplication/division, numeric conversion, and decimal source.
5. Decide what floating coercion is allowed to ignore. The evidence demands a
   reduced coercive list but also appears to require every floating format
   property to match, which would leave little or no reduction.
6. Only then accept, revise, or reject the proposed conventional and
   profile-selected names.

### Binary fixed-point reconstruction

#### Candidate representation

The most direct reading of the maintainer notes is that fixed-point is a
specialization of the intrinsic integer family:

```zax
// Illustrative only: generic names and argument syntax are unsettled.
MySigned16F8 :: alias type Integer$(
  LogicalBits = 16,
  Signedness = Sign.Signed,
  FractionalBits = 8
)
```

`LogicalBits` counts every coefficient bit, including the sign bit for a signed
two's-complement coefficient. `FractionalBits` fixes the scale and is part of
the concrete specialization. It is not per-value metadata.

For a `W`-bit coefficient and `F` fractional bits:

```text
quantum        = 2^-F
unsigned range = 0 through (2^W - 1) * 2^-F
signed range   = -2^(W-1) * 2^-F
                 through (2^(W-1) - 1) * 2^-F
```

For the illustrative signed 16-bit, 8-fractional-bit type:

```text
coefficient range = -32768 through 32767
value range       = -128 through 127.99609375
quantum           = 0.00390625
```

Every logical coefficient pattern is valid. Signed coefficients remain
two's-complement. Default initialization produces coefficient zero and numeric
zero. Storage-envelope and non-value-bit behavior can remain the integer
behavior for coefficient width `W`.

The phrase in the notes that fixed-point minimum and maximum are "identical" to
integer minimum and maximum is coherent only for the stored coefficients. The
presented numeric range is scaled by `2^-F`.

The candidate initially treats `F` as a nonnegative count no greater than the
coefficient width, matching the notes' language about bits dedicated to the
fractional part. Allowing the binary point outside the stored coefficient
(`F > W` or a negative `F`) would create a more general scaled-integer family.
That extension is coherent but lacks an initiating use case and can remain
deferred unless the maintainer intends it now.

#### Relationship to ordinary integers

If integer types are already the intrinsic family at `FractionalBits = 0`, then:

```text
I16 = Integer$(16, Signed, FractionalBits = 0)
U16 = Integer$(16, Unsigned, FractionalBits = 0)
```

Under that model, "fixed-point is an integer" is a concrete family
relationship, not a claim that `I16F8` and `I16` have equal numeric meaning.
They share coefficient representation rules while differing in scale and
therefore in scalar format.

This choice has a useful consequence: an exact specialization with `F = 0`
does not create a second type competing with `I16`. If fixed-point instead
becomes a sibling intrinsic family, work must justify why its zero-fraction
specialization has a distinct intrinsic identity and which operations differ.

#### Arithmetic is not all the same coefficient arithmetic

Addition, subtraction, comparison, negation, and some shifts can often reuse
integer algorithms for operands with the same scale. Multiplication, division,
and scale-changing conversion cannot merely apply the underlying integer
operation and retain the same interpretation.

For `F = 8`, `1.5` has coefficient `384`:

```text
384 * 384 = 147456
```

Interpreting `147456` again at `F = 8` gives `576`, not `2.25`. A same-type
product needs to divide the coefficient product by `2^8`; an exact widened
product instead naturally has `F = 16`. Division has the inverse scaling
problem.

Even when overflow is impossible, a same-type result may not lie on the
destination grid:

```text
(1 / 256) * (1 / 256) = 1 / 65536
```

That exact result is not representable at `F = 8`.

The scalar model therefore needs an explicit result contract:

- Addition and subtraction of one concrete identity can follow current
  required/optional/wrapping/saturating/reporting integer policy shapes, with
  coefficient overflow interpreted as numeric nonrepresentability.
- Ordinary multiplication and division must say whether they:
  - return the same type and report/panic/round when the exact value is not on
    its grid;
  - return a wider associated result type with a different scale; or
  - require the programmer to select a result and rounding policy.
- Bitwise operations act on coefficient representation rather than on an
  abstract real number. They must not be inherited automatically merely
  because the implementation family is integer-backed.
- Shifts need numeric definitions and representability policies. A coefficient
  left shift multiplies the represented value by a power of two; a right shift
  can discard value and therefore needs the same signed rounding clarity as
  division.

A candidate consistent with current checked integer arithmetic is for required
same-type `*` and `/` to require the exact mathematical result to be
representable and panic otherwise, with optional, explicit-rounding, and
reporting forms for other needs. That preserves exact meaning but could make
ordinary fixed-point multiplication unexpectedly failure-prone. A widened
exact result is friendlier to generic arithmetic but creates substantial
associated-type and expression-growth pressure. This needs maintainer review
rather than being inferred from "all math operations are actually the same."

#### Fixed-point conversion

Numeric conversion should operate on represented values, never merely relabel
coefficient bits:

- Increasing `F` shifts the coefficient left and is exact when the destination
  coefficient fits.
- Decreasing `F` is exact only when every discarded low bit is zero.
- Fixed-to-integer conversion is the `F = 0` case and is exact only for an
  integral, in-range value.
- Integer-to-fixed conversion is exact when the scaled coefficient fits.
- A signedness change also applies the current exact/range rules.

```zax
myValue : MySigned16F8 = 1.5

myWhole := myValue as I16?
// absent: 1.5 is not exactly representable as I16

myExact : MySigned16F8 = 2.0
myInteger := myExact as I16
// 2
```

The examples use established `as Destination` and `as Destination?` shapes.
An exact final syntax for truncating or rounded conversion is not established.
`narrowing as` currently means modular integer conversion and should not be
silently reused for "discard the fraction"; those are different policies.

Rounding needs at least a direction/tie rule, overflow behavior, and a way to
distinguish an inexact finite result from an out-of-range result. Whether the
operation returns only a value, an optional, or a report belongs with the
numeric operation surface.

#### Q-name contradiction

The notes first say there will be no predefined fixed-point aliases, then say
that six pairs of aliases "will be added." That is an unresolved contradiction,
not an accepted alias inventory.

The proposed signed names appear to use the common convention:

```text
QI7F8 = 1 sign bit + 7 integer-magnitude bits + 8 fractional bits
        = 16 stored coefficient bits
```

Under that convention, its full-width unsigned counterpart would have eight
integer bits and eight fractional bits, so `QU8F8`, not `QU7F8`. Using
`QI7F8` and `QU7F8` for equal-width signedness counterparts either leaves one
unused unsigned bit or silently changes what the integer-field count means.
The same issue affects every proposed `QI...`/`QU...` pair.

Before any Q alias can be accepted, the name must answer:

- whether the integer count includes or excludes a sign bit;
- whether it counts all bits to the left of the binary point or only magnitude
  bits;
- whether signed and unsigned counterparts have equal total coefficient width;
- whether `I`/`U` describe signedness or are themselves part of the Q count
  convention; and
- whether a spare or padding bit is intentional.

The descriptive names such as `MicroFixed16`, `ControlFixed32`,
`AudioFixed32`, and `MLFixed16` also imply domain recommendations that the
evidence does not establish. A format useful in one controller, audio path, or
model may be unsuitable in another because range and precision requirements
vary. Exact mechanical names can be considered once the count convention is
fixed; domain names should wait for demonstrated standard or ecosystem meaning.

### Binary floating-point reconstruction

#### Widths alone do not define a format

The notes' three main dimensions are necessary but insufficient:

- exponent field width;
- stored significand field width; and
- implicit versus explicit integer bit.

A complete candidate specialization also needs:

- one sign-bit rule;
- exponent-field interpretation and bias;
- the set of exponent codes used for normal, subnormal, zero, infinite, NaN,
  or invalid encodings;
- significand interpretation for each class;
- whether zero has one sign or two;
- whether subnormals exist and how they are normalized;
- whether infinities exist;
- which NaN forms exist, including quiet/signaling and payload behavior where
  applicable;
- treatment of noncanonical or invalid bit patterns;
- canonicalization rules on construction, arithmetic, load, conversion, and
  raw adoption;
- logical bit ordering and byte-order role;
- total logical format width, storage envelope, field placement, and non-value
  storage rules; and
- default zero encoding.

Without those properties, two types with `E = 4` and `M = 3` can have different
ranges, zero behavior, NaN behavior, and valid bit patterns while appearing to
be one specialization.

Use **significand** for the value-bearing precision and **stored fraction
field** when the leading integer bit is implicit. "Mantissa bits" is common
informal vocabulary but is ambiguous about whether the hidden leading bit is
counted.

For a format with `E` exponent bits and stored field width `M`:

```text
implicit integer bit: total logical width = 1 + E + M
                      normal precision    = M + 1 bits

explicit integer bit: total logical width = 1 + E + M
                      normal precision    = M bits
```

Here `M` includes the stored integer bit in explicit mode. This makes
Binary32's `M = 23` and x87-style extended precision's `M = 64` internally
consistent.

#### Format policy is not target capability

A candidate reflected format description can expose immutable facts such as:

```text
logical format width
exponent width and bias
stored significand width
normal precision
implicit/explicit integer-bit mode
zero, subnormal, infinity, NaN, and invalid-pattern policies
logical field placement and endian role
```

Separately, each execution environment can expose support facts such as:

```text
can store/pass this type in Zax code
which required operations are available
which operations have direct hardware support
which operations use automatically supplied software support
cost class and relevant target constraints
```

`HardwareBacked` defined as "at least one hardware floating operation exists"
is too weak for programmer cost reasoning. A target may load a format directly
but emulate arithmetic, or add directly but emulate division and conversion.
Native representation and per-operation support should follow the separation
already established for integers.

Likewise, "software library based" should not imply a user-selected optional
library for a protected fundamental operation. If Zax requires the operation,
the toolchain may supply it with instructions, emitted code, an intrinsic, or
an automatically linked helper. If the operation is optional for that exact
format, its unavailability must be visible before source relies on it.

#### Exact conventional formats

The following names can plausibly be language-defined transparent aliases only
after they identify complete fixed formats:

- `Binary16`: sign 1, exponent 5, stored fraction 10, implicit leading bit,
  IEEE binary16 encoding and special-value rules.
- `Binary32`: sign 1, exponent 8, stored fraction 23, implicit leading bit,
  IEEE binary32 encoding and special-value rules.
- `Binary64`: sign 1, exponent 11, stored fraction 52, implicit leading bit,
  IEEE binary64 encoding and special-value rules.
- `Binary128`: sign 1, exponent 15, stored fraction 112, implicit leading bit,
  IEEE binary128 encoding and special-value rules.
- `BF16`: sign 1, exponent 8, stored fraction 7, implicit leading bit, with one
  explicitly selected bfloat16 encoding policy.

If `Half`, `Single`, `Double`, and `Quad` always mean those exact formats, they
can be transparent conventional aliases. If they mean "at least this much
precision" or another profile choice, they are profile-selected identities and
must not be documented as synonyms for `Binary16` through `Binary128`.

Provider namespaces should report availability and select profile roles; they
should not redefine what `Binary32` means. There is no need for target and
compiler-host copies of an exact format identity merely because operation
support differs.

#### Candidate aliases that are not yet concrete

`E4M3` and `E5M2` are format-family labels, not complete definitions. Existing
industry variants differ in finite-only behavior, infinity, NaNs, signed zero,
subnormals, exponent bias, and interpretation of exceptional patterns. The
notes' E4M3 claim of no infinity, no NaN, no subnormal, and no signed zero does
not identify a familiar variant by itself and conflicts with commonly used
E4M3 encodings that retain subnormals, signed zero, and a NaN encoding.

Zax should use a variant-specific exact name or define and document its own
complete format. A bare `E4M3` alias would be unstable until one policy is
chosen.

`X80M64` needs the same treatment. An x87-style 80-bit format has an explicit
integer bit and noncanonical/pseudo encodings that require policy. Historical
ABIs may place its 80 value bits in 10, 12, or 16 bytes, but an exact Zax
format's own storage envelope cannot vary among those merely because foreign
ABIs do. A foreign ABI wrapper or adapter may add container padding later.
Hard-coding little endian also makes this an absolute stored format, not a
target-independent conventional arithmetic alias.

`MBF40` is explicitly excluded near the beginning of the notes and then listed
among provider aliases. Its exponent/significand arrangement and special-value
rules do not fit the proposed IEEE-style generic merely by supplying widths and
a bias. The coherent initial disposition is to exclude it from this binary
intrinsic family and preserve it as future custom-format or interop evidence,
unless the maintainer deliberately wants a more general floating encoding
family.

#### Width-only profile selection is not enough

The proposed `FastF8` through `FastF128`, `LeastF8` through `LeastF128`, and
maximum aliases use total width as if it ordered floating capability. It does
not.

Two eight-bit formats can trade exponent range against precision. A 16-bit
format can have wider exponent range but less precision than another 16-bit
format. "At least 16 bits" therefore does not say which values or error bounds
the selected type must support.

A floating selector needs requirements such as:

- minimum finite precision;
- minimum normal and/or subnormal range;
- required special values;
- required rounding behavior;
- maximum storage or alignment;
- native-representation or software-fallback policy;
- execution environment; and
- preference among qualifying formats.

`FastF32` could remain ergonomic if `F32` is defined as a requirement contract
rather than just a bit count, but that contract must be explicit. Similarly,
`NativeMaxF`, `OptimizedMaxF`, `SupportedMaxF`, and `LanguageMaxF` need an
ordering relation. "Widest total format" is mechanically measurable but often
not a useful maximum numeric capability.

The current integer names cannot simply be copied because integer width orders
both range and unsigned precision monotonically; floating field partitions do
not.

### Endianness and storage

Every concrete value used in one execution context needs a settled byte
interpretation. That does not necessarily mean absolute endianness belongs in
every target-independent exact type identity.

The current integer/endian model distinguishes:

- an ordinary numeric type whose storage follows the active environment;
- absolute big- and little-endian semantic types used for controlled stored
  representation; and
- explicit compiler-host and target contexts.

Floating/fixed-point work should either reuse that model or explicitly revise
it for all numeric scalars. Adding a little/big parameter only to the new
generic families while current ordinary integers use endian semantic enums
would create two competing endian models.

The maintainer note about a bi-endian CPU is compatible with current
`native` terminology: one compiled execution context must still select concrete
semantics, and runtime mode switching must not silently change existing values'
interpretation. A profile can generate a runtime dispatch when required, but
that is an operation cost, not per-value "agnostic" endianness.

For a non-byte-multiple logical float format, storage rounds up to a whole-byte
envelope. Candidate rules consistent with integers are:

- logical format bits occupy one contiguous defined field;
- no padding appears between sign, exponent, and significand fields;
- non-value envelope bits are outside that field at its most-significant end
  under the type's logical ordering;
- non-value contents are unspecified and cannot affect defined numeric
  behavior; and
- required alignment remains a target layout fact.

"IEEE field order" does not itself define how an unusual non-byte-multiple
format maps into memory under both byte orders. The complete format must define
logical bit numbering first, then define byte serialization/storage order and
where envelope padding lies.

Direct pointer access may expose object-representation bytes, including
unspecified padding, but it must not turn those bits into stable value data.
Defined raw numeric access should expose logical format bits or a specified byte
sequence, not claim deterministic padding.

### Construction, literals, and default values

#### Default construction

Fixed-point zero is coefficient zero and therefore all-zero logical bits.

For floating formats, the simplest language-wide default contract is positive
zero. Requiring its logical encoding to be all zero keeps zero initialization
predictable for intrinsic formats. A custom encoding without all-zero positive
zero would either need active default construction or would not satisfy that
intrinsic contract. Whether this is a mandatory intrinsic-format invariant
needs confirmation.

Signed negative zero remains distinct only in formats that define it. Default
construction should not choose negative zero.

#### Uncommitted real source

The existing literal input demonstrates:

```zax
myFixed : MySigned16F8 = 1.5
mySingle : Single = -17.44
```

A candidate model is to parse unprefixed real source into an exact mathematical
rational during compilation, apply unary sign intent before realization, and
let an already selected fixed/floating destination construct the concrete
value. The source is not first a default `Float`, just as `55` is not first an
`Integer` when a typed integer destination is already known.

The analogy stops at representability:

- `1.5` is exactly representable at binary fixed `F = 1` or greater and in the
  conventional binary floating formats.
- `0.1` is not exactly representable in any finite binary fixed scale or finite
  binary floating significand.
- `17.44` is likewise generally rounded for a binary floating destination.

Zax therefore needs a deliberate literal rule. Plausible choices are:

1. typed binary real realization is exact-only, making common decimal source a
   diagnostic unless an explicit rounding form is used;
2. typed binary floating realization uses one defined default rounding mode,
   while fixed-point remains exact-only; or
3. both families use defined default rounding and provide a way to require
   exact source realization.

Exact-only follows Zax's explicitness goals but makes ordinary floating code
onerous. Default correct rounding follows programmer expectations but must not
hide overflow, underflow, or a build-mode-dependent result. The chosen rule
must define tie behavior and compile-time diagnostics and must not depend on the
compiler host's native floating arithmetic.

Prefixed literals still return one concrete type and do not become uncommitted
real values. General tokenization, exponent spelling, suffixes, radix forms,
and custom literal declarations remain in the live literal inputs.

### Numeric conversion and raw representation

The operation families should answer three different questions:

1. **Numeric conversion:** what destination value represents the source's
   numeric meaning under an exact, optional, or selected rounding policy?
2. **Logical representation access:** what defined sign/exponent/significand or
   coefficient bits encode this value?
3. **Storage reinterpretation:** may this region be viewed under another type,
   preserving bytes and accepting unsafe semantic responsibility?

They must not share one vague "cast."

#### Candidate numeric conversion behavior

- Fixed/integer/floating identities never convert implicitly merely because
  values happen to fit.
- Plain `as Destination` remains exact and is available when type facts or a
  compile-time-known value prove representability.
- `as Destination?` reports value-dependent nonrepresentability.
- Rounded conversion needs an explicit policy when the ordinary literal rule
  has not already supplied one.
- Integer modular `narrowing as` is not automatically a float/fixed rounding
  operation.
- Infinity and NaN cannot become integers or fixed-point values through exact
  conversion.
- A finite float-to-integer/fixed conversion must account independently for
  fractional loss and range.
- Integer/fixed-to-float exactness depends on significand precision, not only
  exponent range.
- Widening Binary32 to a conventional Binary64 preserves every finite numeric
  value exactly, but NaN payload/signaling preservation is a separate format
  promise and must not be smuggled into the word "exact."
- Signed zero raises a similar distinction: mathematical conversion to integer
  zero preserves numeric value but not the source format's zero sign.

These cases may justify distinguishing exact **numeric value** conversion from
representation-preserving conversion. The latter belongs to raw bits,
transcoding, or a specifically promised same-family conversion.

#### Candidate raw access

Useful protected concepts, with exact source syntax deferred, are:

- extract the `W` logical format bits into an exact unsigned integer or another
  bit container;
- optionally extract a specified byte-order sequence;
- construct from valid logical format bits;
- report or reject an invalid/noncanonical source pattern;
- adopt a pattern under unsafe responsibility where the destination permits
  invalid encodings; and
- inspect named sign, exponent, significand, classification, and payload facts
  without requiring raw bit arithmetic.

For a padded unusual format, extracting `W` logical bits must not expose the
storage-envelope padding. Reading all object bytes through a pointer remains a
different low-level operation with target-layout and unspecified-content cost.

NaN payload preservation, signaling behavior, and canonicalization must be
defined before "bits of float" can promise a reversible round trip. If ordinary
loads or arithmetic canonicalize, tooling should say where that can occur.

### Structural compatibility reconstruction

#### Fixed-point safe properties

The candidate complete safe scalar-format comparison is:

- coefficient logical width;
- signedness;
- coefficient value encoding, including two's-complement signed behavior;
- fixed fractional-bit count/position;
- semantic endian role;
- logical-bit placement;
- storage extent and required alignment;
- non-value storage location and behavior; and
- normalization/valid-pattern rules.

For the integer-backed candidate, all coefficient bit patterns are valid and
normalization adds no extra state. Public or programmer identity remains
outside shape.

#### Fixed/integer coercive properties

The current provisional rule is close but "sufficient compatible target storage
envelope" is too loose for a same-storage view. The candidate verified minimum
is:

- equal coefficient logical width;
- equal storage extent for the selected region;
- compatible source placement for the destination's required alignment;
- matching placement of logical bits within that extent; and
- padding/validity rules that do not let destination value behavior consume
  unspecified source padding.

Local unsafe coercion may then reinterpret signedness, `F`, and endian meaning.
Because integer-backed fixed formats accept every coefficient pattern, this
still produces a valid destination representation. Qualification weakening
remains separately unsafe.

A larger destination envelope cannot safely be viewed over a smaller source
merely because it is "sufficient"; that would overread. A smaller destination
would leave source bytes outside the leaf and break aggregate correspondence.
By-value numeric widening is conversion, not coercive layout.

#### Floating safe properties

Safe floating compatibility should require equality of:

- total logical format width;
- sign encoding;
- exponent width, encoding, bias, and class-code allocation;
- stored significand width and interpretation;
- explicit-versus-implicit integer-bit mode;
- normalization and subnormal rules;
- zero and signed-zero rules;
- infinity rules;
- NaN classes, payload interpretation, and canonicalization rules;
- invalid/noncanonical pattern rules;
- semantic endian role and logical field placement;
- storage extent, required alignment, and non-value-bit rules; and
- any other property that changes which value or class a logical bit pattern
  represents.

Hardware support and operation cost do not participate. Two environments can
implement the same format differently without changing scalar compatibility.

#### Floating coercion remains unresolved

Three coherent boundaries are visible:

1. **Complete-format coercion:** require every safe format property and only
   weaken qualification or cross a controlled identity boundary. This appears
   redundant with safe compatibility because identity is already outside shape.
2. **Partition-preserving coercion:** require total width, sign/exponent/
   significand partition, integer-bit mode, field placement, storage, and
   destination validity; permit local unsafe reinterpretation of endian or some
   special-value policy.
3. **Storage-only coercion:** require extent/alignment and destination validity,
   permitting a different exponent partition. This is a checked-layout form of
   arbitrary reinterpretation and approaches view-shaped `unsafe cast`.

The maintainer notes reject option 3 by saying different exponent partitioning
remains incompatible even under coercion. They also list endianness and every
special-value/normalization rule among properties floating coercion must
finalize, which points toward option 1. But structural design requires a
meaningfully reduced coercive property list. Option 2 is the apparent middle
ground, but exactly which special or endian distinctions it may ignore is not
yet evidenced.

This is not a wording detail. Consider the same eight bits interpreted as two
different E4M3 variants: one destination may see a finite number where the
other sees NaN or an invalid pattern. If coercion accepts that, it needs a
destination-validity contract and explicit programmer responsibility. If it
rejects it, programmers needing the reinterpretation still have view-shaped
`unsafe cast`.

#### Integer-list recheck

The current integer safe list remains defensible if:

- `zero fractional-bit position` means the intrinsic `F = 0` specialization;
- endianness role is resolved consistently with the separate endian semantic
  enum model; and
- storage comparison includes logical-bit placement and the inability of
  destination behavior to consume source padding.

The current coercive list should likely replace "sufficient target storage
envelope" with the more exact same-region extent/alignment/bit-placement rules
above. Equal logical width alone is not enough if two scalar families place
those bits differently inside equal-sized storage.

No integer owner edit is authorized or implied by this candidate recheck.

### Type existence, support, and execution context

The notes leave two incompatible readings:

- every concrete specialization is a valid type identity even when unsupported;
  or
- a provider marks some definitions "not supported," potentially making the
  types unavailable.

A useful candidate model separates four states:

1. The language version permits formation of the exact format identity.
2. The selected environment can represent values of that type in Zax storage
   and calling contexts.
3. A particular protected operation is available, through hardware or
   automatically supplied software.
4. The operation has a particular acceleration/cost classification.

This permits tooling to discuss an exact format without claiming every program
can use it. It also avoids one `Supported` Boolean pretending that load/store,
addition, division, conversion, classification, and compile-time evaluation
have one capability.

The unresolved language-level question is what minimum support a complete Zax
toolchain owes. Current integers require every exact width through the language
maximum. The floating notes instead say no hardware or software floating format
is required. If that remains true:

- source must be able to test capability before demanding an optional
  specialization or operation;
- a statically discarded unsupported branch must not fail merely because it
  names the type or operation;
- the diagnostic must distinguish language-invalid format, environment-
  unsupported storage, unavailable operation, and unavailable acceleration;
- protected-domain reservation must not imply an operation exists for every
  specialization; and
- portable APIs cannot promise a profile-selected floating result without a
  minimum profile contract.

These consequences cross into generics and compile-time execution, whose exact
mechanisms remain deferred. This work still needs enough of an availability
contract to keep the scalar model usable before those mechanisms exist.

`Target` and `CompilerHost` should identify explicit environment facts.
Unqualified active-environment roles can follow current integer behavior, but a
body evaluated under both environments may require distinct specializations.
Compile-time execution on the compiler host must not accidentally use host
rounding, host NaN behavior, or host precision when evaluating a target-format
operation.

### Minimal useful operation surface

The complete operator catalog can remain deferred, but a scalar family is not
usable without a bounded ordinary surface.

The initial candidate surface to review is:

- same-identity construction, default zero, copy/move, assignment, and swap;
- classification and equality/ordering behavior;
- unary sign and magnitude where meaningful;
- same-identity `+`, `-`, `*`, and `/` with explicit representability and
  zero-divisor contracts;
- exact and optional conversion among integer, fixed, and floating formats;
- explicitly rounded conversion and arithmetic where exact representation can
  fail;
- finite/minimum/maximum/quantum or precision/range queries;
- logical representation extraction/adoption; and
- support/cost queries.

For floating formats with NaN, equality and ordering must state their NaN
behavior. For formats with signed zero, equality, ordering, minimum/maximum,
sign operations, and raw representation must state when the sign is preserved.
For formats with infinities or exception flags, division by zero and overflow
cannot inherit integer panic behavior without review.

Remainder, fused operations, transcendental functions, exception flags,
environmental rounding modes, atomics, bitwise operators, increment/decrement,
compound policies, reports, and exhaustive protected forms can be decided by
later operator work once this scalar contract constrains them.

### Concrete review examples

These examples retain accepted surrounding Zax declaration and conversion
forms. Generic and rounded-operation fragments are marked illustrative.

#### Fixed scale is part of meaning

```zax
// MySigned16F8 is the illustrative concrete specialization above.
myAmount : MySigned16F8 = 1.5
// coefficient 384, quantum 1/256

myRawSized : I16 = 384

myRawSized = myAmount
// error: equal coefficient storage does not grant numeric conversion

myWhole := myAmount as I16?
// absent: 1.5 is not an integer
```

#### Scale-changing conversion can fail without overflow

```zax
myFine : MySigned16F8 = 1.5

// Illustrative destination: same coefficient width, F = 0.
myCoarse := myFine as I16?
// absent because low coefficient bits would be discarded

myIntegralFine : MySigned16F8 = 2.0
myIntegral := myIntegralFine as I16
// succeeds with value 2
```

#### Multiplication needs a result policy

```zax
mySmallest : MySigned16F8 = 0.00390625 // 1/256

myProduct := mySmallest * mySmallest
// Decision required: exact result is 1/65536 and does not fit the F=8 grid.
// A same-type required result must fail/report/round; a widened result changes
// the expression type and scale.
```

#### Decimal source exposes the rounding decision

```zax
myExact : Binary32 = 1.5

myRounded : Binary32 = 0.1
// Decision required: defined compile-time rounding, or an exactness error.
// It must not depend on compiler-host floating behavior.
```

#### Numeric value and representation are different

```zax
myValue : Binary32 = -0.0

myInteger := myValue as I32
// Candidate numeric result: 0, if exact numeric conversion permits loss of
// zero sign.

// Illustrative future operation; exact spelling is not established.
myBits := representation bits of myValue
// Preserves the Binary32 sign bit instead of numerically converting to I32.
```

#### Equal size is not safe scalar compatibility

```zax
FloatCell :: type {
  value : Binary32
}

IntegerCell :: type {
  value : U32
}

myFloatCell : FloatCell

myIntegerView :=
  myFloatCell as layout IntegerCell &
// error: Binary32 and U32 have different scalar formats

myUncheckedView :=
  myFloatCell unsafe cast IntegerCell &
// unchecked raw view; numeric conversion and coercive compatibility are
// separate questions
```

#### Environment support is not format meaning

```zax
myTargetValue : Binary32 = 1.5
// Binary32 means the same format on every target.
// A target lacking required Binary32 construction/arithmetic support produces
// an availability diagnostic; it does not redefine Binary32 without NaNs or
// with another exponent encoding.
```

### Known holes requiring expansion

- The exact generic relationship between `Integer$(..., F = 0)` and
  `Integer$(..., F > 0)` remains illustrative.
- Fixed-point arithmetic result identities and explicit rounding/report forms
  are not settled.
- Unprefixed real token grammar, exponent syntax, source precision limits, and
  literal rounding are not settled.
- The complete custom binary-format parameterization has not chosen one
  composable encoding-policy model.
- Required NaN payload, signaling, invalid-pattern, and canonicalization
  behavior is not settled.
- Ordinary floating comparison, arithmetic failure, exception, and rounding
  semantics are not settled.
- Endianness needs one numeric-family-wide model consistent with current endian
  semantic enums.
- Exact mandatory toolchain support and static capability selection are not
  settled.
- Floating profile selectors lack a requirement ordering stronger than total
  width.
- Coercive floating compatibility lacks a meaningful reduced property list.
- Defined raw representation operations and reversible round-trip conditions
  are not settled.
- A language maximum for exponent/significand/storage dimensions, if any, is
  not settled.

### Captured consequences and deliberate deferrals

These consequences are material but need not be designed completely in this
work item:

- **Generics and type factories:** the live
  [type-parameters and generics input](../raw/type-parameters-and-generics.md)
  owns concrete parameter declaration, validation, type-result syntax,
  generated aliases, associated product/result types, and profile selector
  mechanisms. This scalar work must first provide the exact format dimensions
  and selector requirements those mechanisms preserve.
- **Literal grammar:** the live
  [literal maintainer notes](../raw/literals-maintainer-notes.md) and
  [literal-operator input](../raw/literal-operators.md) own tokenization,
  prefixes, suffixes, payload parsing, and custom literal declarations. This
  work must contribute the uncommitted-real representability and rounding
  contract. A prefixed literal still returns one concrete type.
- **Operators:** the [operator catalog](../../language/operator-catalog.md)
  already reserves floating and fixed-point intrinsic domains. Later operator
  work owns the exhaustive surface and exact new policy spellings; this work
  must settle the minimal arithmetic, conversion, classification, failure, and
  rounding semantics first.
- **CPU providers:** the live
  [CPU-provider input](../raw/cpu-provider-model.md) owns profile format,
  versions, environment selection, fallback, per-operation capability, and
  cost metadata. It must consume the format-versus-support separation and
  floating selector requirements.
- **Reflection:** the live [reflection input](../raw/reflection.md) owns exact
  metadata APIs. It must eventually expose scalar format separately from
  storage, public identity, environment support, and operation cost without
  making illustrative query wording current syntax.
- **Compile-time execution:** the live
  [compile-time input](../raw/compile-time-execution.md) owns host execution,
  target-context evaluation, static branch behavior, and availability
  diagnostics. It must preserve selected target-format semantics independent of
  host hardware.
- **Interop and persistent representation:** the live
  [interop input](../raw/interop.md) owns ABI, calling convention, foreign
  format correspondence, and adapters. Equal Zax format/storage is not an ABI
  promise. MBF and ABI-padded x87 representations can remain evidence there or
  in a future custom-numeric-format input.
- **Decimal floating, arbitrary precision, and vectors:** no current conclusion
  requires these families. Reopen them when a use case requires decimal
  rounding, unbounded precision, lane packing, or SIMD operations. Binary
  scalar names must not preclude them.
- **Serialization/wire contracts:** absolute byte formats and raw byte
  extraction matter now, but stable protocol promises require a later named
  contract. Ordinary target layout must not become one accidentally.

None of these deferrals may be left only in the archived work item. Before
closure, each surviving item must remain in the named live input or move to an
indexed destination if the current file cannot own it.

### Likely lasting owner boundaries

A cohesive programmer-facing scalar owner could teach fixed-point and binary
floating values together through the shared distinctions among numeric meaning,
format, storage, support, conversion, and raw access. If the material becomes
too large for one reader task, fixed-point and binary floating can become two
concept owners with a small shared scalar-format owner; length alone is not a
reason to split them.

Promotion would also need coordinated changes to:

- [integers](../../language/integers.md) for the `F = 0` family relationship,
  fixed/integer conversion boundary, and final compatibility recheck;
- [structural shapes and compatibility](../../language/structural-shapes-and-compatibility.md)
  for final safe and coercive scalar property lists;
- [endianness](../../language/endianness.md) if its integer-semantic-enum model
  becomes the numeric-wide endian mechanism;
- literal and operator owners for only the behavior actually settled here;
- reflection, provider, generic, compile-time, and interop raw inputs for
  preserved future obligations;
- the legacy floating section in `basics.md`, whose `Float`, `Half`, `Single`,
  `Double`, `Quadruple`, `F16` through `F128`, and width-only generic currently
  preserve older and partly conflicting selection ideas;
- the primary floating maintainer notes and applicable sections of the raw
  numeric-family input once every useful item has been promoted, deferred,
  rejected, or superseded; and
- routers and terminology surfaces identified by the later documentation-fit
  dry run.

This is an ownership hypothesis for review, not authorization to create,
rewrite, move, or retire any owner.

## Dispositions and promotion dry run

### Result: PASS

The pre-promotion documentation-fit dry run passes.

Every aligned finding has one lasting semantic owner, the two new reader paths
are cohesive, the cross-cutting endian and compatibility changes fit existing
owners, all surviving future mechanics have live raw destinations, and the
promotion can be expressed as one exact repository-wide change set without
creating duplicate authority or depending on archived work.

PASS does not authorize promotion, staging, committing, archival, raw-input
retirement, or work item `023`.

### Proposed lasting teaching structure

Add two flat `language/` concept owners:

- `language/fixed-point-scalars.md`
- `language/floating-point-scalars.md`

Do not add a shared scalar umbrella document or another directory/index layer.
The existing public `index.md` remains the router. Existing
`language/endianness.md` owns the cross-family endian model, and
`language/structural-shapes-and-compatibility.md` owns how scalar leaf
relationships participate in aggregate shape/layout conversion.

#### Fixed-point teaching path

`language/fixed-point-scalars.md` should teach in this order:

1. Start with a concrete `I16F8` value and show coefficient, quantum, range,
   and an ordinary calculation.
2. Establish `W`, bounded `F`, `F = 0`, signedness, and canonical names.
3. Introduce Q aliases only after the unambiguous Zax names are understood.
4. Explain same-identity arithmetic, ties-to-even reduction, overflow,
   rounding to zero, and `full precision product`.
5. Teach exact, optional, and rounded scale conversion.
6. Explain coefficient bitwise operations, storage, and endian costs.
7. Finish with representation/coercion, diagnostics, portability, and reference
   details.

This lets a cold reader use fixed-point arithmetic without first learning
floating exponent classes, NaNs, hardware support catalogs, or legacy formats.

#### Floating-point teaching path

`language/floating-point-scalars.md` should teach in this order:

1. Start with `Float` versus exact `Binary32`, including a decimal value such
   as `0.1` and its defined rounding.
2. Separate exact format meaning from environment support and cost.
3. Explain sign, exponent, fraction, significand, range, precision, zero,
   subnormal, infinity, and NaN behavior.
4. Present exact IEEE/OFP8 names and profile-selected `Float`, `FastF<N>`,
   `LeastF<N>`, and maximum roles.
5. Teach ordinary operations, comparison/classification, conversion, failures,
   and representation access.
6. Explain storage, endian identity, support reflection, and portability.
7. Introduce coercive unsigned-integer representation views only after format
   fields are understood.
8. Put `X87Extended80` and `MBF40` in a later legacy section with their
   different operation contracts and validity rules.
9. Finish with diagnostics, costs, source stability, `LanguageLimits`, and
   reference details.

Legacy discovery chronology, rejected domain aliases, and the initial
width-only-format hypothesis do not enter promoted teaching.

### Ownership map

- `language/fixed-point-scalars.md` owns fixed-point mental model, `W`/`F`
  identity, bounds, ranges, canonical and Q aliases, initialization, literal
  realization, arithmetic, full-precision product, bitwise behavior,
  conversions, local representation facts, costs, diagnostics, and
  portability.
- `language/floating-point-scalars.md` owns ordinary and legacy floating format
  semantics, exact/profile-selected names, special values, literal realization,
  operations, conversion, representation adoption, validity/canonicalization,
  support bundle, support classification facts, `LanguageLimits`, costs,
  diagnostics, and portability.
- `language/integers.md` owns the intrinsic integer family's `F = 0` branch,
  concrete-endian integer identities, active/target/compiler-host aliases, and
  the final integer safe/coercive property list.
- `language/endianness.md` owns concrete endian specialization across integers,
  fixed-point, and floating point; absolute and environment-selected names;
  numeric endian conversion; raw byte-preserving reinterpretation; operation
  costs; and the replacement of semantic endian enums.
- `language/structural-shapes-and-compatibility.md` owns coercion's checked-lens
  model, aggregate application of scalar leaf relations, directional
  compatibility, anchors, same-storage views, and the boundary with
  `unsafe cast`. Each scalar owner supplies its format-specific leaf facts.
- `language/qualifiers.md` owns the rule that coercion never grants access
  authority, while preserving existing mutable/writable/varying authority and
  identifying the unsafe restoration obligation after invariant-breaking
  writes.
- `language/enums.md` retains ordinary enum semantics and removes endian enums
  as an example or generated family.
- `language/operators.md` owns protected-domain routing and shared selection;
  it routes fixed and floating results to the two new owners.
- `language/operator-catalog.md` owns the exact `full precision product` phrase,
  updates endian forms to intrinsic scalar conversion, and routes other aligned
  numeric concepts whose exact future words remain unsettled.
- `language/integer-operator-catalog.md` owns the existing integer operation
  surface as applied to concrete-endian `F = 0` specializations and records that
  mixed-endian operands require explicit conversion.
- `language/integer-literals.md` continues to own uncommitted integers and hands
  uncommitted real source to the new scalar owners rather than leaving it as
  unspecified future numeric intent.
- `language/terms.md` owns revised active/native-endian and native/software
  vocabulary where the accepted scalar model uses it cross-cuttingly.
- `project/raw/type-parameters-and-generics.md` remains the live owner for exact
  generic declarations, type-returning selectors, associated full-product
  types, specialization validation, and the eventual numeric language-limit
  values.
- `project/raw/cpu-provider-model.md` remains the live owner for provider file
  shape, profile versioning, support-enum realization, runtime hardware
  variability, operation availability/cost metadata, and X87 provider behavior.
- `project/raw/reflection.md` remains the live owner for exact metadata source
  forms exposing scalar format, support, operation availability,
  `LanguageLimits`, aliases, validity, and canonicalization facts.
- `project/raw/compile-time-execution.md` remains the live owner for
  target-context real realization, host/target semantic separation,
  capability-dependent static branches, and reproducible compile-time
  evaluation.
- `project/raw/literals-maintainer-notes.md` and
  `project/raw/literal-operators.md` remain live for real token grammar,
  exponent/radix/suffix spelling, custom/prefixed literals, and exact-source
  realization syntax after their scalar semantics move to the new owners.
- `project/raw/interop.md` remains the live owner for ABI/calling-convention
  mappings, X87 ABI padding, MBF file exchange, foreign format correspondence,
  and stable serialization/wire contracts.
- `project/raw/numeric-type-families.md` remains live for custom/unbounded
  numeric families, multiword and representation-operation syntax, and other
  numeric mechanics not settled here; its fixed/floating semantic sections no
  longer compete with the new owners.
- `project/raw/feature-catalog.md` remains a legacy router and must route current
  fixed, floating, and endian concepts to their promoted owners.

### Deferred and future-contract dispositions

- Exact generic/factory syntax and the numeric value of
  `Scalars.Floating.LanguageLimits.maximumWidth` remain explicitly deferred to
  type-parameter/generic work. The current scalar model requires a
  language-version compile-time value but does not invent an unsupported number.
- Exact real tokenization and exact-source syntax remain literal work.
- Exact spellings for classification, adjacent values, total ordering,
  rounded conversion, representation adoption, and canonicalization remain
  operator/reflection work. Promoted examples label any illustrative wording.
- Per-operation provider schema, runtime dispatch, instruction selection, and
  software algorithms remain CPU-provider or implementation work.
- MBF40 compatibility vectors derive from Microsoft BASIC for 6502 Version 1.1
  with `ADDPRC=1`; they are future formal-contract/implementation evidence, not
  another conceptual arithmetic model.
- X87 CPU-specific operation results remain versioned provider behavior.
- Decimal floating, arbitrary precision, SIMD/vectors, ABI/FFI, and stable wire
  formats remain with their named live destinations.
- A freely combinable explicit-integer-bit generic remains deferred until a
  format beyond closed `X87Extended80` creates evidence for it.

### Exact promotion change set

#### Add

- `language/fixed-point-scalars.md`
- `language/floating-point-scalars.md`

#### Modify current and public documentation

- `language/integers.md` — make endianness intrinsic, identify `F = 0`, update
  namespaces, conversion, compatibility, costs, diagnostics, and portability.
- `language/endianness.md` — reconstruct the owner around intrinsic scalar
  endianness and retire the semantic-enum model.
- `language/structural-shapes-and-compatibility.md` — install the final
  scalar-owner handoffs, checked-lens coercion model, directional relations, and
  same-region storage rules.
- `language/qualifiers.md` — distinguish qualification-preserving coercion from
  qualification-weakening `unsafe cast`/`unsafe pliable`.
- `language/enums.md` — remove endian-semantic-enum behavior and stale links.
- `language/operators.md` — route protected fixed/floating domains and remove
  their future-work labels.
- `language/operator-catalog.md` — reserve/catalog `full precision product`,
  replace endian-enum forms, route the new owners, and narrow deferred items.
- `language/integer-operator-catalog.md` — apply existing operations to
  concrete-endian integer specializations and state the mixed-endian boundary.
- `language/integer-literals.md` — route uncommitted real behavior and update
  exact-width/endian portability wording.
- `language/terms.md` — update native endianness, native representation,
  software emulation, and scalar-format terminology/handoffs.
- `language/vision.md` — add the two new accepted concept owners and revised
  endian/structural routes to current maturity.
- `index.md` — add direct public routes to both scalar owners and replace the
  endian-semantic-enum description.
- `basics.md` — consume and remove the legacy `Float`/`F16`/width-only generic
  block, replacing it with current-owner routes while retaining unrelated
  legacy string input.
- `casting.md` — route fixed/floating numeric conversion and representation
  behavior to current owners while retaining unrelated legacy string/general
  casting evidence.

#### Modify live raw input and routing

- `project/raw/README.md` — update provider, generic, reflection, literal,
  interop, numeric-family, and floating-note dispositions.
- `project/raw/numeric-type-families.md` — remove competing fixed/floating
  semantics, retain unresolved custom/unbounded/operator mechanics, and route
  current behavior.
- `project/raw/literals-maintainer-notes.md` — consume its fixed/floating
  semantic examples, preserve unresolved literal grammar, and relocate the
  staged generic numeric-size probing concern to its generic/compile-time owner.
- `project/raw/literal-operators.md` — add the remaining ordinary-real
  token/suffix/exact-source boundary without reopening promoted scalar
  semantics.
- `project/raw/cpu-provider-model.md` — broaden the preserved provider pressure
  to fixed/floating selection, support classes, operation reflection/cost, and
  provider-relative X87 behavior.
- `project/raw/type-parameters-and-generics.md` — replace generated endian-enum
  pressure with intrinsic endian specialization and retain fixed/floating
  generic, full-product, selector, and limit mechanics.
- `project/raw/reflection.md` — replace endian eligibility with intrinsic
  endian/format facts and add fixed/floating/support/validity metadata pressure.
- `project/raw/compile-time-execution.md` — preserve target-format realization,
  capability-dependent static selection, and host-independent rounding.
- `project/raw/interop.md` — preserve X87 ABI-padding and MBF/file-format
  boundaries without turning Zax layout into an ABI promise.
- `project/raw/feature-catalog.md` — route current fixed, floating, and endian
  design to the promoted owners.

#### Retain until closure

- `project/raw/floating-point-maintainer-notes.md` remains unchanged through
  promotion because the immutable fixed initiating input links to it while
  `022` is active. All useful content will then be promoted or moved; closure
  may delete it and remove its raw-index entry after `022` becomes historical.
- `project/work/022-floating-and-fixed-point-scalars.md` remains the active
  non-authoritative record and receives only promotion validation/disposition
  results. Archival is a later separately authorized closure operation.
- `project/README.md` retains the `022` active pointer and next number `023`
  until closure.

No archive, implementation repository, formal specification area, generic
folder hierarchy, shared scalar umbrella, or compatibility stub is added.

### Validation required after promotion

- Both new owners pass the cold-reader and direct-entry checks described above.
- Every unmarked example uses established surrounding syntax; unsettled
  operation wording is explicitly illustrative.
- Fixed and floating behavior have one semantic owner each; endianness,
  structural coercion, and qualifications do not duplicate those definitions.
- Searches find no live claim that fixed-point is a binary floating variation,
  no current endian semantic enum, no stale `Quadruple`/`F16` legacy catalog,
  and no "pending floating review" route.
- Current owners do not cite numbered work, raw input, or archived material.
- All raw concerns have the dispositions recorded above, including the staged
  generic numeric-size concern.
- Local Markdown links and heading anchors resolve across the live tree.
- Website and direct repository navigation both reach the two new owners.
- Authority remains conceptual rather than formal, implemented, conforming, or
  guaranteed.
- The changed-file set exactly matches this dry-run change set.
- The staged and unstaged review boundary is preserved until the maintainer
  explicitly changes it.

### Promotion result

Promotion was authorized and applied on 2026-09-15.

- Added separate human-facing fixed-point and binary floating-point owners.
- Reconstructed the existing endian owner around intrinsic scalar endianness.
- Integrated integer, structural, qualifier, enum, operator, literal,
  terminology, vision, website-router, and legacy-page consequences.
- Updated every live raw destination named by the dry run while retaining the
  floating maintainer notes until closure because the immutable initiating input
  still links to them.
- Preserved the staged review boundary; the promotion remains entirely in the
  unstaged layer, including the two new untracked owner files.

Promotion validation passed:

- `git diff --check` and the staged diff check report no whitespace errors.
- Local file and heading-anchor links resolve in all 27 changed Markdown files.
- Markdown code fences balance in all changed files.
- Focused searches find no current endian semantic enum, generated endian
  family, stale fixed/floating future route, old `F16`/`Quadruple` catalog, or
  qualification-weakening coercion statement in `language/`.
- Current language owners contain no link to numbered work, raw input, or
  archived material.
- The changed-file set matches the exact PASS change set: two added owners,
  fourteen modified current/public files, ten modified raw/project files, and
  this active record.

The promotion does not archive `022`, retire its still-linked primary raw input,
create `023`, stage changes, or authorize a commit.

### Post-promotion review correction dry run

#### Result: PASS

Maintainer review exposed a bounded correction set without reopening the scalar
model or owner structure. The existing owners can absorb every correction:

- family-root paths become the canonical current-execution-environment routes;
- coercion gains safe and unsafe local reference forms selected by validity and
  writable-invariant risk;
- coercion teaching leads with the useful-lens purpose before the precise
  zero-translation rule;
- endian examples distinguish conditional active-identity matches from
  guaranteed absolute mismatches;
- exact/optional/rounded conversion receives an explicit no-panic/absence/
  destination-policy boundary;
- fixed-point contextual integers, representation masks, and remainder are
  defined;
- decimal `e`/`E` exponent source is established;
- payload-dependent literal-result type pressure is restored to its literal and
  generic destinations; and
- a repository-wide `Does Not Own` metadata audit is captured in the indexed
  cross-cutting audit queue under the existing documentation rule.

The correction requires no new owner, directory, router, raw placeholder, or
archive change. Its exact promotion set is:

- `language/integers.md`
- `language/structural-shapes-and-compatibility.md`
- `language/qualifiers.md`
- `language/endianness.md`
- `language/fixed-point-scalars.md`
- `language/floating-point-scalars.md`
- `language/integer-literals.md`
- `language/operator-catalog.md`
- `language/operators.md`
- `language/terms.md`
- `casting.md`
- `project/raw/literals-maintainer-notes.md`
- `project/raw/literal-operators.md`
- `project/raw/type-parameters-and-generics.md`
- `project/raw/compile-time-execution.md`
- `project/raw/cross-cutting-audit.md`
- this active work record

The supplemental dry run therefore passes. Authorization in the maintainer's
2026-09-15 review permits this correction promotion, but does not authorize
staging, archival, creation of `023`, or commit.

#### Correction promotion result

The authorized correction was applied across the exact supplemental set.

- Family-root canonical current-environment paths now coexist with explicit
  target, compiler-host, little-endian, and big-endian paths.
- Safe versus unsafe coercion follows destination validity and writable source
  invariants; every coercion is a local reference view and neither form grants
  qualification authority.
- Endian and numeric examples now use reference-shaped coercion and distinguish
  guaranteed mismatches from active-environment identity matches.
- Exact conversion is non-panicking by availability, optional conversion
  reports absence, and rounded conversion follows destination overflow policy.
- Fixed-point bitwise literals, same-identity remainder, and decimal exponent
  source are documented in their owners.
- Payload-dependent literal-result type pressure is preserved in literal,
  generic, and compile-time raw inputs.
- The later cross-owner `Does Not Own` metadata sweep is indexed in
  `project/raw/cross-cutting-audit.md`.

Validation passed: both staged and unstaged diffs have no whitespace errors;
local files, heading anchors, and code fences validate across all 17 correction
files; focused searches find no stale always-unsafe coercion claim, source-like
`Native` spelling, or conflicting namespace route; and the staged promotion
baseline remains unchanged beneath the unstaged correction.

### Closure

Work item `022` is complete. Its fixed-point, binary floating-point,
endianness, representation, support, conversion, literal, operation, and
compatibility findings were promoted to their current owners and integrated
across affected public, current, legacy, terminology, and raw surfaces.

The documentation-fit dry run and post-promotion correction dry run both passed.
The maintainer reviewed and aligned the promoted result. The consumed primary
floating-point maintainer input retires with this closure, while every surviving
generic, literal, provider, reflection, compile-time, interop, and documentation
audit consequence remains in an indexed live destination.

Work item `023` continues with literal source and literal operators. This record
is retained only for targeted historical audit.
