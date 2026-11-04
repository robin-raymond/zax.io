# Zax integers

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers choosing, storing, converting, or calculating with Zax integers |
| Applies To | Fundamental finite integer types and their programmer-visible behavior; not a formal grammar or specification |
| Implementation State | Not established by this repository |
| Owns | Integer choice and mental model; the `F = 0` intrinsic-family branch; exact and profile-selected families; canonical names and namespaces; logical width, range, signed representation, concrete endianness, storage, padding, and alignment; software emulation; integer identity types and conversion relationships; count/storage/counterpart/delta/distance associated types; arithmetic build contracts; costs, diagnostics, portability, and source stability |
| Does Not Own | Fixed-point `F > 0` behavior ([fixed-point scalars](fixed-point-scalars.md)); cross-family endian operations ([endianness](endianness.md)); uncommitted integer evaluation and realization ([integer literals and realization](integer-literals.md)); literal prefix and raw-pattern source ([literal source and operators](literal-source-and-operators.md)); string/character identity and encoding behavior ([strings and characters](strings-and-characters.md)); general identity declarations ([identity types](identity-types.md)); enum members and policies ([enums](enums.md)); complete integer operations ([integer operator catalog](integer-operator-catalog.md)); general forms and precedence ([operator catalog](operator-catalog.md)); shared operator selection ([operators](operators.md)); generic declarations and instantiation ([generics](generics.md)); or the deferred integer-factory interface and CPU-profile mechanisms |
| Source / Provenance | Legacy [basics](../basics.md) and retired root casting-page integer evidence, refined against current operator, endian, qualifier, declaration, and identity design |

## Choosing an integer says what the value is for

Zax does not silently choose a common integer type when different integer
domains meet.

```zax
myByte : U8 = 1
myWide : U16 = 2

mySum := myByte + myWide
// error: state the intended result type

mySum16 := (myByte as U16) + myWide
```

`U8` and `U16` express different value ranges. The programmer chooses which
range the calculation should use.

The same principle applies to signedness and to role types such as `Byte`,
`Integer`, `Word`, and `TypeSize`. Zax performs no implicit widening,
narrowing, signedness change, or target-dependent promotion.

Most integer operations use operands of the same integer identity. An operation
may instead declare a dedicated operand role:

```zax
mySum := myLeft + myRight       // both operands have one integer identity
myShifted := myValue << myCount // count uses the associated bit-count type
```

Associated counts, declared identity bridges, conversion/admission, and future
multiword operations are explicit exceptions. Mixed-endian values have distinct
concrete identities and require explicit numeric conversion. None creates
general promotion.

## Logical width

Every integer has a finite **logical width** and signedness known from its
concrete type.

The logical width determines:

- representable values;
- arithmetic overflow;
- signedness;
- bitwise results;
- shifts and rotations;
- counts, masks, and bit positions; and
- the integer's own storage envelope.

The compiler may use wider registers, temporaries, or intermediate operations
when doing so produces exactly the same logical-width behavior.

For example, a compiler may calculate a `U8` operation in a 32-bit register. It
may not expose 32-bit range, overflow, bit, result, or stored-layout behavior.
The stored `U8` still uses its defined one-byte envelope.

## Exact intrinsic family

A byte is always eight bits.

The predefined exact-width family is:

| Signed | Unsigned | Logical width |
| --- | --- | --- |
| `I8` | `U8` | 8 bits |
| `I16` | `U16` | 16 bits |
| `I32` | `U32` | 32 bits |
| `I64` | `U64` | 64 bits |
| `I128` | `U128` | 128 bits |

For width `W`:

```text
unsigned range = 0 through 2^W - 1
signed range   = -2^(W-1) through 2^(W-1) - 1
```

Signed values use two's-complement representation. Every logical bit pattern
represents a valid value.

The built-in integer family is open to specialization. Each realized
specialization is **sealed** to added storage: its representation is exactly
its logical bits and padding.

```zax
MyI57 :: alias type Integer$(
  LogicalBits: 57,
  Signedness: Sign.Signed,
  FractionalBits: 0,
  Endianness: Endian.Little
)
```

`MyI57` is another name for a concrete 57-bit intrinsic specialization. It is
not a user-implemented integer; it is one concrete type.
The intrinsic family `Integer$` shares its name with the non-generic `Integer`
role; the spellings never collide. See
[generics](generics.md#sharing-a-name-with-a-non-generic-type).

Zax does not predefine every possible `I<W>` and `U<W>` name. Programmers name
unusual widths deliberately.

A specialization remains open to added functions and operators. A
[partial](partials.md) can give an integer type an operation it does not
declare, such as `+` with a programmer type on the right, visible only where the
partial is granted. Protected all-integer signatures stay unavailable. Because
`Integer` and other role names are aliases, a partial on one extends whatever
specialization that name resolves to on the current profile.

Functions supplied by the language, the compiler, or a CPU provider remain
future work.

## Integer storage and padding

Every stored integer begins at a byte-aligned address. Required alignment may be
greater than one byte.

An integer's **storage envelope** is the smallest whole number of bytes capable
of containing its logical bits:

```text
storage bits = ceil(logical width / 8) * 8
```

For whole-byte exact integers, every bit in that envelope participates in the
integer value:

```text
I32 logical width = 32 bits
I32 storage       = 4 bytes
```

An unusual width leaves non-value bits in the envelope:

```text
I47 logical width     = 47 bits
I47 storage           = 6 bytes
I47 non-value storage = 1 bit

I57 logical width     = 57 bits
I57 storage           = 8 bytes
I57 non-value storage = 7 bits
```

Non-value bits have unspecified contents and cannot affect defined integer
behavior.

Integer storage padding is different from container layout padding:

```zax
MyType :: type {
  myByte : U8
  myWide : U64
}
```

`U8` has no unused bits in its one-byte envelope. `MyType` may nevertheless
insert bytes between `myByte` and `myWide` to align `myWide`. Those bytes belong
to `MyType`'s layout, not to `U8`.

## Storage carrier and widening

`T storage type` is the smallest whole-byte unsigned exact integer capable of
holding `T`'s storage envelope:

```text
I32 storage type -> U32
I47 storage type -> U48
I57 storage type -> U64
```

The carrier is not restricted to power-of-two widths. It names capacity, not a
safe raw conversion and not necessarily the same required alignment.

Widening uses the logical value:

- unsigned values zero-extend;
- signed values sign-extend from their logical sign bit; and
- non-value storage bits never become source value bits.

Zax provides no language-level raw storage extraction for a padded unusual width
such as `I57`.

## Canonical namespace and short names

The family root is the canonical current-execution-environment catalog:

```zax
Scalars.Integers.I32
Scalars.Integers.Integer
Scalars.Integers.Near.TypeSize
```

Explicit environment and absolute-endian paths include:

```zax
Scalars.Integers.Target.I32
Scalars.Integers.CompilerHost.I32
Scalars.Integers.Target.Integer
Scalars.Integers.CompilerHost.Integer
Scalars.Integers.Little.I32
Scalars.Integers.Big.I32
```

Short source names are transparent aliases of the current-environment paths:

```zax
I32
Integer
Target.Integer
CompilerHost.Integer
```

`Scalars` and language-provided short aliases are protected declarations
automatically available in every module root. They retain one language-owned
identity across generative imports. Complete visibility and module-root behavior
is defined by [Zax namespaces and modules](namespaces-and-modules.md#language-provided-declarations).

`I32` always fixes 32 logical value bits but resolves the active environment's
concrete endianness. Use `Scalars.Integers.Little.I32` or
`Scalars.Integers.Big.I32` when stored byte order must be part of a
target-independent type name.

Memory-domain paths follow the environment:

```zax
Scalars.Integers.Near.TypeSize
Scalars.Integers.Target.Near.TypeSize
Scalars.Integers.CompilerHost.Far.UPointer
```

The canonical order is scalar family, integer family, optional explicit
environment or absolute endianness, explicit memory domain where applicable,
then role.

## Native representation and software emulation

A CPU profile classifies widths the CPU natively represents.

**Native representation** means the CPU directly represents that scalar width.
It does not promise that multiplication, division, atomics, or another
particular operation has a direct instruction.

**Software emulation** implements an integer through several native operations
or automatically supplied helper behavior.

A **software fallback** occurs when type selection chooses software emulation
because no native candidate satisfies the request.

For example, an eight-bit CPU may natively represent `Byte` while using
optimized software emulation for a 16-bit `Integer`. The CPU may still lack an
eight-bit multiplication instruction.

Environment-relative names have three routes:

```zax
Integer               // active execution environment
Target.Integer        // generated target behavior
CompilerHost.Integer  // compiler-running machine
```

Shared code may be specialized for compiler-host and target execution. Exact
types or explicit environment paths are used when representation must agree
across that boundary.

The exact profile format, versioning, ABI interaction, and instruction/cost
metadata remain future CPU-provider work.

## Profile-selected integer families

Profile-selected public names are distinct
[identity types](identity-types.md). They retain their role even when several
names use the same exact intrinsic specialization.

### Minimum-width selection

| Names | Meaning |
| --- | --- |
| `FastI8`/`FastU8` through `FastI128`/`FastU128` | CPU-provider-preferred implementation having at least the requested width |
| `LeastI8`/`LeastU8` through `LeastI128`/`LeastU128` | Smallest native width having at least the requested width |

When no native candidate satisfies the requirement, the selected family may use
the declared software fallback.

Advanced bounded selection remains future integer-factory work. The factory
will consider minimum width, maximum width, signedness, preference,
environment, and fallback, then return one exact specialization. Such a
selection can be written as a type-returning function that computes an
`Integer$` instantiation; see
[building types from computed values](generics.md#building-types-from-computed-values).
The factory interface itself remains future work.

### Conventional CPU-relative family

| Signed | Unsigned | Requirement |
| --- | --- | --- |
| `Small` | `USmall` | Smallest conventional family member; at least 8 bits |
| `Short` | `UShort` | At least 16 bits |
| `Integer` | `UInteger` | General CPU-relative integer; at least 16 bits |
| `Long` | `ULong` | At least 32 bits and no narrower than `Integer` |
| `LongLong` | `ULongLong` | At least 64 bits and no narrower than `Long` |

```text
Small <= Short <= Integer <= Long <= LongLong
```

### Legacy character-width roles

| Role | Requirement |
| --- | --- |
| `Legacy.Char` | Exact profile-native narrow-character width, signedness, and encoding; always defined |
| `Legacy.WChar` | Exact profile-native wide-character width, signedness, and encoding; optional |

These are distinct profile identities for native or historical character
interoperability, not ordinary Zax text. A conventional eight-bit profile may
represent `Legacy.Char` with `I8` or `U8` according to its selected signedness.
A legacy wide profile may select sixteen, thirty-two, or another declared width
and encoding.

A platform without a native wide-character model does not synthesize
`Legacy.WChar` from `I32`, `U32`, or `Rune`. Exact C/C++ signed and unsigned
character ABI identities remain future interop work rather than paired Zax
roles.

`Legacy` means a legacy-era representation model, not deprecated or unsupported.
ASCII, Unicode scalar, validated text, native string, and NUL-termination
behavior is defined by [Zax strings and characters](strings-and-characters.md).

### Machine-word roles

These roles are unsigned:

| Name | Logical width |
| --- | --- |
| `Byte` | Exactly 8 bits |
| `HalfWord` | `ceil(Word width / 2)` |
| `Word` | Natural width selected by the CPU provider |
| `DWord` | `Word width * 2` |
| `QWord` | `Word width * 4` |
| `OWord` | `Word width * 8` |

The provider selects `Word`; Zax derives the others exactly. A derived width
without native support uses software emulation. A width beyond the language
maximum is unavailable for that profile.

`Byte` remains a distinct identity from `U8`.

### Maximum families

| Signed | Unsigned | Meaning |
| --- | --- | --- |
| `NativeMaxI` | `NativeMaxU` | Widest scalar width the CPU profile classifies as native |
| `OptimizedMaxI` | `OptimizedMaxU` | Widest family for which the profile supplies its preferred general-purpose optimized implementation |
| `SupportedMaxI` | `SupportedMaxU` | Widest exact specialization supported by this toolchain/version/configuration |
| `LanguageMaxI` | `LanguageMaxU` | Widest exact specialization permitted by this Zax language version |

`OptimizedMaxI`/`OptimizedMaxU` may use native representation or deliberately
optimized software emulation. The profile must expose the selected width, broad
cost expectations, and which operations are native or emulated. The name does
not promise that every operation is one instruction or universally fastest.

The initial language maximum is:

```text
2^23 bits = 8,388,608 bits = 1,048,576 bytes
```

A complete toolchain for this language version supports every exact width
through that maximum. Increasing it changes the `LanguageMaxI`/`LanguageMaxU`
identity and is a language-version compatibility event.

### Pointer-representation integer roles

| Domain | Address role | Exact-difference role |
| --- | --- | --- |
| Near | `Near.UPointer` | `Near.PointerDelta` |
| Ordinary | `UPointer` | `PointerDelta` |
| Far | `Far.UPointer` | `Far.PointerDelta` |

These are integer capacity types, not pointer objects. Numeric fit does not
establish pointer validity, provenance, permissions, segment/tag meaning, arena,
or lifetime.

Each `PointerDelta` is its domain's `UPointer delta type`, so it holds the exact
difference between any two addresses of that domain, positive or negative. No
separate signed address role is needed.

Profiles satisfy:

```text
Near pointer capacity <= ordinary pointer capacity <= Far pointer capacity
```

Mirrored representations retain distinct identities. Conversion between a
pointer and its domain's `UPointer` is defined by
[Zax conversions and casts](casting.md#pointers-and-integers).

### Counts, sizes, and indexes

| Role | Meaning |
| --- | --- |
| `BitCount` | Ordinary profile-selected unsigned bit count |
| `LargeBitCount` | Unsigned count covering the language maximum integer width |
| `ByteCount` | Ordinary profile-selected unsigned byte count |
| `LargeByteCount` | Unsigned count covering the language maximum integer's storage bytes |
| `TypeSize`, `Near.TypeSize`, `Far.TypeSize` | Type/allocation byte capacity in each memory domain |
| `IndexSize`, `Near.IndexSize`, `Far.IndexSize` | Element count, index, and endpoint capacity in each memory domain |

`BitCount` has at least eight logical bits and therefore represents at least
zero through 255. It also represents every predefined ordinary scalar width in
its profile and has storage no larger than `TypeSize`.

`LargeBitCount` represents zero through 8,388,608 and therefore needs at least
24 unsigned logical bits.

`LargeByteCount` represents zero through 1,048,576 and therefore needs at least
21 unsigned logical bits.

Within each memory domain:

```text
IndexSize.maximum = TypeSize.maximum
IndexSize representation = TypeSize representation
```

They remain distinct identities but declare exact conversion bridges in both
directions.

For a collection of length `L`, valid positions are zero through `L - 1` and
slice/splice endpoints are zero through `L`. No separate endpoint type is
required.

Array code answers two different numeric questions:

- `IndexSize` counts elements, positions, endpoints, and reserved element
  capacity.
- `TypeSize` measures the bytes needed for backing storage.

The near and far array families use the corresponding near or far identities.
Calculating backing extent therefore checks:

```text
element count * element storage extent
plus metadata and alignment
```

An ordinary iterable `count` example returns ordinary `IndexSize`. Near/far and
generic iterables must use a count identity that the destination array
can accept explicitly; representation coincidence never creates a silent
cross-domain conversion. Complete array behavior is defined by
[Zax arrays and slices](arrays-and-slices.md#counts-storage-extent-and-allocation).

Profiles also satisfy:

```text
Near.TypeSize.maximum <= TypeSize.maximum <= Far.TypeSize.maximum
Near.IndexSize.maximum <= IndexSize.maximum <= Far.IndexSize.maximum
```

## Initialization and compile-time realization

Default initialization of a fundamental integer produces numeric zero with an
all-zero logical representation.

A number written directly in source, such as `42`, has a mathematical value but
has not yet chosen an integer width. A typed declaration supplies that type:

```zax
myByte : Byte = 42
```

In this example, the source `42` is the uncommitted integer. It stops being
uncommitted when the declaration gives it type `Byte`.

The compiler checks whether `42` fits `Byte` and creates the `Byte` value
directly. It does not first create an `Integer` and implicitly convert it. An
out-of-range value is a compile error.

Function and operator results are concrete. An expression already typed as
`Integer` remains `Integer` merely because it is constant or was evaluated
during compilation:

```zax
compileTimeInteger final : (result : Integer)() = {
  return 42
}

myInteger : Integer = compileTimeInteger()
myByte : Byte = myInteger // error: distinct integer identities
```

`compileTimeInteger()` and `myInteger` are already concrete `Integer` values;
neither is an uncommitted integer merely because the value is known during
compilation.

Zax calls a not-yet-typed mathematical source value such as the earlier `42` an
**uncommitted integer**.
[Zax integer literals and realization](integer-literals.md) explains its sign
intent, width-invariant operations, type selection, conditional behavior, and
failures.

An unqualified radix literal may use generic uncommitted specialization:

```zax
myDefault := h'FF'  // UInteger
myByte : U8 = h'FF' // U8
```

Compile-time processing selects one concrete result prototype before the
literal body runs. The executed result is never an anonymous or typeless
integer.

An exact integer type also supplies raw logical-pattern literals:

```zax
mySigned := I8.h'FF' // I8 value -1
myByte := U8.dec'255'
```

Digit order describes logical significance rather than stored byte order.
Complete prefix, specialization, grouping, and raw-pattern behavior is defined
by [Zax literal source and literal operators](literal-source-and-operators.md).

## Integer identity types

Profile-selected roles are distinct identities so representation coincidence
does not change source validity:

```zax
myExact : I32
myNatural : Integer

myNatural = myExact // error even when this profile uses I32 for Integer
```

### Conversion to exact intrinsic integers

```zax
myWide := myU16 as U32
myOptional := myU16 as U8?
myNarrow := myU16 narrowing as U8
```

- `as Destination` is available when declared type facts or a compile-time-known
  value prove that the exact value fits.
- `as Destination?` returns present when the exact value fits and absent
  otherwise.
- `narrowing as Destination` performs defined modular conversion and never
  panics because of range.
- `unsafe as` is not ordinary integer narrowing.

Conversion availability follows type/family contracts, not accidental equality
in one CPU profile. A profile may eliminate a check without making a portable
source form appear or disappear. Complete optional result behavior is defined
by [Zax optional values](optional-values.md#construction-and-optional-conversion-are-different).

### Admission into an integer role

```zax
myLong := Long from myI32
mySmall := Small optional from myU16
myNarrowSmall := Small narrowing from myU16
```

- `from` is available when the complete source range fits the identity's
  guaranteed range.
- `optional from` reports a nonrepresentable value through absence.
- `narrowing from` performs the defined modular conversion before creating the
  admitting identity.

An identity's immediate underlying value always qualifies for ordinary
admission when the identity uses `admit`. Complete optional wrapper behavior is
defined by [Zax optional values](optional-values.md).

### Projection and direct bridges

An exposed integer identity provides exact, optional, and narrowing conversion
to exact intrinsic integers. An opaque identity provides only immediate
underlying projection.

Two identities do not convert merely because their representations match.
`IndexSize` and `TypeSize` deliberately declare an exact bridge because their
ranges and representations are equal:

```zax
mySize : TypeSize = myIndex as TypeSize
myIndex : IndexSize = mySize as IndexSize
```

General admission, projection, unchecked/unsafe distinctions, and construction
requirements are defined by [Zax identity types](identity-types.md).

## Signedness counterparts

Exact intrinsic types derive the equal-width opposite-signedness specialization:

```text
I32 <-> U32
```

Named profile roles are created as relational pairs by the integer factory or
another validated generic mechanism:

```text
Small <-> USmall
FastI16 <-> FastU16
```

The compiler does not infer relationships from names. Both identities share one
selector except for signedness and are recorded as unique mutual counterparts.

One-sided roles such as `Word`, `Byte`, `BitCount`, and `UPointer` return an unnamed exact
intrinsic counterpart and intentionally leave the role identity. Regaining the
role requires explicit admission.

Exact relational-pair declaration syntax remains future work.

## Associated count and result types

Count operations return an associated type so ordinary integers can use compact
counts while extremely wide integers use a larger count. Generic code asks the
operand type which result it uses instead of branching on width.

```zax
MyCount :: alias type MyInteger bit count type
```

The bit-count type is selected from logical width:

```text
T bit count type =
  BitCount      when T.logicalWidth <= BitCount.maximum
  LargeBitCount otherwise
```

It represents every value from zero through `T.logicalWidth` and is directly
usable as a protected shift count for `T`.

The byte-count type is selected from storage-envelope byte size:

```text
T byte count type =
  ByteCount      when T.storageEnvelopeBytes <= ByteCount.maximum
  LargeByteCount otherwise
```

Other associated results include:

```zax
MyByteCount :: alias type MyInteger byte count type
MyDelta :: alias type MyInteger delta type
MyDistance :: alias type MyInteger distance type
MyStorage :: alias type MyInteger storage type
MyCounterpart :: alias type MyInteger signedness counterpart type
```

These type-qualified operations use the concrete integer type identity as their
lookup anchor, return type identities, and perform no runtime receiver
construction. Because they return types, they run only during compilation, and
generic prototypes may use them:

```zax
bitsOf$ final : (UseType : type)(result : UseType bit count type)(value : UseType) = {
}
```

See [types are compile-time values](generics.md#types-are-compile-time-values).

## Integer operations

Integers support protected families for:

- arithmetic and selectable overflow policies;
- comparison;
- equal-width signedness counterpart;
- negation and magnitude;
- division and remainder;
- compounds and increment/decrement;
- bitwise operations;
- counts, masks, positions, and reductions;
- shifts and rotations;
- assignment and swap;
- exact, optional, and narrowing conversion/admission; and
- exact `delta` and `distance`.

The complete signed/unsigned availability, result types, failures, and
per-operator behavior are in the
[Zax integer operator catalog](integer-operator-catalog.md). General form,
fixity, and precedence are in the
[Zax operator catalog](operator-catalog.md).

## Unchecked arithmetic contracts

Normally, required arithmetic checks whether its exact result fits. Failure
panics instead of silently producing another value.

An explicit compiler/build setting may disable representability checks. Doing so
removes the arithmetic-overflow panic path and lets the compiler assume every
required result fits. If that promise is violated, the result and surrounding
behavior are undefined.

Reasons to select the unchecked contract include:

- removing overflow branches in hot arithmetic;
- improving vectorization and loop optimization;
- relying on domain invariants already validated elsewhere; and
- avoiding duplicate checks after a trusted boundary proved the input range.

Reasons to retain checks include:

- contract violations have undefined consequences;
- tests may not exercise the bad range;
- external input may violate assumed invariants; and
- build configuration becomes part of reproducibility and auditing.

A second independent setting may disable zero-divisor checks by promising that
every divisor supplied to required division or remainder is nonzero.

Neither setting is a debug-versus-release semantic difference. Both are off by
default, recorded in build metadata, and leave optional, wrapping, saturating,
reporting, and narrowing forms unchanged. Known compile-time violations remain
diagnostics.

Source-level panic controls are described by
[safety and analysis](safety-and-analysis.md#controlling-panic-checks); exact
build-option syntax and category names remain future analysis-control work.

## Structural scalar compatibility

An integer is the intrinsic family's zero-fractional-bit specialization:

```text
I<W> = Integer$(W, Sign.Signed, FractionalBits: 0, concrete endianness)
U<W> = Integer$(W, Sign.Unsigned, FractionalBits: 0, concrete endianness)
```

The [fixed-point owner](fixed-point-scalars.md) defines `F > 0`.

The intrinsic family normalizes its arguments before comparing identities. A
native or agnostic endianness selector resolves to a concrete endianness, and
`FractionalBits: 0` is the integer specialization, so argument sets that denote
the same integer produce one type. Programmer-declared generics have no such
family-specific normalization; their arguments compare by the ordinary
[identity rules](generics.md#comparing-arguments).

An integer scalar's safe structural format includes:

- logical bit width;
- signedness;
- two's-complement signed encoding;
- zero fractional-bit position;
- concrete endianness and logical-bit placement; and
- storage-envelope extent, alignment, non-value-bit, valid-pattern, and
  normalization rules.

Every applicable property must match for safe scalar shape or layout
compatibility. Distinct public or programmer identities may retain equal format
without becoming interchangeable; compatibility posture or an explicit
operation supplies any permitted crossing.

Integer coercion deliberately uses a reduced relationship. Equal logical bit
width, equal selected storage extent, compatible alignment and logical-bit
placement, and destination-valid padding behavior permit local coercive
reinterpretation even when signedness, fixed fractional position, or endian
meaning differs:

```zax
mySigned : I32 = -1
myUnsignedView :=
  mySigned as coercive layout U32 &
```

Every integer bit pattern is valid, so this integer relation is safely
coercible when the requested view preserves available access authority.
Coercion is target-sensitive, local, reference-shaped, and never a declared
posture. Cross-endian coercion likewise preserves valid integer bits while
changing their interpreted value; numeric endian conversion instead preserves
value. Fixed-point and binary floating-point owners supply their own scalar leaf
relationships. See
[Zax structural shapes and compatibility](structural-shapes-and-compatibility.md#scalar-compatibility).

## Enum boundaries

An exact integer such as `I57` or a language-provided integer role such as
`Integer`, `Long`, or `IndexSize` may back an enum. An arbitrary user identity
does not become eligible merely because it ultimately uses integer storage.

For an enum backed by `I57`, its underlying value remains logical `I57`; no
operation exposes the `U64` storage carrier. For an enum backed by `Integer`,
the immediate underlying type and value remain `Integer`; the profile-selected
exact intrinsic lies across a second identity boundary.

Flags require an unsigned eligible backing type. Their default backing is
`UInteger`; strict and relaxed enums default to `Integer`.

Integer backing does not grant ordinary enum arithmetic. Strict enums select
operations explicitly, relaxed enums request the eligible exposed-identity
surface, and flags receive their mask-preserving bitwise surface. Complete enum
behavior is defined by [Zax enums](enums.md).

## Intrinsic endianness

Every concrete integer specialization is little- or big-endian, including an
unusual width:

```zax
myLittle : Scalars.Integers.Little.U32
myBig : Scalars.Integers.Big.U32
```

Unqualified `U32` resolves the active environment's endianness. A selector
described as native or "agnostic" is resolved before the concrete type exists; it is not
per-value runtime state.

Endian specializations receive the complete applicable integer operation
surface. Like-identity operations preserve their declared interpretation even
when the CPU uses another byte order. Mixed-endian operands require explicit
conversion.

For non-byte-multiple widths, logical bits occupy the low-order portion of the
highest participating byte and unused high bits remain non-value storage.
Complete conversion, raw-representation, and environment behavior is defined by
[Zax endianness](endianness.md).

## Costs and diagnostics

Programmers and tooling need to expose:

- public name and canonical identity;
- logical width, range, and signedness;
- integer storage envelope, non-value bits, and required alignment;
- container padding as a separate layout fact;
- native representation versus software emulation;
- selected CPU profile and environment;
- concrete or active-selected endianness;
- exact, optional, narrowing, unchecked, or unsafe conversion;
- runtime checks and optional/reporting paths;
- arithmetic build contracts;
- associated result types;
- exposed identity and declared bridges;
- unavailable maximum-width delta; and
- endian conversion versus byte-preserving coercion.

Ordinary diagnostics lead with readable public names:

```text
cannot convert U16 to U8 without an explicit narrowing policy
```

Expanded tooling may show:

```text
public type: U8
canonical path: Scalars.Integers.U8
intrinsic specialization: Integer$(8, Sign.Unsigned)
```

The generic specialization is detailed information, not ordinary error
vocabulary.

## Portability and source stability

- Exact-width integer range and numeric behavior are portable across
  environments.
- An unqualified exact-width name resolves active-environment endianness; use an
  absolute-endian path when raw stored byte order must be portable.
- Profile-selected roles vary only according to their documented contracts.
- Distinct roles never become implicitly compatible because representations
  coincide.
- Changing a profile selection is a versioned compatibility event.
- Increasing the language maximum changes maximum-role identity.
- Changing declared range facts may change conversion availability.
- Changing count-tier selection changes associated result identity.
- Enabling an unchecked contract changes program preconditions and must remain
  reproducible in build metadata.

Use exact-width types for stored formats, protocols, cross-environment
constants, and code whose precise overflow boundary is part of its meaning.

## Boundaries and maturity

This document is current conceptual design, not formal grammar, an ABI contract,
a CPU-profile format, or an implementation mapping.

The integer-factory interface, relational-pair declarations, reflection
metadata, CPU-provider files, literal grammar, pointer
validity, language- or provider-supplied partials, build-option syntax, and
foreign correspondence
remain future work. Those mechanisms must preserve the concrete integer
guarantees established here.
[Current composition](composition.md) and
[current enum behavior](enums.md) must preserve the same concrete types,
identities, ranges, representation, conversions, and source behavior.
