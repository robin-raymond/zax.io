# Zax fixed-point scalars

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers choosing, storing, converting, or calculating with fixed-point values |
| Applies To | Finite binary-scaled fixed-point specializations of Zax integers; not a formal grammar or specification |
| Implementation State | Not established by this repository |
| Owns | Fixed-point mental model; coefficient width and bounded fractional-bit position; exact and Q names; range, quantum, initialization, literals, type-owned mathematical constants, arithmetic, full-precision product, bitwise behavior, numeric conversion, local representation relationships, costs, diagnostics, and portability |
| Does Not Own | The `F = 0` integer surface ([integers](integers.md)); cross-family endian semantics ([endianness](endianness.md)); aggregate compatibility and coercive conversion syntax ([structural shapes and compatibility](structural-shapes-and-compatibility.md)); exact generic/factory syntax; complete operator forms; or real-literal source and raw-pattern grammar ([literal source and operators](literal-source-and-operators.md)) |
| Source / Provenance | Legacy numeric input in [basics](../basics.md), refined against current integer, endian, structural, literal, and operator design |

## Start with a coefficient and a fixed scale

A fixed-point value stores an integer coefficient while its type fixes where the
binary point belongs:

```zax
myTemperature : I16F8 = 21.5
```

`I16F8` has:

- a signed 16-bit coefficient;
- eight fractional bits;
- a quantum of `1 / 256`; and
- the represented value `coefficient * 2^-8`.

`myTemperature` therefore stores coefficient `5504`:

```text
5504 / 256 = 21.5
```

The coefficient and fractional-bit count are both fixed by the concrete type.
No value carries a runtime scale field.

Fixed-point and binary floating-point values are separate scalar families. A
fixed-point value has no moving exponent, NaN, infinity, or subnormal class.
See [binary floating-point scalars](floating-point-scalars.md) for that model.

## Width, fractional bits, and range

Write:

- `W` for total coefficient width; and
- `F` for the number of low coefficient bits interpreted fractionally.

The represented value is:

```text
coefficient * 2^-F
```

The smallest step between adjacent values is the **quantum**:

```text
quantum = 2^-F
```

For an unsigned coefficient:

```text
minimum = 0
maximum = (2^W - 1) * 2^-F
```

For a signed two's-complement coefficient:

```text
minimum = -2^(W-1) * 2^-F
maximum = (2^(W-1) - 1) * 2^-F
```

For `I16F8`:

```text
coefficient range = -32768 through 32767
value range       = -128 through 127.99609375
quantum           = 0.00390625
```

`F` remains within the represented coefficient:

```text
signed:   0 <= F <= W - 1
unsigned: 0 <= F <= W
```

Zax does not currently use negative `F` or place the binary point beyond those
bounds. Those forms would create a more general scaled-integer family with much
larger scale factors rather than the bounded fixed-point model taught here.

Every coefficient bit pattern is valid. Signed coefficients retain the
two's-complement representation defined by [Zax integers](integers.md).

## Exact names

Zax names an exact fixed-point specialization by total coefficient width and
fractional-bit count:

```text
I<W>F<FractionBits>
U<W>F<FractionBits>
```

Examples:

| Signed | Unsigned | Coefficient width | Fractional bits |
| --- | --- | --- | --- |
| `I16F8` | `U16F8` | 16 | 8 |
| `I32F16` | `U32F16` | 32 | 16 |
| `I32F8` | `U32F8` | 32 | 8 |
| `I64F32` | `U64F32` | 64 | 32 |
| `I16F14` | `U16F14` | 16 | 14 |
| `I16F12` | `U16F12` | 16 | 12 |

The family root is the canonical current-execution-environment catalog:

```zax
Scalars.Fixed.I16F8
Scalars.Fixed.U32F16
```

Short names are transparent aliases of those public declarations. Explicit
environment paths include `Scalars.Fixed.Target.I16F8` and
`Scalars.Fixed.CompilerHost.I16F8`. The later
[endianness section](#endianness-and-storage) gives the absolute canonical
little/big paths that complete concrete identity.

### Zero fractional bits are ordinary integers

`F = 0` is exactly the corresponding integer specialization:

```text
I16F0 = I16
U16F0 = U16
```

This is one intrinsic family relationship, not a conversion between two
coincidentally represented types. The integer owner defines the complete `F = 0`
surface.

### Q aliases

Zax also provides transparent Q aliases for familiar source. Its convention is
explicit: the integer count excludes the sign bit.

```text
QI<i>F<f> => signed width   = 1 + i + f
QU<i>F<f> => unsigned width = i + f
```

The initial aliases are:

| Zax exact names | Q aliases |
| --- | --- |
| `I16F8` / `U16F8` | `QI7F8` / `QU8F8` |
| `I32F16` / `U32F16` | `QI15F16` / `QU16F16` |
| `I32F8` / `U32F8` | `QI23F8` / `QU24F8` |
| `I64F32` / `U64F32` | `QI31F32` / `QU32F32` |
| `I16F14` / `U16F14` | `QI1F14` / `QU2F14` |
| `I16F12` / `U16F12` | `QI3F12` / `QU4F12` |

For example, `QI7F8` and `I16F8` are two names for one type. Q conventions
outside Zax sometimes count the sign differently; the `I16F8` form remains the
unambiguous Zax-native spelling.

Zax does not provide domain names such as `AudioFixed32` or `ControlFixed16`.
Audio, control, simulation, and inference systems use several incompatible
range/precision choices. A domain library or application can define a meaningful
identity after validating its own requirements.

## Endianness and storage

Endianness is part of the concrete intrinsic specialization:

```zax
Scalars.Fixed.Little.I16F8
Scalars.Fixed.Big.I16F8
```

An unqualified `I16F8` selects the active execution environment's concrete
endianness. Explicit `Target.I16F8` and `CompilerHost.I16F8` paths select their
named environments.

Numeric behavior always follows the declared type. Operating on a value whose
endianness differs from the CPU may require byte swaps, endian-aware
instructions, or software support. Complete cross-family behavior is defined by
[Zax endianness](endianness.md).

Storage uses the integer coefficient's byte envelope. When `W` is not a multiple
of eight:

- the used part of the highest participating byte is its low-order portion;
- unused high bits are non-value bits;
- their contents are unspecified; and
- they cannot affect defined fixed-point behavior.

Required alignment remains a target layout fact. Container padding remains
separate from non-value bits inside the scalar's own envelope.

## Initialization and real-number source

Default initialization produces coefficient zero and numeric zero. Its logical
representation is all zero.

A typed real number is realized directly in the selected fixed-point type:

```zax
myExact : I16F8 = 1.5
myRounded : I16F8 = 0.1
```

`1.5` is represented exactly. Decimal `0.1` is not on the `I16F8` grid, so
ordinary realization rounds it to the nearest representable value, with ties to
even. Ordinary precision loss is not a diagnostic.

A sufficiently small nonzero source may round to zero. A finite source whose
magnitude exceeds the destination range is a compile-time error:

```zax
myTooLarge : I16F8 = 128.0 // error: outside the I16F8 range
```

The compiler computes the destination result independently of compiler-host
floating arithmetic.

Decimal real source accepts `e` or `E` followed by an optionally signed decimal
exponent:

```zax
myLarge : I32F8 = 1.5e+4
mySmall : I16F14 = 1E-3
```

The exponent is a power of ten applied to the exact mathematical source before
fixed-point realization. Its presence makes `1e3` real-number source even
without a fractional point. Bare `e3` is not a number literal.
Ordinary real source without another destination defaults to `Float`.
Uncommitted exact-real arithmetic calculates before the fixed-point commitment:

```zax
myResult : I16F8 = 0.1 + 0.2
// Exact rational 0.3 is realized once as I16F8.

myConcrete : I16F8 =
  (: I16F8 = 0.1) + 0.2
// The inner declaration commits before +, so concrete I16F8 arithmetic occurs.
```

Binary and hexadecimal payloads may use a decimal `p`/`P` exponent denoting a
power of two:

```zax
myBinary : I16F8 = b'1.1p-3'
myHex : I16F8 = h'1.8p+1'
h'1.8' // equivalent to h'1.8p0'
```

Exact token, default, prefix, and exponent behavior is owned by
[literal source and operators](literal-source-and-operators.md#ordinary-numeric-source).

An exact fixed-point type also supplies type-qualified raw logical-pattern
literals:

```zax
myRaw := I16F8.h'0180'
// Coefficient 384, represented value 1.5.
```

The payload supplies coefficient bits, not a mathematical hexadecimal real.
Logical digit order is independent of storage endianness. Complete raw-pattern
rules are defined by
[literal source and operators](literal-source-and-operators.md#type-qualified-raw-scalar-patterns).

A future exact-realization request remains separate from raw-pattern source.

### Type-owned mathematical constants

Every fixed-point type exposes an applicable constant only when the correctly
rounded mathematical value fits:

```zax
myPi := I16F8.pi
myRoot := I32F16.sqrt2
```

The shared catalog is `pi`, `tau`, `e`, `phi`, `sqrt2`, `sqrt3`, `ln2`,
`ln10`, `log2e`, `log10e`, `invPi`, `twoOverPi`, `invSqrtPi`, `halfPi`, and
`quarterPi`.

Each value is rounded directly from its mathematical definition rather than
derived from another already rounded constant. Fixed-point representation
limits remain `minimum`, `maximum`, and `quantum`; the family has no NaN,
infinity, signed zero, subnormal class, or floating epsilon.

Complete cross-family constant naming and source behavior is routed through
[literal source and operators](literal-source-and-operators.md#type-owned-scalar-constants).

## Arithmetic keeps one concrete type

Ordinary fixed-point arithmetic uses operands of the same exact identity. Zax
does not implicitly rescale independently typed operands:

```zax
myFine : I16F8 = 1.5
myCoarse : I16F4 = 2.0

myResult := myFine + myCoarse
// error: I16F8 and I16F4 are different fixed-point identities
```

Convert deliberately to the scale and range in which the operation should
occur.

### Addition and subtraction

Addition and subtraction apply to coefficients at the already shared scale.
Their mathematical result retains `W` and `F`.

Required arithmetic checks whether the exact result fits. Overflow follows the
applicable required, optional, wrapping, saturating, or reporting policy rather
than silently widening the type.

### Multiplication

Multiplying two coefficients doubles the apparent scale. An ordinary
same-type product rescales once and rounds back to the existing grid:

```text
resultCoefficient =
  roundToNearestEven((leftCoefficient * rightCoefficient) / 2^F)
```

For `I16F8`, `1.5` has coefficient `384`:

```text
384 * 384 / 256 = 576
576 / 256       = 2.25
```

An implementation may use a wider intermediate or a native fractional multiply
instruction. The observable result remains `I16F8`.

A tiny exact product may round to zero without panicking:

```zax
myQuantum : I16F8 = 0.00390625
myRoundedToZero := myQuantum * myQuantum
// I16F8 value 0: the exact product is below half of one I16F8 quantum
```

Reporting operations may expose that precision was discarded. Overflow is a
separate condition governed by the selected arithmetic policy.

### Full-precision product

Use `full precision product` when the complete coefficient product and doubled
fractional precision must remain available:

```zax
myLeft : I16F8 = 1.5
myRight : I16F8 = 2.25

myFull := myLeft full precision product myRight
// I32F16, exact value 3.375
```

For two like `W`, `F` operands, the associated result has sufficient coefficient
width for the full product and `2F` fractional bits, conceptually `2W`, `2F`.
Exact generic associated-type declaration syntax remains future work.

### Division

Ordinary division also retains the operand type:

```text
resultCoefficient =
  roundToNearestEven((leftCoefficient * 2^F) / rightCoefficient)
```

Required division by zero fails. A finite quotient not lying on the existing
grid rounds to nearest, ties to even. There is no universally exact finite
fixed-point quotient type: values such as one third require unbounded
fractional precision.

### Remainder

Remainder applies to one fixed-point identity and uses a quotient truncated
toward zero:

```zax
myValue : I16F8 = 10.5
myRemainder := myValue % 4
// The integer source becomes I16F8 value 4.0; result is I16F8 value 2.5.
```

Because both coefficients have the same `F`, coefficient remainder directly
produces the fixed-point remainder. A zero divisor fails. Bare uncommitted
`10.5 % 4` still depends on future default real-type selection because neither
operand has selected a concrete identity.

## Bitwise operations inspect the coefficient

Fixed-point complement, AND, OR, XOR, AND-NOT, shifts, rotations, counts, masks,
and positions operate on or inspect the finite coefficient representation.

This is a deliberate fixed-point surface. It does not imply that every
integer-specific numeric relationship transfers merely because fixed-point is
implemented by the intrinsic integer family.

Left shift has the numeric effect of multiplication by a power of two when the
result fits. Right shift may discard coefficient bits and therefore follows its
defined signed rounding behavior. Rotations are representation operations
rather than arithmetic scaling.

An integer source beside a concrete fixed-point operand realizes as that same
fixed-point identity:

```zax
myBits : U32F8 = 3.0
myResult := myBits ^ 1
// `1` becomes U32F8 value 1.0, coefficient 256.
```

The literal does not silently become a raw `U32F0` mask. To toggle the lowest
coefficient bit, use one quantum:

```zax
myLowestChanged := myBits ^ 0.00390625
// U32F8 quantum, coefficient 1
```

or use an explicit same-endian U32 representation view for mask-oriented work.

## Numeric conversion

Conversion preserves represented numeric value unless an explicit rounding or
modular policy says otherwise.

### Exact and optional conversion

Increasing `F` shifts the coefficient left and is exact when the destination
coefficient fits. Decreasing `F` is exact only when every discarded low bit is
zero.

```zax
myFraction : I16F8 = 1.5
myWhole := myFraction as I16?
// absent: 1.5 is not exactly representable as I16

myExact : I16F8 = 2.0
myInteger := myExact as I16
// exact value 2
```

Integer-to-fixed conversion is the reverse `F = 0` relationship and also checks
range after scaling.

Plain `as Destination` is available only when type facts or a
compile-time-known value prove exact representability. It introduces no runtime
panic. `as Destination?` reports any value-dependent range or fractional
nonrepresentability through absence.

### Rounded conversion

Rounded conversion selects a rounding rule and then checks the destination
range. Precision loss and a tiny nonzero value rounding to zero do not panic.
When the rounded result is outside a fixed/integer destination range, a required
form may panic, an optional form reports absence, and a saturating form chooses
the applicable endpoint.

Its exact source phrase remains operator work and examples using future wording
must mark it as illustrative.

Integer modular `narrowing as` does not mean "discard fractional bits." Modular
coefficient conversion and numeric rounding are separate policies.

## Representation and coercion

An exact representation operation can expose the coefficient independently of
numeric conversion. Its final source spelling remains unsettled.

Safe scalar compatibility requires equality of:

- coefficient logical width;
- signedness and coefficient encoding;
- fractional-bit position;
- concrete endianness;
- logical-bit placement;
- storage extent and alignment;
- non-value-bit placement and behavior; and
- normalization and valid-pattern rules.

Local coercive conversion uses a reduced relationship. Equal coefficient width,
compatible exact storage geometry, and a valid destination permit a
zero-translation reinterpretation of signedness or `F`:

```zax
myValue : I16F8 = -1.5

myRawView :=
  myValue as coercive layout U16F4 &
```

The same bytes now have another known coefficient-and-scale interpretation.
Numeric value need not be preserved.

Every integer-backed fixed-point bit pattern is valid, so matching-geometry
integer/fixed coercion is safe even when it changes numeric interpretation.
Integer/fixed cross-endian coercion may likewise expose a precisely known valid
byte-order reinterpretation. Numeric endian conversion instead preserves value
and may rearrange bytes.

Coercion is local and produces a reference view, not an independent copy. It
never grants access authority. Existing mutable, writable, or varying authority
may be preserved, but readonly, immutable, or final input cannot gain stronger
access. An enclosing aggregate may still require `unsafe as coercive` when
another participating scalar relation has value-dependent validity or writable
invariant risk.

Complete aggregate, anchor, qualification, and same-storage view behavior is
defined by
[Zax structural shapes and compatibility](structural-shapes-and-compatibility.md).
`unsafe cast` is defined by [Zax conversions and casts](casting.md).

## Availability and cost

A concrete fixed-point identity exists independently of whether one target
provides every numeric operation. Its representation-level size, alignment,
zero initialization, copying, and raw access remain known.

An available operation may use:

- one or more native integer or fractional instructions;
- wider registers or intermediates;
- automatically supplied software;
- runtime selection permitted by the target contract; or
- compile-time evaluation.

The language promises the selected result and failure policy, not one lowering.
Unsupported operations produce compiler diagnostics instead of silently
returning an incomplete result.

Important visible costs include:

- scale adjustment and rounding;
- overflow or zero-divisor checks;
- wider intermediates;
- endian adaptation;
- software emulation;
- reporting or optional results; and
- raw/coercive portability responsibility.

## Diagnostics

Diagnostics should identify:

- public and canonical fixed-point names;
- coefficient width, signedness, `F`, quantum, and range;
- concrete endianness and storage envelope;
- the source and destination grids for a failed conversion;
- exact, optional, rounded, wrapping, saturating, reporting, or coercive policy;
- discarded precision separately from overflow;
- division by zero;
- mixed identities requiring explicit conversion;
- unavailable target operation and support classification; and
- invalid size, alignment, or representation relationship for coercion.

For example:

```text
cannot exactly convert I16F8 value 1.5 to I16
I16 has no fractional values; use optional or explicitly rounded conversion
```

## Portability and maturity

- Exact fixed-point value sets and arithmetic results are stable.
- An unqualified name selects the active environment's concrete endianness;
  choose an absolute-endian path for stable stored byte order.
- Profile and provider changes may change cost or operation availability, but
  not the meaning of an exact supported operation.
- Q aliases are transparent names under Zax's fixed convention.
- Changing `W`, `F`, signedness, endianness, rounding, or overflow policy can
  change values, layout, source validity, and compatibility.

This document is current conceptual design, not formal grammar, a CPU-provider
format, an ABI/wire contract, or an implementation mapping. Exact generic
declarations, reflection APIs, rounded-conversion wording, and exhaustive
protected operator forms remain future focused work and must preserve the
programmer-visible behavior established here.
