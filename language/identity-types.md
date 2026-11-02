# Zax identity types

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers defining, reading, or converting aliases and representation-related types |
| Applies To | Transparent type aliases and distinct identities over existing types; not a formal grammar or specification |
| Implementation State | Not established by this repository |
| Owns | Transparent aliases and canonical identity preservation under alias property overlays; identity boundaries; immediate underlying type/value/place operations; identity declarations and original-owner body authority; admission; identity projection; exposed and opaque surfaces; identity-specific application of the shared composition exposure filter; contextual-posture reset and non-forwarding; declared bridges; construction/transfer requirements; costs, diagnostics, and source stability |
| Does Not Own | Integer-family membership and numeric-source realization ([integers](integers.md), [integer literals and realization](integer-literals.md)); enum members and policies ([enums](enums.md)); qualifier semantics ([qualifiers](qualifiers.md)); the shared exposure filter and general composition behavior ([Zax composition](composition.md)); partial behavior ([partials](partials.md)); or general [structural shape and compatibility](structural-shapes-and-compatibility.md) |
| Source / Provenance | Legacy alias and enum evidence refined through fundamental-integer and conversion review |

## Two ways to build on an existing type

Zax can give an existing type another name, or define a new type that uses an
existing type as its underlying type.

```zax
MyShortName :: alias type SomeLongTypeName
MyCount :: identity admit expose type U32
```

`MyShortName` and `SomeLongTypeName` are the same type. The alias only provides
another name.

`MyCount` and `U32` are different types. `MyCount` uses `U32` as its underlying
type, so it begins with the same representation and value capacity. The
identity declaration decides how `U32` values may create `MyCount` values and
whether `U32` functions and operators become available on `MyCount`.

```zax
myRaw : U32 = 10
myCount : MyCount = MyCount from myRaw

myRaw = myCount // error: different identities
```

The explicit `from` states where the program crosses between them.

Enums specialize this model. An enum is an integer-backed identity that adds
known member values and strict, relaxed, flags, string, and generated-operation
policies defined by [Zax enums](enums.md).

An enum may use a language-provided integer identity such as `Integer` as its
immediate backing:

```zax
MyEnum :: enum {
}

Backing :: alias type MyEnum underlying type // Integer
```

The enum-to-`Integer` boundary and the deeper `Integer`-to-exact-intrinsic
boundary remain separate. Immediate underlying operations cross only one
boundary.

## Transparent aliases

```zax
MyShortName :: alias type SomeLongTypeName
```

A transparent alias is another name for one existing type:

- both names refer to the same type identity;
- no conversion is required between the names;
- both names have the same values, operations, and representation;
- a use may carry an explicitly overlaid qualification, stance, indirection, or
  compatibility profile without changing canonical identity; and
- the alias creates no additional overload domain.

```zax
myOriginal : SomeLongTypeName
myAlias : MyShortName = myOriginal
```

Diagnostics may retain the alias spelling when that helps explain source:

```text
MyShortName (alias of SomeLongTypeName)
```

That presentation does not make the alias another type.

### Alias property overlays do not create identity

A concrete alias may abbreviate the complete property profile accepted where a
type is explicitly declared:

```zax
DeepView :: alias type MyType readonly & deep
CopyView :: alias type DeepView writable copy
```

Both aliases retain the canonical `MyType` identity. `CopyView` inherits the
reference shape, replaces the access and transfer axes it states, and receives
ordinary defaults only for still-unresolved properties.

The overlay does not convert or modify an existing value. A declaration using
`CopyView` still fails when its source cannot provide writable access or the
requested transfer. Alias expansion never manufactures capability.

The overlay algorithm and exact alias categories are defined by
[declarations and bindings](declarations-and-bindings.md#exact-aliases-and-property-overlays).
[Qualifiers](qualifiers.md), [transfer stances](transfer-stances.md), and
[structural compatibility](structural-shapes-and-compatibility.md#compatibility-posture)
retain the meaning and safety constraints of their axes.

## Identity boundary and underlying type

An **identity boundary** separates types that are not interchangeable even when
their representation is the same.

Assignment, argument passing, return, and operator matching do not cross the
boundary unless the language or type declares an explicit operation:

```zax
myCount : MyCount
myRaw : U32

myCount = myRaw // error: identity boundary
myCount = MyCount from myRaw
```

The **identity type** is the new outer type, such as `MyCount`.

The **underlying type** is the existing type on which it is based, such as
`U32`.

The **underlying value** is the value represented before admission or returned
after identity projection.

An identity boundary is semantic, not necessarily expensive. Crossing it may
require no representation change while still requiring explicit source.

## Identity declaration

An identity declaration answers two independent questions:

1. May every valid underlying value create this identity automatically?
2. Which functions and operators from the underlying type become available?

Both answers are explicit:

```text
Name :: identity <admission> <surface> type UnderlyingType
```

Exactly one admission keyword is written:

| Keyword | Meaning |
| --- | --- |
| `admit` | Permit ordinary `from` for every valid underlying value when the requested construction or transfer is viable |
| `restricted` | No ordinary admission is generated; the identity declares validation, unchecked admission, unsafe admission, or no admission |

Exactly one surface keyword is written:

| Keyword | Meaning |
| --- | --- |
| `expose` | Make eligible underlying functions and operators available after adapting them to the new identity |
| `opaque` | Do not make underlying functions or operators available automatically |

The four combinations serve different purposes:

```zax
MyInteger :: identity admit expose type Integer
MyValidatedInteger :: identity restricted expose type Integer
MyToken :: identity admit opaque type U64
MyDocumentHandle :: identity restricted opaque type Integer
```

There is no omission default for either choice. Admission and exposure state
consequential intent.

### Original definition body and completion

An identity without a body completes as soon as its declaration
finishes:

```zax
MyInteger :: identity admit expose type Integer
```

An original definition body may keep the identity open while its owner adds
constructors, functions, and operators:

```zax
// Illustrative future identity-body/completion syntax.
MyContextualInteger :: identity admit expose type Integer {
  +++ contextual final : ()(rhs : Integer) = {
  }

  operator binary '+' contextual existing

  double final : ()() = {
    _ *= 2
  }
}
```

This is a compile-time definition phase, not a runtime state. No code observes a
partially defined identity. The closing brace completes the identity's own
definition. Later additions come only from partials, as described in
[partial definitions](#partial-definitions).

The body belongs to the original identity owner. It does not introduce class
inheritance: the identity remains a distinct representation/composition-based
type with the admission and exposure choices stated by its header.

A new identity resets inherited completion posture to `explicit`. The owner
must opt its construction and exposed operator into `contextual` completion
separately:

```zax
myValue := 0 + (: MyContextualInteger = 5)
```

Exact identity-body and `contextual existing` grammar remains future source
integration. Completion behavior is defined by
[Zax operators](operators.md#contextual-completion).

### Integer underlying types do not grant integer membership

An identity does not become an intrinsic integer merely because its underlying
type is an integer:

```zax
MyInteger :: identity admit expose type Integer
myValue := 0 + (: MyInteger = 5)
// error by default: identity construction and exposed + are explicit
```

`admit` supplies declared admission and `expose` adapts eligible operations to
the new identity. Neither grants fundamental integer realization or general
implicit admission from numeric source. Contextual completion is an independent
opt-in owned by the identity declaration.

A transparent alias remains the underlying integer type and retains its
behavior. A future custom numeric-family mechanism may define genuine
programmer-supplied integer participation; ordinary identity declarations do
not. See
[Zax integer literals and realization](integer-literals.md#identities-do-not-become-integers-through-storage).

## Admission

Admission creates a value of an identity type from a value of its underlying
type. A type family may also declare admission from another explicitly related
type.

### Ordinary admission

An `admit` identity supplies ordinary `from` for its underlying type:

```zax
myRaw : U32 = 10
myCount : MyCount = MyCount from myRaw
```

Every valid `U32` value is a valid underlying value for `MyCount`, so no
semantic validation is required.

Type-specific families may declare other ordinary admissions. For example,
integer role identities may accept values from another integer type when its
complete value range fits. Those relationships belong to the type family that
defines them.

### Restricted admission

A `restricted` identity receives no automatic ordinary `from`:

```zax
myHandle : MyDocumentHandle =
  MyDocumentHandle from myRaw // error unless MyDocumentHandle declares it
```

The identity may instead declare one or more policies:

```zax
myHandle : MyDocumentHandle? =
  MyDocumentHandle optional from myRaw

myUnchecked :=
  MyDocumentHandle unchecked from myRaw

myUnsafe :=
  MyCapability unsafe from myBits
```

- `optional from` validates and returns absent when the value is rejected.
- `unchecked from` skips semantic validation but retains defined mechanical
  behavior.
- `unsafe from` bypasses an invariant required for memory, lifetime,
  representation, concurrency, or optimizer correctness.

`restricted` does not decide which of these exists. The identity's invariants
decide whether admission is validated, unchecked, unsafe, or unavailable.

The resulting wrapper's construction, qualification, and lifetime behavior is
defined by [Zax optional values](optional-values.md).

Complete safe-subset classification remains future safety work.

## Identity projection

Identity projection takes an identity value and returns its underlying value
without changing the represented value:

```zax
myCount : MyCount
myRaw := myCount as U32
```

`myRaw` is the `U32` value represented by `myCount`. Projection changes the
static type from `MyCount` to its underlying `U32`; it does not make another
identity valid.

An opaque identity receives this immediate projection without receiving every
operation of its underlying type.

Type-specific exposed identities may provide additional conversions. Integer
identity conversion is taught by [Zax integers](integers.md#integer-identity-types).

## Direct bridges between identities

Two identity types defined independently from the same underlying type remain
unrelated:

```zax
MyDocumentHandle :: identity restricted opaque type Integer
MyFileHandle :: identity restricted opaque type Integer

myDocument : MyDocumentHandle
myFile : MyFileHandle = myDocument // error
```

The general route is explicit projection followed by admission:

```zax
myFile :=
  MyFileHandle unchecked from (myDocument as Integer)
```

A direct bridge exists only when the type family declares that relationship.
Representation equality alone is insufficient.

For example, integer size and index identities may deliberately declare exact
bridges because their ranges and representations are equal:

```zax
mySize : TypeSize = myIndex as TypeSize
myIndex : IndexSize = mySize as IndexSize
```

## Exposed and opaque behavior

`expose` makes eligible functions and operators of the underlying type
available on the identity type. The simplest case is an identity whose complete
representation is exactly its underlying value:

```text
Integer + Integer -> Integer
```

For a representation-trivial `MyInteger`, exposure can adapt that operation
conceptually to:

```text
MyInteger + MyInteger -> MyInteger
```

No missing state has to be invented: a complete `MyInteger` consists of the
underlying integer at offset zero. Mechanically compatible by-copy inputs and a
copied underlying-self result can therefore substitute the identity type.

Results belonging to another domain stay in that domain. An integer comparison
still returns `Boolean`; a shift count still uses the integer's associated count
type.

Additional resident data changes the answer. Exact identity-body grammar
remains future source integration, but consider an identity that conceptually
stores a tag as well:

```zax
// Illustrative future identity-body syntax.
TaggedInteger :: identity admit expose type Integer {
  tag : Tag
}
```

A copied `Integer` result is not a complete `TaggedInteger`: no mechanical rule
can choose the missing `tag`. It may nevertheless pass through unchanged as an
`Integer`; only remapping it to `TaggedInteger` is unavailable:

```text
TaggedInteger + Integer -> Integer
```

Exposure may also retain operations whose results already belong to another
domain, such as a comparison returning `Boolean`, when all inputs map safely.
Only an offset-zero, representation-trivial identity has the narrower exception
shown above in which a copied underlying-self result can substitute the complete
identity type.

Reference and pointer results have a different concern. They can map from the
underlying receiver back to the identity receiver only when an explicit,
verified `self` contract proves that the returned reference or pointer is the
receiver. Exposure never guesses result origin.

These rules use the same
[mechanical eligibility filter](composition.md#mechanical-eligibility) as
ordinary composition. An identity with additional data receives only the subset
that an analogous `expose` member container could safely expose. Exposure checks
the mechanical mapping; it does not prove user-defined cache or cross-member
invariants. The identity owner may fence a generated signature or write a
custom wrapper.

The additional-data rule constrains future identity-body syntax; the
illustrative body form above is not itself settled.

This is a programmer model, not required compiler lowering and not class
inheritance.

`opaque` performs none of this automatic exposure. The identity may still
declare its own functions and operators.

### Immediate underlying operations do not forward

The original definition body normally uses the adapted identity surface:

```zax
double final : ()() = {
  _ *= 2
}
```

When owner code needs the underlying relationship directly, each identity
boundary provides operations for exactly its immediate layer:

```zax
UnderlyingType :: alias type MyIdentity underlying type
myUnderlying := myIdentity underlying value
```

`underlying type` returns the immediate underlying type. `underlying value`
returns one copied/projected value of that type. Existing
`as UnderlyingType` projection remains the explicit conversion-shaped route to
the same immediate underlying value.

`underlying place` is generated as a private final post-unary operation. It is
eligible only inside the private context implementing this immediate identity
boundary and grants no stronger place, value, access, or lifetime permission
than the receiver path already carries:

```zax
doubleUnderlying final : ()() = {
  (_ underlying place) *= 2
}
```

The operation produces a place, not a first-class reference with independent
alias or escape guarantees. A function may bind a reference from that place
when ordinary permissions allow it.

`underlying type`, `underlying value`, and `underlying place` describe the
current boundary and are not mechanically forwarded by `expose`. An outer
identity regenerates them for its own immediate layer. It may obtain a copied
deeper value without gaining the inner identity's private place access:

```zax
myDeeperValue := (_ underlying place) underlying value
myDeeperValue.doSomething()
```

The first operation reaches the outer identity's immediate underlying place.
The second invokes the inner value's public copied/projected operation. Changes
to `myDeeperValue` do not mutate the nested stored value.

A second `underlying place` is unavailable unless the current code is also
private-eligible for that inner identity or the inner owner deliberately
publishes another access operation. Wrapping a private identity does not grant
permission to tunnel through it.

Optional naming of the underlying place remains future identity design.
`own`-published member paths preserve the physical place's qualification and
lifetime under
[current composition rules](composition.md#publishing-stored-data-with-own).

## Construction and transfer

By-value admission requests a new identity value:

```zax
myIdentity := MyIdentity from myUnderlying
```

The operation needs a viable way to establish the underlying stored value:

- `copy`;
- `deep` copy;
- `move`;
- consuming/`last` transfer;
- direct construction; or
- another declared transfer.

Identity syntax does not manufacture copyability. If no applicable construction
or transfer exists, by-value admission is unavailable. `move` may fall back to
`copy`; `last` may fall back to `move` and then `copy`; `deep` requires an exact `deep`
contract. Complete stance meaning and source state are defined by
[Zax transfer stances](transfer-stances.md).

A same-storage identity reference is a different operation. It would create a
view of existing storage rather than a new value, perform no copy, and schedule
no independent destruction. It constructs another reference bound permanently
to the same instance place and forwards the underlying origin and lifetime
requirements. Identity syntax cannot hide replacement, move, or destruction of
that place.

Exact identity-reference admission, representation-cast, alias, qualifier, and
qualified type-identity discovery remain future identity and casting work.
Complete reference behavior is defined by
[lifetimes and references](lifetimes-and-references.md#same-storage-identity-views).

The compiler never silently substitutes a reference view for a requested
by-value result.

## Representation and qualifications

An identity begins with its underlying value at byte offset zero and adds no
instance storage merely because it has another identity. An identity definition
may carry additional resident data; that data affects total size, alignment,
copy behavior, and exposure eligibility. Complete layout, shape, and
same-storage-reference behavior follows
[Zax structural shapes and compatibility](structural-shapes-and-compatibility.md).

A representation-trivial identity retains the scalar format or aggregate shape
of its immediate underlying value while adding a distinct outer identity.
Equal shape never creates implicit admission or projection. A compatibility
posture or explicit structural operation may authorize a recast independently
from the identity's `admit`/`restricted` and `expose`/`opaque` policies.

A whole-root same-storage compatible reference can retain varying replacement
authority only when complete resident-lifetime, qualification, tracking,
construction, replacement, and destruction obligations match. Additional
resident identity data or incompatible custom lifecycle behavior prevents that
relationship.

Identity does not weaken qualifications. Projection, admission, construction,
and transfer use the applicable place, value, access, `copy`, `deep`, `move`, `last`,
and lifetime rules.

Storage compatibility never grants implicit conversion.

### Nothing preparation

A representation-trivial identity uses its compiler-prepared underlying
default when a Nothing instance needs a readable member:

```zax
MyCount :: identity admit expose type U32
```

The prepared `MyCount` representation starts from the prepared `U32` default.
This is not ordinary source admission through `from` and does not bypass an
identity boundary for ordinary values.

An identity with additional resident data follows the recursive aggregate
member rules. If an exact member path has no valid prepared representation,
access through compiler-provided Nothing uses the applicable panic or hardware
trap where supported. A trapping policy requests target trapping before
exposing the identity, while custom Nothing storage is prepared by its
`+++ final once` body.

Complete behavior belongs to
[Zax Nothing instances](nothing-instances.md#what-compiler-preparation-provides).

## Partial definitions

An identity is a type, so a [partial](partials.md) can add functions to it
unless its declaration closes callable additions:

```zax
MyIntegerExtension :: partial MyInteger {
  // Additional MyInteger functions.
}
```

A partial adds; it does not suppress, hide, restrict, remove, or replace
behavior exposed by the original identity declaration, and it is visible only
where a scope grants it.

Like any type, an identity accepts added stored members only when it writes
`seal open storage`. Stored members change its representation, and so the
exposure mappings and bridges that depend on it. Partials supplied by the
language, the compiler, or a CPU provider remain future work.

## Costs and diagnostics

An identity may share representation with its underlying type while still
introducing meaningful work or constraints:

- explicit construction or transfer;
- validation and optional failure;
- an unchecked or unsafe assertion;
- copies, moves, references, and temporaries;
- another overload domain; and
- exposed or locally declared operations.

Diagnostics should distinguish:

- transparent alias from distinct identity;
- the identity type from its underlying type;
- missing ordinary admission from failed validation;
- optional, unchecked, and unsafe admission;
- immediate identity projection from unavailable identity-to-identity
  conversion;
- equal representation without a declared bridge;
- an operation absent because the identity is opaque; and
- unavailable by-value admission because no construction or transfer applies.

Diagnostics lead with source/public names and make underlying representation
detail available on demand.

## Source stability

- Changing a transparent alias changes the type denoted by that name.
- Changing one overlaid qualifier, stance, indirection, or compatibility
  property changes which declarations and sources can use that alias without
  creating another canonical identity.
- Changing an identity's underlying type may change layout, value capacity, and
  cost without erasing its identity boundary.
- Changing `admit`/`restricted` or `expose`/`opaque` changes source validity.
- Adding a direct bridge enables previously invalid identity conversion.
- Changing underlying behavior may affect an exposed identity under the current
  shared [composition exposure filter](composition.md#mechanical-eligibility).
- Adding resident identity data may make mappings that require a copied
  identity result ineligible.

These are compatibility events, not invisible implementation choices.

## Boundaries and maturity

This document is current conceptual design, not formal grammar, a complete
layout/ABI contract, or an implementation mapping.

Future work owns language- and provider-supplied partials, generic identity
factories, and reflection APIs. [Structural shapes and compatibility](structural-shapes-and-compatibility.md),
[current composition](composition.md), and
[current enum behavior](enums.md) preserve the explicit identity boundary and
admission/exposure choices defined here.
