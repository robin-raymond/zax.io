# Raw input: CPU provider model

| Field | Value |
| --- | --- |
| Status | Raw future-work input / non-authoritative |
| Audience | Future work defining target and compiler-host CPU profiles, native scalar and legacy character representations, numeric selection, and operation support |
| Applies To | Native representation classification; integer, fixed, floating, and legacy character selectors; support classes; machine-word families; pointer/size capacities; mapping stability; metadata; and fallback |
| Owns | Preserved CPU-provider format, versioning, selection, support, cost, runtime-hardware, X87, legacy character profile, and fallback requirements |
| Does Not Own | Accepted scalar semantics, CPU-profile source format, ABI contracts, or implementation algorithms |
| Source / Provenance | Current integer, fixed-point, floating-point, endian, and native/compiler-host/target execution design; work item `034` provider-partial deferral |

Current programmer-visible integer roles, ranges, and profile constraints are
owned by [Zax integers](../../language/integers.md). Fixed-point formats are
owned by [Zax fixed-point scalars](../../language/fixed-point-scalars.md), and
floating formats, role contracts, and support meanings by
[Zax binary floating-point scalars](../../language/floating-point-scalars.md).
This input retains the future provider format, versioning, metadata, runtime
selection, and compatibility mechanism.

## Native representation

A CPU provider classifies scalar formats that the selected CPU natively
represents. This says only that the CPU directly represents that format. It
does not promise direct multiplication, division, atomics, shifts, conversion,
or any other individual operation.

For example, an eight-bit target may classify `Byte` as native while a
minimum-sixteen-bit `Integer` uses a software-composed representation. The same
CPU may still lack an eight-bit multiplication instruction.

Per-operation instruction availability and cost are separate metadata.

## Scalar support classes

Future profiles need an extensible support classification with at least:

```text
Unsupported
Software
HardwareGuaranteed
HardwareOptional
TrapEmulated
```

The classification is a coarse selection fact rather than an exhaustive
operation bitmap. Current floating design separately defines its complete core
numeric bundle. An exact type may expose individual operations without
qualifying for that bundle, and future reflection can ask whether one exact
signature is available.

Runtime-optional hardware cannot change static type identity. It may select
another implementation of the same already chosen format. A trap-emulated
instruction path remains observably different in cost from guaranteed hardware
even when it implements the same scalar result.

A provider declaration cannot weaken an exact format's semantics. It either
supplies an operation for every valid input under that operation's contract or
reports the operation unavailable.

## Nothing-instance trap capabilities

Target profiles must eventually report whether the CPU and execution
environment can protect compiler-provided Nothing backing against:

- reads;
- writes; and
- execution through an invalid or protected function target.

[Zax Nothing instances](../../language/nothing-instances.md) uses write
protection whenever it is cheaply available and allows types or directives to
request trapping reads. The language does not emulate missing capability by
checking every potential access. A profile must therefore expose when the
requested trap is unavailable and the corresponding access has an undefined
boundary.

Built-in types always use the trapping read policy and retain one canonical
identity across modules. A provider cannot make built-in Nothing readable for
one module and trapping for another; it reports whether the selected target can
enforce the canonical policy.

Future provider metadata must distinguish immediate hardware trapping from
debugger-after-the-fact detection, generated panic-function routing, and
ordinary readable prepared backing. Exact CPU/OS protection mechanisms and
costs remain provider details.

## Integer selector facts

A provider supplies concrete selections satisfying language family contracts:

- widths classified as native scalar representations;
- provider-preferred fastest native choices;
- smallest qualifying native choices;
- the natural `Word` width;
- widest native signed and unsigned representations;
- ordinary, near, and far pointer-representation widths;
- type-size and index-size capacities;
- ordinary bit-count and byte-count representations; and
- software fallbacks when no native width satisfies a selector.

The integer factory consumes minimum/maximum width, signedness, preference,
environment, and fallback dimensions and returns one exact intrinsic
specialization.

The provider's concrete facts may eliminate checks and determine layout. They do
not ordinarily change source conversion availability established by public
family range contracts.

## Fixed-point provider facts

Concrete fixed-point formats and results are language-defined. A provider may
classify exact coefficient widths and operations as native, software,
hardware-optional, trap-emulated, or unsupported.

Provider metadata should identify native fractional multiply/divide operations,
widened full-product support, intermediate width, rounding support, overflow
policies, endian adaptation, and broad cost where relevant. It must not select a
different rounding result for ordinary `*` or `/`.

## Floating selectors and support

Profiles select:

- `Float`, the preferred general-purpose format with complete core numeric
  support;
- `FastF<N>`, the preferred supported format with at least `N` logical storage
  bits;
- `LeastF<N>`, the smallest native format with at least `N` logical storage
  bits;
- `NativeMaxF`, the selected widest natively represented format;
- `OptimizedMaxF`, the selected widest preferred optimized format; and
- `SupportedMaxF`, the selected widest format with the required support
  contract.

Width does not order floating precision and exponent range. When several formats
qualify, the versioned provider deliberately selects one and exposes its exact
format.

There is no provider-selected `LanguageMaxF` type. Future language-limit
mechanics expose compile-time scalar limits such as
`Scalars.Floating.LanguageLimits.maximumWidth`.

Profiles also need:

- representation support independently of numeric support;
- complete core-bundle support;
- individual operation availability;
- software/helper requirements;
- guaranteed versus runtime-optional hardware;
- broad operation costs;
- concrete endian selection; and
- active, target, and compiler-host mappings.

### X87 provider behavior

`X87Extended80` fixes encoding and validity but intentionally has
provider-relative operation behavior. A profile must identify the applicable
rounding, precision-control, exception, and instruction contract closely enough
for source and reflection to reason about portability. Providers may use their
hardware behavior rather than emulate one historical x87 generation.

### MBF40 support

`MBF40` fixes historical arithmetic behavior from Microsoft BASIC for 6502
Version 1.1 with `ADDPRC=1`. A provider may support only storage and conversion,
or any individually complete arithmetic operations. Declared arithmetic must
match that historical contract.

### Optimized maximum

`OptimizedMaxI`/`OptimizedMaxU` preserve a selection need distinct from native,
supported, and language maxima:

> Select the widest integer family for which the provider supplies its preferred
> general-purpose optimized implementation, whether through native
> representation or deliberately optimized software emulation.

An eight-bit CPU may select `U8` as `NativeMaxU` and an optimized 16-bit software
implementation as `OptimizedMaxU`. A 32-bit profile may select optimized 64-bit
behavior without claiming native 64-bit representation.

Future profiles must expose the selected width, broad cost expectations, and
which important operations use native instructions or software emulation.
“Optimized” does not promise one instruction or universal fastest behavior.

## Machine-word relationships

The provider chooses `Word` width `W`. Zax derives:

```text
HalfWord = ceil(W / 2)
DWord    = W * 2
QWord    = W * 4
OWord    = W * 8
```

The provider cannot assign unrelated meanings to these names. A derived width
without native representation uses software support. A width beyond the
language maximum makes that derived family member unavailable for the profile.

## Count and capacity relationships

The provider chooses ordinary `BitCount` and `ByteCount` representations subject
to language minima and the profile's predefined scalar families. Large count
types cover the language-wide maximum independently of ordinary native width.

Within each memory domain:

```text
IndexSize.maximum = TypeSize.maximum
IndexSize representation = TypeSize representation
```

Across memory domains:

```text
Near pointer capacity <= ordinary pointer capacity <= Far pointer capacity
Near.TypeSize.maximum <= TypeSize.maximum <= Far.TypeSize.maximum
Near.IndexSize.maximum <= IndexSize.maximum <= Far.IndexSize.maximum
```

Mirrored representations do not collapse distinct near/ordinary/far intent
identities.

## Legacy character profile

Current [string and character design](../../language/strings-and-characters.md)
moves native character roles under:

```text
Legacy.Char
Legacy.WChar (optional)
```

A CPU/target provider must eventually expose:

- exact narrow and wide logical widths;
- selected signedness for each available role;
- native narrow and wide encoding identities;
- whether the active profile uses ASCII, EBCDIC, UTF, a code page, or another
  declared mapping;
- target and compiler-host selections independently;
- storage alignment and endianness; and
- conversion/support/cost facts.

A conventional profile may use `I8` or `U8` representation for `Legacy.Char`
while retaining its distinct public identity. `Legacy.Char` is always defined.
A legacy wide profile may select sixteen, thirty-two, or another declared width;
otherwise `Legacy.WChar` is unavailable rather than synthesized from an integer
or `Rune`. “Legacy” does not mean unsupported.

The provider model must make an optional role detectable through future static
declaration-resolution/capability analysis without allowing an unavailable type
to be instantiated.

## Mapping stability

Changing a provider selection may change storage, alignment, overflow
boundaries, precision/range, operation availability, generated code, and
compatibility. Future work must decide:

- which mappings belong to a versioned target contract;
- how profile versions are selected and recorded;
- which changes are source, ABI, cache, or serialization compatibility events;
- how compiler-host and target profiles coexist in one build;
- how shared code names active versus explicitly pinned environment types; and
- which diagnostics identify an unsupported or incomplete profile.

ABI correspondence remains interoperability work. A native representation does
not by itself promise a foreign ABI type or calling convention.

## Provider-supplied functions

Current [partials](../../language/partials.md) let programmers add functions to
language-provided scalar types. Whether a CPU provider may also supply
optimized `final` functions on those types is undecided. Add design pressure only
if a concrete need appears. Any such mechanism must preserve protected
all-intrinsic signatures and must not depend on source or import order.

## Metadata requirements

Tooling and reflection need requested selector dimensions, selected exact
format, native/software/optional/trap classification, core support, individual
operation availability, environment, fallback, optimized maximum, X87 behavior,
profile identity/version, and per-operation capability/cost information where
later defined.

Diagnostics should lead with public integer role names and expose profile and
exact-specialization detail on demand.

### Compile-time support queries

[Compile-time selection](../../language/compile-time-code.md#choosing-code-at-compile-time)
is now current: a discarded `if compile` branch may name an unsupported
operation without demanding executable support, and `Compilation.target.hasFeature`
answers feature questions. Future work must expose the remaining facts above to
compile-time code so source can inspect:

- complete core support for an exact scalar type;
- one exact operation's availability;
- software, guaranteed hardware, runtime-optional hardware, or trap-emulated
  classification;
- the selected exact format and endianness; and
- language and profile size limits.

A selected branch that uses an unsupported operation still receives an
availability diagnostic. Layout queries (`size of`, `alignment of`, `offset of`)
must also define how unusual widths and padding participate when the compiler
host and target differ, and how a mismatch is diagnosed.

## Activation and retirement

Activate this input for CPU profiles, target descriptions, compiler-host/target
execution environments, native scalar selection, integer/fixed/floating
selectors, support classes, operation availability/cost, X87 behavior,
machine-word families, legacy character profiles, pointer and size capacities,
or mapping compatibility.
Move accepted behavior into target-profile, scalar, build, reflection,
compatibility, and interoperability owners, then retire this file after every
preserved requirement is dispositioned.
