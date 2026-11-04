# Raw input: type parameters and generics

| Field | Value |
| --- | --- |
| Status | Raw future-work input / non-authoritative |
| Audience | Future work extending generics into aliases, qualifier families, numeric factories, protocol constraints, or generated type families |
| Applies To | Generic questions that remain open after the generic foundation became current design |
| Owns | Preserved questions on generic aliases and forwards, alias and qualified type-identity discovery, qualifier-parameterized families, generic protected numeric conversion, Nothing policy per specialization, intrinsic numeric families and factories, relational pairs, associated-type facilities, fixed and floating specialization details, bounded size probing requirements, protocol and allocation-policy constraints, string terminator policies, whole-type contracts, generic enums and partials, an exact-type specialization form, and their activation and retirement |
| Does Not Own | Current generic behavior ([Zax generics](../../language/generics.md)) or current declaration, scalar, type, and iteration behavior |
| Source / Provenance | Legacy `meta-types.md` and `meta-functions.md` evidence together with operator-phrase review of type parameters and type-qualified discovery, work item `012` optional-depth substitution pressure, work item `015` allocation-policy pressure, work item `020` composition constraints, work item `034` partial deferrals, and work item `036`, which promoted the generic foundation |

## Why this input exists

[Zax generics](../../language/generics.md) is current design. It owns both
forms of generic, `$` lists and `$(...)` supply, `[<constant>]` inputs,
per-input processing, filling compile-time parameters, types as compile-time
values (including building types from values during compile-time execution),
instantiation identity, constraints and `requires`, specialization ranking,
lookup inside generic bodies, open extension with program-wide agreement,
formation and consistency, and `[<instantiation-depth=...>]`.

This file keeps the generic questions that foundation deliberately leaves
open. Each section states what is still undecided and the constraint current
design places on it.

## Generic aliases and forwards

A fully applied alias already works: it names one instantiation and creates no
identity.

```zax
MyFlags :: alias type MyBits$(Boolean)
```

A partially applied alias, or an alias for a whole generic type family, is
different: it identifies a family with remaining parameters rather than one
concrete type. Bare `MyBits$`, with no parentheses, is reserved for naming the
family. Generic function families already use `alias variable`.

```zax
// Illustrative future shapes:
MyFlagSet$ :: alias type family MyBits$
MyIntMap$(Value : type) :: alias type family Map$(Integer, Value)
```

Future work must define the declaration shape. Concrete aliases may carry an
overlaid profile of qualification, indirection, transfer stance, and
compatibility posture. Generic aliases must decide how remaining parameters
interact with that profile while preserving:

- explicit more-local properties replace inherited properties on the same axis;
- omitted properties inherit before common defaults apply;
- canonical type identity remains separate from the resolved profile; and
- no specialization or alias manufactures capability on an actual source.

**Generic forwards.** Current non-generic forwards anchor a name and category
before one exact completion. Generic work needs the corresponding model for a
generic type family, a partially applied generic alias, a generic callable
family, and generated qualifier or stance specializations. It must decide which
facts a forward promises, when constraints are checked, and how an exact
concrete alias completion differs from a still-generic declaration. Source
order may require a forward; it must never rank specializations.

## Alias and qualified type-identity discovery

Inside a generic type's body, the current instantiation, written in full,
anchors type-qualified discovery. Still undecided:

- whether a type-qualified operation is discovered through an alias;
- whether an alias and its target share type-qualified operations; and
- how qualified type identities anchor discovery without becoming runtime
  receivers.

### Qualified type identities, `unbound`, and `once`

Reference admission creates pressure for a qualified type identity to supply
type-owned discovery:

```zax
// Illustrative; qualified type-identity discovery is not established.
myView := MyIdentity & from myUnderlying
```

Future work must decide:

- whether `MyIdentity &` is one qualified type-identity lookup anchor;
- whether expected-result context instead selects a reference-returning
  `MyIdentity from ...` operation;
- how alias and generic identities participate;
- whether the declaration is type-qualified `unbound` or deliberately
  type/instance `once`;
- whether an explicit type parameter slot would duplicate the source type
  identity; and
- whether `once varying` has a corresponding type-qualified role.

The result must never silently change between by-value identity construction and
same-storage reference view.

## Qualifier-parameterized declaration families

[Zax qualifiers](../../language/qualifiers.md) and
[transfer stances](../../language/transfer-stances.md) require every ordinary
callable prototype to resolve one exact mutable/immutable, final/varying,
writable/readonly, and transfer shape. Omission supplies concrete defaults; it
does not create qualifier erasure or implicit generic specialization.

A worked-out generic type already keeps the argument's qualifiers. An explicit
mechanism should also let an author produce exact specializations over
qualifiers themselves without hand-writing every valid combination.

Illustrative syntax:

```zax
relay$(Mutability, PlaceStance) final : (
  result : MyValue Mutability readonly PlaceStance &
)(
  input : MyValue Mutability readonly PlaceStance &
) = {
  // ...
}
```

The spelling is not accepted. Future work must decide:

- how qualifier parameters are named and constrained, and what kind they have;
- whether qualifier arguments are worked out from value inputs;
- how a result repeats worked-out input metadata;
- how qualifier specializations fit the term-set ranking;
- how generated lifecycle families interact with programmer generic families;
- whether stance itself may be a generic parameter;
- how diagnostics show the resolved concrete prototype;
- and how source compatibility changes when another specialization becomes
  viable.

Every selected specialization remains one exact concrete prototype.

## Runtime surfaces select a finite set

Composition `expose`, independently of `own`, function-value work, and any
future dispatch work must select a finite exact set of instantiations rather
than reserve space for every theoretical specialization of an open generic
family. Generic functions are `final`, and a runtime function value names one
instantiation; future work that adds runtime surfaces over generics must keep
this property.

## Generic protected numeric conversion

Integer, fixed-point, and floating-point conversions are protected intrinsic
signatures supplied by the language, for example integer-to-float `as`. Generic
numeric code will need to express those protected conversions over generic
integer and floating families, such as converting a generic integer to a generic
float, without programmer declarations on the intrinsic types.

Conversion families among exact intrinsic specializations may be supplied by
the language or the compiler as generated partials. Add design pressure here
only when a concrete need for built-in conversion families appears. Such
generated partials must not depend on source order, and must preserve
protected all-intrinsic signatures.

## Nothing policy per concrete specialization

[Zax Nothing instances](../../language/nothing-instances.md) establishes that
each concrete specialization may require its own semantic Nothing policy even
when an implementation can share physical compiler-provided backing.

Future generic work must define:

- when an omitted, explicit default, trapping, or custom policy is resolved for
  a specialization;
- whether `+++ final once` in generic source produces one custom Nothing
  initializer per concrete specialization;
- reachability analysis and elimination when a specialization can never expose
  vacant-pointer access or receiverless `_` use;
- interaction with module-instance identity and generic arguments;
- diagnostics when substituted members have no readable prepared
  representation;
- reflection of generic-source policy versus one specialization's effective
  policy; and
- backend deduplication that preserves language-level specialization identity,
  custom state, and synchronization.

Equal layout or equal generated code must not merge custom Nothing storage or
semantic identity. Merely forming a concrete specialization does not require
materialized Nothing backing.

## Intrinsic endian and numeric families

Endianness is an intrinsic scalar specialization dimension. See
[Zax endianness](../../language/endianness.md). Intrinsic families normalize
their arguments before identity: an environment or "native" selector resolves
to `Little` or `Big`, and a fixed-point specialization with zero fractional bits
is the integer specialization.

Future work must decide:

- how `Little`, `Big`, active, target, and compiler-host selectors appear as
  `$` arguments or factory inputs;
- how absolute-endian, active, and public role aliases are generated;
- how fixed-point width, signedness, and bounded `F` are validated;
- how ordinary floating exponent/fraction dimensions select a closed encoding
  policy;
- how closed legacy formats avoid incoherent free parameter combinations; and
- how concrete specializations and aliases appear in reflection and
  diagnostics.

Every selected result is one exact closed specialization. Generic generation
does not create a second scalar semantic model.

## Integer specializations and factory selection

The concrete programmer-visible integer families and relationships are current
in [Zax integers](../../language/integers.md). The intrinsic family is written
with ordinary `$` arguments:

```zax
MyI57 :: alias type Integer$(
  LogicalBits: 57,
  Signedness: Sign.Signed,
  FractionalBits: 0,
  Endianness: Endian.Little
)
```

Each realized combination is a concrete, closed intrinsic type.

A factory returns one concrete specialization from dimensions such as minimum
and maximum logical width, signedness, concrete or environment-selected
endianness, fastest or smallest preference, environment, native-representation
requirement, and software fallback. Current generics makes such a factory
expressible: a type-returning function may compute `$` arguments while it runs.

```zax
// The factory interface remains future work.
MySelectedInteger :: alias type IntegerFactory.select(
  minimumBitCount: 13,
  maximumBitCount: 24,
  signedness: Sign.Signed,
  environment: Target
)
```

Future work must define the factory interface and its selection rules. A
selected public role identity retains its role when two profiles happen to
choose the same exact specialization.

## Relational signedness pairs

The exact intrinsic family derives its counterpart by changing only signedness:

```text
Integer$(W, Sign.Signed, F, Endian) <-> Integer$(W, Sign.Unsigned, F, Endian)
```

Named role identities require an explicit relational pair:

```text
Small <-> USmall
FastI16 <-> FastU16
```

`UPointer` has no signed counterpart role; pointer differences use
`PointerDelta`, its `delta type`.

The integer factory or another validated generic mechanism should define both
identities together:

1. apply one shared selector except for signedness;
2. realize equal-width intrinsic counterparts;
3. establish both public identity branches;
4. bind them as unique mutual counterparts; and
5. generate their protected counterpart operations.

The compiler must not infer relationships from spelling. Future programmer code
should be able to define owner-authorized pairs through the same mechanism. The
compiler validates equal logical width, opposite signedness, mutual and unique
pairing, and ownership; no declaration may replace the intrinsic pairing or pair
a type owned elsewhere.

One-sided roles have no paired public identity. Their counterpart relation may
return the unnamed exact intrinsic counterpart. Restricted or opaque semantic
identities gain no counterpart merely from integer storage.

## Integer associated types

Generic numeric code needs statically known type relationships:

```zax
MyBitCount :: alias type MyNumericType bit count type
MyByteCount :: alias type MyNumericType byte count type
MyDelta :: alias type MyNumericType delta type
MyDistance :: alias type MyNumericType distance type
MyStorage :: alias type MyNumericType storage type
MyCounterpart :: alias type MyNumericType signedness counterpart type
```

These are type-returning, type-qualified operations, so they are compile-only
and usable in generic prototypes, such as `result : UseType bit count type`.
Future work must determine whether they remain specialized unbound operator
phrases, become one general associated-type facility, or a combination. They
must remain usable in result declarations and constraints without forcing
generic code to branch manually between ordinary and large count tiers.

## Fixed-point specializations and full products

Current format and arithmetic semantics are defined by
[Zax fixed-point scalars](../../language/fixed-point-scalars.md). Future work
retains:

- validation of `0 <= F <= W - 1` for signed and `0 <= F <= W` for unsigned;
- generation of `I<W>F<F>`/`U<W>F<F>` and Q aliases;
- constraints requiring one exact fixed-point identity;
- the associated full-precision-product result type; and
- exposure of coefficient, quantum, range, and endian facts to generic code.

For two like `W`, `F` operands, `full precision product` needs an associated
type sufficient for the complete coefficient product and `2F` fractional bits.

## Floating specializations and language limits

Current ordinary, profile-selected, and legacy format semantics are defined by
[Zax binary floating-point scalars](../../language/floating-point-scalars.md).
Future work retains:

- ordinary IEEE-style exponent/fraction parameter syntax;
- selection among closed encoding policies;
- prevention of freely combined explicit-integer-bit modes until another
  format requires one;
- the `Float`, `FastF$(N)`, `LeastF$(N)`, and maximum-role factories;
- support-class and operation constraints;
- exact environment and endianness selection; and
- immutable type-owned `Scalars.Floating.LanguageLimits` values.

The numeric value of `LanguageLimits.maximumWidth`, and any separate exponent,
fraction, storage, or alignment limits, remain future language-version
decisions. A limit must be available while concrete types are formed.

### Bounded size probing

Literal input preserves a concrete bounded selection need:

```zax
mySmall := h'FF'
myLarge := h'FFFFFFFFFFFFFFFFFFFFFFFFFFFFF'
```

A literal's suggested result may now depend on its payload, and a
type-returning function may compute a width while it runs and build the
instantiation from it. Future numeric work must use those mechanisms while
preserving:

- one selected concrete callable result, never an anonymous runtime value;
- a fixed declared suggestion such as `UInteger` when no other hole applies;
- separate suggestion and sign intent;
- no value-magnitude overload ranking;
- ordinary range checking only after one specialization is selected;
- no retry of another type after selected range failure; and
- bounded static advancement through larger candidates up to a language or
  profile maximum, rather than instantiating one extreme maximum-width value,
  exception-driven fallback, or source-order overload search.

## Protocol and allocation-policy constraints

Constraints are written with `requires`, and a `wellformed` payload declaring a
lambda can check operations without constructing values. What remains future is
a standard, composable set of protocol constraints.

### Cursor-protocol constraints

[Zax iteration](../../language/iteration.md#cursor-driven-from-traversal)
defines the concrete cursor protocol:

```text
iterate source
value at cursor
advance cursor
erase and advance cursor
erase cursor
```

Future constraints must let a declaration constrain an unknown source or
cursor by the protocol pieces it actually needs. It must be possible to express:

- whether `iterate` returns one cursor or an optional cursor;
- the stable concrete cursor type;
- whether `value at` exists and its value, reference, or proxy result shape;
- the relationship between a yielded reference and the source or cursor life
  path;
- whether progression mutates cursor state and returns `Boolean` or returns a
  replacement cursor, optionally absent;
- whether erase-and-advance or erase-and-exit is available;
- required source, cursor, and yielded-value qualifications;
- whether the cursor may be copied, moved, stored, or returned; and
- cursor/source mutation and invalidation guarantees.

A `#` traversal requires progression but no `value at` result. Constraints must
therefore compose protocol capabilities rather than require one monolithic
iterator interface.

Current [arrays and slices](../../language/arrays-and-slices.md) add an optional
exact `source count` operation and contextual array-entry `from`. Future
constraints must express:

- whether exact non-consuming `count` exists;
- which `IndexSize`, `Near.IndexSize`, or `Far.IndexSize` identity carries the
  count;
- agreement between that count and the corresponding traversal;
- the element type and qualifications produced by `value at`;
- whether a source is an owning fixed/resizable array, borrowed slice, or
  another iterable;
- exact storage provider type/instance or storage-relaxed acceptance;
- provider capabilities such as contiguity, jagged shape, stable-prefix
  mutation, or handle transfer;
- logical array identity versus resolved storage profile;
- storage-erased versus exact reference behavior;
- unique provider-handle and associated stability token types;
- storage-dependent size and structural-layout constraints;
- provider-factory/default-context selection;
- consuming range/chunk transfer;
- place-stability guarantees when required; and
- whether structural mutation invalidates the cursor or yielded references.

The intrinsic `T[N]`, `T[min..max]`, and `T[]` families are not themselves
written as programmer generic types. Generic constraints must describe the
existing operations; they must not introduce a second cursor protocol or
reinterpret concrete `each from` behavior.

### Allocation-policy constraints

[Zax pointers, allocation, and arenas](../../language/pointers-and-arenas.md)
allows the same pointer type to carry different allocation disposition,
collection, arena, and control-block history. `T * strong` alone therefore
cannot prove prompt destruction, prompt recovery, collectability, a particular
arena capability, or cross-thread release.

Future work must define how a declaration constrains:

- object and control-block arena capabilities;
- prompt, deferred-recovery, or attached-lifespan disposition;
- collection eligibility;
- declaration-attached versus open-ended raw allocation;
- and whether a supplied allocation policy may be propagated to a nested
  declaration.

Those constraints must remain visible in callable compatibility and
specialization. They must not infer a guarantee from hidden allocation history.

## Whole-type abstract-contract pressure

Member-level composition roles do not provide a whole-type contract. Future
work may need a no-storage contract that describes the required shape of a type
and can be used by constraints, structural matching, or composition.

No source form is established, so this input does not invent a representative
declaration. Future work must decide:

- how the required value, callable, and operator shapes are written;
- whether matching is nominal, structural, explicitly fulfilled, or a
  combination;
- which names, qualifications, transfer stances, labels, and result-origin
  guarantees participate;
- whether a generic body could then be checked against the contract once,
  instead of per instantiation; and
- how contract evolution affects existing specializations and fulfillments.

A contract could appear as another `requires` term without changing the
term-set ranking. The facility must not turn member-level `abstract` metadata
into inheritance, runtime interface storage, a vtable, or implicit dynamic
dispatch. A preferred route also cannot manufacture a whole-type contract or
cause one to be inferred.

[Zax structural shapes and compatibility](../../language/structural-shapes-and-compatibility.md)
supplies the accepted distinction among identity, direct and flattened shape,
layout, operation permission, and whole-type conformance. Future contracts must
use those meanings without treating a compatibility posture as a generic
contract or making shape equality into implicit admission.

## Legacy string terminator policies

Current string design defines concrete specializations:

```text
Legacy.TerminatedCharString$(TerminatorPolicy)
Legacy.TerminatedWideString$(TerminatorPolicy)
```

A terminator policy is a type argument whose behavior is found through
operations owned by the policy type. Future work must let a platform/API-owned
policy statically determine:

- terminator code unit or sequence;
- in-band rejection, escaping, or translation;
- required contiguous storage and physical suffix;
- logical length treatment;
- admission/conversion behavior;
- safe mutation/reallocation restoration; and
- any additional platform-specific contract.

Each realized policy application is one concrete string identity. A runtime
policy value must not make one string change termination semantics dynamically.

## Generic enums and generic partials

A `$` list is accepted on types, unions, variants, and identity types. Two
categories are deferred:

- **Generic enums** need rules for generating members from parameters.
- **Generic partials**, meaning a partial over a generic family rather than one
  concrete type, need visibility rules. They must fit current lookup: a
  partial granted where a use is written may contribute to what that use sees,
  but every use of the same instantiation must make the same choices.

## An exact-type specialization form

A specialization for one exact type is written today with an ordinary
constraint:

```zax
MyBits$(UseType : type) requires UseType == Boolean :: type { packed : Byte[2] }
```

A dedicated exact form could rank above any constraint on the same parameter.
Adding one later is compatible, because it would only resolve uses that are
ambiguous today. Candidate spelling considered: `UseType : type == Boolean`,
with the risk that a one-character slip to `= Boolean` silently becomes a
default.

## Constraints this input places on current work

Optional wrapper composition adds a resolved-type intent requirement:

```zax
MyInner :: alias type MyValue?
MyOuter :: alias type MyInner?

inner : MyInner
outer : MyOuter = inner             // error: hidden optional depth is added
outerExplicit : MyOuter = [{ inner }]
```

A type parameter substitution may create the same relationship even when the
generic source does not spell both `?` layers. Generics must preserve:

- no automatic flattening of optional layers;
- one independent qualification and presence state per layer;
- construction-packet acknowledgement when a construction adds a layer around
  an already-optional source;
- same-type optional construction remaining ordinary; and
- declared operations returning nested optionals retaining their exact result
  type and state contract.

Diagnostics must show enough resolved structure to explain why an apparently
simple initialization needs an explicit packet.

Also:

- integer-factory and associated-type examples remain illustrative until their
  interfaces are designed;
- fixed/floating factory, full-product, and `LanguageLimits` examples remain
  illustrative until the same; and
- public signedness pairs are declared relationally rather than inferred from
  names.

## Activation and retirement

Activate this input when generic aliases or forwards, qualifier-parameterized
families, alias or qualified type-identity discovery, numeric factories,
relational pairs, associated-type facilities, intrinsic fixed or floating
specialization details, bounded size probing, protocol or allocation-policy
constraints, whole-type contracts, string terminator policies, generic enums or
partials, or an exact-type specialization form is reviewed. Move accepted
behavior into [Zax generics](../../language/generics.md) and the declaration,
numeric, iteration, allocation, string, and reflection owners, then retire this
file after every preserved question is dispositioned.
