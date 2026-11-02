# Zax unions

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers using untagged overlapping storage and explicit low-level representation views |
| Applies To | Programmer-visible union declarations, lenses, admissibility, initialization, transfer, safety, and costs; not a formal layout or ABI specification |
| Implementation State | Not established by this repository |
| Owns | Untagged offset-zero union storage; lens declarations and access; union admissibility; all-bit-pattern validity; plain and unsafe unions; zero-fill; jagged overlap; size/alignment pressure; all-bit copy; lens-validity proof; union costs, diagnostics, and source stability |
| Does Not Own | General type definitions ([type definitions](type-definitions.md)); scalar formats; general unsafe contracts ([safety and analysis](safety-and-analysis.md)); future foreign-union and ABI contracts; or managed alternatives ([variants](variants.md)) |
| Source / Provenance | Maintainer-reviewed union direction superseding the legacy active-member model, reconciled with current scalar, lifetime, qualifier, pointer, callable, array, and safety design |

## Start with several lenses over the same bits

```zax
MyRegister :: union {
  lowByte : U8
  signedWord : I32
  unsignedWord : U32
}

myRegister : MyRegister
myRegister.signedWord = -1

print(myRegister.unsignedWord)
```

`MyRegister` owns one backing region. Every declared name is a typed **lens**
beginning at the same address:

- `lowByte` views the first byte;
- `signedWord` views the first four bytes as `I32`; and
- `unsignedWord` views those four bytes as `U32`.

The union has no active-lens tag. Writing `signedWord` does not construct that
lens or destroy `unsignedWord`. It changes the common representation observed
through every lens.

`I32` and `U32` can interpret every 32-bit pattern, so `-1` through the signed
lens remains a valid unsigned representation. The numeric interpretation
changes; the bits do not.

> A union is one untagged value with several named typed lenses. It is not a
> managed “one of these payloads” value.

Use a [variant](variants.md) when payload construction, destruction, ownership,
or active-alternative selection is required.

## Default construction zero-fills the backing

```zax
value : MyRegister
```

Default union construction fills the complete backing with zero bits. It does
not invoke a lens type's constructor.

For a plain union, zero must therefore be a valid representation through every
lens. Because every plain-union lens accepts every bit pattern, zero follows
automatically.

`union unsafe` may contain a lens for which zero is not a valid ordinary value.
The union backing still exists, but source may not use that lens until its
validity is proved or asserted.

## Union-admissible storage

Before asking whether arbitrary bits are valid, Zax asks whether a type can be a
union lens at all.

A type is **union-admissible** when its stored representation can be:

- overwritten;
- copied as part of the complete union backing; and
- abandoned when the union lifetime ends

without invoking or bypassing required construction, destruction, replacement,
resource disposition, reference binding, ownership accounting, or
placement-repair work.

This is intentionally narrower than “has bytes” and more precise than the
overloaded term “trivial.”

### Derived aggregates

An ordinary aggregate can be union-admissible when:

- every direct stored component is union-admissible;
- it has no required custom construction, destruction, copy, replacement,
  ownership, or placement hook; and
- no hidden structural component requires lifecycle maintenance.

Layout padding does not prevent admissibility. Padding belongs to the
representation and may hold unspecified bits.

The compiler does not inspect arbitrary function bodies to infer semantic
invariants. A restriction affects union safety only when it is represented by a
language-visible identity, admission policy, lifecycle operation, hidden
component, or stored member.

### Types that are never union-admissible

These categories cannot appear even in `union unsafe`:

- `String` and other allocation- or lifetime-managing values;
- arrays, including arrays using contiguous `Flat` storage;
- references, whose fixed binding and referent lifetime cannot be overwritten
  as bits;
- managed `unique`, `unique shareable`, `strong`, `weak`, or atomic pointer
  forms, including interior pointers;
- callables carrying a bound receiver lifetime;
- `outer tracked` values, whose hidden placement state requires repair; and
- any ordinary type whose required lifecycle hooks or recursively contained
  members create the same obligations.

```zax
MyInvalidUnion :: union unsafe {
  text : String             // error: managed allocation/lifetime
  values : U32[4]           // error: owns element lifetimes
  owner : MyValue * unique  // error: owns a life path
  view : MyValue &          // error: fixed reference binding
}
```

`unsafe` can accept representational validity responsibility. It cannot erase a
required lifecycle or ownership operation.

### Unsafe-only passive representations

A raw pointer owns no pointee lifetime and performs no release, so its pointer
value can be passively overwritten and copied. Arbitrary bits do not establish
a valid address, provenance, alignment, live pointee, or destination Nothing
representation. Raw pointers are therefore permitted only in `union unsafe`.

An unbound callable may likewise be permitted in `union unsafe` when its
representation has no receiver-lifetime obligation. Zero-filled or overlaid
bits need not describe an available callable.

## Every bit pattern is valid in a plain union

A union-admissible type is **all-bit-pattern-valid** when every pattern in its
complete storage envelope represents an ordinary valid value.

Padding and non-value bits are ignored by that type and may have any contents.
For example, an unusual integer width may ignore high envelope bits. Those bits
can still affect a wider overlapping lens.

A plain union requires every lens to be:

1. union-admissible; and
2. all-bit-pattern-valid.

That conservative rule makes the complete union overlay-safe without requiring
pairwise code analysis. After any write, every lens remains valid because every
possible pattern is valid for every lens.

Examples commonly eligible for plain unions include:

- exact integers, including signed/unsigned peers and unusual widths whose
  non-value bits are ignored;
- IEEE-style Binary16/32/64/128 formats whose complete bit domains are valid;
  and
- plain aggregates recursively composed from eligible stored values.

A restricted identity or strict enum does not admit every backing value through
ordinary safe behavior:

```zax
MyCode :: enum U32 {
  Ready = 1
  Failed = 2
}

MyCodeBits :: union unsafe {
  raw : U32
  code : MyCode
}
```

The type is passive enough for an unsafe union, but arbitrary `U32` writes need
not produce an ordinarily admitted `MyCode`.

This rule may reject two restricted types whose valid subsets happen to match
exactly. Use `union unsafe`. A future explicit relationship can recover such a
case if real pressure justifies the extra contract.

## Jagged overlays are intentional

Lenses need not have equal extent:

```zax
MyJaggedRegister :: union {
  lowByte : U8
  wholeWord : U32
}
```

The union:

- begins every direct lens at offset zero;
- has enough extent for its largest lens; and
- satisfies the strongest required lens alignment.

Writing `lowByte` changes only the first byte. The other three bytes retain
their previous zero-filled or written contents.

Concrete endianness decides which part of `wholeWord` that first byte
represents. The source is representation-safe but intentionally
environment-sensitive:

```zax
register : MyJaggedRegister
register.wholeWord = 0
register.lowByte = 1

// The resulting wholeWord value depends on concrete byte order.
```

Use absolute-endian scalar types or another explicit representation when the
byte relationship must remain stable across environments.

### Padding and non-value bits

A lens write may leave its padding or non-value bits unspecified. A plain union
remains safe because every receiving lens accepts every resulting pattern.

In an unsafe union, source that interprets those overlapping bits through a
restricted lens needs proof or explicit unsafe responsibility. The language
does not promise that ignored bits remain stable after another write.

Bytes added to an ordinary type with `[<reserve=n>]` follow the same rule: they
carry no value. See
[explicit member layout](type-definitions.md#explicit-member-layout).

## Plain and unsafe unions

### Plain union

Every lens is always valid to read or write under its qualifications:

```zax
MyBits :: union {
  signedValue : I32
  unsignedValue : U32
  floatValue : Binary32
}

bits : MyBits
bits.unsignedValue = 1065353216
print(bits.floatValue) // valid Binary32 representation
```

This is not numeric conversion. It is another interpretation of the same stored
bits.

### Unsafe union

`union unsafe` permits passive lenses whose complete bit domains are not
ordinary safe value domains:

```zax
value : MyCodeBits

value.code = MyCode.Ready
use(value.code) // the written lens is known valid

value.raw = readRawCode()
```

After the arbitrary raw write, using `value.code` requires a proof that the bits
name an admitted code or the exact unsafe category `union-lens-validity`.

Exact unsafe-enclosure syntax remains future analysis-control work. The current
illustrative spelling is:

```zax
unsafe<union-lens-validity>{
  use(value.code)
}
```

A false assertion has undefined consequences. It does not validate or convert
the bits.

### Portable minimum proof

The selected language contract preserves these baseline facts:

- zero-fill proves every zero-valid lens;
- writing a valid value proves that written lens;
- a direct whole-union copy preserves facts known at the source;
- branch convergence retains only facts true on every incoming path;
- a readonly call preserves lens facts;
- a call or alias that may write the union invalidates affected facts; and
- an opaque function result carries no lens-validity fact without a future
  callable contract.

A compiler may privately prove more and use that information for optimization
or advice. Private cleverness alone cannot make assertion omission portable or
make portable asserted source invalid. Stronger source validity belongs to an
explicitly selected analysis contract. See
[safety and analysis](safety-and-analysis.md#language-contracts-and-compiler-analysis).

## Lens access, qualification, and references

A lens derives authority from the path used to reach the union:

```zax
bits : MyBits
reader : MyBits readonly & = bits

print(reader.unsignedValue)
reader.unsignedValue = 1 // error: readonly union path
```

A lens creates no independently replaceable place. A reference obtained from a
plain-union lens remains tied to the union backing:

```zax
view : U32 readonly & = bits.unsignedValue
bits.floatValue = 1.0
print(view) // observes the changed common representation
```

For `union unsafe`, reference formation itself requires current proof that the
requested lens is valid. A later overlapping write can invalidate the typed use
of that reference even though its address remains unchanged.

Structural flattening stops at a union. A lens is an alternate interpretation,
not another resident member that shape comparison can silently enter.

## Whole-union operations

Whole-union construction or assignment from the same union type copies every
backing bit:

```zax
source : MyBits
source.unsignedValue = 1065353216

copy : MyBits = source
destination : MyBits
destination = source
```

The compiler cannot choose one lens because no lens is active.

`move` and `last` use ordinary fallback to this `copy`; the source remains
unchanged. `deep` is unavailable because an unsafe union may contain passive raw
pointers or similar values whose copied representation is not an independent
resource graph.

No equality, ordering, or hashing is generated from backing bits. Padding and
domain interpretation make those operations type-specific. A programmer can
compare a chosen lens or define an ordinary final function with the desired
policy.

Ending the union lifetime abandons its backing. It calls no union or lens
destructor.

### Selective copy

Source can deliberately copy one lens instead of the complete union:

```zax
destination.lowByte = source.lowByte
```

Only that lens extent is written; a jagged destination suffix retains its
previous bits.

## Forming an immutable or compile-time union

Union construction packets are unavailable because they would suggest
construction of an active lens. Use an ordinary factory:

```zax
MyRegister :: union {
  byte : U8
  word : U32

  fromWord final : (
    result : MyRegister
  )(
    value : U32
  ) unbound = {
    resultValue : MyRegister
    resultValue.word = value
    return resultValue
  }
}

constantRegister final :
  MyRegister immutable = MyRegister.fromWord(4995)
```

The factory zero-fills one ordinary union, writes a lens, and returns the
complete bits. It may run at compile time when ordinary compile-time execution
rules permit. No raw representation literal is implied.

## Named, forwarded, and anonymous unions

A named union exposes its incomplete self-name while its body resolves. Direct
self storage remains infinitely recursive; finite pointer indirection is
possible only when that pointer form is otherwise union-admissible, which
excludes managed pointers.

Source-order or mutual relationships use the category-specific anchor:

```zax
MyOverlay :: forward union
```

It must complete through a direct union declaration or an exact alias to an
existing union:

```zax
MyOverlay :: alias union ExistingUnion
```

An anonymous union is one stable compile-time identity:

```zax
bits : :: union {
  byte : U8
  word : U32
}

MyBitsType :: alias union type of bits
```

Repeated values from that declaration share its identity. Equal anonymous union
source elsewhere does not. Anonymous recursive self-syntax is unavailable.

An exact alias preserves the specialized category:

```zax
MyBitsAlias :: alias union MyRegister
```

## Type body organization

A union body begins with its lens prologue:

```zax
MyRegister :: union {
  signedValue : I32
  unsignedValue : U32

  printBoth final : ()() readonly = {
    print(_.signedValue, _.unsignedValue)
  }
}
```

The first declaration that is not a lens begins ordinary definition mode.
Lenses cannot resume afterward.

Allowed later declarations include fixed functions and operators, `once`
declarations, aliases, and nested definitions that add no per-instance storage.
A direct varying callable declaration would add hidden per-instance storage and
is rejected; write a callable value explicitly as a lens where it is otherwise
eligible.

Union constructors, replacement constructors, destructors, and custom
whole-union copy/move hooks are prohibited. The exact same-type copy
construction and assignment contracts remain protected all-bit operations;
unrelated domain operators and named functions may implement explicit policy
through justified lens access.

## `Float`, exact formats, and source portability

`Float` is profile-selected. It may appear in a plain union when its resolved
exact representation is union-admissible and all-bit-pattern-valid for that
profile. The same source may be rejected under a profile selecting a format
with invalid patterns or incompatible lifecycle requirements.

This is consistent with using a profile-selected type. Source that requires one
portable relationship names exact formats:

```zax
PortableBits :: union {
  floatValue : Binary32
  rawValue : U32
}
```

Use `union unsafe` when the selected `Float` remains passive but does not accept
every pattern.

## Composition and foreign-layout boundaries

Composition modifiers apply to the complete union value. They do not treat one
lens as selected payload state or automatically publish through a lens.

A Zax union resembles a C/C++ offset-zero overlay, but that resemblance is not
an ABI promise. Zax's zero-fill, admissibility, qualification, all-bit copy, and
unsafe proof rules do not establish:

- C or C++ active-member behavior;
- aliasing rules;
- exact padding or calling convention;
- pointer or function representation; or
- stability across compilers, targets, or language versions.

A foreign union needs a named interop contract or adapter.

## Costs

Programmers must be able to discover:

- the largest lens extent and strongest alignment;
- environment-sensitive byte interpretation;
- complete zero-fill;
- all-bit whole copy;
- retained suffix bytes after jagged writes;
- unspecified padding/non-value bits;
- proof loss for unsafe lenses;
- absence of member construction and destruction; and
- compile-time analysis required for admissibility and validity.

A union adds no discriminant. `union unsafe` likewise adds no runtime validity
tag. Debug tooling may instrument assertions, but such instrumentation is not a
language guarantee.

## Diagnostics

Diagnostics should distinguish:

- a lens type that is not union-admissible from one that is passive but not
  all-bit-pattern-valid;
- the first nested lifecycle, ownership, reference, or placement obligation
  blocking admissibility;
- plain union requiring `union unsafe`;
- invalid or unproved unsafe lens access;
- a lens reference invalidated by an overlapping unsafe write;
- alignment or incomplete-type failure;
- a prohibited lens initializer or lifecycle hook;
- a varying callable declaration that would create hidden storage;
- unavailable `deep`;
- an attempted generated comparison; and
- profile-dependent `Float` rejection with the selected exact format shown.

## Source stability and maturity

Changing a lens type, extent, alignment, validity domain, lifecycle behavior, or
profile selection can change union validity and representation. Adding a larger
or more strongly aligned lens changes complete union storage. Adding lifecycle
work can make a formerly admissible type ineligible.

This document defines current conceptual design, not formal grammar, a layout or
ABI contract, a proof algorithm, reflection schema, foreign-union behavior, or
compiler lowering.
