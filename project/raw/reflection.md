# Raw input: reflection

| Field | Value |
| --- | --- |
| Status | Raw future-work input / non-authoritative |
| Audience | Future work defining reflection, type metadata, declaration metadata, or source reflection |
| Applies To | Reflection pressure exposed by operator-phrase, type-identity, scalar-family, composition-surface, and source-presentation review |
| Owns | Preserved reflection, composition-category, scalar metadata, support/operation availability, validity/canonicalization, representative-source, activation-pressure, and retirement questions |
| Does Not Own | Accepted reflection semantics or the current domain facts reflected |
| Source / Provenance | Legacy `meta-types.md`, `meta-functions.md`, and `enums.md` metadata evidence together with operator-phrase review of `type of`, qualifier predicates, phrase fences, work item `012` optional-layer metadata pressure, work item `015` allocation metadata pressure, work item `020` composition constraints, and work item `036` generic type-operation pressure |

## Why this input exists

Current documentation protects a small, deliberately bounded set of reflection
query forms and defers everything else. See the
[operator catalog](../../language/operator-catalog.md#protected-forms).

Reserved now:

- exact `type of`;
- a type-information operation whose exact words remain open;
- the four direct predicates `is constant`, `is final`, `is immutable`, and
  `is readonly`; and
- future declaration reflection whose exact form remains open.

Not reserved: any general `expression information of` or `access information of`
form. This file keeps the deferred questions live.

## `type of`

```zax
Selected :: alias type type of expression
```

`type of` returns the selected expression's concrete static type identity. It
does **not**:

- execute the expression merely to discover its type;
- choose among result-only overloads;
- choose between operator-phrase trees;
- create a type for zero or multiple results; or
- attach expression-specific facts to canonical type identity.

Every successfully selected single expression result has a concrete static type,
even when that type is anonymous, intermediate, structural, or known only to the
compiler.

Future work must decide how `type of` renders an anonymous or intermediate type,
whether the result is usable everywhere a named type is, and how it interacts
with qualification.

## Type-information wording and metadata shape

The type-information operation is reserved in concept only. Illustrative wording
such as:

```zax
// Illustrative wording only; no exact source form is reserved.
info := type information of MyType
```

is not a source reservation. Future reflection work selects the exact words.

The metadata itself should be immutable, readonly, and final. One unified
metadata structure may carry optional type-, expression-, declaration-, or
access-specific sections rather than requiring an unrelated result type for every
subject.

Future work must decide:

- the exact words;
- the metadata structure and how optional sections are discovered;
- whether metadata is a value, a type identity, or both;
- lifetime and storage of metadata; and
- what is available at runtime versus only at compile time.

## Alias versus canonical identity

Canonical type identity must remain independent of which expression produced it.
Future work must decide:

- whether an alias is visible through reflection or erased to its target;
- whether qualified type uses have distinct reflected identities;
- how a generic instantiation reports its parameters; and
- how a qualifier-generic declaration family differs from one exact demanded
  specialization; and
- how recursive and self-referential types are reported without infinite
  expansion.

Current alias design adds non-type and property-profile distinctions reflection
must retain:

- an exact variable alias versus a runtime reference binding;
- one alias of a varying slot versus a captured function value;
- a polymorphic variable-family alias versus one selected visible prototype;
- an exact namespace alias without reopening authority;
- an exact reshape or literal-family alias;
- canonical type identity versus overlaid qualification, indirection, transfer
  stance, and compatibility posture; and
- the source alias path used by diagnostics.

An explicit property overlay never creates another canonical type identity.

### Generative module and forward metadata

Every import creates one generative module instance even when source and
injection inputs are equal. Reflection must distinguish:

- immutable source identity;
- import-site/module-instance identity;
- injected direct declarations;
- exact injected aliases to declarations owned elsewhere;
- individualized versus explicitly shared module state;
- local import binding and exported import aliases;
- namespace identity and owning module instance;
- owner-authorized reopening contributions;
- a forward name/category anchor;
- its direct or exact-alias completion; and
- unresolved pending suffixes.

Two generative declarations remain distinct even when every reflected field
other than module-instance provenance appears equal. Compatibility posture may
describe a relationship without merging identity.

### Callable storage and installed receiver

`type of myValue.memberFunction` inspects callable type information without
evaluating `myValue`. The member expression itself forms the bound callable when
evaluated. Reflection must distinguish:

- bound versus unbound implementation;
- final versus varying storage;
- static receiver-lifetime capacity;
- installed unbound, borrowed, unique, strong, atomic, or weak binding kind;
- erased receiver type and qualifications where visibility permits;
- a receiverless type-call route using the type's Nothing instance;
- lambda capture receiver identity and declarations; and
- visible prototype versus minted implementation.

Reflection must not present a runtime callable as an unfilled receiver slot or
merge static storage capacity with its current installed mode. Current callable
behavior belongs to
[Zax lambdas and callable composition](../../language/lambdas-and-callable-composition.md).

### Nothing-instance metadata

[Zax Nothing instances](../../language/nothing-instances.md) requires future
reflection to distinguish:

- the effective compiler-default, trapping, or custom policy for one concrete
  type;
- an explicit type policy from an inherited namespace-opening default, module
  default, or language default;
- the written directive source and the opening to which it attached;
- a prepared readable member path from one that panics when accessed;
- a receiverless type-call route from an instance-qualified route;
- runtime pointer presence and function-value availability from static type
  policy;
- requested trap policy, target read/write trap capabilities, and whether
  missing capability leaves an undefined access boundary;
- canonical built-in identity and its fixed cross-module trapping policy;
- custom Nothing storage and initialization cost; and
- semantic pointer vacancy and function availability from a tag, sentinel
  address, shared backing, thunk, target-protected region, or other physical
  representation.

Reflection must not expose compatible backing overlap as ordinary shared
resident identity, merge concrete generic specializations, or imply that a
vacant pointer and absent optional share one state. Exact metadata schema,
source form, and compile-time/runtime availability remain future work.

## Declaration reflection

The direct predicate list is intentionally small. Declaration properties such as
`private`, export status, deprecation, and attached directives belong to future
declaration reflection rather than to an expanding `is <property>` family.

Future work must decide:

- the form of declaration reflection;
- which declaration properties are exposed;
- whether reflection can observe a private member from outside its permitted
  context; and
- whether a declaration's replacement permission — as distinct from the
  underlying place stance reported by `is final` — is reflectable;
- how transfer stance appears on declarations, parameters, results, and
  receivers;
- how a compatible visible prototype identifies the concrete minted
  implementation it reuses without implying body reprocessing;
- how internal implementation-prototype facts differ from outward alias labels,
  defaults, stance, presence, and scheduled/open presentation;
- and whether a declaration is programmer-defined, generated, explicitly
  defaulted, delegated, bodyless, or forbidden.

### Composition metadata categories

Future reflection must not flatten these composition facts into one apparent
member or callable category:

- a physical stored-member declaration;
- an `own`-published access path to that declaration;
- a no-storage data `via` route;
- an independent `preferred` route and its qualification-complete target;
- an independent `expose` relationship and each exact generated wrapper;
- a singular `via` route or `existing` definition, versus `via family` or
  `existing family` adoption and each adopted family member;
- an exact fence, versus a non-executable `forbidden family` fence over the
  complete outer visible callable name or structured operator form, independent
  of source provenance;
- a required or `abstract optional` role, independently of whether it is exact
  or `abstract relaxed`, including which qualifier axes relaxation leaves open;
  and
- each direct, explicitly named fulfillment, including several independent
  qualification-specialized fulfillments of one relaxed role.

Future work must decide the metadata identities, enumeration rules, visibility,
source presentation, and links among these categories. In particular, several
relaxed-role fulfillments are not one merged implementation or a dispatch table,
and family adoption is not singular adoption with an unrecorded wildcard.

Reflection must also keep selected-contract static origin proof, the plain
proof-required `outer cast` or `via` operation, the `outer tracked` type
capability and runtime placement state, and an explicitly checked
`tracked outer cast` or `tracked via` operation distinct. No general reflection
syntax, metadata schema, or compile-time/runtime availability for these
composition facts is accepted by this raw input.

The replacement-permission question above is a live consequence: `is final`
deliberately reports the resolved type-use or referent-place truth, so a program
currently has no way to ask whether *this* declaration may exercise
replacement.

## Type, union, and variant metadata

Current [type definitions](../../language/type-definitions.md),
[unions](../../language/unions.md), and
[variants](../../language/variants.md) add categories that future reflection
must not collapse:

- a physical direct stored member;
- a fixed implementation with no per-instance slot;
- a varying per-instance callable slot;
- a type-owned `once` declaration;
- an unmanaged union lens rather than an ordinary resident member;
- union-admissible and all-bit-pattern-valid status;
- plain versus unsafe union declaration and each lens's extent/alignment;
- flow-sensitive validity of one unsafe-union value, which is a value fact
  rather than canonical union type identity;
- a variant alternative declaration and its payload type;
- absent versus one named active alternative on one runtime value;
- generated/protected variant wrapper lifecycle operations;
- contained-reconstruction `.=` source presentation;
- hidden discriminant or niche facts without promising one representation; and
- language-generated versus programmer-defined body declarations.

Runtime active selection is not canonical variant identity. Likewise, proving
one unsafe-union lens valid does not change the union's type. Exact metadata
types, source forms, privacy, and compile-time/runtime availability remain
future work.

## Allocation reflection

Current allocation behavior is defined by
[Zax pointers, allocation, and arenas](../../language/pointers-and-arenas.md).
Future reflection must expose enough information to distinguish:

- `@`, `@!`, `@<`, and `@!<`;
- resident type and pointer result role;
- explicit, context-provided, role-implied, and defaulted policy choices;
- object and detached control-block arenas;
- inline or detached control placement;
- allocation disposition and collection eligibility;
- declaration-attached versus open-ended raw state;
- selected constructor and construction-packet source;
- allocation records versus ownership control blocks;
- the operation currently holding final disposition authority; and
- a protected `reset` from its destructor and storage-recovery components.

Opaque allocation and observation add separate private type-witness pressure.
Reflection must distinguish `OpaqueOwner`, `OpaqueObserver`, and
`OpaqueReferenceObserver`; allocation-root ownership from an interior
observation; exact hidden type from current vacancy/liveness; and safe recovery
authority from raw representation exposure. No general source form is accepted
here.

Source reflection must preserve an explicit `@{...}` enclosure and applicable
intent acknowledgement even when every stated value equals the effective
default.

## Optional layer and source metadata pressure

[Zax optional values](../../language/optional-values.md) permits independently
qualified repeated wrapper layers:

```zax
MyType readonly? writable? immutable
```

Reflection must preserve:

- the number and order of optional, pointer, and reference layers;
- qualifications attached to each layer;
- wrapper `final`/`varying` truth separately from declaration-side replacement
  permission;
- compact `??` as a different source token from spaced `? ?`;
- aliases that hide optional depth without changing canonical resolved
  structure.

Runtime presence is an expression/value fact, not part of canonical `T?` type
identity. Future reflection must distinguish static wrapper metadata from the
current absent/present state of one value.

## Expression facts versus type identity

```zax
compileValue := 42
runtimeValue := readInteger()
```

Both may have type `Integer`, while only `compileValue` is constant. Constancy is
an expression-evaluation fact and stays a direct query rather than becoming part
of `Integer` identity.

Several distinct result slots each have a type but do not collectively form one
expression type. A future explicit combiner would itself return one typed
structure; see
[Zax structural shapes and compatibility](../../language/structural-shapes-and-compatibility.md#recompose-several-results-with--).

Future reflection must report structural facts without merging their authority:

- canonical type identity separately from direct and flattened shape;
- scalar semantic format separately from byte layout;
- effective compatibility posture, including default `compatible strict`;
- a result's default source anchor and an explicit use-site anchor;
- physical resident places separately from `own` and data-`via` paths;
- no-storage `reshape` declarations and each directional path mapping;
- hidden `outer tracked` components without promising one backend
  representation; and
- shared compiler-hidden arithmetic-report identity separately from the
  caller-facing lack of a source name.

Posture and anchor belong to declaration or use metadata rather than canonical
type identity.

## Source reflection of fences and transparent enclosures

An explicit
[phrase fence](../../language/operator-phrases.md#exact-phrase-fencing) is
required source presentation, yet it creates no node in the final expression
tree. The same is true of the current transparent
[`bare{...}` source enclosure](../../language/source-structure.md#keyword-neutral-bare-source).

Source reflection and documentation tooling must retain enough information to
reproduce both. Future work must decide:

- how a fence is represented when the tree has no corresponding node;
- how a transparent enclosure is represented;
- whether formatters and documentation generators consume the same
  representation; and
- whether source reflection is a separate facility from type and declaration
  reflection.

### Literal source and specialization

Current [literal source](../../language/literal-source-and-operators.md) adds
facts that may disappear from the final value tree:

- the written qualified or unqualified literal prefix;
- the selected literal declaration and exact-alias relationship;
- quote delimiter kind;
- physical payload segments and source spans;
- `<|>` source merge boundaries;
- payload spelling before parser interpretation;
- explicit `<+>` joins;
- `intent<same-prefix-literal-join>` acknowledgement;
- generic `uncommitted` result declaration;
- suggested commitment and the one selected concrete specialization; and
- raw-pattern versus mathematical source.

The literal operator receives only the merged payload `String`; source
reflection must preserve omitted presentation facts separately. Ordinary
`type of` reports the final concrete specialization rather than an
`uncommitted` meta-type. Future reflection must decide which facts are available
to compiler code, runtime code, formatters, documentation tools, and
diagnostics.

### Optional declarations and type-owned constants

Platform-optional `Legacy.WChar` and `Legacy.WideString` require a symbol-aware
compile-time query that can inspect whether a declaration resolves without
first producing an ordinary unknown-type error. Future reflection must decide:

- whether the query consumes an enclosed source unit or declaration path;
- how an unavailable standard role differs from an unknown imported name;
- whether private/ineligible declarations count as defined;
- target versus compiler-host availability;
- source-refactoring behavior; and
- what metadata exists when the type itself is unavailable.

Quoted string lookup is possible but loses symbol-aware checking and is not the
preferred baseline.

Generated fixed/floating type constants add reflection facts:

- mathematical definition and correctly rounded concrete value;
- special class (`inf`, `ninf`, `nan`, `snan`, `nzero`) and availability;
- canonical versus raw NaN representation;
- format limits and fixed-point quantum; and
- language/provider version responsible for generation.

String reflection must expose the logical underlying unit identity (`U8`,
`AsciiChar`, `Ucs2`, `U16`, `U32`, `Legacy.Char`, or optional
`Legacy.WChar`) independently of current storage representation. Future value
reflection/capability queries may additionally report whether one value is
already contiguous, but must not turn that transient fact into canonical string
type identity.

A terminated legacy string specialization additionally needs static policy
metadata for terminator units, in-band acceptance/escaping, contiguity, physical
suffix, logical length, admission, and mutation restoration. Baseline
`Legacy.CharString`, optional `Legacy.WideString`, and MBCS have no implied
terminator policy.

## Enum metadata

[Zax enums](../../language/enums.md) now owns demand-generated string
conversion and the semantic requirements for future member iteration. General
reflection remains responsible for deciding how tools and generic code inspect:

- enum policy and backing identity;
- declared members, names, values, aliases, order, and default;
- generated, replaced, and forbidden operations;
- ordinary admission versus reachable unnamed values; and
- the relationship between narrow generated iteration and general declaration
  metadata.

Reflection must not claim that a runtime value remembers which duplicate-valued
member name produced it.

## Array and slice metadata

Current [arrays and slices](../../language/arrays-and-slices.md) establishes
facts that future reflection must expose without turning representation into a
contract.

Type-level facts include:

- owning fixed, owning resizable, or borrowed slice role;
- element type and complete element qualifications;
- rank and each dimension's exact or ranged bounds;
- logical array identity versus resolved storage provider/profile;
- exact provider type/instance and statically required storage capabilities;
- inline/provider-backed owning representation;
- storage-erased versus exact reference profile;
- descriptor-layer qualifications; and
- explicit versus defaulted storage, bounds, and source properties where
  provenance affects stability.

Value-level facts include:

- current logical length for each applicable dimension;
- usable array capacity;
- provider physical slot/byte extent when its API exposes that fact;
- absent or present suggested capacity;
- current operation/capability availability;
- provider identity and unique handle under permitted typed access;
- stability kind/token and observed/current versions; and
- dynamically exposed storage state only when a declared API makes that state
  programmer-visible.

Array expressions also create source-reflection pressure for `[ ... ]`,
`[ [` nested source, `[[ ... ]]` capture distinction, required array-entry
`from` expansion, final default-remainder `..`, storage clauses, and nested
construction packets.

Reflection must distinguish:

- `active size of T` (or `target` / `host`), which reports one self-contained
  owning representation;
- `active size of T &`, which reports the reference representation;
- a logical type obtained by stripping reference;
- actual owner storage erased behind an unconstrained reference; and
- external provider backing, which neither size query includes.

Knowing a provider type may permit safe typed provider/handle access. Erased
storage may expose an unsafe raw provider-object pointer, never an automatically
contiguous element pointer.

Exact API names, metadata record shape, iterable count provenance, stability
representation, provider/handle reflection, and source-location representation
remain future work.

## Traversal reflection roles

[Zax iteration](../../language/iteration.md) defines current enum and structural
roles as:

```text
value, name, [future reflection]
```

Future reflection work owns the third role's type, contents, lifetime, storage,
and compile-time/runtime availability. The position is preserved so later
metadata does not reorder current bindings, but no reflection binding is
currently available.

Reflection must also distinguish:

- structural traversal of accessible stored instance member places;
- traversal of declarations belonging to a type;
- enum member declarations and duplicate-valued aliases;
- generated, function, operator, and type-owned `once` declarations; and
- metadata that may be inspected only in a compile-time context.

Adding declaration traversal must not silently broaden `each value : in
instance`, whose current domain is stored instance member places.

## Integer-specific metadata

The integer facts themselves are current in
[Zax integers](../../language/integers.md). This section retains the future
reflection shape and tooling exposure required to report them.

Future reflection must let integer tooling and generic code discover:

- logical bit width and signedness;
- minimum and maximum values;
- exact intrinsic specialization;
- transparent alias and public intent-identity relationships;
- admission and exposure choices;
- immediate projection type;
- signedness counterpart and whether it is a named pair or unnamed exact type;
- bit-count, byte-count, delta, distance, and storage associated types;
- storage-envelope byte count, non-value padding extent, and alignment;
- exact, native, or software representation classification;
- requested native-selection constraints;
- selected width, active/target/compiler-host environment, and fallback;
- optimized-maximum selection and software-emulation classification;
- applicable protected operation and conversion families;
- declared range-containment and identity-bridge relationships;
- concrete endianness and active/target/compiler-host selection; and
- normalization requirements for representation-dependent exchange.

Native representation says only that the CPU provider classifies the width as a
native scalar representation. Per-operation instruction availability and cost
remain separate metadata.

Diagnostics should lead with the programmer's source alias or the most useful
public catalog name. Expanded output may show the canonical
`Scalars.Integers...` path and the underlying `Integer$(...)` specialization
without making the generic expansion the ordinary error vocabulary.

Future work must decide whether these facts form an integer-specific metadata
section, general associated-type relationships, convenient phrase operations,
or several layers. This input does not reserve exact reflection phrases.

## Fixed-point and floating metadata

The domain facts are current in
[fixed-point scalars](../../language/fixed-point-scalars.md) and
[binary floating-point scalars](../../language/floating-point-scalars.md).
Reflection must eventually expose them without redefining their meaning.

Fixed-point metadata includes:

- coefficient width, signedness, `F`, quantum, range, and concrete endianness;
- canonical and transparent Q names;
- storage extent, alignment, non-value bits, and normalization;
- associated full-precision-product type;
- exact/optional/rounded conversion relationships; and
- native/software/optional/trap operation support and broad costs.

Floating metadata includes:

- exact format identity and public/profile aliases;
- sign, exponent, bias, fraction, significand precision, and endianness;
- normal, subnormal, zero, infinity, NaN, payload, invalid, and noncanonical
  policies;
- representation and canonicalization relationships;
- active/target/compiler-host selection;
- core support and the extensible support classification;
- exact operation availability and broad cost;
- X87 provider-relative behavior; and
- MBF40's historical operation policy.

`Scalars.Floating.LanguageLimits` values must be discoverable during type
formation. Reflection syntax must not imply that a runtime metadata value can
serve as a compile-time type limit.

Operation reflection should ask about one concrete signature or capability
without requiring one permanently exhaustive bitmap. It must distinguish:

- format semantics from provider support;
- native representation from direct instruction availability;
- guaranteed from runtime-optional hardware;
- an individually available operation from the complete core bundle; and
- canonical/noncanonical validity from one value's runtime classification.

## Compile-time reflection pressure

[Compile-time code](../../language/compile-time-code.md#the-compile-time-___)
fixes one principle for reflection while compiling: a type keeps one identity
in both the compile-time world and the target program, while its shape may
differ. Plain reflection in compile-time code describes the compile-time
world's shape at that point; a question about the target's final shape waits
for it or is rejected. The `ExecutionContext` is the main case, because its
shape grows as partials are declared:

```zax
// Illustrative reflection spelling.
compile{
  for member in members of ExecutionContext {   // the compile-time shape so far
    emitAccessor(member)
  }
}
```

Future work must choose the reflection spelling, and the explicit form that asks
for the target's shape. It must also choose the exact spelling for asking
whether a hook point has fulfillments, whose availability rule is current in
[partials](../../language/partials.md#asking-at-compile-time-whether-a-hook-is-fulfilled).

## Generic type operations

[Zax generics](../../language/generics.md) works out a type parameter as exactly
the argument's type, including its qualifiers and any reference or pointer
shape, and compares types with exact `==`. Generic code therefore needs type
operations that reflection must supply.

**Removing qualifiers.** A constraint that should accept every qualification of
one type compares the type with its qualifiers removed:

```zax
// Illustrative spelling; a shorter form such as `deduced(UseType)` is a future choice.
show$ final : (UseType : type)()(value : UseType readonly)
    requires unqualified type of UseType == Boolean = { }

myConstant : Boolean immutable = true
show(myConstant)   // UseType is `Boolean immutable`; the comparison sees `Boolean`
```

**Removing references and pointers.** A generic that wants value semantics
whatever the argument needs the same for `&` and `*`:

```zax
keep$ final : (UseType : type)()(value : UseType) = {
  myStored := value   // a reference when UseType is `Boolean &`
}

myView : Boolean & = myFlag
keep(myView)          // future: strip the reference to store a copy
```

**Looser comparisons.** Comparisons weaker than exact `==`, such as "the same
type apart from qualifiers" or "similar to", belong here rather than in `==`.

**Layout descriptors obtained from an environment.** Common layout queries
have dedicated forms that always name an environment (`active size of`,
`target size of`, `host alignment of`); there is no plain `size of`. Rarer facts, such
as padding, byte order, storage envelope, or provider support class, should
come from a layout descriptor obtained from a named environment, so the
environment is explicit by construction:

```zax
// Illustrative spellings.
if compile Compilation.target.layout(UseType).paddingBits > 0 { ... }
if compile Compilation.compilerHost.layout(UseType).byteOrder == Endian.Big { ... }
order := Compilation.active.layout(UseType).byteOrder   // explicitly the active environment
```

Every environment-dependent layout fact comes from an explicitly named
environment: there is no plain `layout(UseType)` that implies one. The reason
is the same as for the dedicated queries. Compile-time code runs on the build
machine, and an implied environment there would silently produce a
build-machine fact that could later decide something about the target. Layout
agreeing between one build machine and target, or a profile saying a value
never changes, is never treated as proof that it is fixed.

An explicit `active` request is acceptable, because the programmer has named
the choice; while compiling it means the compiler host. A lint may point out
an `active` descriptor used in a compile-time decision. Future work defines the
descriptor's contents and spelling.

**Instantiation facts.** Reflection must be able to report which declaration
(general or specialization) an instantiation came from, and its completed
arguments, so diagnostics and tooling can explain a selection.

Exact `==` on types is current: an alias equals its target, an identity type is
distinct, and qualifiers participate.

## Constraints this input places on current work

- current documentation must not present illustrative reflection wording as an
  exact source reservation;
- canonical type identity must not absorb expression-specific facts; and
- the direct predicate family must not expand to cover declaration properties;
- public aliases remain useful diagnostic vocabulary even when reflection exposes
  canonical identity; and
- scalar-family-specific facts need not become universal fields on every type.

## Activation and retirement

Activate this input when reflection, type or declaration metadata, scalar-family
metadata, `LanguageLimits`, support/operation availability,
validity/canonicalization, source reflection, or reflection-dependent tooling is reviewed. Move
accepted behavior into a reflection owner and the domain owners whose facts it
exposes, then retire this file after every preserved question is dispositioned.
