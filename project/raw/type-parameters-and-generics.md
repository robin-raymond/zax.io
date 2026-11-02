# Raw input: type parameters and generics

| Field | Value |
| --- | --- |
| Status | Raw future-work input / non-authoritative |
| Audience | Future work defining generics, type parameters, constraints, or computed type results |
| Applies To | Type-input pressure exposed by operator-phrase, declaration, invocation, scalar-family, and composition review |
| Owns | Preserved type-parameter, generic, whole-type contract, intrinsic endian/fixed/floating specialization, numeric factory, bounded probing, language-limit, relational-pair, associated-type, representative-source, activation, and retirement questions |
| Does Not Own | Accepted generic semantics or current declaration/scalar/type behavior |
| Source / Provenance | Legacy `meta-types.md` and `meta-functions.md` evidence together with operator-phrase review of type parameters and type-qualified discovery, work item `012` optional-depth substitution pressure, work item `015` allocation-policy pressure, work item `020` composition constraints, and work item `034` partial deferrals |

## Why this input exists

Current documentation accepts a deliberately narrow type-input model:

- a prototype may declare a `ParameterType : type` slot;
- a caller completes it with one concrete type identity;
- that type argument has no runtime storage, lifetime, or evaluation; and
- a concrete type identity may anchor type-owned `unbound` or `once` operator
  discovery without creating a separate receiver category.

See
[declarations and bindings](../../language/declarations-and-bindings.md#operator-phrase-declarations-and-type-parameters)
and
[function invocation](../../language/function-invocation.md#type-parameter-slots).

Nothing beyond that is accepted. Generic declarations, constraints, computed type
results, and generated type families remain undecided, and this file keeps those
questions live.

## Accepted narrow baseline

```zax
Source :: type {
  operator binary 'as' final : (
    result : DestinationType
  )(
    DestinationType : type
  ) readonly = {
  }
}

converted := source as DestinationType
```

A value receiver may also accept a type argument without that argument
contributing receiver discovery:

```zax
schema : MyReceiverType
result := schema for SomeType
```

Only *mechanically determined* reserved type results and value results dependent
on concrete type arguments are assumed today.

## Legacy generic evidence

Legacy material uses a `$` sigil for type variables and a `Name$(...)` form for
parameterized types:

```zax
MyType$(TypeA, TypeB) :: type {
    value1 : $TypeA
    value2 : $TypeB
}

addThenMultiply final : (
    result : $Type
)(
    value1 : $Type,
    value2 : $Type
) = {
}
```

That spelling has not been reviewed against the current `ParameterType : type`
slot. Future work must decide whether the two are the same mechanism written
differently, two distinct mechanisms, or one superseding the other.

## Compile-time selection pressure from legacy directives

The root legacy compiler-directive page keeps three sections as deferred input
for generic work: `requires` (a compile-time Boolean block that enables or
disables a candidate), `concept` (a function used as a type's selection
criteria), and `compile` on inputs (arguments that must be compile-time values).
The legacy `true`/`false` declarative after a prototype, which statically
enables or disables a candidate, is part of the same pressure. Current design
constrains them:

- **Checking code.** [`wellformed{...}`](../../language/compile-time-code.md#checking-whether-code-is-well-formed-wellformed)
  is current. It fully checks code a payload creates and trusts existing code
  by its prototype; generic work must define how deep a generic instantiation
  created by a payload is checked, and any SFINAE-like selection must follow
  that rule.
- **Compile-time values.** A compile-time block may use only types and
  [compile-time values](../../language/compile-time-code.md#compile-time-values),
  never runtime values, and has no runtime side effects.
- **Compile-time parameters.** The useful form of `compile` on inputs is an
  argument whose value the function body uses while compiling, which is
  per-value specialization. Its motivation includes arguments that must not
  come from runtime data, such as format strings and SQL templates. The
  maintainer's earlier analysis applies here: `readonly` may suffice to keep
  such a value stable inside the body, while `immutable` would need `final` so a
  replacement constructor cannot change it.
- **Generic bodies and compile points.** A compile point inside a generic body
  runs once per instantiation.
- **Type arguments.** A type argument alone never forces compile-time execution;
  calls follow ordinary
  [execution policies](../../language/compile-time-code.md#letting-a-function-decide-execution-policies).

## Generic type arguments and constraints

Future work must decide:

- how a generic declaration names its type parameters;
- whether a type parameter may be constrained, and by what vocabulary;
- how a constraint interacts with fixed-arity viability and partial-order
  preference;
- whether a type argument may be inferred from value arguments;
- default type arguments; and
- diagnostics when several instantiations are equally viable.

### Whole-type abstract-contract pressure

Member-level composition roles do not yet provide a whole-type generic
constraint. Future work may need a no-storage contract that describes the
required shape of a type and can be used by generic constraints, structural
matching, or composition.

No source form for that whole-type contract is established, so this input does
not invent a representative declaration. Future work must decide:

- how the required value, callable, and operator shapes are written;
- whether matching is nominal, structural, explicitly fulfilled, or a
  combination;
- which names, qualifications, transfer stances, labels, and result-origin
  guarantees participate;
- how a generic body is checked against the contract; and
- how contract evolution affects existing specializations and fulfillments.

The facility must not turn member-level `abstract` metadata into inheritance,
runtime interface storage, a vtable, or implicit dynamic dispatch. A preferred
route also cannot manufacture a whole-type contract or cause one to be inferred.

[Zax structural shapes and compatibility](../../language/structural-shapes-and-compatibility.md)
now supplies the accepted distinction among identity, direct and flattened
shape, layout, operation permission, and whole-type conformance. Future generic
constraints must use those meanings without treating a compatibility posture as
a generic contract, making an anchor invent a deduction target, or turning
shape equality into implicit admission.

## Dependent value result types

`as` already demonstrates a result type determined by a type argument:

```zax
converted := source as DestinationType
```

Future work must decide how far that generalizes: whether an ordinary user
declaration may state a result type in terms of its own type parameters, how such
a result participates in narrow expected-result selection, and where inference
stops.

### Restricting which types a slot accepts

A programmer-declared `as` is written for one intended destination, but nothing
yet restricts the type argument:

```zax
Celsius :: type {
  degrees : Binary64

  operator binary 'as' final : (
    result : DestinationType
  )(
    DestinationType : type
  ) readonly = {
    result .= [{
      .degrees = degrees * 9.0 / 5.0 + 32.0
    }]
  }
}

display := boiling as Fahrenheit   // intended
other := boiling as Kelvin         // also selected; fails only if Kelvin lacks `degrees`
```

[Zax conversions and casts](../../language/casting.md#writing-your-own-as)
currently teaches "one declaration, one intended destination" without inventing
constraint syntax. Constraints on type parameter slots, or exact type-argument
overloads, must let a type state which destinations each declaration accepts.

This restriction also gates a [partial](../../language/partials.md) that adds
`as` to a language scalar, so that `myCount as MyTally` works where the partial
is granted. Without destination restriction, a partial `as` on `I32` would also
compete with protected conversions such as `myCount as I64`, which partials may
not claim. The legacy partial input recorded this conversion as a motivating use.

### Generic protected numeric conversion

Integer, fixed-point, and floating-point conversions are protected intrinsic
signatures supplied by the language, for example integer-to-float `as`. Generic
numeric code will need to express those protected conversions over generic
integer and floating families, such as converting a generic integer to a generic
float, without programmer declarations on the intrinsic types.

Current [partials](../../language/partials.md) let programmers add functions to
language-provided types, but conversion families among exact intrinsic
specializations may instead be supplied by the language or the compiler as
generated partials. Add design pressure here only when a concrete need for
built-in conversion families appears. Such generated partials must not depend on source order, and must
preserve protected all-intrinsic signatures.

## Generic code and partials

How generic code sees operations that [partials](../../language/partials.md)
add is undecided. A partial is visible only where a
scope grants it, and it targets a concrete type. The open question is what a
generic body sees once it becomes concrete:

```zax
// Library module. Generic syntax is illustrative.
sum final : (result : R)(left : T, right : U) = {
  return left + right
}

// User module.
Module.MyIntegerOps :: expose partial    // adds Integer + MyType
total := sum(myInteger, myValue)
```

Candidate answers:

- **The body sees grants visible where the generic is defined.** `left + right`
  finds nothing for `Integer + MyType`, so operators added by partials never
  work through library generics.
- **The body sees grants visible where it becomes concrete.** The caller's
  grants flow into library code and can redirect other calls in the body that
  the library author never saw.
- **A stated requirement is satisfied at the call site.** If the generic
  requires "`T + U` exists", the caller's visible candidates satisfy it, and the
  body uses exactly that operation without re-resolving anything else.

The answer depends on where and how a generic becomes concrete. Whatever
generic work decides, a caller's grant must never silently redirect a selection
that the library body already resolves without that grant.

Generic partials, meaning a partial over a generic family rather than one
concrete type, are also generic work.

## Computed type results

A general computed or runtime-dependent type result is **not** assumed:

```zax
// Illustrative pressure; no general computed type result is accepted.
Selected :: selectType(condition)
```

The mechanically determined protected type results, such as `type of`, do not
authorize arbitrary user-defined runtime-dependent type-result
functions. Future work must decide whether a compile-time function may return a
type identity, what identity such a result has, and how it interacts with
overload selection and reflection.

### Concrete-at-alias-boundary requirement

Current alias design preserves this future direction:

```zax
// Illustrative until type-returning execution syntax is designed.
MySelectedType :: alias type giveMeAType()
```

The call must finish applicable static processing and return one concrete known
type before the alias resolves. The alias never denotes a random future type or
a type that will be chosen from later runtime use.

A fully selected generic specialization follows the same rule:

```zax
// Illustrative generic syntax.
MyConcreteType :: alias type MyType$(TypeA, TypeB)
```

It aliases one concrete specialization and creates no identity. A partially
applied alias is different: it identifies a known generic family with remaining
parameters rather than one concrete type. Generic work must define a dedicated
generic alias declaration shape and cannot silently treat that family as an
ordinary concrete `type`.

Concrete aliases may carry an overlaid profile of qualification, indirection,
transfer stance, and compatibility posture. Future generic aliases must decide
how remaining parameters interact with that profile while preserving:

- explicit more-local properties replace inherited properties on the same axis;
- omitted properties inherit before common defaults apply;
- canonical type identity remains separate from the resolved profile; and
- no specialization or alias manufactures capability on an actual source.

### Generic forwarding pressure

Current non-generic forwards anchor a name and category before one exact
completion. Generic work needs the corresponding model for:

- a generic type family;
- a partially applied generic alias;
- a generic callable family;
- generated qualifier or stance specializations; and
- one concrete demanded specialization.

It must decide which facts a forward promises, when constraints are checked,
whether one generic family completion satisfies every specialization, and how
an exact concrete alias completion differs from a still-generic declaration.
Source order may require a forward; it must never rank specializations.

## Generic and alias type-qualified discovery

For a non-generic declaration, the enclosing type name identifies the owner
inside an unbound type-qualified operator body:

```zax
MyType :: type {
  operator pre unary 'custom type info for' final : (
    result : MyCustomTypeInfo
  )() unbound = {
  }
}
```

Undecided:

- what the enclosing name means inside a generic definition;
- whether a type-qualified operation is discovered through an alias;
- whether an alias and its target share type-qualified operations;
- how qualified type identities anchor discovery without becoming runtime
  receivers; and
- whether a generic instantiation may add or remove type-qualified operations.

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

A future explicit generic mechanism should let an author produce exact
specializations without hand-writing every valid combination.

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

- how qualifier parameters are named and constrained;
- whether qualifier arguments are inferred from value inputs;
- how a result repeats inferred input metadata;
- when the generic body is checked against all constraints;
- whether invalid specializations remain latent until demanded;
- how concrete declarations compare with inferred specializations;
- how generated lifecycle families interact with programmer generic families;
- whether stance itself may be a generic parameter;
- how diagnostics show the inferred concrete prototype;
- and how source compatibility changes when another specialization becomes
  viable.

Every selected specialization remains one exact concrete prototype. Only
demanded specializations need executable code, and implementation deduplication
must not erase language-level identity.

### Generic processing versus compatible prototypes

[Zax function invocation](../../language/function-invocation.md#minted-implementation-model)
defines a non-generic callable body as minted once under its implementation
prototype. A compatible visible prototype reuses that implementation without
reprocessing its body.

Future generics differ. A generic body may need to be reprocessed for each
demanded concrete type, qualification, stance, value, or other generic argument:

```text
generic body + concrete argument set
-> substitute the concrete contract
-> check and select body operations
-> mint one concrete implementation
```

Each concrete result has its own implementation-prototype facts. Reusing backend
code for equivalent layouts must not erase those language-level specializations.
Future work must define which facts are generic inputs, when reprocessing occurs,
and how diagnostics distinguish the generic source from one minted concrete
implementation.

Runtime surfaces are different. Composition `expose`, independently of `own`,
future function-value work, and any future dispatch work must select a finite
exact set rather than reserve space for every theoretical specialization of an
open generic family.

Future generic deduction must decide whether and when an independent
`preferred` route can participate after deduction. Composition currently
permits projection only after a concrete expected type or complete shape exists.
It must not invent a deduction target, widen member, callable, or operator
discovery, or outrank an exact direct match. Generic work must preserve that
boundary unless a later design explicitly replaces it.

### Nothing policy per concrete specialization

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
semantic identity. A fully selected specialization is one concrete type; no
universal Nothing type or generic source value is introduced.

Merely forming a concrete specialization does not require materialized Nothing
backing. Preparation may occur during ordinary global startup, and a compiler
may omit or combine backing whenever it proves no observable access requires a
separate instance.

## Intrinsic endian and numeric families

Endianness is now an intrinsic scalar specialization dimension rather than a
generated enum family. See [Zax endianness](../../language/endianness.md).

Future generic work must decide:

- how `Little`, `Big`, active, target, and compiler-host selectors appear as
  type arguments or factory inputs;
- when an environment selector resolves to one concrete endian identity;
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
in [Zax integers](../../language/integers.md). This section retains only the
future generic/factory mechanism needed to produce them.

Future integer work assumes a built-in generic family conceptually like:

```zax
// Illustrative only.
MyI57 :: alias type Integer$(
  LogicalBits = 57,
  Signedness = Sign.Signed,
  FractionalBits = 0,
  Endianness = Endian.Little
)
```

Each realized combination is a concrete, closed intrinsic type. It is not still
generic after realization.

A separate integer factory returns one concrete specialization from dimensions
such as:

- minimum and maximum logical width;
- signedness;
- concrete or environment-selected endianness;
- fastest or smallest preference;
- active, target, or compiler-host environment;
- native-representation requirement; and
- software fallback.

Illustrative use:

```zax
// Exact factory and type-result syntax remain unsettled.
MySelectedInteger :: alias type IntegerFactory.select(
  minimumBitCount: 13,
  maximumBitCount: 24,
  signedness: Sign.Signed,
  environment: Target
)
```

Future work must define a type-returning factory function rather than treating
selection as an array or receiverless phrase. A selected public role identity
retains its role when two profiles happen to choose the same exact
specialization.

## Relational signedness pairs

The exact intrinsic family derives its counterpart by changing only signedness:

```text
Integer$(W, Signed, F, Endian) <-> Integer$(W, Unsigned, F, Endian)
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

These are type-qualified operations returning concrete type identities with no
runtime receiver construction. Future work must determine whether they are
specialized unbound operator phrases, one general associated-type facility,
type metadata queries, or a combination.

The relationships must remain usable in result declarations and constraints
without forcing generic code to branch manually between ordinary and large
count tiers.

## Fixed-point specializations and full products

Current format and arithmetic semantics are defined by
[Zax fixed-point scalars](../../language/fixed-point-scalars.md). Generic work
retains:

- declaration syntax for `Integer$(W, Signedness, F, Endianness)`;
- validation of `0 <= F <= W - 1` for signed and `0 <= F <= W` for unsigned;
- normalization of `F = 0` to the existing integer specialization;
- generation of `I<W>F<F>`/`U<W>F<F>` and Q aliases;
- constraints requiring one exact fixed-point identity;
- associated full-precision-product result selection; and
- exposure of coefficient, quantum, range, and endian facts to generic code.

For two like `W`, `F` operands, `full precision product` needs an associated
type sufficient for the complete coefficient product and `2F` fractional bits.
Exact result-type query syntax remains unsettled.

## Floating specializations and language limits

Current ordinary, profile-selected, and legacy format semantics are defined by
[Zax binary floating-point scalars](../../language/floating-point-scalars.md).
Generic work retains:

- ordinary IEEE-style exponent/fraction parameter syntax;
- selection among closed encoding policies;
- prevention of freely combined explicit-integer-bit modes until another
  format requires one;
- `Float`, `FastF<N>`, `LeastF<N>`, and maximum-role factories;
- support-class and operation constraints;
- exact environment and endianness selection; and
- immutable type-owned `Scalars.Floating.LanguageLimits` values.

The numeric value of `LanguageLimits.maximumWidth`, and any separate exponent,
fraction, storage, or alignment limits, remain future language-version
decisions. A limit must be available while concrete types are formed; a
type-qualified `unbound` or `once` declaration is not inherently compile-time
merely because it belongs to a type.

### Bounded size probing

Current [literal source and operator design](../../language/literal-source-and-operators.md#every-invocation-has-one-concrete-result)
establishes a generic `uncommitted` scalar result slot. Compile-time/generic
processing uses payload, candidate-hole, sign-intent, and suggestion facts to
select one concrete result prototype before processing and invoking the literal
body.

The exact generic mechanism remains future work. It must preserve:

- one selected concrete callable result, never an anonymous runtime value;
- a fixed declared suggestion such as `UInteger` when no other hole applies;
- separate suggestion and sign intent;
- no value-magnitude overload ranking;
- no result-overload family for integer versus `p` real payloads;
- ordinary range checking only after one specialization is selected; and
- no retry of another type after selected range failure.

Literal input also preserves a concrete bounded selection need:

```zax
mySmall := h'FF'
myLarge := h'FFFFFFFFFFFFFFFFFFFFFFFFFFFFF'
```

A numeric literal implementation receives a payload `String`, but parsed
magnitude may determine whether its suggested result is an ordinary or much
wider exact integer. Future generic work should permit a literal declaration or
associated type factory to compute that concrete suggestion before the literal
body and surrounding expression are processed.

More generally, compile-time code should be able to begin with an ordinary
numeric size, test bounded conversion/support requirements, and deliberately
advance through larger candidates until a language/profile maximum is reached.
It should not be forced to instantiate one extreme maximum-width integer or
floating value merely to discover a workable type.

Future generic and compile-time work must define this as bounded static
specialization rather than exception-driven runtime fallback, source-order
overload search, or reopening an invoked literal result.

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
generic source does not spell both `?` layers. Future generic work must preserve:

- no automatic flattening of optional layers;
- one independent qualification and presence state per layer;
- construction-packet acknowledgement when a construction adds a layer around
  an already-optional source;
- same-type optional construction remaining ordinary;
- declared operations returning nested optionals retaining their exact result
  type and state contract.

Generic diagnostics must show enough resolved structure to explain why an
apparently simple initialization needs an explicit packet.

### Cursor-protocol constraints

[Zax iteration](../../language/iteration.md#cursor-driven-from-traversal)
defines the concrete cursor protocol without requiring a generic concept:

```text
iterate source
value at cursor
advance cursor
erase and advance cursor
erase cursor
```

Future generic work must let a declaration constrain an unknown source or
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
exact `source count` operation and contextual array-entry `from`. Future generic
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
- storage-dependent `size of` and structural-layout constraints;
- provider-factory/default-context selection;
- consuming range/chunk transfer;
- place-stability guarantees when required; and
- whether structural mutation invalidates the cursor or yielded references.

The intrinsic `T[N]`, `T[min..max]`, and `T[]` families do not establish the
syntax for declaring general user-defined generic collections. Generic work
must express the current concrete behavior rather than reinterpreting it.

Generic syntax must describe these existing operations. It must not introduce a
second cursor protocol or reinterpret concrete `each from` behavior.

### Allocation-policy constraints

[Zax pointers, allocation, and arenas](../../language/pointers-and-arenas.md)
allows the same pointer type to carry different allocation disposition,
collection, arena, and control-block history. `T * strong` alone therefore
cannot prove prompt destruction, prompt recovery, collectability, a particular
arena capability, or cross-thread release.

Future generic work must define how a declaration constrains:

- object and control-block arena capabilities;
- prompt, deferred-recovery, or attached-lifespan disposition;
- collection eligibility;
- declaration-attached versus open-ended raw allocation;
- and whether a supplied allocation policy may be propagated to a nested
  declaration.

Those constraints must remain visible in callable compatibility and
specialization. They must not infer a guarantee from hidden allocation history.

- a concrete type argument is never a runtime value;
- a type-qualified operation is not inherently compile-time;
- non-generic enclosing type identity is sufficient for current behavior; and
- current documentation must not imply that a general computed type result
  exists;
- integer-factory and associated-type examples remain illustrative until
  computed type results are designed; and
- fixed/floating factory, full-product, and `LanguageLimits` examples remain
  illustrative until the same mechanism is designed; and
- public signedness pairs are declared relationally rather than inferred from
  names.

### Generic type, union, and variant completion

Current [type definitions](../../language/type-definitions.md),
[unions](../../language/unions.md), and
[variants](../../language/variants.md) require one complete concrete definition
before representation- or lifecycle-dependent checks finalize.

Future generic work must ensure that each concrete specialization closes before
the compiler decides:

- direct and recursive stored layout;
- generated construction, replacement, transfer, and destruction families;
- union-admissible and all-bit-pattern-valid status;
- maximum union lens extent and alignment;
- variant alternative extent, alignment, transfer availability, and
  discriminant needs; and
- variant switch alternative coverage.

An open generic declaration cannot defer those questions to runtime. Exact
constraints may express them statically, but current concepts do not reserve a
particular constraint syntax.

### Legacy string termination policies

Current string design defines conceptual concrete specializations:

```text
Legacy.TerminatedCharString<TerminatorPolicy>
Legacy.TerminatedWideString<TerminatorPolicy>
```

Future generic work must let a platform/API-owned policy statically determine:

- terminator code unit or sequence;
- in-band rejection, escaping, or translation;
- required contiguous storage and physical suffix;
- logical length treatment;
- admission/conversion behavior;
- safe mutation/reallocation restoration; and
- any additional platform-specific contract.

Each realized policy application is one concrete string identity. A runtime
policy value must not make one string change termination semantics dynamically.
Exact generic syntax, policy constraints, specialization identity, and
reflection remain future work.

## Activation and retirement

Activate this input when generics, type parameters, constraints, whole-type
abstract contracts, computed type results, qualified type-identity discovery, intrinsic
integer/fixed/floating families, endian selection, numeric factories,
language-limit values, bounded size probing, relational type pairs, associated
types, cursor-protocol constraints, or generated type families are reviewed.
Move accepted behavior into generic, declaration, invocation, type, numeric,
iteration, and reflection owners, then retire this file after every preserved
question is dispositioned.
