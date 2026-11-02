# 010: Fundamental integer types and finite bit domains

| Field | Value |
| --- | --- |
| Status | Historical working material / non-normative / audit-only |
| Work Item | `010` |
| Created | 2026-08-29 |
| Completed | 2026-08-31 |
| Owns | Historical record of the bounded review defined by the fixed initiating input |
| Does Not Own | Current language design or deferred mechanisms assigned to live owners below |

## Non-authority notice

This completed file is historical, non-normative, and audit-only. Current
integer and identity behavior belongs to the promoted language owners, and
deferred mechanisms belong to the indexed raw inputs named below.

## Fixed initiating input

This section records the information aligned when work item `010` was created.
It is intentionally incomplete rather than exhaustive and must not be rewritten
as the work develops.

### Initiating concern

Develop a coherent human-developer-facing model for Zax's fundamental finite
integer types and the bit domains on which current integer operations depend:

1. what makes a type fundamental or intrinsic;
2. signed and unsigned fixed-width integer families;
3. concrete public names, aliases, and type identity;
4. value domains, storage representation, and logical bit extent;
5. width, size, alignment, padding, and portability;
6. context-selected or CPU-natural integer forms;
7. integer conversion and signedness relationships;
8. the current arithmetic, policy, shift, rotate, count, mask, reduction,
   reversal, and extraction/deposit operation domains;
9. the canonical shift-compatible bit-count result type;
10. endian backing eligibility and representation consequences;
11. costs, failures, diagnostics, reflection pressure, and source stability; and
12. lasting documentation owners and boundaries.

Use the current operator, catalog, qualifier, declaration, and endianness owners
as constraints. Recover Zax-specific intent before borrowing a numeric model
from another language.

### Motivating pressure

Current conceptual owners already rely on concrete intrinsic integer domains:

- fixed-width signed and unsigned operands have protected operator signatures;
- arithmetic policy forms distinguish required, optional, wrapping, saturating,
  and reporting behavior;
- shifts and rotates depend on a finite logical bit extent;
- count, set-bit mask, and set-bit position operations depend on exact width;
- set-bit positions return an optional canonical count directly usable as a
  built-in shift count;
- multiword shifts, reversal, and masked extraction/deposit remain accepted or
  deferred concepts constrained by finite extent;
- endian semantic enums require a fundamental integer backing type with known
  size, alignment, and representation; and
- future enums, literals, generics, reflection, and compatibility need a stable
  programmer-facing integer foundation.

Legacy material proposes many concrete, context-sized, CPU-sized, aliased, and
generic-looking integer forms. Checked-in names and explanations remain evidence
rather than accepted design merely because they exist.

### Known assumptions

- [Zax language vision](../../../language/vision.md) is accepted foundational
  direction.
- [Zax language principles](../../../language/principles.md) owns reusable
  cross-cutting design and use principles.
- [Zax operators](../../../language/operators.md) owns shared operator discovery,
  selection, results, evaluation, protection, and failure categories.
- [Zax operator catalog](../../../language/operator-catalog.md) owns current exact
  operator forms, precedence, protected integer operation families, and accepted
  arithmetic/bit behavior.
- [Zax endianness](../../../language/endianness.md) owns the current semantic enum
  model that requires applicable fundamental integer backing types.
- [Zax qualifiers](../../../language/qualifiers.md) owns value, access, and
  place-replacement qualifications applied to integer values and places.
- [Zax declarations and bindings](../../../language/declarations-and-bindings.md)
  owns type/value naming intent, aliases at the currently established depth, and
  declaration boundaries.
- [Raw numeric type-family input](../../raw/numeric-type-families.md) preserves
  future signedness, finite-extent, representation, and policy questions.
- Every successfully selected single expression result has a concrete static
  type, including compiler-known intermediate results.
- Concrete programmer-visible integer types can be defined before their future
  shared generic skeleton is known.
- A future generic model must explain or generate established concrete types
  without silently changing their names, identity, ranges, representation,
  layout, protected signatures, conversions, or source behavior.
- There is no language implementation in this repository.

### Known inclusions

- The conceptual distinction between fundamental, intrinsic, language-provided,
  alias, and custom integer types.
- The concrete signed and unsigned fixed-width family.
- Exact public type names and whether names such as `Byte`, `U8`, `Integer`, and
  `UInteger` are distinct identities, aliases, or context-selected forms.
- Minimum and exact widths, value ranges, zero, sign representation, and whether
  every storage bit participates in the logical value.
- Logical bit extent versus storage size, alignment, allocation capacity, and
  padding.
- Host, target, native, CPU-natural, and portable width pressure.
- Default initialization and zero/default value pressure at the depth needed to
  understand integer use.
- Literal realization pressure at the depth needed to establish integer value
  domains, without completing literal grammar.
- Widening, narrowing, signedness changes, exact-value conversions, panic,
  truncation, and explicitness.
- Equal-width signedness counterparts and their relationship to ordinary
  conversion.
- Current protected arithmetic, comparison, bitwise, count, reduction, shift,
  rotate, magnitude, mutation, and swap domains.
- Required, optional, wrapping, saturating, and reporting operation policies as
  they constrain the integer type model.
- Shift-count input requirements.
- The canonical unsigned count type shared by leading/trailing-zero counts and
  optional set-bit positions, including direct viability as a built-in shift
  count.
- Leading/trailing-zero counts, leading/trailing set-bit masks, and optional
  set-bit positions.
- Finite-extent pressure on population count, reductions, reversal, rotates,
  masked extraction/deposit, and multiword operations.
- Signed mask results and the distinction between bit-pattern behavior and
  arithmetic overflow.
- Fundamental backing eligibility for full-domain endian semantic enums.
- Storage/shape compatibility pressure without treating compatibility as
  conversion permission.
- Costs, panic and optional paths, compile-time failures, diagnostics,
  portability, optimization freedom, formatting, and source stability.
- Documentation fit, including whether detailed integer teaching warrants a
  dedicated current owner.
- The future possibility of a concise basic-types guide that summarizes and
  routes to mature integer, floating-point, string, enum, literal, and other
  foundational type owners.

### Known starting boundaries

- Complete floating-point semantics and representation.
- Complete fixed-point and arbitrary-width integer behavior.
- Complete enum declaration, validity, iteration, reflection, and operation
  inheritance.
- Complete generic syntax, constraints, specialization, instantiation, and
  generated type-family behavior.
- Complete literal parsing, prefix, escaping, payload, and custom realization.
- Complete structural identity, subtyping, storage compatibility, and truncating
  transfer.
- Complete reflection and compile-time execution.
- Complete ABI, foreign interoperability, serialization framework, and memory
  layout contract.
- Formal grammar, compiler implementation, lowering, instruction selection, and
  toolchain internals.

These areas need not be designed to begin. They do not prohibit following,
recording, or dispositioning material consequences.

### Intentionally unresolved framing

- The exact concrete intrinsic integer catalog.
- Whether `Integer` and `UInteger` are fixed, minimum-width, optimal CPU-width,
  target-context, or other selected forms.
- Which names are concrete type identities versus identity-preserving aliases.
- The relationship between `Byte`, character storage, raw bytes, and `U8`.
- Whether signed integers use one required representation or only a value-domain
  contract.
- Whether fundamental integer storage may contain padding outside logical bit
  extent.
- Exact size and alignment portability guarantees.
- Exact widening, narrowing, truncation, panic, and unsafe conversion behavior.
- The canonical bit-count type's name and representation.
- Which deferred bit operations receive exact phrase forms.
- How context-sized types behave during compiler-host versus target execution.
- How future generic syntax explains the concrete family.
- Whether a basic-types overview is justified now, later, or not at all.
- Which findings belong in an integer owner, the operator catalog, endianness,
  literal, compatibility, generic, reflection, or future owners.

### Documentation-structure pressure

Detailed integer behavior may justify a dedicated current owner such as
`language/integers.md`. A future concise `language/basic-types.md` could provide
a mental map and route to mature detailed owners for integers, floating point,
strings, enums, literals, and other foundational types.

This work does not presume either file must be created. The documentation-fit
dry run must decide whether integer teaching already warrants its own owner and
whether `index.md` remains sufficient basic-type routing until several sibling
owners exist. Do not scaffold empty or speculative type-document families.

### Initial stopping guidance

Stop when the work has:

- established a usable programmer model for every current fundamental finite
  integer form;
- established concrete names, identity and alias relationships, widths, ranges,
  logical extent, storage, and portability;
- reconciled every current protected integer operator family with its valid type
  domains;
- established conversion and signedness relationships at the depth programmers
  need;
- established the canonical shift-compatible bit-count result requirement;
- established or explicitly deferred the unresolved reversal,
  extraction/deposit, multiword, and unusual-width behavior;
- constrained endian backing, enums, literals, generics, reflection,
  compatibility, and compile-time contexts without silently designing them;
- identified costs, failures, diagnostics, formatting, and source-stability
  consequences;
- preserved every deferred consequence in a live owner or indexed raw
  destination;
- identified plausible lasting owners and whether a detailed integer owner or
  later basic-types guide is justified; and
- gathered enough information for a pre-promotion documentation-fit dry run.

Do not promote findings, create speculative type-document families, archive this
work item, or begin work item `011` without the separately required discussion,
alignment, and authorization.

## Reading scope

### Required

- [Documentation architecture](../../documentation.md) - governs ownership,
  focused reading, numbered work, promotion, and archival.
- [Zax language vision](../../../language/vision.md) - provides foundational
  purpose, audience, and language direction.
- [Zax language principles](../../../language/principles.md) - provides explicit
  cost, readability, optimization, and source-stability constraints.
- [Zax operators](../../../language/operators.md) - provides shared operator
  selection, result, evaluation, protection, and diagnostic behavior.
- [Zax operator catalog](../../../language/operator-catalog.md) - provides exact
  current integer operation forms, policy families, protected domains, and
  finite-bit behavior that the type model must support.
- [Zax endianness](../../../language/endianness.md) - provides the current
  fundamental-backing use case and representation/semantic distinction.
- [Zax qualifiers](../../../language/qualifiers.md) - provides qualification truth
  for integer values, places, and receiver operations.
- [Zax declarations and bindings](../../../language/declarations-and-bindings.md) -
  provides naming intent, declaration, alias, and type-boundary constraints.
- [Raw numeric type-family input](../../raw/numeric-type-families.md) - preserves
  signedness, finite extent, representation, and operation-policy questions.
- Focused [legacy intrinsic types](../../../basics.md#intrinsic-types) - preserves
  the existing candidate type catalog, names, widths, and alias intent.
- Focused [legacy casting](../../../casting.md) - preserves current evidence about
  intrinsic widening, narrowing, signedness, panic, and unsafe truncation.

### Consequence-driven

- Read [raw literal input](../../raw/literal-operators.md) when integer value range,
  default type, base, width, or signedness makes literal realization necessary.
- Read [raw structural-typing input](../../raw/structural-typing.md) when storage,
  shape, representation, alias identity, or truncating transfer constrains the
  integer model.
- Read [raw type-parameter and generic input](../../raw/type-parameters-and-generics.md)
  when the public identity of a concrete integer depends on future family syntax.
- Read [raw enum input](../../raw/enum-types.md) only when a finding constrains
  fundamental backing eligibility or whether every backing integer value is
  valid.
- Read [raw reflection input](../../raw/reflection.md) when integer family
  relationships or representations must be discoverable.
- Read [raw compile-time execution input](../../raw/compile-time-execution.md) when
  host, target, native, or context-sized behavior constrains integer meaning.
- Read [raw interoperability input](../../raw/interop.md) when ABI or foreign-layout
  pressure becomes unavoidable.
- Read focused legacy compiler directives, meta-types, or meta-functions only
  when a concrete host/target or generic consequence requires their evidence.
- Expand beyond this list whenever discussion or a material finding establishes
  a concrete need. State why the additional reading is relevant and keep it
  focused.

Material not listed in this reading scope is neither required nor prohibited. It
remains available when discussion or a concrete consequence makes it relevant.

### Audit-only

- `project/archive/`, including work items `001` through `009`.
- The historical `operator.md` path.

Do not read archived work item `009` while conducting ordinary work on `010`.
Its applicable findings have been promoted into current owners and indexed raw
inputs. Read it only when the language maintainer explicitly requests a targeted
audit or provenance investigation.

## Working record

The findings in the next section are aligned for this work item but remain
non-authoritative until separately promoted into lasting owners. The initial
reconstruction remains afterward as candidate history and is not current where
it conflicts with the aligned findings.

### Aligned findings after maintainer review

#### Programmer model

Zax has one built-in integer family that is open to specialization but closed to
extension. Each realized combination of logical width, signedness, and other
eventually established integer-family dimensions is one concrete,
compiler-recognized intrinsic type. Once realized, that type is not generic and
programmer code cannot add declarations to its closed intrinsic surface.

Illustratively:

```zax
// Integer$(...) syntax remains future generic work.
MyI57 :: alias type Integer$(57, Sign.Signed)
```

`MyI57` is a programmer-named exact-width intrinsic specialization, not a custom
nominal integer implementation. The compiler supplies its protected integer
behavior over a logical width of exactly 57 bits.

Language-provided names select these concrete specializations. Some are
transparent aliases for one identity. Others establish intent identities that
remain distinct even when two selectors happen to choose the same
representation on one target.

```zax
small : U8 = 1
wide : U16 = 2

sum := small + wide
// error: distinct integer types; state the intended result type

sum16 := (small as U16) + wide
```

Width, signedness, role, and environment selection express programmer intent.
There is no implicit integer widening, narrowing, signedness change, or
target-dependent compatibility caused by two selectors coincidentally choosing
the same representation.

#### Exact widths, bytes, representation, and storage

A Zax byte is always exactly eight bits.

The predefined exact-width family is:

| Signed | Unsigned | Logical width |
| --- | --- | --- |
| `I8` | `U8` | 8 bits |
| `I16` | `U16` | 16 bits |
| `I32` | `U32` | 32 bits |
| `I64` | `U64` | 64 bits |
| `I128` | `U128` | 128 bits |

For logical width `W`:

- the unsigned value range is `0` through `2^W - 1`;
- the signed value range is `-2^(W-1)` through `2^(W-1) - 1`;
- signed values use two's-complement representation;
- the signed and unsigned forms are different concrete types and equal-width
  signedness counterparts;
- the byte-multiple exact types have no value or storage padding; and
- every logical bit pattern represents a valid value.

These restrictions follow the intended modern C++ representation direction
without importing C++ integer promotion or conversion behavior.

Every stored integer begins at a byte-aligned address. Its required alignment may
be greater than one byte and remains an environment/target property.

An unusual logical width occupies the smallest whole-byte storage envelope that
can contain it:

```text
I57 logical width          = 57 bits
I57 storage envelope       = 8 bytes
I57 non-value storage      = 7 bits
```

The extra storage bits have unspecified contents. They do not participate in
the integer value, and defined integer arithmetic, comparison, bitwise,
reduction, shift, rotate, count, widening, and narrowing behavior cannot depend
on them.

Widening uses the logical value:

- an unsigned source zero-extends;
- a signed source sign-extends from its logical sign bit; and
- storage padding is never treated as source value bits.

Raw extraction and deterministic exchange must distinguish unspecified in-memory
padding from a normalized representation. A future normalized storage or endian
operation should write unused high storage bits as zero for unsigned values and
as the canonical sign extension for signed values. A raw operation that exposes
the complete storage envelope must identify the padding as unspecified rather
than presenting it as stable numeric information.

Logical width, storage-envelope size, and required alignment are separate
properties. Register width, allocation slack, container padding, and compiler
carrier width never extend the logical integer domain.

#### Compile-time integer realization

A typed slot may directly realize an unrealized compile-time integer result at
the requested width and signedness:

```zax
myValue := compileTimeResult()
myByte : Byte = compileTimeResult()
// compile-time error when the result is outside 0 through 255
```

This is direct realization, not implicit conversion from a hidden preselected
`Integer`. A compile-time operation may also be result-polymorphic so that the
complete typed declaration selects an already concrete `Byte` result.

Merely proving that an ordinary concretely typed `Integer` expression is
constant does not permit an otherwise forbidden implicit type change:

```zax
ordinary final : (result : Integer)() = {
  return 42
}

myByte : Byte = ordinary()
// error: ordinary() already returns Integer
```

Otherwise type legality could change according to whether a compiler happened
to evaluate an expression at compile time. Future literal and compile-time work
must establish the compiler-known unrealized integer category or equivalent
result mechanism.

A context-free ordinary positive integer literal provisionally realizes as
`Integer`; pre-unary `+` requests `UInteger`. A typed slot realizes the literal
directly as its requested integer type. Literal work must make the minimum signed
value expressible without first requiring an unrepresentable positive value in
that same type.

#### Integer conversion

Ordinary `as` performs an explicit exact-value integer conversion. It preserves
the mathematical value or panics when the destination cannot represent it. A
compile-time-known failing conversion becomes the established compile-time panic
diagnostic.

Integer `unsafe as` has deterministic modular behavior rather than undefined
integer corruption:

> Preserve the source integer modulo `2^W`, where `W` is the destination logical
> width, then interpret those bits using the destination signedness.

This produces the corresponding sign extension, zero extension, truncation, and
equal-width reinterpretation:

```zax
signed : I8 = -1
unsigned : U8 = 255
wide : U16 = signed unsafe as U16 // 65535
narrow : I8 = unsigned unsafe as I8 // -1
```

Changing width or signedness remains explicit even when ordinary widening cannot
fail.

#### Active, target, and compiler-host environments

Environment-relative integer names need one contextual spelling and explicit
namespace-qualified alternatives:

```zax
Integer               // selected for the active execution environment
Target.Integer        // explicitly selected for generated target behavior
CompilerHost.Integer  // explicitly selected for the compiler-running machine
I32                   // exact logical width in either environment
```

The same pattern applies to other environment-selected families. Exact-width
integer names do not need separate target and compiler-host width variants.

The terms **active execution environment**, **target**, and **compiler host**
must be defined once for general execution rather than reinvented per type
family. Existing current terminology establishes the distinction for endianness;
complete execution-context behavior remains compile-time work.

Shared code may be specialized for compiler-host execution and target execution.
Values that cross that boundary use exact-width types or explicitly pinned
environment types when representation compatibility matters.

A stable integer catalog namespace should anchor global convenience aliases.
The exact namespace hierarchy remains naming integration, but environment
qualification must use one consistent axis rather than several reordered name
patterns.

#### Transparent aliases and intent identities

A transparent alias is another name for the same type identity:

```zax
MyShortInteger :: alias type Integer
```

`type of` reports the underlying real type identity, and no conversion exists
between the two spellings because they already denote one type.

An identity declaration creates a new identity over one known underlying type.
Its admission and exposure choices are independent and explicit:

```zax
MyInteger :: identity admit expose type Integer
MyRestrictedInteger :: identity restricted expose type Integer
MyHandle :: identity restricted opaque type Integer
MyOpaqueInteger :: identity admit opaque type Integer
```

The conceptual declaration shape is:

```text
Name :: identity <admission keyword> <surface keyword> type SourceType
```

Exactly one admission keyword is written:

- `admit` automatically supplies safe `IdentityType from sourceValue` for every
  valid immediate source value;
- `restricted` supplies no automatic safe `from`; unchecked entry uses
  `unsafe from`, and the identity may separately declare validated safe creation.

Exactly one surface keyword is written:

- `expose` re-exposes eligible source behavior after meaning-preserving
  adjustment into the identity's type domain;
- `opaque` re-exposes no source behavior automatically.

These are declaration keywords, not operator phrases. At this maturity there is
no omission default for either axis because both choices communicate
consequential intent.

Projection extracts the immediate underlying value:

```zax
base := myInteger as Integer
```

Admission establishes the identity from that immediate source:

```zax
myInteger := MyInteger from base
restricted := MyRestrictedInteger unsafe from base
```

The identity type owns admission. The underlying closed type does not acquire
conversions to every future identity.

Sibling conversion therefore states both steps and cannot hide an unsafe
boundary:

```zax
myFile := MyFileHandle unsafe from (myDocument as Integer)
```

An independently declared identity begins another branch. A transparent alias
of an existing identity remains in that branch:

```zax
MyInteger :: identity admit expose type Integer
MyIntegerAlias :: alias type MyInteger

myValue : MyInteger
myAlias : MyIntegerAlias
myValue = myAlias // same identity branch
```

#### Exposed composition, `own`, and `partial`

An exposed identity behaves conceptually like private `own` composition:

```zax
MyInteger :: type {
  boxed own private : Integer
}
```

This is a programmer mental model, not a promised literal source expansion or
compiler lowering. The identity has no additional instance storage and preserves
the underlying representation shape while remaining a distinct type.

Re-exposure cannot publish source signatures unchanged. It must substitute the
identity where doing so preserves meaning:

```text
Integer + Integer -> Integer
```

becomes conceptually:

```text
MyInteger + MyInteger -> MyInteger
```

External result domains such as `Boolean` and the associated bit-count type
remain external. Constructors, admission, projection, arbitrary conversions,
associated results, signedness counterparts, qualification, private
declarations, and operations whose meaning does not survive substitution all
need deliberate exposure rules.

This is concrete future `own` design pressure. No current indexed raw file
specifically owns identity-aware `own` surface transformation, so the concern
must receive a raw destination before `010` closes.

`partial` can add declarations to a programmer-owned identity without modifying
its closed intrinsic source:

```zax
MyIntegerExtension :: partial MyInteger {
  // Additional MyInteger behavior.
}
```

`partial` only adds. It cannot suppress, hide, restrict, remove, or replace an
exposed declaration. Any exposure filtering belongs to the original
identity/`own` mechanism. A partial declaration is another authorized piece of
the same type definition, not arbitrary mutation of a type owned elsewhere.
These requirements belong in the indexed
[partial-type input](../../raw/partial-types.md).

#### Ordinary enums do not inherit integer behavior

An ordinary integer-backed enum is conceptually comparable to an identity with
restricted admission and an opaque underlying surface:

```zax
// Mental-model comparison only; this is not enum declaration syntax.
MyClassification :: identity restricted opaque type Integer
```

The comparison explains independent identity, explicit raw adoption, and lack of
automatic integer behavior. An enum remains its own declaration construct with
named values, possible gaps or duplicate values, validity rules, and its
language-generated enum operations.

Ordinary enums do not automatically receive arithmetic, shifts, rotates,
increment/decrement, ordering, signedness conversion, or the complete bitwise
surface of their backing integer. A future flag enum may explicitly request a
selected surface. Endian semantic enums likewise receive only their deliberately
defined operations.

Use the plain statements “every backing integer value is valid” or “some backing
integer values are invalid” when that distinction matters. Do not promote the
earlier rejected umbrella label for that distinction. The fixed initiating input
remains immutable historical input; current and future teaching should use the
plain statements.

Complete enum validity, members, operation selection, and generation remain in
[raw enum input](../../raw/enum-types.md).

#### Native selection and integer factories

An exact integer constructor and a native integer selector answer different
questions:

- the built-in `Integer$(...)` family realizes an exact intrinsic specialization;
- an integer factory examines constraints and returns one such concrete type.

Illustratively:

```zax
MySelectedInteger :: alias type IntegerFactory.select(
  minimumBitCount: 13,
  maximumBitCount: 24,
  signedness: Sign.Signed,
  preference: IntegerPreference.Fastest,
  environment: Target
)
```

The exact factory, call, parameter, and type-result syntax remains future generic
work. The established input requirements are:

- minimum logical width;
- maximum logical width;
- signedness;
- preference such as fastest or smallest;
- active, target, or compiler-host environment;
- whether software fallback is permitted; and
- the fallback selection when no native representation satisfies the bounds.

Common aliases provide C++-like focused requests:

- `FastI<N>`/`FastU<N>` select the CPU-provider-preferred native representation
  with at least `N` logical bits;
- `LeastI<N>`/`LeastU<N>` select the smallest native representation with at least
  `N` logical bits;
- when no native representation satisfies a required minimum, the established
  fallback may select a software-represented exact integer.

No general smallest-at-most family is useful without a lower bound. Advanced
minimum/maximum combinations use the factory rather than target conditionals or
an exhaustive predefined alias matrix.

The selected CPU provider defines which scalar widths have a **native
representation** and ranks its general-purpose fastest choice. Native
representation says only that the CPU natively represents that width. It does
not promise native multiplication, division, atomics, or any other individual
operation.

For example, on a 6502-like target an eight-bit `Byte` may have a native
representation while a minimum-sixteen-bit `Integer` uses software composition.
Even the native eight-bit representation does not thereby gain a hardware
multiplication instruction.

The factory and associated relationships are required future input for
[type-parameter and generic work](../../raw/type-parameters-and-generics.md).

#### Associated integer types

An integer type exposes statically known related type identities through
type-receiver operations:

```zax
MyBitCount :: alias type MyNumericType bit count type
MyByteCount :: alias type MyNumericType byte count type
MyDelta :: alias type MyNumericType delta type
MyStorage :: alias type MyNumericType storage type
MyCounterpart :: alias type MyNumericType signedness counterpart type
```

These are operator phrases with type receivers. They execute no runtime
construction and must be available for type formation. Exact declarations and
their relationship to general associated types remain future generic and
reflection work.

`T bit count type` can represent every count from zero through `T`'s logical
width. It is also directly accepted by every applicable protected shift on `T`.
Ordinary types may select `BitCount`; extremely wide types may select
`LargeBitCount`.

```zax
MyCount :: alias type MyNumericType bit count type
leadingZeros : MyCount = #<myValue

myPosition := myValue trailing set bit position
if ?myPosition
  shifted := myValue << myPosition.
```

The optional set-bit position uses the same associated type even though its
nonempty range ends at `W - 1`. Generic code asks the numeric type for the
relationship and never branches manually between `BitCount` and
`LargeBitCount`.

`T byte count type` selects `ByteCount`, `LargeByteCount`, or another established
byte-count tier able to represent the required byte extent.

`T storage type` is the smallest byte-sized unsigned integer carrier large
enough for `T`'s complete storage envelope:

```text
I32 storage type -> U32
I57 storage type -> U64
U57 storage type -> U64
```

It names a carrier capacity, not a safe raw conversion and not necessarily an
alignment-equivalent type. Raw extraction still must disposition unspecified
padding and normalization.

`T signedness counterpart type` has the same logical width and opposite
signedness. It differs from `T storage type` for unusual signed widths:

```text
I57 signedness counterpart type -> U57
I57 storage type                -> U64
```

`T delta type` is signed and large enough to represent the exact mathematical
difference between any applicable pair of `T` values.

The ordinary `BitCount` storage size must not exceed `TypeSize` storage size.
Exact tier thresholds, the maximum wide tier, and whether `ByteCount` shares a
`TypeSize` identity remain open refinements.

#### Exact delta operation

A language-provided binary `delta` phrase requests the exact mathematical
difference without requiring source-level operand widening:

```zax
myDifference := myLeft delta myRight
```

For a full `W`-bit signed or unsigned value domain, the result may require
`W + 1` signed logical bits:

```text
minimum = -(2^W - 1)
maximum = +(2^W - 1)
```

This is deliberately different from ordinary subtraction, whose result remains
in the operand type and uses its selected panic/optional/wrapping/saturating/
reporting policy.

Pointer delta uses the same conceptual operation:

```zax
myDistance := myPointerA delta myPointerB
```

It promises no arithmetic overflow for a semantically valid pointer difference.
Pointer and lifetime work still decides which pointers are comparable, what
arena/allocation relationship is required, and how invalid subtraction reports
failure. The programmer-visible model does not require literally widening raw
pointer representations before subtraction.

Exact phrase declaration, precedence, protected signatures, and policy variants
remain operator-catalog refinement after the numeric result domains are settled.

#### Candidate integer catalog

The planned inventory is:

##### Exact intrinsic family

| Names | Purpose |
| --- | --- |
| `I8`/`U8`, `I16`/`U16`, `I32`/`U32`, `I64`/`U64`, `I128`/`U128` | Predefined exact logical widths |
| Programmer alias of `Integer$(W, Sign)` | Exact supported unusual width without predefining every `I<W>`/`U<W>` name |

##### Native-selection families

| Names | Purpose |
| --- | --- |
| `FastI8`/`FastU8` through `FastI128`/`FastU128` | Provider-preferred representation with at least the named width |
| `LeastI8`/`LeastU8` through `LeastI128`/`LeastU128` | Smallest native representation with at least the named width |
| Integer factory | Complete bounded selection dimensions and fallback |

##### Conventional CPU-relative family

| Names | Purpose |
| --- | --- |
| `Small`/`USmall` | Smallest conventional CPU-relative type, at least 8 bits |
| `Short`/`UShort` | Smaller conventional type, at least 16 bits |
| `Integer`/`UInteger` | General CPU-relative type, at least 16 bits |
| `Long`/`ULong` | Larger conventional type, at least 32 bits and no narrower than `Integer` |

`Longest`/`ULongest` are superseded by explicit maximum families.

##### Maximum families

| Names | Purpose |
| --- | --- |
| `NativeMaxI`/`NativeMaxU` | Widest scalar representation classified as native by the selected CPU provider |
| `SupportedMaxI`/`SupportedMaxU` | Widest exact specialization supported by the active toolchain version/configuration |
| `LanguageMaxI`/`LanguageMaxU` | Widest exact specialization permitted by the applicable Zax language contract |

Naming `LanguageMaxI` on a toolchain that does not implement it produces an
unsupported-language-capability diagnostic. `SupportedMaxI` intentionally
tracks the current toolchain and therefore may change identity when toolchain
capability changes.

##### Character-width integer family

| Names | Purpose |
| --- | --- |
| `Char`/`UChar` | Ordinary character-width signed/unsigned integer, minimum 8 bits |
| `WChar`/`UWChar` | Wide-character-width signed/unsigned integer, minimum 32 bits |

These names describe integer width roles. Unicode scalar validity and text
semantics remain separate from the integer catalog.

##### Machine-word family

All are unsigned:

| Name | Purpose |
| --- | --- |
| `Byte` | Exactly eight bits, with a familiar byte intent distinct from an arbitrary `U8` use |
| `HalfWord` | Half the selected natural word width, with minimum/rounding details still to refine |
| `Word` | Selected natural CPU word width |
| `DWord` | Twice the logical width of `Word` |
| `QWord` | Four times the logical width of `Word` |
| `OWord` | Eight times the logical width of `Word` |

The proposed bus-width aliases are discarded because a modern target may have
several unrelated buses and transfer widths with no one useful integer meaning.

##### Pointer-representation family

| Names | Purpose |
| --- | --- |
| `IPointer`/`UPointer` | Signed/unsigned integer representation associated with ordinary pointers |
| `NearIPointer`/`NearUPointer` | Corresponding near-pointer integer representation |
| `FarIPointer`/`FarUPointer` | Corresponding far-pointer integer representation |
| `PointerDelta` | Exact signed valid ordinary pointer difference |
| `NearPointerDelta` | Exact signed valid near-pointer difference |
| `FarPointerDelta` | Exact signed valid far-pointer difference |

`PointerDelta` naming is preferred over `DeltaPointer` because it reads as the
delta associated with pointers and composes with the near/far prefixes. Pointer
representation values remain integers; pointer objects retain their separate
validity, provenance, lifetime, and operation rules.

##### Count and storage-support family

| Names | Purpose |
| --- | --- |
| `BitCount`/`LargeBitCount` | Ordinary and extreme logical bit-count domains |
| `ByteCount`/`LargeByteCount` | Ordinary and extreme byte-count domains |
| `T bit count type` | Select the applicable count tier for `T` |
| `T byte count type` | Select the applicable byte-count tier for `T` |
| `T storage type` | Select an unsigned carrier for `T`'s storage envelope |
| `T delta type` | Select an exact signed difference type |
| `T signedness counterpart type` | Select the equal-width opposite-signedness type |

##### Size and index family

| Names | Purpose |
| --- | --- |
| `TypeSize`, `NearTypeSize`, `FarTypeSize` | Every valid type size and ordinary allocation request in the corresponding memory domain |
| `TypeSizeDelta`, `NearTypeSizeDelta`, `FarTypeSizeDelta` | Exact signed differences between those size values |
| `IndexSize`, `NearIndexSize`, `FarIndexSize` | Element counts, valid positions, and permitted one-past endpoints in the corresponding domain |
| `IndexSizeDelta`, `NearIndexSizeDelta`, `FarIndexSizeDelta` | Exact signed differences between those index values |

Every valid ordinary allocation request in an address-space domain must fit its
corresponding `TypeSize`. `IndexSize` must represent valid positions, counts, and
any materialized one-past endpoint; allocation maxima must leave enough range or
represent one-past state separately.

`ByteCount` and `TypeSize` provisionally share one useful intent branch unless
concrete allocation pressure demonstrates different valid domains.

##### Environment qualification

Every environment-relative family has an active spelling and explicit target and
compiler-host routes:

```zax
Integer
Target.Integer
CompilerHost.Integer
```

The pattern applies to native selectors, conventional types, machine words,
maximum-native types, pointer-related sizes, and other context-selected names.

##### Deliberately separate semantic families

Enums, Unicode scalar types, UUIDs, resource handles, and endian types may use
integer storage or the identity mechanism but do not become exposed fundamental
integer aliases merely because of that representation.

No other major scalar integer-selection family is currently known to be missing.
Future design pressure may refine or remove names rather than preserving this
inventory by inertia.

#### Integer-specific reflection requirements

Future integer reflection must make these facts discoverable:

- logical bit width and signedness;
- minimum and maximum values;
- signedness counterpart type;
- bit-count, byte-count, delta, and storage associated types;
- storage-envelope byte count and non-value padding extent;
- required alignment;
- exact, native, or software representation classification;
- requested native-selection constraints;
- selected width, environment, and fallback;
- intrinsic representation specialization;
- transparent alias and intent-identity relationships;
- admission and exposure choices;
- immediate projection type;
- applicable protected operation families; and
- endian eligibility and padding-normalization requirements.

Native representation metadata says only that the CPU provider classifies the
width as natively represented. Per-operation instruction availability and cost
remain separate facts.

These requirements belong in [raw reflection input](../../raw/reflection.md) and
the eventual integer owner. Future reflection work decides its metadata shape
and whether any individual fact earns a convenient phrase operation.

#### Documentation findings

Programmer-facing integer teaching should use meaningful custom names:

```zax
MyInteger
myValue
MyDocumentHandle
```

`My...`/`my...` helps distinguish programmer-defined declarations from
language-provided names when that distinction matters. Alphabetic placeholders
such as `AInteger` and `BInteger` belong only in one compact comparison where
their adjacency is itself being taught; standalone and reusable examples use
meaningful role names.

Candidate examples follow established Zax grammatical categories and ordering:

```zax
myValue final : MyType immutable
myView final : MyType immutable readonly varying & = myValue
```

Declaration keywords remain declaration keywords, operator phrases remain
operator phrases, and qualifiers remain in the established position for the
construct they qualify. Candidate source does not invent punctuation or
compound pseudo-keywords that conflict with Zax token conventions.

Do not omit a source choice merely to shorten an example when omission would
obscure the intent or boundary being taught. When syntax remains unsettled,
preserve accepted surrounding grammar, invent only the unresolved portion, mark
it illustrative, and keep it internally consistent.

These are reusable documentation refinements for a later authorized update to
the documentation architecture. Conceptual `own` expansion and exact deferred
mechanics stay with their feature inputs rather than becoming scenario-specific
general documentation rules.

#### Aligned deferrals and capture destinations

| Deferred concern | Live destination | Activation pressure | Constraint carried from `010` |
| --- | --- | --- | --- |
| Exact `Integer$(...)` and integer-factory syntax | [Raw type-parameter and generic input](../../raw/type-parameters-and-generics.md) | Generic integer definitions or native selection are designed | Every realized specialization is concrete and closed; the factory returns one exact specialization from explicit dimensions |
| Identity-aware `own` surface transformation | [Raw owned-composition input](../../raw/owned-composition.md) | Composition, `own`, identity types, or automatic surface exposure is reviewed | Self-type signatures require meaning-preserving substitution; admission and arbitrary conversions are excluded |
| Add-only partial identity definitions | [Raw partial-type input](../../raw/partial-types.md) | Partial ownership and extension are reviewed | `Name :: partial ExistingType`; partial adds only and cannot suppress exposed behavior |
| Integer-specific metadata | [Raw reflection input](../../raw/reflection.md) | Integer reflection or type metadata is reviewed | Preserve the complete metadata list above without prematurely reserving a phrase for every fact |
| Compile-time unrealized integer results | [Raw compile-time input](../../raw/compile-time-execution.md) and [raw literal input](../../raw/literal-operators.md) | Typed compile-time results or literal realization are designed | Direct realization is allowed; ordinary concretely typed constants do not gain implicit conversion |
| Enum validity and selected operation surfaces | [Raw enum input](../../raw/enum-types.md) | Enum validity, flags, generation, or operation selection is reviewed | Integer backing does not grant arithmetic; use plain validity statements rather than the rejected umbrella label |
| Index and one-past behavior | [Raw indexing input](../../raw/indexing-and-slicing.md) | Indexing, slicing, container sizes, or endpoints are designed | Index types represent applicable counts, positions, and materialized endpoints; delta is exact |
| Pointer representations and valid delta | [Raw lifetime input](../../raw/lifetimes.md) | Pointer arithmetic, provenance, near/far memory, or lifetime domains are designed | Pointer delta cannot overflow for a valid difference but does not make arbitrary pointers comparable |
| Associated numeric types and extreme widths | [Raw numeric-family input](../../raw/numeric-type-families.md) | Custom finite integers, extreme widths, or associated numeric relationships are designed | Bit/byte count, delta, storage, and counterpart types are statically discoverable per numeric type |
| CPU profiles and native selection | [Raw CPU-provider input](../../raw/cpu-provider-model.md) | Native representations, machine-word families, target/compiler-host profiles, or mapping compatibility are designed | Profiles satisfy language range/capacity facts; native representation does not promise individual instruction support |
| Endian identity and unusual-width normalization | [Zax endianness](../../../language/endianness.md) plus applicable future enum/numeric work | Endian generic generation or unusual-width exchange is reviewed | Intent identity may survive endian wrapping; deterministic exchange normalizes non-value storage |
| ABI and foreign correspondence | Future interoperability work | A real foreign boundary requires exact integer correspondence | Exact Zax width and representation do not alone promise a foreign ABI type |

The identity-aware `own` and CPU-provider pressures now have indexed raw
destinations and do not depend on this active record as their only live source.

#### Later aligned refinements

These findings supersede conflicting details earlier in the working record.

##### Canonical namespace and environment paths

The canonical integer catalog is anchored under:

```zax
Scalars.Integers.I32
Scalars.Integers.Target.Integer
Scalars.Integers.CompilerHost.Integer
```

`Scalars` leaves room for other scalar families without presuming their design.
Root names such as `I32` and `Integer`, and concise paths such as
`Target.Integer`, are transparent prelude aliases or imports. They do not create
another identity or another documentation owner.

Memory-domain namespaces compose after the environment:

```zax
Scalars.Integers.Near.TypeSize
Scalars.Integers.Target.Near.TypeSize
Scalars.Integers.CompilerHost.Far.UPointer
```

The canonical qualifier order is scalar family, integer family, execution
environment when explicit, memory domain when explicit, then concrete role.

##### Public identity classification

Exact `I<W>`/`U<W>` names are transparent public names for exact intrinsic
`Integer$(W, Sign)` specializations.

The following public role families establish `identity admit expose` types over
the exact specialization selected by their family rule or the integer factory:

- `FastI<N>`/`FastU<N>` and `LeastI<N>`/`LeastU<N>`;
- `Small`/`USmall`, `Short`/`UShort`, `Integer`/`UInteger`,
  `Long`/`ULong`, and `LongLong`/`ULongLong`;
- `Char`/`UChar` and `WChar`/`UWChar`;
- `Byte`, `HalfWord`, `Word`, `DWord`, `QWord`, and `OWord`;
- `NativeMaxI`/`NativeMaxU`, `SupportedMaxI`/`SupportedMaxU`, and
  `LanguageMaxI`/`LanguageMaxU`;
- `BitCount`, `LargeBitCount`, `ByteCount`, and `LargeByteCount`;
- ordinary, near, and far pointer-representation integers;
- ordinary, near, and far type-size and index-size types; and
- their named delta types.

The identities stay distinct when two role selectors choose the same exact
specialization. Semantic types such as enums, resource handles, UUIDs, and
Unicode scalar values do not receive an exposed integer surface merely because
they use integer storage.

The conventional family includes:

```text
Small <= Short <= Integer <= Long <= LongLong
LongLong width >= 64
ULongLong width = LongLong width
```

`Longest`/`ULongest` remain superseded by the explicit maximum families.

##### Range facts and conversion availability

Integer conversion availability follows declared type and family range facts,
not accidental equality in one CPU profile and not unconstrained whole-program
analysis.

The integer model records exact facts and symbolic requirements such as:

```text
U8 range is contained by U16
Byte has exactly the U8 value range
USmall width <= UInteger width
FastU16 width >= 16
```

A CPU profile supplies concrete selections satisfying those contracts. A
stronger selected width may eliminate a check but does not ordinarily make a
portable source form appear or disappear.

Compile-time-known values may prove an explicit conversion valid. General
flow-sensitive range proofs remain future analysis and may optimize an optional
path without changing baseline source validity.

Numeric conversion is available only inside the intrinsic integer family and
through identities whose integer surface is exposed.

For exact intrinsic integers:

```zax
myWide := myU16 as U32
myOptional := myU16 as U8?
myNarrow := myU16 narrowing as U8
```

- `as Destination` is available when the source contract or a compile-time-known
  value proves exact representability;
- `as Destination?` returns present when exact conversion succeeds and absent
  otherwise;
- `narrowing as Destination` performs the established defined modular
  conversion and does not panic for integer range;
- `unsafe as` is not ordinary numeric narrowing and remains for conversions that
  actually weaken language guarantees.

The optional behavior is a protected integer conversion to an optional integer
destination, not a general rule for every `as OptionalType`.

Admission into an `admit` identity uses the destination type receiver:

```zax
myLong := Long from myI32
mySmall := Small optional from myU16
myNarrowSmall := Small narrowing from myU16
```

- `from` exists when declared family range facts guarantee exact admission;
- `optional from` returns absent rather than losing information or panicking;
- `narrowing from` performs the defined modular conversion before establishing
  an identity for which every resulting underlying value is admissible.

A `restricted` identity receives no automatic ordinary or narrowing admission.
It may declare:

```zax
myValue := MyIdentity optional from myRaw
myValue := MyIdentity unchecked from myRaw
myValue := MyIdentity unsafe from myRaw
```

- `optional from` validates and returns absent on rejection;
- `unchecked from` skips semantic validation but has defined mechanical
  behavior and cannot by itself permit memory, lifetime, representation, or
  optimizer-invalid state;
- `unsafe from` bypasses an invariant on which such language guarantees may
  rely.

The exact boundary between unchecked logic validity and true unsafe behavior
remains future safety work. Existing enum and endian `unsafe from` terminology
must be reconsidered under that distinction rather than changed silently here.

An exposed integer identity mirrors the exact, optional, and narrowing
conversion surface when projecting to exact intrinsic integers. An opaque
identity receives only projection to its immediate underlying type; any further
numeric conversion is another explicit expression.

No direct identity-to-identity conversion is generated merely from equal
representation. Source projection and destination admission remain separate:

```zax
myFast := FastU16 from (mySmall as U16)
myFile := MyFileHandle unchecked from (myDocument as Integer)
```

A direct bridge between distinct identity families requires both:

1. a declared semantic relationship authorizing it; and
2. a range fact proving every source value fits the destination.

This permits the protected, exact bridges:

```zax
mySize : TypeSize = myIndex as TypeSize
myIndex : IndexSize = mySize as IndexSize
```

while equal representations never connect unrelated handles.

Programmer documentation should teach conversions by asking:

1. whether source and destination share one identity branch;
2. whether their family exposes numeric conversion or declares a bridge;
3. whether declared range facts guarantee containment; and
4. which explicit optional, narrowing, unchecked, or unsafe policy applies when
   they do not.

Language-owned or compiler-generated partial definitions may provide protected
exact-family conversions. Whether CPU providers or programmer code may add
partial intrinsic functionality remains deferred to partial-type work.

##### Diagnostics and canonical type presentation

Diagnostics lead with the type name written by the programmer or the most useful
public catalog alias:

```text
cannot convert U16 to U8 without an explicit narrowing policy
```

Expanded detail may show:

```text
public type: U8
canonical path: Scalars.Integers.U8
intrinsic specialization: Integer$(8, Sign.Unsigned)
```

The generic specialization remains available to tooling and detailed
diagnostics but does not replace readable public vocabulary in ordinary errors.
A transparent user alias may be shown as `MyCount (alias of U16)`.

This presentation does not change `type of` or canonical identity. It preserves
the source spelling needed to understand an error while making the underlying
identity and selector available on demand.

##### Required arithmetic and the unchecked build contract

Required integer arithmetic retains exact-result-or-panic semantics independent
of debug or release optimization mode.

A separate explicit compiler/build policy may remove required runtime overflow
checks by changing the contract:

> Every required arithmetic result is representable by its result type.

When the contract holds, the operation produces the exact mathematical result.
When it is violated, Zax makes no behavioral promise. The result is not
implicitly wrapping and may be affected by any optimization valid under the
assumption that overflow never occurs.

The policy:

- is explicit and independent from optimization level;
- shifts proof responsibility to the programmer;
- is recorded in build metadata and exposed to tooling;
- never changes optional, wrapping, saturating, reporting, or narrowing forms;
- does not suppress a compile-time diagnostic for provable contract violation;
  and
- permits proven-unnecessary checks to be removed in every build regardless.

The exact option name, source/project/invocation placement, and scope over
division-by-zero, signed division edge cases, magnitude, increment/decrement,
and other panic sources remain analysis-control work. The aligned contract here
specifically covers representability overflow in required integer arithmetic.

##### Relational signedness pairs

The exact intrinsic family automatically obtains its equal-width counterpart by
changing only the signedness dimension:

```text
Integer$(W, Signed) <-> Integer$(W, Unsigned)
```

Public role identities do not infer counterpart names. The integer factory or
another validated generic mechanism defines paired identities relationally:

```text
Small <-> USmall
FastI16 <-> FastU16
IPointer <-> UPointer
LongLong <-> ULongLong
```

One paired definition:

1. applies one shared selector except for signedness;
2. realizes equal-width intrinsic counterpart specializations;
3. establishes both public intent identities;
4. records them as mutual counterparts; and
5. generates protected counterpart operations.

Future programmers should be able to use the same owner-authorized mechanism
for pairs they define together. The compiler validates equal logical width,
opposite signedness, mutual and unique pairing, and ownership. No declaration
overrides the intrinsic counterpart of a closed exact specialization or pairs a
type owned elsewhere.

One-sided roles such as `Word`, `Byte`, and `BitCount` receive no named intent
counterpart. Their counterpart relationship returns the unnamed exact intrinsic
specialization and therefore intentionally leaves the role identity. Restricted
or opaque semantic identities receive no counterpart merely from integer
storage.

##### Machine words and platform-selected counts

The CPU provider selects `Word` width `W`. Zax derives the remaining machine-word
widths exactly:

```text
HalfWord = ceil(W / 2)
DWord    = W * 2
QWord    = W * 4
OWord    = W * 8
```

A provider cannot assign another meaning to these names. A derived width without
a native representation uses the exact software-represented specialization. A
width above `LanguageMaxU` is unavailable for that profile.

The CPU provider selects ordinary count representations subject to language
capacity rules:

- `BitCount` is unsigned, preferably native, represents every predefined
  ordinary scalar width in the profile, represents at least `0..128`, and is no
  larger in storage than `TypeSize`;
- `ByteCount` is unsigned, preferably native, and represents every predefined
  ordinary scalar storage envelope in the profile;
- `LargeBitCount` represents `0..LanguageMaximumIntegerWidth`;
- `LargeByteCount` represents
  `0..ceil(LanguageMaximumIntegerWidth / 8)`.

Associated selection is capacity-based:

```text
T bit count type =
  BitCount      when T.logicalWidth <= BitCount.maximum
  LargeBitCount otherwise

T byte count type =
  ByteCount      when T.storageBytes <= ByteCount.maximum
  LargeByteCount otherwise
```

Applicable protected shifts directly accept either tier. The four public count
types remain distinct intent identities even when a profile selects equal
underlying representations.

##### Maximum integer width

The initial language-wide maximum logical integer width is:

```text
2^23 bits = 8,388,608 bits = 1,048,576 bytes
```

This is an intentional Zax language-version contract chosen within the known
feasibility envelope, not a promise to use one backend representation.

Consequently:

- `LargeBitCount` needs at least 24 unsigned logical bits;
- `LargeByteCount` needs at least 21 unsigned logical bits;
- a complete toolchain for the language version supports every exact width
  through `2^23`;
- an incomplete toolchain may report its unsupported valid-language capability;
  and
- increasing the limit changes `LanguageMaxI`/`LanguageMaxU` identity and is a
  language-version compatibility event.

##### Unusual-width enums, endian eligibility, and raw storage

An arbitrary exact width such as `I57` may back an ordinary enum. Enum
`underlying value` returns the logical `I57`, not its `U64` storage carrier.

Endian families are available for exact integer specializations whose logical
width is a whole number of bytes and whose storage has no non-value bits:

```text
I24 -> endian eligible
U40 -> endian eligible
I57 -> not endian eligible
```

Eligibility is not limited to predefined short names.

Zax provides no raw storage extraction operation for `I57`. Unsafe pointer and
raw-memory behavior remains pointer work. Because `I57` has no endian family and
no raw extraction operation, its seven non-value bits have no normalized
big/little-endian placement.

##### `delta` and `distance`

`delta` and `distance` are eager, left-associative language-defined binary phrase
operators at additive precedence.

```zax
myDelta := myLeft delta myRight
myDistance := myLeft distance myRight
```

`delta`:

- requires the same integer identity on both operands;
- returns `T delta type`;
- produces exact mathematical `left - right`;
- has no wrapping, saturating, optional, reporting, compound, or mutation form;
  and
- is unavailable when no language-supported result type covers
  `-(2^W - 1)..+(2^W - 1)`.

The CPU profile selects the preferred signed result width satisfying that hard
range, commonly the next native or byte-oriented size rather than exact `W + 1`.
`delta` is unavailable for a maximum-width integer because its exact result
domain exceeds the language maximum.

`distance`:

- requires the same integer identity on both operands;
- returns `T distance type`;
- produces exact `abs(left - right)` without intermediate overflow;
- uses an unsigned result able to represent `0..2^W - 1`;
- remains available at the language maximum; and
- has no policy or mutation forms.

Pointer delta/distance retain separate comparability and validity requirements
for future pointer work.

##### Size, index, and memory-domain relationships

`TypeSize`, `IndexSize`, and pointer-representation integer types are freely
admitting capacity identities. Actual allocation, bounds, pointer validity, and
provenance are checked by consuming operations.

Within each ordinary, near, or far memory domain:

```text
IndexSize.maximum = TypeSize.maximum
IndexSize representation = TypeSize representation
```

They remain different intent identities. Their declared equal-range relationship
provides safe direct `as` bridges in both directions.

For a collection of length `L`, valid positions are `0..L-1` and valid
slice/splice endpoints are `0..L`. Since the type already represents `L`, no
separate splice-index type or extra one-past capacity is needed.

CPU profiles satisfy:

```text
Near pointer capacity <= ordinary pointer capacity <= Far pointer capacity
Near.TypeSize.maximum <= TypeSize.maximum <= Far.TypeSize.maximum
Near.IndexSize.maximum <= IndexSize.maximum <= Far.IndexSize.maximum
```

When near/far representations mirror the ordinary domain, their intent
identities remain distinct rather than becoming target-dependent transparent
aliases. Integer-representation transfer uses the applicable exact, optional,
or narrowing admission. Actual pointer-object transfer remains future pointer
work because numeric capacity does not establish tags, segments, provenance,
permissions, or lifetime validity.

#### Remaining refinements before dry run

No integer-model alignment remains open.

Two independent compiler/build settings are off by default:

1. **Unchecked required-arithmetic overflow** replaces runtime
   representability checks with the contract that every required arithmetic
   result is representable by its result type. It covers required addition,
   subtraction, and multiplication value/compound forms, increment/decrement,
   minimum signed negation or magnitude, and signed minimum divided by `-1`.
2. **Unchecked nonzero divisors** replaces required division/remainder
   zero checks with the contract that every divisor supplied to required `/`,
   `%`, `/=`, or `%=` is nonzero.

When either contract holds, the selected operation produces its ordinary exact
result. A contract violation has no promised behavior and is not implicitly
wrapping. Compile-time-known violation remains a diagnostic. Optional,
wrapping, saturating, reporting, and narrowing forms retain their defined
behavior regardless of either setting.

The settings are independent from debug/release optimization mode and from each
other. Their exact option names, source/project/build placement, and future
source-local equivalents remain analysis-control work.

#### Audit-backed promotion ownership map

The language maintainer authorized direct promotion after the alignment-capture
audit rather than performing the missed documentation-fit dry run. This map
records the complete intended integration set; it is not a claim that the dry
run occurred.

| Concern | Lasting owner or disposition |
| --- | --- |
| Fundamental integer model, catalog, ranges, representation, environment-relative names, counts, conversions, arithmetic contracts, `delta`, `distance`, costs, and diagnostics | New dedicated integer owner |
| Transparent aliases, identity declarations, admission, projection, exposure, opacity, and declared bridges | New dedicated identity-type owner |
| Exact conversion/admission phrases, `delta`/`distance`, precedence, protected signatures, arithmetic policies, and compact count/shift behavior | Operator catalog |
| Shared protected-domain, diagnostic, cost, and source-stability effects | Operators |
| Identity and alias non-value declaration integration | Declarations and bindings |
| Whole-byte/no-padding endian eligibility and removal of rejected terminology | Endianness |
| Cross-cutting vocabulary for identity, admission, native representation, and execution environments | Language-design terms |
| Current-owner discovery | Website index and vision maturity route |
| Reusable example-quality findings | Documentation architecture |
| Legacy intrinsic and conversion evidence | `basics.md` and `casting.md` disposition notes linking current owners |
| CPU-provider mapping, `own`, partial, generics, reflection, unusual-width mechanics, enum admission, compile-time realization, indexing, pointer validity, analysis controls, and safety | Indexed raw inputs retained until their focused work |

The promotion must not turn deferred generic, CPU-provider, `own`, partial,
reflection, pointer, enum, literal, or build-option syntax into accepted source
forms. Current owners teach the established programmer-visible constraints and
route only concrete dependencies.

#### Alignment-capture audit after premature raw edits

| Field | Value |
| --- | --- |
| Date | 2026-08-30 |
| Scope | The staged `010` findings, every later alignment in the review conversation, and every unstaged/untracked raw capture created before the required documentation-fit dry run |
| Result | **PASS for alignment-capture fidelity; the later two-setting arithmetic alignment closes the final semantic question** |
| Not Performed | This was not the pre-promotion documentation-fit dry run and does not authorize promotion, staging, or further edits |

This audit was required because raw-owner and structural edits were made before
the documentation-fit dry run that should have proposed and tested them. It
checks whether those edits faithfully preserve the reviewed findings; it does
not retroactively make the missed dry run occur.

The audit compared the post-staging conversation with the current aligned
sections of this file. It found one omitted aligned finding: ordinary diagnostics
prefer public/source alias names and show canonical generic specializations only
as expanded detail. That finding is now recorded immediately above.

The newest aligned findings are otherwise present here:

- canonical `Scalars.Integers` paths and environment/memory-domain ordering;
- the complete public identity classification, including
  `LongLong`/`ULongLong`;
- declared range facts and the exact/optional/narrowing conversion matrix;
- `unchecked from` between validated admission and true unsafe admission;
- explicit identity bridges such as `IndexSize`/`TypeSize`;
- the unchecked required-arithmetic contract and its no-promised-behavior
  violation;
- relational factory-defined signedness pairs and unnamed one-sided
  counterparts;
- machine-word formulas and platform-selected count tiers;
- the `2^23` language maximum;
- unusual-width enum, endian, and raw-storage boundaries;
- `delta` and `distance`; and
- ordinary/near/far size, index, pointer-capacity, and transfer relationships.

The raw-capture ownership audit found:

| Future concern | Captured destination | Audit result |
| --- | --- | --- |
| Intrinsic and identity partial authority | [Partial-type input](../../raw/partial-types.md) | Correctly preserves language/compiler/provider/programmer provenance, protected signatures, and add-only partial behavior |
| Exact generic specializations, integer factory, relational pairs, and associated types | [Type-parameter and generic input](../../raw/type-parameters-and-generics.md) | Correctly deferred without presenting illustrative factory syntax as accepted |
| Integer-specific metadata and diagnostic presentation | [Reflection input](../../raw/reflection.md) | Complete enough to recover the aligned metadata model without reserving a phrase for every fact |
| Count tiers, conversions, storage, counterpart relationships, `delta`, and `distance` | [Numeric-family input](../../raw/numeric-type-families.md) | Correctly preserves the numeric mechanics and their generic dependencies |
| Enum admission classification and unusual-width backing | [Enum input](../../raw/enum-types.md) | Correctly preserves future `unchecked`/`unsafe` review and excludes padded widths from endian generation |
| Deferred compile-time integer realization | [Compile-time input](../../raw/compile-time-execution.md) and [literal input](../../raw/literal-operators.md) | Correctly distinguishes unrealized results from already concrete constant integers |
| Index capacity and endpoints | [Indexing input](../../raw/indexing-and-slicing.md) | Correctly records equal `IndexSize`/`TypeSize` representation and no separate splice-index requirement |
| Pointer capacity, transfer, and valid difference | [Lifetime input](../../raw/lifetimes.md) | Correctly separates numeric representation transfer from pointer validity and provenance |
| Unchecked arithmetic build contract | [Analysis-control input](../../raw/analysis-controls.md) and [safety input](../../raw/safety.md) | Correctly records no-promised-behavior violation and retains the unresolved scope question |
| CPU-provider mapping | [CPU-provider input](../../raw/cpu-provider-model.md) | Indexed placeholder has a bounded role, concrete activation pressure, constraints, and retirement path |
| Identity-aware owned composition | [Owned-composition input](../../raw/owned-composition.md) | Indexed placeholder captures signature substitution, conversion exclusions, partial boundaries, and retirement |

The two new raw placeholders satisfy the raw-input requirements at the depth
available now: each identifies why future work is needed, what input must not be
lost, what it does not decide, what activates it, and how it retires. Both are
indexed by the raw router.

No current programmer-facing owner was edited. The rejected umbrella label
remains only in immutable initiating input within this file; mutable current
work and raw captures use plain validity statements.

The audit found no raw capture that claims accepted language authority, no
unindexed new raw file, no broken local link, and no deferred finding whose only
remaining home is this active record.

The future documentation-fit dry run must still independently decide:

- whether each raw capture remains, moves, combines, or retires;
- the lasting owner for identity and integer teaching;
- every affected current and legacy file;
- navigation and terminology changes;
- the exact promotion and raw-capture change set; and
- whether the proposed structure teaches the aligned design coherently.

This audit therefore validates preservation of evidence and alignment only. It
does not claim that the premature raw edits are the structure the dry run will
approve.

### Initial reconstruction retained for comparison

The sections from **Review entry point** through **Likely lasting documentation
fit** are the staged initial reconstruction. They remain raw candidate history.
The aligned findings above supersede them wherever they differ.

### Review entry point

The strongest recoverable core is a family of closed, finite integer types whose
logical width, signedness, value range, and bit behavior are knowable from the
type. The current operator catalog already depends on that model:

```zax
maximum : U8 = 255
one : U8 = 1

wrapped := maximum +% one      // candidate result: U8(0)
leadingZeros := #<maximum      // candidate result: BitCount(0)
lowBit := #~>maximum           // U8(1)

position := maximum trailing set bit position
if ?position
  shifted := one << position.  // the position payload is a valid shift count
```

The most consequential unresolved question is not the existence of `I8` through
`I128` and `U8` through `U128`. It is what `Integer` and `UInteger` mean.
Legacy material calls them aliases or templates selected for the fastest CPU
type, while current owners require every selected expression to have one
concrete static type and place a high value on source stability.

These alternatives have materially different results:

1. `Integer` is an identity-preserving alias of one fixed type such as `I64`.
   Its range and identity are portable, but the name no longer means
   CPU-natural.
2. `Integer` is a distinct target-selected intrinsic type. Its identity remains
   `Integer` even when its current representation matches `I32` or `I64`, but
   overflow and layout may vary by target.
3. `Integer` is a target-selected alias of `I32`, `I64`, or another fixed type.
   This preserves the strongest legacy alias reading, but changes overload
   identity, `type of`, and possibly source validity when the selected target
   changes.
4. Zax has no concrete default `Integer`; uncommitted literals remain unrealized
   until context supplies a fixed type. This maximizes portability but makes
   inferred declarations and context-free expressions substantially harder to
   explain.

The provisional model below uses alternative 2 so the rest of the design can
be tested. That is a review device, not a recommendation or decision.

```zax
natural : Integer = 1
fixed : I32 = 1

mixed := natural + fixed
// candidate error: distinct integer types do not silently promote
```

The second high-impact question is representation. Signed bitwise operations,
sign-filling right shift, wrapping, raw storage, endian types, signed set-bit
masks, and deterministic truncation all become simpler and more predictable if
fundamental signed integers require two's-complement representation with no
value padding. Leaving representation target-defined would preserve more target
freedom but weaken the low-level programmer model in every one of those areas.

### Candidate programmer model

#### Vocabulary

The following distinctions appear to fit the current owners:

- A **fundamental integer type** is a language-defined scalar integer identity
  with a closed value and operation domain. It can participate in protected
  intrinsic signatures and, when representation requirements are met, can back
  an enum.
- **Intrinsic** describes behavior the language and toolchain must provide for a
  fundamental type. It does not promise an instruction, lowering, runtime
  helper, or other implementation technique.
- **Language-provided** is broader. A semantic enum, alias, or future library
  type may be supplied with Zax without becoming a fundamental integer.
- An **alias** is another source name for the same canonical type identity. It
  does not create another overload domain, range, representation, or conversion
  boundary.
- A **custom integer-like type** is an independent type even when its storage
  resembles a fundamental integer. It owns ordinary custom operations and does
  not acquire a protected intrinsic domain merely from compatible storage.

This distinction prevents the word `intrinsic` from conflating type identity,
required operation availability, and compiler implementation.

#### Canonical fixed-width family

The initial candidate canonical family is:

| Signed | Unsigned | Logical width |
| --- | --- | --- |
| `I8` | `U8` | 8 bits |
| `I16` | `U16` | 16 bits |
| `I32` | `U32` | 32 bits |
| `I64` | `U64` | 64 bits |
| `I128` | `U128` | 128 bits |

For logical width `W`:

- `UW` has values `0` through `2^W - 1`;
- `IW` has values `-2^(W-1)` through `2^(W-1) - 1`;
- every one of the `W` bits participates in the value representation;
- every `W`-bit pattern is valid;
- signed representation is two's complement;
- zero has the all-zero representation in both families; and
- the signed and unsigned forms are equal-width counterparts but remain
  different type identities.

The candidate makes an addressable byte an eight-bit byte for these names.
Their exact instance sizes are therefore 1, 2, 4, 8, and 16 bytes. They have no
value or storage padding. Alignment remains a target property reported by
`alignment of`; equal width or size does not imply one portable alignment
value.

This deliberately separates:

- **logical bit extent**, which controls value range and every bit operation;
- **instance size**, which measures occupied bytes;
- **alignment**, which constrains placement;
- **allocation capacity**, which belongs to a containing allocation; and
- **external ABI layout**, which requires its own contract.

Hidden register width, allocation slack, and container padding never extend an
integer's logical bit domain.

#### Provisional CPU-natural pair

For review, `Integer` and `UInteger` are modeled as a distinct equal-width pair:

- the target or active semantic context selects one exact finite width;
- the width is at least 16 bits, preserving the legacy minimum;
- the signed type is two's complement and the unsigned type is its equal-width
  counterpart;
- both have the same no-padding representation guarantees as the canonical
  fixed family;
- their canonical identities remain `Integer` and `UInteger`, even when their
  current width, size, and alignment equal a fixed-width type; and
- conversion between `Integer` and an equal-layout `I32` or `I64` remains an
  actual type conversion.

The selection rule cannot merely say "fastest CPU type." It must eventually
identify a deterministic target property, because different toolchains could
otherwise choose different widths for the same target.

One source instance must not change `Integer` width partway through type
checking or evaluation. If compiler-host execution and target-context
evaluation need different natural widths, they require context-pinned
instantiations and an explicit boundary between them. The compile-time
execution design must decide how source requests each context and how values
cross that boundary. `native`, `host`, and `target` are not synonyms.

This provisional pair is the main risk to source stability:

```zax
value : Integer = 32767
one : Integer = 1
next := value + one
```

The same source can succeed on one selected width and panic on another. Fixed
types should therefore be the normal choice for stored formats, protocols,
cross-target constants, and code whose overflow boundary is part of its
meaning.

#### Pointer- and size-related integers

Legacy material proposes `UPointer`, `SPointer`, `SLongPointer`, and `TypeSize`.
The current operator catalog already reserves an applicable signed or unsigned
protected integer family for pointer-sized numeric integers, but it does not
settle these exact identities.

The useful constraints recovered here are:

- `UPointer` must represent the complete pointer representation domain required
  for supported integer/pointer round trips;
- `SPointer` must have the same storage width as `UPointer` and represent
  permitted pointer differences within one memory arena;
- a type that promises conversion from every `UPointer` value without panic
  needs more positive range than the equal-width signed counterpart, which is
  the motivation for `SLongPointer`;
- `TypeSize` must represent every supported type size and ordinary requested
  allocation size; and
- sharing width or layout with a canonical fixed type must not silently decide
  alias identity or conversion permission.

The candidate does not yet add these four names to the canonical catalog.
Pointer representation, maximum object size, unsupported targets, and whether
these are distinct semantic identities or aliases must be resolved together.
In particular, a 128-bit `UPointer` would need a signed exact-value destination
wider than the current `I128` maximum, so the legacy `SLongPointer` promise
cannot be accepted casually.

#### Alias and semantic-name candidates

The fixed family should have one canonical identity per width and signedness.
The following is a candidate disposition of legacy names:

| Legacy name(s) | Candidate treatment | Reason |
| --- | --- | --- |
| `Byte` | Identity-preserving alias of `U8` | Familiar octet spelling with no new numeric semantics |
| `DByte`, `QByte`, `OByte` | Do not include initially | They duplicate fixed widths with less direct names |
| `FastI8`...`FastI128`, `FastU8`...`FastU128` | Defer or omit | Target-selected aliases reproduce the `Integer` identity and stability problem at every minimum width |
| `Small`, `Short`, `Long`, `Longest` and unsigned partners | Do not include initially | Their relative CPU descriptions overlap the exact and natural families without a stable cross-target contract |
| `Word`, `DWord`, `QWord` | Defer to hardware/target modeling | "Natural word" and doubled capacity need exact target rules and unsupported-target behavior |
| `Char`, `WChar`, `UChar`, `UWChar` | Do not treat as integer aliases | Character storage and code-unit semantics should decide distinct identities and operations |
| `Rune` | Do not treat as an alias of `UWChar` | A Unicode scalar-value domain is not merely an unsigned integer width |
| `UUID` | Do not treat as an alias of `U128` | UUID validity, formatting, ordering, and operations are a semantic type concern |

This is intentionally stricter than the legacy catalog. An alias is
cost-free, but it also grants every operation of its target and cannot enforce
domain distinctions. A semantic name should become an independent type or stay
outside the language rather than use an alias to imply safety it does not
provide.

### Integer operations on the candidate model

#### Operand domains and results

Protected binary arithmetic, comparison, and bitwise operations should require
both value operands to have the same canonical fundamental integer type unless
an exact family explicitly says otherwise. There is no C-style usual arithmetic
conversion:

```zax
small : U8 = 1
wide : U16 = 2

sum := small + wide
// candidate error: choose and state the destination width

sum16 := (small as U16) + wide
```

This keeps overload selection, range, policy, and result identity visible. It
also means an alias of `U8` participates as `U8`, while an independent wrapper
does not.

For a same-type operand `T`, the current catalog implies:

| Family | Candidate built-in result |
| --- | --- |
| `+`, `-`, `*`, `/`, `%` and their value policies | `T`, `T?`, or a report containing `T` according to the written policy |
| `==`, `!=`, `<`, `<=`, `>`, `>=` | `Boolean` |
| `~`, `&`, `\|`, `^`, `&~` and bitwise phrase forms | `T` |
| `#~<`, `#~>` | `T` |
| `#`, `#<`, `#>` | The canonical unsigned bit-count type |
| reductions | `Boolean` |
| shifts and rotates | The left operand's type `T` |
| required and policy magnitude | `T`, `T?`, or a report containing `T` |
| ordinary assignment and successful compounds | Writable access to the destination, except where the selected policy returns its report |
| protected swap | Zero results |

The arithmetic report examples in the operator catalog currently spell their
numeric fields as `Integer`. That cannot be literal for an `I8`, `U64`, or
`UInteger` operation. The candidate interpretation is that every such field
has the selected operand/result type `T`; later promoted examples need to state
that without pretending current generic syntax is known.

#### Policy behavior

The operator catalog's policy suffixes remain the behavioral source:

- the required form returns the mathematical result or panics when it is not
  representable;
- the optional form returns `T?` and is empty on an unrepresentable result;
- the wrapping form reduces modulo `2^W` and interprets the resulting bit
  pattern as `T`;
- the saturating form clamps to `T`'s nearest bound;
- reporting forms expose the documented wrapped, saturated, and overflow facts;
  and
- mutating forms leave the destination unchanged where the catalog says failure
  or intervention does not write.

Two's-complement representation makes signed wrapping and the minimum signed
value's behavior predictable without making ordinary arithmetic a bit-pattern
operation.

Default initialization of a fundamental integer is provisionally numeric zero,
with the all-zero representation. That gives `name : T` one cheap, stable
intrinsic result while preserving the general rule that ordinary declarations
do initialize their values.

#### Equal-width signedness counterpart

Pre-unary `+` is not ordinary numeric identity. On a concretely typed integer it
requests the equal-width opposite-signedness type:

```zax
unsignedValue : U8 = 100
signedValue := +unsignedValue // I8(100)
```

The established policy suffixes handle values that the counterpart cannot
represent:

- bare `+value` panics;
- `+?value` is optional;
- `+%value` preserves the low `W`-bit pattern;
- `+|value` clamps to the counterpart's range; and
- the reporting forms describe the same transition.

This operation depends on an explicit counterpart relationship, not merely
equal size. A custom finite integer-like type must advertise its counterpart
through future numeric-family behavior; storage compatibility alone is
insufficient.

#### Bit extent and signed bit patterns

Every accepted count, mask, shift, rotate, reversal, reduction,
extraction/deposit, and multiword operation observes logical extent `W`, never
register width, padding, or allocation capacity.

For signed `T`, bitwise operations act on the required `W`-bit two's-complement
representation and return `T`. Preserving the sign bit in `#~<value` is not
arithmetic overflow:

```zax
value : I8 = -128
mask := #~<value // I8 with bit pattern 10000000; no panic
```

The current shift rules remain coherent:

- the count operand is an unsigned fundamental integer or a nonnegative literal
  realized against that requirement;
- a logical shift by at least `W` returns zero;
- signed `>>` by at least `W` returns all sign bits;
- `>>>` always zero-fills;
- rotation and explicit modulo-count shift phrases reduce the count modulo `W`;
  and
- outgoing bits never widen the result implicitly.

The signedness of the count is a viability condition, not a runtime negative
count check:

```zax
signedCount : I8 = 1
shifted := value << signedCount
// candidate error: built-in shift counts are unsigned
```

#### Canonical bit-count type

The least disruptive initial candidate is:

```zax
BitCount :: alias type UInteger
```

The canonical static identity would therefore be `UInteger`; `BitCount` would
be a readable identity-preserving public alias. All built-in fixed widths fit
because even the legacy minimum 16-bit `UInteger` can represent counts through
128.

Built-in shifts should provide direct protected signatures for applicable
unsigned count types rather than rely on implicit integer conversion. This is
how a `UInteger` payload is directly viable without weakening the general
no-implicit-promotion rule:

```zax
position := bits trailing set bit position

if ?position
  shifted := value << position.
```

This proposal remains uncertain. `TypeSize` may be a better canonical identity
if future runtime-sized bit domains use it, while a future finite numeric
protocol may need an associated count type. The present decision only has to
cover protected fundamental integers, but it must not claim that `UInteger`
automatically scales to arbitrary-width or runtime-sized custom values.

#### Conversion and literal boundaries

The candidate separates five mechanisms:

1. An identity-preserving alias requires no conversion.
2. A literal is realized directly as the requested destination type when its
   value fits; this is not conversion from a hidden preselected integer type.
3. `source as Destination` performs a numeric exact-value conversion and panics
   at runtime if the destination cannot represent the value.
4. A compile-time-known failing `as` becomes the established compile-time panic
   diagnostic rather than generating a program guaranteed to panic.
5. Integer-to-integer `unsafe as` performs deterministic modulo-`2^W`
   truncation to the destination width, then interprets the destination bit
   pattern. It should not produce undefined behavior merely because integer
   ranges differ.

```zax
wide : U16 = 300

exact := wide as U8
// candidate runtime panic when wide is not compile-time known

truncated := wide unsafe as U8
// candidate result: U8(44)
```

Even mathematically safe widening remains explicit under this candidate:

```zax
small : U8 = 10
wide : U16 = small // candidate error: no implicit cross-type conversion
wide : U16 = small as U16
```

Literal work still owns default type, base prefixes, explicit width, arbitrary
compile-time precision, and expected-result participation. To test the integer
model, this record provisionally treats an unprefixed context-free integer
literal as `Integer` and `+55` as `UInteger`, matching current unary-plus
evidence. A complete typed declaration may instead realize the literal directly
as its requested fixed type.

No claim is made here about the exact grammar or semantics of `h'...'` and other
attached literal prefixes.

### Endian backing consequences

The integer model can make "applicable fundamental integer" precise enough for
the existing endian owner. A candidate eligible backing type must have:

- one concrete fundamental integer identity;
- an exact finite logical width that is a multiple of eight;
- a no-padding representation of the same byte size;
- every representation bit participating in the value;
- every bit pattern valid;
- a specified signed representation when signed; and
- known size and alignment in the active semantic context.

The fixed `I8`/`U8` through `I128`/`U128` families satisfy those conditions
under this candidate. An alias does not generate another endian family because
it introduces no new identity.

The provisional context-selected `Integer`/`UInteger` pair should not receive
public generated endian family names until enum and compile-time work decide
how a context-pinned identity is named and whether it is source-stable. Pointer-
and size-related integers should also remain ineligible until their numeric
representation and intended serialization meaning are established.

For an eligible signed backing type, required two's-complement representation
makes semantic endian encode/decode and raw storage extraction determinate.
The endian enum remains an independent type with the same size, alignment, and
representation extent; compatibility grants no implicit transfer.

### Contradictions and risks exposed by the reconstruction

1. **Natural integer identity is unresolved.** The legacy page alternates among
   alias, template, and CPU-optimal descriptions. Those are not interchangeable
   under overload selection, reflection, cross-target compilation, or `type of`.
2. **The protected domain is broader than its exact signature inventory.**
   Current owners protect fixed-width and pointer-sized numeric families but use
   `Integer` in many examples without saying whether mixed widths, natural
   integers, and aliases share signatures.
3. **Generic arithmetic reports use `Integer` as if it meant the selected
   operand type.** Keeping that spelling literally would change result width and
   possibly overflow behavior for fixed-width operations.
4. **The canonical bit-count type is behaviorally constrained but unnamed.**
   Choosing `UInteger`, `TypeSize`, or a distinct type affects shifts, generic
   code, reflection, and future runtime-sized domains.
5. **Legacy `unsafe as` is too broad.** It combines deterministic integer
   truncation with unrelated representation corruption and undefined behavior.
   Integer conversion needs a precise bit result even if other unsafe casts do
   not.
6. **Legacy aliases erase semantic boundaries.** `UUID`, `Rune`, and character
   types would inherit every integer operation and be indistinguishable in
   overloads if they remained transparent aliases.
7. **An eight-bit byte is assumed rather than clearly owned.** `Byte = U8`,
   fixed sizes, `size of`, endianness, and non-byte-multiple future widths all
   depend on this decision.
8. **Alignment portability is not recoverable from width.** The language can
   guarantee exact integer size without promising one cross-target alignment.
9. **Context-selected types threaten compile-time consistency.** The same source
   cannot safely let `Integer` mean compiler-host width for one subexpression
   and target width for another without explicit context and value boundaries.
10. **`SLongPointer` exceeds the current fixed catalog on the widest supported
    pointer.** Its legacy exact-value promise either constrains supported
    targets, requires a wider intrinsic, or must be weakened.

### Decisions needing maintainer review

The decisions are ordered so later ones do not disguise consequences of earlier
ones:

1. Should fundamental signed integers require two's complement, no value
   padding, and all-bit-pattern validity?
2. Is an addressable Zax byte always eight bits for the canonical fixed family?
3. Which `Integer`/`UInteger` alternative should anchor ordinary inferred
   integer code?
4. Should same-type operands and explicit cross-type conversion be the baseline,
   with no implicit integer promotion?
5. Which legacy names survive as transparent aliases, and which semantic names
   must be independent types?
6. Is `UInteger` (optionally exposed through `BitCount`) the protected
   fundamental bit-count result, or should `TypeSize` or a distinct identity own
   that role?
7. Are context-selected and pointer-related integers initially excluded from
   generated endian families?
8. Should integer-to-integer `unsafe as` be deterministic modulo truncation,
   leaving representation reinterpretation to a separately named mechanism?

The first review should concentrate on decision 3 after confirming that
decision 1 is an acceptable representation foundation. Alias identity and
conversion rules cannot be made stable until the natural pair is settled.

### Known holes requiring refinement

- The exact deterministic target property selecting the provisional natural
  width is absent.
- The pointer- and size-related integer catalog cannot be completed without
  pointer representation and maximum-object constraints.
- The conversion model has no optional or reporting general cast form; unary
  signedness conversion has policies, but unrelated narrowing currently has
  only panic or unsafe truncation.
- Exact report type identity, reflection, and naming remain structural-type
  questions even after their numeric field types are corrected.
- Formatting of aliases versus canonical names in diagnostics and `type of`
  remains unresolved.
- The effect of a target change on serialized compile-time constants and cached
  build artifacts needs compile-time ownership.
- Non-byte-multiple fundamental widths are not included. If later added, storage
  padding, byte order, masks, reversal, and enum backing must all be revisited
  together.
- Multiword operations still need exact receiver orientation, component
  constraints, result shape, aliasing behavior, and phrase spelling.
- Masked extraction/deposit and bit/byte reversal still need exact phrase forms
  and signed-result rules, although the finite logical extent they operate on is
  established by this candidate.
- Float, fixed-point, arbitrary-width, enum operation inheritance, and complete
  generic numeric protocols remain intentionally unresolved.

### Consequences and explicit deferrals

The following consequence-driven inputs were read because the candidate crossed
their activation boundaries:

- [raw literal input](../../raw/literal-operators.md), because default signedness,
  expected width, range failure, and unary-plus realization constrain integer
  declarations;
- [raw structural-typing input](../../raw/structural-typing.md), because alias
  identity, no-padding storage, arithmetic report shape, and endian
  compatibility must remain distinct from conversion;
- [raw compile-time execution input](../../raw/compile-time-execution.md), because
  a context-selected natural width cannot use `host`, `target`, and `native`
  interchangeably;
- [raw enum input](../../raw/enum-types.md), because fundamental backing eligibility
  and all-bit-pattern validity constrain endian enums for which every backing
  integer value is valid; and
- [raw reflection input](../../raw/reflection.md), because integer width,
  signedness, canonical identity, alias spelling, and representation need a
  future discoverable model.

Material consequences not designed here remain captured as follows:

| Deferred concern | Live destination | Activation pressure | Constraint on this work |
| --- | --- | --- | --- |
| Literal defaults, bases, prefixes, and arbitrary compile-time precision | [raw literal input](../../raw/literal-operators.md) | Literal-focused work or inability to realize an integer expression predictably | Integer types define representable domains; literal syntax must not silently insert cross-type promotions |
| Structural identity, layout compatibility, and anonymous report identity | [raw structural-typing input](../../raw/structural-typing.md) | Compatibility, report, or structural-type work | Alias identity, storage compatibility, and conversion permission remain separate |
| Host/target compile-time execution | [raw compile-time input](../../raw/compile-time-execution.md) | Any context-selected integer or layout query used during build-time execution | One evaluation uses a pinned semantic context; `native` never means unspecified compiler host |
| Generic numeric skeleton and custom finite integer protocol | [raw numeric-family input](../../raw/numeric-type-families.md) and [raw generic input](../../raw/type-parameters-and-generics.md) | Generic code must abstract over established concrete integer identities | Future generation must preserve names, identity, range, layout, operations, and conversions established here |
| Complete enum behavior and operation inheritance | [raw enum input](../../raw/enum-types.md) | Non-endian enum arithmetic, validity, or generation | Fundamental backing does not grant numeric operation inheritance or implicit transfer |
| Reflection syntax and metadata shape | [raw reflection input](../../raw/reflection.md) | Generic inspection, diagnostics, serialization, or type metadata | Canonical identity must not absorb expression facts; aliases must not create another canonical type |
| ABI and foreign integer correspondence | Future interoperability work | A real foreign boundary requires exact correspondence | Exact Zax size and representation do not by themselves promise a foreign ABI type or calling convention |
| Character, rune, UUID, and word semantic types | Future focused type owners | Their real domain behavior is reviewed | They must not receive the complete integer surface merely through a convenient alias |
| Unusual finite widths, runtime-sized bit domains, and arbitrary-width integers | [raw numeric-family input](../../raw/numeric-type-families.md) | A use case needs widths outside the canonical family | Hidden capacity and storage padding never change logical bit results |
| Multiword, reversal, and masked extraction/deposit details | [raw numeric-family input](../../raw/numeric-type-families.md) | Exact public forms or protected signatures are required | Every operation remains defined over finite logical extents, not allocation capacity |

Generic syntax and interoperability input were not expanded during this initial
pass. The concrete identities can be reviewed without choosing a generic
declaration language or a foreign ABI, and the table above records the
constraints that must reactivate those concerns.

### Likely lasting documentation fit

If a model at this depth is accepted, a dedicated human-facing integer owner is
likely justified. It would teach:

- the canonical family and natural/specialized forms;
- ranges, representation, logical extent, size, alignment, and portability;
- default initialization and literal realization boundaries;
- conversions and signedness counterparts;
- the operation families and result types;
- costs, panic, optional, reporting, and compile-time failures; and
- endian, enum, generic, reflection, compatibility, and ABI boundaries.

The operator catalog should retain exact forms, precedence, and compact
per-family behavior rather than duplicate the complete type model. Endianness
should retain semantic enum teaching and consume a precise integer-backing
eligibility rule. Declarations and qualifiers need only retain their general
alias, initialization, and access rules. The focused legacy intrinsic and
casting sections remain evidence until every useful integer claim is promoted,
deferred, rejected, or superseded.

A concise basic-types router is not yet justified by integer work alone. It can
be reconsidered when several mature sibling type owners create a real
human-reading need.

## Post-promotion teaching review and repair input

The staged promotion is a candidate review boundary, not wholesale acceptance.
Post-promotion review found that the technical coverage is strong but the two new
owners often present working-record vocabulary before giving a programmer enough
plain language and examples to understand it. The promotion must be repaired
after this section is reviewed and an actual documentation-fit dry run passes.

Everything below is aligned repair input or an explicitly identified tentative
structure/future pressure. It does not edit or supersede current owners merely by
appearing here.

### Capture obligation

When discussion exposes material future design pressure, it must receive a live
owner or indexed raw destination even if the maintainer does not separately
repeat “capture this.” This is a content-preservation obligation, not standing
authorization to edit a repository file.

### Teachability controls presentation order

Completeness and teachability are both required, but they answer different
questions:

- **teachability controls presentation order;**
- **completeness controls coverage.**

Promotion preserves every aligned finding without presenting it in discovery,
audit, or contract order. A current owner begins with ordinary use, concrete
examples, and plain language. It defines each specialized term before that term
carries technical weight. Exact rules, failures, costs, corner cases, and
reference material follow only after readers have the prerequisite model.

A future documentation repair should add these promotion checks:

1. **Cold-reader test.** A competent programmer who has not read the working
   record can understand common use from the opening sections.
2. **Vocabulary-before-use test.** Every Zax-specific or overloaded term is
   defined in plain language before later rules depend on it.
3. **Concrete-paraphrase test.** An abstract statement is followed immediately
   by what it changes in source, values, operations, failures, or costs.
4. **Teaching-versus-reference test.** Exhaustive matrices and per-form behavior
   follow the conceptual model or move into an independently useful catalog.
5. **No working-record transcription.** Promotion reconstructs human-facing
   teaching rather than polishing the working record's analytical ordering.
6. **Two-pass promotion.** First construct the reader's model; then trace every
   aligned finding into the appropriate later layer.

A documentation-fit dry run must fail when ownership is unique but readers would
still have to reconstruct the concept from abstract or poorly ordered material.
These rules belong in documentation architecture, not duplicated operating
prompts.

### General identity teaching repair

#### Plain opening model

The general identity owner should begin:

> Zax can give an existing type another name, or define a new type that uses an
> existing type as its underlying type.
>
> A transparent alias is only another name. An identity type is a separate type
> whose representation and available behavior begin from its underlying type.

```zax
MyShortName :: alias type SomeLongTypeName
MyCount :: identity admit expose type U32
```

Then state directly:

- `MyShortName` and `SomeLongTypeName` are the same type.
- `MyCount` and `U32` are different types.
- `MyCount` stores the same kind of value as its underlying `U32`, but it does
  not implicitly mix with `U32`.

Avoid “target” when describing a transparent alias because target already has an
execution-environment meaning. A transparent alias has no underlying-type
containment relationship: it is another name for one existing type.

#### Identity vocabulary

| Term | Plain meaning |
| --- | --- |
| Transparent alias | Another name for one existing type; both names refer to the same identity |
| Identity type | A new outer type that uses an existing underlying type for its representation |
| Underlying type | The existing type on which an identity type is based |
| Underlying value | The represented value before admission or after identity projection |
| Identity boundary | The point where equal representation stops granting interchangeability |
| Admission | Create an identity value from its underlying value or another explicitly related value |
| Identity projection | Return the underlying value represented by an identity value |
| `expose` | Make eligible underlying functions/operators available after changing their parameter/result types where required to operate on the identity |
| `opaque` | Do not make underlying functions/operators available automatically |

An **identity boundary** needs a current terminology definition:

> An identity boundary separates types that are not interchangeable even when
> their representation is the same. Assignment, argument passing, return, and
> operator matching cross it only through declared projection, admission, or a
> direct bridge.

```zax
myCount : MyCount
myRaw : U32

myCount = myRaw // error: distinct identities
myCount = MyCount from myRaw
```

Use **identity type** for the outer type and **underlying type** for the existing
inner relationship. Avoid **boxing type** because “boxing” often implies
allocation or indirection; reserve “boxed” for the conceptual `own` expansion
when useful.

#### Admission and projection

Define admission before presenting its keywords:

> Admission creates a value of an identity type from a value of its underlying
> type. A type family may also declare admission from another explicitly related
> type.

- `admit` means every valid underlying value may create the identity through
  ordinary `from`.
- `restricted` means the compiler supplies no automatic ordinary admission; the
  identity declares validation, unchecked admission, unsafe admission, or no
  admission.

Avoid “entry path,” which can sound like control-flow or program-entry
terminology.

Define projection concretely:

> Identity projection takes an identity value and returns its underlying value
> without changing the represented value.

```zax
myCount : MyCount
myRaw := myCount as U32
```

`myRaw` is the `U32` value represented by `myCount`. Projection changes the
static type from `MyCount` to its underlying `U32`; it does not vaguely “leave an
identity.”

#### Exposure wording

Explain `expose` through functions and operators rather than “identity-aware
signatures”:

> `expose` makes eligible functions and operators of the underlying type
> available on the identity type. Parameter and result types are changed where
> necessary so the operation uses the new identity.

```text
Integer + Integer -> Integer
```

becomes conceptually:

```text
MyInteger + MyInteger -> MyInteger
```

The complete transformation remains future `own` work.

#### Keep integer policy out of the general owner

The general identity owner should not teach CPU profiles, integer ranges,
numeric narrowing, `Small`, `Long`, modular conversion, or integer count types.
It owns the general identity mechanism.

Integer-specific application belongs in an **Integer identity types** section of
the integer owner:

- why `Integer`, `FastI16`, `Byte`, and `TypeSize` are intent identities;
- range-based exact/optional/narrowing admission;
- projection to exact intrinsic integers;
- relational signedness pairs;
- direct `IndexSize`/`TypeSize` bridges; and
- profile-stable conversion availability.

A third identity/integer document is not yet justified. Split only if the
integer owner becomes incoherent after this repair.

Avoid undefined “sibling identity” vocabulary. Say:

> Two identity types defined independently from the same underlying type remain
> unrelated unless a bridge is declared.

### Cross-cutting exact-value terminology

**Exact value** should be a broad current term:

> An exact value is the same mathematical or semantic value requested by an
> operation, without rounding, truncation, wrapping, saturation, substitution,
> or discarded information.

This can apply to integers, floating point, fixed point, decimal types, and
other conversions.

Use narrower qualified terms where required:

- **exact-width integer**: logical width is explicitly fixed;
- **exact conversion**: preserves the exact source value;
- **exact arithmetic result**: the mathematical result without a policy
  transformation.

Do not use bare “exact” where readers cannot tell which meaning applies.

### Integer teaching repairs

#### Observable width and compiler freedom

Replace statements that the compiler can “never enlarge” an integer with:

> The integer's logical width determines every programmer-visible result and its
> stored representation. A compiler may use wider registers, temporaries, or
> intermediate operations when that produces the same logical-width behavior. It
> may not expose the wider width through values, overflow behavior, bit
> operations, storage size, or layout.

For `U8`, a compiler may calculate in a 32-bit register while preserving
eight-bit observable semantics and the specified one-byte stored envelope.

#### Sealed specializations

Use **sealed** rather than asking “closed” to carry several meanings:

> The intrinsic integer family is open to specialization. Each realized
> specialization is sealed against ordinary extension.

A **sealed type** cannot receive ordinary externally added members or
operations. Future partial work decides whether language-, compiler-,
CPU-provider-, owner-, or programmer-supplied `final` partial functions may add
behavior without changing stored shape.

Sealing and partial authority are independent design axes. The terminology owner
needs a concise `sealed type` definition, while partial input retains the exact
authority/coherence questions.

#### Same identity and dedicated operand roles

Replace the overly absolute same-type rule with:

> Integer operations normally use operands of the same integer identity. An
> operation may instead declare a dedicated role type when that operand has a
> different meaning.

```zax
mySum := myLeft + myRight       // same integer identity
myShifted := myValue << myCount // associated bit-count type
```

Dedicated-role exceptions include associated bit/byte counts, declared identity
bridges, conversion/admission, mixed endian right operands, and future multiword
component relationships. None permits general implicit promotion.

#### Canonical paths without “prelude”

“Prelude” is not established vocabulary. Teach:

> Short source names are transparent aliases of the canonical
> `Scalars.Integers...` declarations.

How those aliases become visible remains future module/import work.

#### Integer padding and container padding

Distinguish:

1. **integer storage padding**: unused bits inside the integer's own storage
   envelope; and
2. **container layout padding**: bytes a containing type inserts around members
   to satisfy alignment/layout.

A whole-byte exact integer has no unused bits in its own storage envelope. A
container may still insert padding before or after that integer member.

```zax
MyType :: type {
  myByte : U8
  myWide : U64
}
```

Padding between the members belongs to `MyType`, not `U8`.

#### Storage carrier

`T storage type` selects the smallest whole-byte unsigned exact integer large
enough for `T`'s storage envelope. It is not restricted to power-of-two widths:

```text
I32 storage type -> U32
I47 storage type -> U48
I57 storage type -> U64
```

```text
storage carrier width = ceil(logical width / 8) * 8
```

Required alignment remains independent from carrier width.

#### Software terminology

Use:

- **software emulation** for implementing an integer wider or otherwise
  unsupported by the CPU's native scalar representation;
- **software fallback** when selection chooses software emulation because no
  native candidate satisfies the request; and
- **native representation** for a width the CPU profile says the CPU directly
  represents.

Avoid “software representation,” which can imply that the language-level value
representation changed.

#### Optimized maximum family

The existing maxima do not express “largest width with a profile-recommended,
well-optimized implementation.” Add the candidate relational pair:

```zax
OptimizedMaxI
OptimizedMaxU
```

Meaning:

> The widest integer family for which the CPU provider supplies its preferred
> general-purpose optimized implementation, whether through native
> representation or deliberately optimized software emulation.

Illustrative profiles:

```text
6502-like:
  NativeMaxU    = U8
  OptimizedMaxU = U16

32-bit:
  NativeMaxU    = U32
  OptimizedMaxU = U64

64-bit:
  NativeMaxU    = U64
  OptimizedMaxU = U64 or U128 according to the provider
```

The provider must expose the selected width, broad cost expectations, and which
operations use native instructions or software emulation. The name does not
promise every operation is one instruction or universally fastest.

Exact name and provider-contract wording remain subject to the repair review;
the missing selection concept itself must be preserved.

#### `BitCount` capacity

If `BitCount` has at least eight logical bits, its complete minimum range is:

```text
0..255
```

The earlier `0..128` wording described only the largest predefined exact integer
width it had to count and incorrectly looked like the type's complete range.

#### Associated count teaching

Explain the reason before the formula:

> Count operations return an associated type so ordinary integers can use a
> compact count while extremely wide integers use a larger count. Generic code
> asks the operand type which count it uses instead of branching on width.

```zax
MyCount :: alias type MyInteger bit count type
```

Selection facts:

```text
T bit count type:
  selected from T.logicalWidth

T byte count type:
  selected from T.storageEnvelopeBytes
```

Bit-count selection is based on logical bit width, not the storage-envelope byte
size. Both associated results are directly usable by their applicable
operations.

#### Range syntax pressure

Preserve these candidates for future range/index work:

```zax
0..255
0..<myCount
```

Future work must decide:

- whether `..` is an inclusive range and `..<` is half-open;
- tokenization beside postfix `.` dereference;
- longest-token recognition;
- grouping needed around adjacent dereference/range punctuation;
- empty and reversed ranges; and
- whether ranges are values, syntax consumed directly by indexing, or both.

Optional-pointer/dereference adjacency is useful parser pressure, but no exact
grouping syntax is accepted here.

#### Unchecked arithmetic teaching and directive pressure

Lead with the practical behavior:

> Normally, required arithmetic checks whether its exact result fits. Failure
> panics instead of silently producing another value. Disabling those checks
> removes the arithmetic-overflow panic path and lets the compiler assume the
> result always fits. If that promise is violated, the result and surrounding
> behavior are undefined.

Reasons to select the unchecked contract include:

- removing overflow branches in hot arithmetic;
- improving vectorization and loop optimization;
- letting the optimizer rely on already validated domain invariants; and
- avoiding duplicate checks after a trusted boundary proved the input range.

Reasons to retain checks include:

- contract violations have undefined consequences;
- tests may not exercise the bad range;
- external input may violate assumed invariants; and
- build configuration becomes part of reproducibility and auditing.

This is never a debug-versus-release semantic difference. Future
analysis-control/compiler-directive work must own exact option syntax, project
and source scope, metadata, diagnostics, and these teaching tradeoffs.

### Integer operator ownership repair

The current general catalog mixes exact source-form ownership with detailed
protected integer behavior. The repair dry run should evaluate four layers:

1. `operators.md`: general discovery, selection, evaluation, protection, and
   result behavior.
2. `operator-catalog.md`: closed source forms, exact phrase spellings, fixity,
   precedence, and which domains reserve each form.
3. `integers.md`: integer types, representation, identities, and a concise
   overview of supported operator families.
4. Proposed `integer-operator-catalog.md`: complete protected integer behavior.

`integer-operator-catalog.md` is the preferred candidate name because it begins
with the subject and names the reader's lookup task.

The proposed catalog would own:

- arithmetic policies and result types;
- comparison behavior;
- signedness counterpart;
- negation and magnitude;
- division and remainder;
- compound arithmetic;
- increment/decrement;
- bitwise forms;
- counts, masks, positions, and reductions;
- shifts and rotates;
- assignment and swap;
- integer-specific conversion/admission;
- `delta` and `distance`;
- signed/unsigned availability;
- panic, optional, wrapping, saturation, and report behavior; and
- the two unchecked build contracts as they affect integer operations.

The general catalog may state:

```text
+! is a recognized arithmetic-policy form at additive precedence.
```

The integer catalog states the protected integer result. A custom type may
return another result shape when the form's open domain permits it.

The dry run must decide whether this split reduces duplicate teaching without
forcing readers to reconstruct ordinary integer use across too many owners.

### `own` is a general composition mechanism

Integer/scalar identities supplied the immediate design pressure, but `own` is
not a numeric feature. It may expose or adapt behavior from any contained type.
Future owned-composition teaching needs at least one nonnumeric example and must
not imply restriction to scalars, representation wrappers, or identity
declarations.

### Construction, transfer, views, and `cast as`

#### By-value admission requires construction or transfer

```zax
myIdentity := MyIdentity from myUnderlying
```

requests a new value. Establishing its underlying stored value requires a viable:

- copy;
- move;
- consuming/`last` transfer;
- direct construction; or
- another declared transfer.

Identity syntax cannot manufacture copyability. If none applies, by-value `from`
is unavailable.

#### Same-storage identity view

A reference result is a different operation:

```zax
myView : MyIdentity & = MyIdentity from myUnderlying
```

It may create an identity-typed view of existing storage instead of a new value.
It performs no copy and schedules no independent destruction.

Future work must decide:

- exact representation/layout compatibility and equal base address;
- qualifications and declaration-side replacement permission;
- aliasing and lifetime;
- whether underlying and identity views may coexist;
- identity invariants and admission policy;
- lifecycle methods and destructor expectations;
- optimizer type assumptions; and
- temporary-source lifetime.

The compiler must never silently substitute a reference result for a requested
by-value result.

#### `cast as` pressure

A representation-compatible same-storage view motivates:

```zax
return myUnderlying cast as MyIdentity &
```

`cast as` needs review as a compiler-recognized representation/shape operation:

- `own` supplies the shape relationship;
- identity admission decides whether the view is ordinary, optional, unchecked,
  unsafe, or unavailable;
- the closed underlying type cannot know future identities;
- the compiler may generate the bridge from the declared identity/shape
  relationship; and
- programmer partials must not be required merely to inform the underlying type
  about a later identity.

The operation is not automatically safe. An admitting identity with equal shape
and compatible lifecycle may permit an ordinary reference view. Restricted
invariants or incompatible lifecycle behavior may require validation,
`unchecked`, `unsafe`, or rejection.

Exact `cast as` spelling and its relationship to current `as`/`unsafe as` remain
future casting and owned-composition work.

#### Type receiver and `once` pressure

The current conceptual type-receiver mechanism suggests:

```zax
// Illustrative; reference admission and cast syntax remain unsettled.
operator type binary 'from' final : (
  result : MyIdentity &
)(
  rhs : UnderlyingType &
) = {
  return rhs cast as MyIdentity &
}
```

`operator type` already supplies the type receiver; an additional explicit
`MyIdentity : type` parameter would duplicate it under the current model.

The source candidate:

```zax
myView := MyIdentity & from myUnderlying
```

creates pressure for a qualified type identity such as `MyIdentity &` to act as
a type receiver and participate in discovery.

Future work must also reconcile:

- `operator type` with legacy/static `once final` behavior;
- whether `once varying` has a related receiver model;
- qualified alias and generic type receivers;
- result-context selection between value and reference admission; and
- how type-receiver operations are generated for identity families.

These are type-receiver, declaration, generic, qualifier, and `once` design
pressures, not accepted syntax here.

#### Partial pressure

Future partial work must consider whether a sealed intrinsic or identity may
accept:

- language-generated conversion/cast operations;
- compiler- or CPU-provider operations;
- owner-supplied `final` functions that add no instance storage; or
- programmer partials.

If partials may add stored members, exposed identity signature transformation
and same-storage casts require a stronger shape check. A reference result may be
reshaped to the identity only when the actual storage has the complete identity
shape. Partial declaration/import order cannot change that answer silently.

### Required future destinations

| Pressure | Required live destination before archival |
| --- | --- |
| Teachability-first promotion checks and capture obligation | Documentation architecture |
| Identity boundary, underlying type/value, exact value, and sealed type terminology | Current terminology and applicable concept owners |
| General identity rewrite and integer-specific identity application | Identity and integer owners |
| `OptimizedMaxI`/`OptimizedMaxU` and software-emulation profile | CPU-provider, numeric-family, and generic input |
| Inclusive `..` and half-open `..<` range candidates | Indexing/range and source/operator input |
| Unchecked arithmetic directive teaching and configuration | Analysis-control and safety input |
| Integer operator catalog split | Documentation-fit structure proposal and operator/integer owners |
| General `own` scope and signature transformation | Owned-composition input |
| By-value admission transfer requirements | Construction/lifetime and identity input |
| Same-storage identity references and `cast as` | Owned-composition, structural-typing, casting, qualifier, and lifetime input |
| Qualified type receivers and `operator type`/`once` relationship | Generic, declaration, qualifier, and partial input |
| Partial effects on identity shape and sealed intrinsic surfaces | Partial and owned-composition input |

The repair dry run must verify that every row has one coherent lasting or indexed
raw owner and that current documentation never depends on this working record
for meaning.

### Repair sequence

1. Review and align this `010` repair input.
2. Perform the actual documentation-fit dry run that was previously missed.
3. Record PASS or FAIL, the exact structure proposal, owner map, and repair
   change set.
4. Wait for separate promotion-repair authorization.
5. Rewrite promoted teaching from examples outward, apply the approved owner
   split and raw captures, validate, and record actual dispositions here.

## Promotion-repair documentation-fit dry run

| Field | Value |
| --- | --- |
| Date | 2026-08-31 |
| Scope | Post-promotion teaching, terminology, owner-boundary, operator-catalog, and deferred-pressure repairs recorded above |
| Result | **PASS** |
| Meaning | The proposed repair has a coherent owner structure, teachable reading path, live deferred destinations, and exact change set |
| Does Not Authorize | Promotion repair, staging, archival, closure, or creation of work item `011` |

This is the actual documentation-fit dry run for the proposed promotion repair.
It does not retroactively supply the dry run missed before the staged promotion.

### Structure proposal

Retain the flat `language/` owner layout and add one independently useful
reference owner:

```text
language/
  identity-types.md
  integers.md
  integer-operator-catalog.md
  operator-catalog.md
  operators.md
```

No new directory family or basic-types router is justified.

The reading path is:

1. [Zax identity types](../../../language/identity-types.md) for the general
   alias/identity/underlying-type/admission/exposure model;
2. [Zax integers](../../../language/integers.md) for integer choice, representation,
   identity application, ranges, storage, conversion, counts, profile-relative
   types, and a concise operation-family overview;
3. proposed `language/integer-operator-catalog.md` for complete protected integer
   behavior and lookup; and
4. [Zax operator catalog](../../../language/operator-catalog.md) for
   language-wide exact forms, fixity, precedence, reservation, and non-integer
   domain references.

[Zax operators](../../../language/operators.md) remains the shared discovery,
selection, evaluation, protection, and result model.

### Ownership map

| Finding or repair | One lasting owner | Required local integration or deferred consequence |
| --- | --- | --- |
| Teachability-first promotion order and cold-reader/vocabulary/reference checks | [Documentation architecture](../../documentation.md) | Dry-run and promotion sections gain the checks; prompt sources retain only their existing concise route |
| Transparent alias, identity type, underlying type/value, identity boundary, admission, identity projection, `admit`/`restricted`, `expose`/`opaque`, and direct bridges | [Identity types](../../../language/identity-types.md) | Declarations retains syntax integration; terms gains concise cross-cutting definitions |
| Integer families, storage, software emulation, profile-relative selection, `OptimizedMaxI`/`OptimizedMaxU`, counts, conversion relationships, and integer-specific identity application | [Integers](../../../language/integers.md) | CPU-provider and generic raw inputs retain mechanism and syntax |
| Complete protected integer operator behavior | Proposed `language/integer-operator-catalog.md` | General catalog retains exact forms/precedence and links integer behavior |
| General operator discovery, selection, evaluation, and protection | [Operators](../../../language/operators.md) | Link the new integer reference where protected domain behavior is needed |
| General exact operator forms, phrase spellings, fixity, precedence, and reservations | [Operator catalog](../../../language/operator-catalog.md) | Remove detailed integer result/policy teaching after relocating it |
| Broad **exact value**, **identity boundary**, **underlying type/value**, **sealed type**, and **software emulation** vocabulary | [Language-design terms](../../../language/terms.md) | Identity and integer owners teach full behavior |
| By-value identity admission requires viable construction/copy/move/consuming transfer | [Identity types](../../../language/identity-types.md) | Construction owner owns the actual transfer/lifecycle behavior |
| General `own` scope, signature transformation, same-storage identity view, and compiler-generated shape bridge | [Owned-composition raw input](../../raw/owned-composition.md) | Structural, qualifier, lifetime, and casting inputs receive only their local constraints |
| `cast as` representation/shape operation | [Owned-composition raw input](../../raw/owned-composition.md) until a casting owner exists | Structural-typing raw input records compatibility; current owners do not reserve exact syntax |
| Qualified type receiver and `operator type`/`once` relationship | [Type-parameter and generic raw input](../../raw/type-parameters-and-generics.md) | Declaration, qualifier, and partial inputs record local consequences |
| Partial authority over sealed intrinsic/identity types and shape-changing members | [Partial-type raw input](../../raw/partial-types.md) | Owned-composition input records exposure/shape consequences |
| `OptimizedMaxI`/`OptimizedMaxU` selection and provider metadata | [CPU-provider raw input](../../raw/cpu-provider-model.md) | Integer owner states public meaning; numeric/generic inputs retain family generation |
| Inclusive `..` and half-open `..<` candidates | [Indexing and slicing raw input](../../raw/indexing-and-slicing.md) | Source/operator inputs receive tokenization and precedence consequences when activated |
| Unchecked arithmetic option/configuration and teaching tradeoffs | [Analysis-control raw input](../../raw/analysis-controls.md) | Safety input owns contract classification; integer operator catalog owns programmer-visible operation effects |

Each concern has one primary owner. Local links and consequences do not create
competing definitions.

### Teaching-fit test

The proposed repair passes the teaching checks:

- **Cold-reader:** identity and integer owners begin with short, valid examples
  and ordinary language before declaration matrices or associated types.
- **Vocabulary before use:** identity boundary, underlying type/value, exact
  value, sealed type, and software emulation are defined before carrying later
  rules.
- **Concrete paraphrase:** projection, admission, exposure, compiler widening,
  padding, unchecked arithmetic, and same-storage views each state what changes
  in source or behavior.
- **Teaching versus reference:** exhaustive protected operator behavior moves to
  a dedicated integer catalog after the type model is available.
- **No working-record transcription:** the owners are rewritten from a teaching
  outline rather than edited sentence by sentence in discovery order.
- **Completeness:** every aligned rule remains in one of the layered owners or an
  indexed future destination.

### Exact promotion-repair change set

Create:

- `language/integer-operator-catalog.md`.

Rewrite or materially reorganize:

- `language/identity-types.md`;
- `language/integers.md`; and
- `language/operator-catalog.md`.

Integrate current owners and routers:

- `language/operators.md`;
- `language/terms.md`;
- `language/declarations-and-bindings.md`;
- `language/construction-and-destruction.md`;
- `language/vision.md`;
- `index.md`; and
- `project/documentation.md`.

Update deferred inputs:

- `project/raw/owned-composition.md`;
- `project/raw/partial-types.md`;
- `project/raw/cpu-provider-model.md`;
- `project/raw/indexing-and-slicing.md`;
- `project/raw/analysis-controls.md`;
- `project/raw/safety.md`;
- `project/raw/structural-typing.md`;
- `project/raw/type-parameters-and-generics.md`;
- `project/raw/lifetimes.md`;
- `project/raw/numeric-type-families.md`;
- `project/raw/reflection.md`; and
- `project/raw/README.md` only if its activation summaries need the new pressure.

Update `project/work/010-integer-types.md` after repair with actual dispositions,
validation, and any deviation from this exact set.

No change is proposed for:

- `project/handoff.md` or `project/rehydrate.md`, because their existing concise
  dry-run route is sufficient;
- legacy `basics.md`, `casting.md`, or `alias.md`, because the consumed-material
  cleanup already leaves only unresolved input;
- operating-guidance revision;
- archival or work item `011`; or
- implementation documentation.

### Deferred material retained after repair

The repair does not decide:

- exact integer-factory or relational-pair syntax;
- CPU-profile file format or versioning;
- final `own`, partial, `cast as`, qualified type-receiver, or `once` behavior;
- general structural compatibility and same-storage aliasing;
- literal or compile-time unrealized-result mechanics;
- complete enum, pointer, lifetime, ABI, or reflection design;
- exact range syntax; or
- exact unchecked compiler-option syntax.

Each item has a live indexed raw destination and a constraint recorded above.
No current owner will depend on `010` or raw input for its present meaning.

### Dry-run conclusion

**PASS.** The repair can produce a human-readable current design without
duplicate authority, orphaned findings, speculative directory structure, or
loss of deferred pressure.

Per the required stopping boundary, promotion repair must wait for separate
authorization after this result is reviewed.

## Dispositions and audit-backed promotion

| Field | Value |
| --- | --- |
| Date | 2026-08-30 |
| Promotion Basis | Direct maintainer authorization following the alignment-capture audit; the missed documentation-fit dry run was not retroactively claimed |
| Result | **REOPENED** - post-promotion review found teachability, terminology, ownership, and deferred-pressure repairs requiring an actual documentation-fit dry run |
| Implementation | None in this repository |

The aligned programmer-facing design is promoted into:

- [Zax integers](../../../language/integers.md), the dedicated owner for finite
  integer families, representation, conversion, counts, arithmetic contracts,
  `delta`, `distance`, costs, diagnostics, and portability; and
- [Zax identity types](../../../language/identity-types.md), the dedicated owner for
  transparent aliases, explicit identities, admission, projection, exposure,
  opacity, and declared bridges.

Current-owner integration updates:

- [Zax operator catalog](../../../language/operator-catalog.md) owns exact
  conversion/admission forms, `delta`/`distance`, precedence, protected domains,
  count results, and unchecked arithmetic contracts;
- [Zax operators](../../../language/operators.md) owns shared protection,
  diagnostics, costs, and source-stability consequences;
- [Zax declarations and bindings](../../../language/declarations-and-bindings.md)
  integrates `alias type` and explicit identity declaration forms;
- [Zax endianness](../../../language/endianness.md) consumes the no-padding
  whole-byte eligibility rule and uses plain backing-value validity wording;
- [Zax language-design terms](../../../language/terms.md) defines the cross-cutting
  identity, alias, environment, and native-representation vocabulary;
- [Zax language vision](../../../language/vision.md) and the
  [website index](../../../index.md) route readers to the new owners; and
- [documentation architecture](../../documentation.md) incorporates the aligned
  reusable example-quality repairs and limits `Does Not Own` metadata to
  plausible competing ownership or direct handoffs.

Legacy [basics](../../../basics.md), [casting](../../../casting.md), and
[alias](../../../alias.md) pages now identify their current successors while
retaining unrelated legacy evidence. The promoted integer catalog, obsolete
integer conversion examples, and duplicate transparent-alias teaching are
removed rather than retained beneath disposition notes. Legacy UUID/Rune intent
was extracted into indexed numeric-family input before its obsolete transparent
integer aliases were removed.

Deferred mechanisms remain indexed in `project/raw/` with current-owner
disposition notes. In particular:

- CPU-profile syntax and mapping stability remain with
  [CPU-provider input](../../raw/cpu-provider-model.md);
- general `own` transformation remains with
  [owned-composition input](../../raw/owned-composition.md);
- intrinsic extension authority remains with
  [partial-type input](../../raw/partial-types.md);
- factory, paired-identity, and associated-type syntax remains with
  [generic input](../../raw/type-parameters-and-generics.md);
- metadata shape remains with [reflection input](../../raw/reflection.md);
- enum admission classification remains with [enum input](../../raw/enum-types.md);
- literal and compile-time realization mechanisms remain with
  [literal](../../raw/literal-operators.md) and
  [compile-time](../../raw/compile-time-execution.md) input;
- pointer, index, analysis-control, and safe-subset mechanics remain with their
  indexed raw owners; and
- fixed-point, unbounded, multiword, reversal, and extraction/deposit details
  remain with [numeric-family input](../../raw/numeric-type-families.md).

Validation passed:

- changed current concerns have one owner;
- current owners do not cite numbered or raw project records;
- every new raw file is indexed and has activation/retirement criteria;
- the rejected validity label remains only in immutable initiating/history
  material, not current or live raw teaching;
- old operator-catalog anchors and canonical-count wording are removed from live
  owners;
- superseded public integer and transparent-alias teaching is consumed while
  unresolved non-integer and advanced alias/casting material remains;
- current and live-input `Does Not Own` metadata no longer acts as an exhaustive
  exclusion list; detailed boundaries remain in document bodies;
- local files and heading anchors resolve;
- Markdown code fences and whitespace are coherent;
- required promoted forms and concepts are present; and
- the staged `010` review boundary remains unchanged, with the authorized
  promotion and capture work unstaged.

The earlier mechanical PASS records what was applied and validated at that time.
It no longer indicates current promotion readiness. The post-promotion review
above reopens `010` until the repair dry run, authorized repair, and final
validation are complete.

## Promotion repair disposition

| Field | Value |
| --- | --- |
| Date | 2026-08-31 |
| Basis | Maintainer-authorized repair following the PASS documentation-fit dry run above |
| Result | **PASS** |
| Current Status | The promoted documentation now reflects the post-promotion review; `010` remains active until separately authorized closure |

### Applied owner structure

The repair retains the flat `language/` structure and establishes:

- [Zax identity types](../../../language/identity-types.md) as the plain-language
  owner for aliases, underlying types/values, identity boundaries, admission,
  identity projection, exposure/opacity, bridges, and construction requirements;
- [Zax integers](../../../language/integers.md) as the teaching owner for integer
  choice, representation, storage, profile-selected families, integer identity
  application, associated counts, software emulation, and unchecked-contract
  motivation;
- [Zax integer operator catalog](../../../language/integer-operator-catalog.md) as
  the complete protected integer behavior reference; and
- [Zax operator catalog](../../../language/operator-catalog.md) as the
  language-wide exact-form, fixity, precedence, reservation, and domain-routing
  reference.

No new directory or basic-types router was introduced.

### Teaching and terminology repairs

The identity and integer owners were rebuilt from concrete examples and ordinary
language rather than edited sentence by sentence.

Current terminology now defines:

- exact value;
- identity boundary;
- identity type;
- underlying type and value;
- sealed type; and
- software emulation/fallback.

Integer teaching now distinguishes compiler-internal widening from observable
logical width, integer storage padding from container layout padding, and
logical-width count selection from storage-envelope byte-count selection. It
adds the `I47 -> U48` carrier example, corrects the minimum `BitCount` range to
zero through 255, and introduces `OptimizedMaxI`/`OptimizedMaxU`.

Documentation architecture now makes teachability determine presentation order,
adds cold-reader/vocabulary/concrete-paraphrase/reference/two-pass checks, and
applies them to future documentation-fit dry runs. It also records that concrete
future design pressure must be captured even when the maintainer does not repeat
the request, without treating that obligation as edit authorization.

### Deferred-pressure dispositions

The repair updates live raw owners for:

- general-purpose `own` composition beyond scalar identities;
- by-value identity transfer and same-storage identity references;
- representation-compatible `cast as` pressure;
- sealed intrinsic/identity partial authority and stored-member shape changes;
- qualified type receivers and `operator type`/`once` relationships;
- `OptimizedMaxI`/`OptimizedMaxU` profile selection and software emulation;
- inclusive `..` and half-open `..<` range candidates;
- unchecked arithmetic teaching/configuration tradeoffs;
- structural, lifetime, qualifier, reflection, and generic consequences; and
- feature-catalog provenance.

Exact syntax and mechanics remain deferred at their stated maturity.

### Change-set deviations discovered during integration

The dry-run structure and primary owner map remained valid. Catalog splitting
exposed additional reference integrations not named individually in the dry-run
file list:

- legacy `basics.md` and `casting.md`;
- current core-flow, mixfix, operator-phrase, and source-structure owners;
- enum raw input; and
- the legacy feature catalog.

These changes update links or ownership provenance only. They do not add another
concept owner or change the affected documents' behavior.

The dry run listed declaration integration, but no additional unstaged
declaration edit was necessary because the staged candidate already contained
the required identity-declaration integration.

### Validation

Promotion-repair validation passed:

- all changed Markdown and staged content pass whitespace checks;
- current and deferred local links and heading anchors resolve;
- current language owners do not link to project working/raw records;
- the identity owner contains no CPU-profile or integer narrowing policy;
- the general operator catalog contains no protected integer result/failure
  teaching;
- the integer owner gives a complete operation-family overview and routes exact
  behavior to the integer operator catalog;
- the integer operator catalog covers arithmetic, comparison, counterpart,
  magnitude, division/remainder, compounds, increment/decrement, bitwise,
  counts, positions, shifts/rotates, conversion/admission, `delta`, `distance`,
  assignment/swap, and both unchecked contracts;
- rejected/stale terminology and old heading anchors are absent from live
  documentation;
- raw inputs remain indexed and non-authoritative; and
- the staged review boundary was not modified by the agent.

The repaired promotion therefore satisfies the approved dry-run structure and
teaching checks.

## Post-repair correction input

The preceding repair PASS is historical for the staged baseline. The following
small aligned corrections reopen the promoted material until their incremental
repair is authorized and applied.

### Intent-gated saturating magnitude is available

The asymmetric integer magnitude form exists:

```zax
bare{ ||myValue| }
```

Natural unacknowledged source:

```zax
||myValue| // error: confusable source requires explicit intent
```

is an intent error because it resembles a malformed norm. The operation itself
is not unavailable. `bare{...}` explicitly confirms that the asymmetric
`|| ... |` form is intended.

The general operator catalog owns recognition, circumfix shape, precedence, and
the intent-gating requirement. The integer operator catalog owns protected
integer behavior:

- ordinary magnitude when representable;
- signed maximum for the minimum signed value; and
- the same value for unsigned operands.

General `bare{...}` parsing, nesting, source reflection, and keyword-neutral
mechanics remain future bare-source work. This specific use supplies concrete
source pressure without completing that mechanism.

### Remove invented zero-through-128 provenance

No historical or aligned requirement defined `BitCount` as zero through 128.
That sentence was an agent-authored explanation incorrectly presented as prior
design input.

The current rule is simply:

> `BitCount` has at least eight logical bits and therefore represents at least
> zero through 255.

That range is sufficient for counts and positions on the predefined exact-width
family through 128 bits. Sufficiency is an observation, not another requirement.
The invented provenance must be deleted rather than reworded.

### Unchecked contract selection remains compiler-directive work

The arithmetic-result and nonzero-divisor contracts are current. Their selection
hierarchy is not.

Future compiler-directive work must decide whether each independent contract may
be selected at:

- project/build level;
- module or namespace level;
- source-file level;
- lexical region;
- declaration;
- expression/operation; or
- some deliberately smaller set.

It must also decide:

- inheritance into nested scopes;
- whether narrower source overrides an outer default;
- whether project policy can forbid local weakening;
- whether project policy can force checks;
- whether a build can force unchecked behavior over source that requests checks;
- conflict diagnostics;
- imported/library and generated source behavior;
- language-contract version interaction; and
- reproducible metadata/tooling presentation.

The recommended future direction is a project/build default with narrower
explicit source choices, plus an optional minimum-safety policy that may forbid
weakening. A build may force checks because contract-respecting behavior is
unchanged; it should not silently force unchecked behavior over an explicit
source request for checks.

Neither contract is defined through debug versus release mode.

### Range punctuation candidates

Preserve:

```zax
1..=5       // candidate closed range
0..<myCount // candidate half-open range
```

`..=` is the closed-range candidate, visually inspired by an English en dash.
`..<` is the half-open candidate.

Bare `..` receives no two-ended range meaning at this time. Leaving it
unassigned avoids conflict with the strong Rust expectation that `..` is
half-open and preserves it for future open-ended/rest investigation.

Future range/source work must decide value versus syntax status, precedence,
association, endpoint types, empty/reversed ranges, omitted endpoints,
longest-token recognition, and adjacency with `.`, `...`, and postfix
pointer/optional dereference.

The earlier raw suggestion that `..` might be inclusive is superseded.

## Incremental correction documentation-fit dry run

| Field | Value |
| --- | --- |
| Date | 2026-08-31 |
| Scope | Intent-gated saturating magnitude, `BitCount` provenance cleanup, unchecked-contract directive hierarchy, and range punctuation candidates |
| Result | **PASS** |
| Does Not Authorize | Promotion repair, staging, archival, closure, or work item `011` |

### Ownership and exact change set

| Concern | Owner/update |
| --- | --- |
| Recognition and intent gating of asymmetric `|| ... |` | `language/operator-catalog.md` |
| Protected integer saturating-magnitude behavior | `language/integer-operator-catalog.md` |
| General `bare{...}` mechanics and this concrete activation pressure | `project/raw/bare-source.md` |
| Removal of invented zero-through-128 provenance | `project/raw/numeric-type-families.md` |
| Contract hierarchy, overrides, locking, metadata, and teaching pressure | `project/raw/analysis-controls.md` |
| `..=` closed and `..<` half-open candidates; bare `..` unassigned | `project/raw/indexing-and-slicing.md` |
| Actual correction disposition and validation | `project/work/010-integer-types.md` |

No new owner, directory, router, prompt-source, or legacy-page change is
required.

### Teaching and integration check

The change set passes:

- the integer catalog shows a valid intent-acknowledged source example before
  explaining the confusable natural form;
- the general catalog distinguishes operation availability from source intent
  gating;
- no false provenance remains in raw numeric input;
- compiler-directive detail remains deferred rather than leaking into current
  option syntax;
- the two range candidates are shown with their endpoint behavior; and
- bare `..` receives no accidental meaning.

**PASS.** The corrections have one current or raw owner each, require no
structural change, and preserve the repaired teaching model.

Per the required stopping boundary, the seven-file correction must wait for
separate promotion authorization.

## Incremental correction promotion disposition

| Field | Value |
| --- | --- |
| Date | 2026-08-31 |
| Basis | Maintainer-authorized promotion following the PASS incremental documentation-fit dry run |
| Result | **PASS** |

Applied exactly the seven-file correction set:

- [general operator catalog](../../../language/operator-catalog.md): asymmetric
  saturating magnitude is recognized as available through
  `bare{ ||myValue| }`; unacknowledged natural source remains an intent error;
- [integer operator catalog](../../../language/integer-operator-catalog.md):
  protected saturating-magnitude behavior is defined for signed and unsigned
  integers;
- [bare-source input](../../raw/bare-source.md): the specific usage acknowledgement
  is recorded while general enclosure mechanics remain future work;
- [numeric-family input](../../raw/numeric-type-families.md): invented
  zero-through-128 provenance is removed;
- [analysis-control input](../../raw/analysis-controls.md): project/module/file/local
  selection, overrides, safety locking, imported/generated source, and
  reproducibility pressure are retained for both unchecked contracts;
- [indexing and slicing input](../../raw/indexing-and-slicing.md): `..=` is the
  closed-range candidate, `..<` is half-open, and bare `..` remains unassigned;
  and
- this active work record: findings, dry-run result, applied disposition, and
  validation.

No structural, router, prompt-source, legacy-page, or additional owner change
was required. The applied file set matches the incremental dry run exactly.

Validation passed:

- changed Markdown has no whitespace errors;
- local links and heading anchors remain valid;
- `bare{ ||myValue| }` is taught as available rather than unavailable;
- the unacknowledged form is marked as an intent error;
- no false zero-through-128 requirement remains in live raw numeric input;
- both unchecked contracts retain future directive hierarchy/override pressure;
- raw range input contains `..=`/`..<` and assigns no two-ended meaning to `..`;
  and
- the staged review boundary remains unchanged, with this seven-file correction
  unstaged.

## Closure

| Field | Value |
| --- | --- |
| Date | 2026-08-31 |
| Result | **CLOSED** |
| Current Owners | Zax integers, identity types, integer operator catalog, general operator catalog, and integrated current owners named above |
| Deferred Input | Indexed raw files named by the final promotion and correction dispositions |
| Successor | `011: Integer literals and compile-time realization` |

Every material finding has a current owner, indexed future destination,
discarded/superseded disposition, or historical role. Current documentation does
not depend on this archive record for meaning.

The successor incorporates the accepted integer foundation and focuses on
integer literal realization. It does not require this archived file as ordinary
reading.
