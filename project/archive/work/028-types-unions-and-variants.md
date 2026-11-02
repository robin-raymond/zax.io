# 028: Types, unions, and variants

| Field | Value |
| --- | --- |
| Status | Historical working record / non-normative / audit-only |
| Work Item | `028` |
| Created | 2026-09-20 |
| Completed | 2026-09-21 |
| Owns | The bounded review defined by the fixed initiating input |
| Does Not Own | Complete generics, pattern matching beyond concrete variant pressure, FFI/ABI contracts, reflection schemas, compiler implementation, or runtime layout except where a programmer-visible type/union/variant decision constrains them |

## Non-authority notice

This file is a collaborative working record. Existing statements, new
observations, candidate wording, and aligned findings remain non-authoritative
until a separately discussed, aligned, and explicitly authorized promotion
incorporates them into their lasting owners.

## Fixed initiating input

This section records the aligned information known when work item `028` was
created. It is intentionally incomplete and must not be rewritten as work
develops.

### Initiating concern

Establish a coherent programmer-facing model for defining and using Zax types,
including ordinary stored/member behavior, unmanaged overlapping `union`
storage, managed `Variant` alternatives, and the type-declaration integration of
`outer tracked`.

Zax discusses type behavior throughout declarations, construction, lifetime,
qualification, composition, structural compatibility, identity, enums,
optionals, and pointers without one dedicated current language entry teaching
how a type definition works as a whole. The root `type-definition.md` page
contains substantial legacy evidence but is not authoritative language design.

The maintainer-supplied
[types, unions, and variants notes](../raw/types-unions-variant-maintainer-notes.md)
are the latest primary input for this work item. They may add to, replace,
supersede, or reject conflicting legacy evidence.

Current `language/` owners remain current for the concerns they already own.
Root legacy pages and other legacy examples are always input to mine for useful
intent, not authority merely because current review has not yet replaced them.
When refreshed maintainer notes and legacy material conflict, treat the
maintainer notes as the likely current direction and bring any conflict with a
current `language/` owner to explicit review.

### Motivating pressure

The work must give a programmer one teachable model for:

- what `Name :: type { ... }` introduces;
- which declarations contribute stored shape, behavior, metadata, or no
  per-instance storage;
- named and anonymous type identity;
- incomplete self-reference, forwards, recursive layout, and completion;
- member construction, lifetime, replacement, destruction, and generated
  operations;
- qualification and access across a type and its members;
- physical containment, composition-facing paths, and structural shape;
- unmanaged overlapping storage and its active-lifetime/safety obligations;
- managed variant alternatives and their active-path behavior;
- selection, access proof, transfer, replacement, and destruction of
  alternatives; and
- `outer tracked` as a type capability with hidden placement and lifecycle cost.

Without a cohesive owner, programmers must reconstruct basic type meaning from
several specialized documents, and union/variant work can accidentally invent a
second lifecycle, qualification, or composition model.

### Known inclusions

- Ordinary named type declaration and body organization.
- Stored members, instance functions, unbound/type-owned functions, nested
  declarations, and other non-storage body contributions.
- Type identity, aliases, incomplete self-names, forwards, recursion, size, and
  layout pressure.
- Default/direct construction, generated lifecycle operations, replacement,
  destruction, and member order.
- Qualification, transfer, reference origin, member-place stability, and
  composition boundaries.
- Anonymous types where concrete evidence is sufficient.
- Unmanaged `union` storage, overlapping lenses, active resident-instance
  obligations, unsafe boundaries, alignment, and destruction.
- Managed `Variant` alternatives, discriminant/presence state, active
  alternative paths, construction, access, transfer, replacement, and
  destruction.
- Variant interaction with `switch`, exhaustiveness, and future pattern
  matching to the depth needed for a coherent current model.
- `outer tracked` declaration, hidden representation, placement relationship,
  replacement/copy/destruction effects, and outer-cast integration.
- Costs, diagnostics, source stability, terminology, navigation, legacy
  disposition, and documentation ownership.
- Complete disposition of the maintainer notes, reached raw input, and useful
  legacy `type-definition.md` material.

### Known starting boundaries

- Complete type-parameter/generic syntax, constraints, and specialization.
- A general pattern-matching language beyond concrete managed-variant pressure.
- Complete FFI, ABI, stable layout contracts, foreign unions, and calling
  conventions.
- General reflection APIs and metadata schema.
- Complete partial/open type extension authority.
- Compiler data structures, lowering, tagging algorithms, niche optimization,
  and code generation except where programmer-visible behavior or cost
  constrains them.

These boundaries do not erase consequences. Preserve every material pressure in
a current owner or indexed raw destination rather than leaving it only in the
eventual archive of this work item.

### Intentionally unresolved framing

- Whether ordinary type teaching and union/variant teaching belong in one
  lasting owner or a cohesive type owner with specialized linked owners.
- Exact `union` and `Variant` declaration/source forms.
- Whether unmanaged union access ever has a safe active-member path or always
  requires explicit unsafe responsibility.
- Variant default state, alternative naming, recursive alternatives, and
  selection/binding source.
- Which lifecycle operations are generated for unions and variants.
- How anonymous type identity interacts with named type definitions.
- Exact placement and spelling of `outer tracked` among type-declaration
  properties.
- Which accepted facts are conceptual language guarantees and which remain
  future formal layout, reflection, ABI, or implementation contracts.

### Initial stopping guidance

Stop when the work has:

- established a cold-reader-friendly ordinary type-definition model;
- defined representative ordinary, recursive, composed, outer-tracked, union,
  and managed-variant source;
- reconciled declaration, identity, stored shape, construction, lifetime,
  qualification, transfer, selection, safety, diagnostics, and cost behavior;
- separated managed alternatives from unmanaged overlapping storage;
- preserved generic, pattern, reflection, ABI/FFI, partial-type, and
  implementation deferrals in live owners;
- dispositioned every useful maintainer-note, raw, and reached legacy finding;
  and
- completed the required documentation-fit dry run.

Do not promote findings, archive this work item, begin work item `029`, or
redesign complete generics, pattern matching, FFI/ABI, reflection, partial
types, or compiler implementation without the separately required discussion,
alignment, and authorization.

## Reading scope

### Required

- [Documentation architecture](../documentation.md) - governs focused reading,
  decision maturity, promotion, deferral, and closure.
- [Types, unions, and variants maintainer notes](../raw/types-unions-variant-maintainer-notes.md) -
  latest maintainer-supplied primary input; read and disposition completely
  after assignment.
- Legacy `type-definition.md` - principal historical type, member,
  anonymous-type, function-member, and union evidence consumed by this work and
  removed after promotion; recover through Git history for a targeted audit.
- Raw `project/raw/variants-and-unions.md` - historical
  managed-alternative/unmanaged-overlay input consumed and removed after
  promotion; recover through Git history for a targeted audit.
- [Declarations and bindings](../../language/declarations-and-bindings.md) -
  owns `type` declarations, stored/member declarations, identity, forwards,
  incomplete self-names, aliases, and declaration qualification.
- [Construction, replacement, and destruction](../../language/construction-and-destruction.md) -
  owns complete/member lifecycle, generated operations, replacement, destruction
  order, and manual lifecycle boundaries.
- [Lifetimes and references](../../language/lifetimes-and-references.md) - owns
  life paths, instance places, resident instances, member renewal, references,
  and conditional-path consequences.
- [Qualifiers](../../language/qualifiers.md) - owns place, value, access, member,
  indirection, and unsafe-pliability qualification.
- [Transfer stances](../../language/transfer-stances.md) - owns `copy`, `deep`,
  `move`, `last`, projection, source state, and generated-operation pressure.
- [Composition](../../language/composition.md) - owns physical containment,
  `own`/`preferred`/`expose`, semantic-indirection boundaries, `outer tracked`,
  and outer casting.
- [Structural shapes and compatibility](../../language/structural-shapes-and-compatibility.md) -
  owns stored shape, flattening, layout, compatible/coercive views, and
  transformation.
- [Identity types](../../language/identity-types.md) - owns nominal identity,
  transparent aliases, admission/projection, and underlying boundaries.
- [Enums](../../language/enums.md) - supplies a nearby integer-backed identity
  model and selection/exhaustiveness pressure without deciding variants.
- [Optional values](../../language/optional-values.md) - supplies the current
  managed absent/present wrapper and boxed-life-path comparison.
- [Switch, case, and default](../../language/switch.md) - owns current runtime
  value selection, exhaustiveness, and active proof flow.
- [Raw pattern matching](../raw/pattern-matching.md) - preserves future
  destructuring and alternative-binding pressure reached by managed variants.
- [Safety and analysis](../../language/safety-and-analysis.md) - owns proof,
  unsafe assertions/permissions, and known-invalid lifecycle boundaries.
- [Source structure](../../language/source-structure.md) - owns body, brace,
  delimiter, spacing, and declaration/expression presentation.
- [Language-design terms](../../language/terms.md) - owns cross-cutting type,
  place, lifetime, identity, and safety vocabulary.

### Consequence-driven

- [Pointers, allocation, and arenas](../../language/pointers-and-arenas.md) when
  `outer tracked`, allocated types, erased ownership, alignment, or provenance
  consequences become concrete.
- [Function invocation](../../language/function-invocation.md) and
  [lambdas/callable composition](../../language/lambdas-and-callable-composition.md)
  when callable members, generated behavior, variant callbacks, or result
  routing becomes concrete.
- [Raw type parameters and generics](../raw/type-parameters-and-generics.md) when
  generic type definitions, recursive specialization, generic variants, or
  constraints become concrete.
- [Raw partial types](../raw/partial-types.md) when extension authority, stored
  member additions, or type completion becomes concrete.
- [Raw reflection](../raw/reflection.md) when type/member/alternative metadata,
  anonymous identity, or source reflection becomes concrete.
- [Raw interoperability](../raw/interop.md) when foreign layout, C-style unions,
  stable ABI, or representation contracts become concrete.
- Relevant legacy constructor, casting, union, enum, optional, or composition
  material only when a concrete question cannot be resolved from the primary
  notes, current owners, and focused raw inputs.

### Audit-only

- Archived work only when a concrete provenance or regression question cannot
  be answered from current owners and live raw input.

## Initial working posture

Begin with programmer-visible examples of:

1. an ordinary named type with stored members and one instance function;
2. a self-referential type using finite indirection;
3. a type using composition without flattening lifecycle;
4. an `outer tracked` type and one checked outer relationship;
5. an unmanaged union with its active-lifetime responsibility; and
6. a managed variant whose active alternative changes safely.

For each, state identity, stored shape, live paths, construction/destruction,
qualification, access, transfer, hidden state, and failure cost before proposing
one general taxonomy.

Use the maintainer notes as the latest primary evidence. Preserve current owner
constraints and treat legacy material as input rather than authority.

## Working record

### Initial reconstruction status

Unless a later section explicitly labels a finding aligned, the reconstruction
below remains candidate analysis for review. Aligned findings in this working
record are still non-authoritative until separately promoted into their lasting
owners.

The required reading was completed. Concrete consequences also required focused
reading of the current pointer, callable, array, integer, and floating-point
owners and the raw reflection, partial-type, and interoperability inputs:

- pointer and callable storage determine which values can appear in an
  unmanaged overlay without hidden ownership or receiver-lifetime work;
- arrays remain lifecycle-owning values even when a `Flat` provider makes their
  element storage contiguous;
- the current scalar owners answer the maintainer note's open
  `Float`/`U32` representation question;
- type, member, lens, alternative, discriminant, and hidden-placement facts
  create concrete future reflection categories;
- type completion and stored shape constrain any future partial-type mechanism;
  and
- a C- or C++-like union model creates ABI pressure without establishing a
  foreign-layout contract.

### Initial review entry point

The current candidate model has three deliberately different constructs:

- An ordinary `type` introduces one type identity. Each instance has one
  physical stored shape, one complete lifetime, and direct member places.
  Functions, nested definitions, routes, roles, and other metadata can belong to
  the type without adding per-instance storage.
- A `union` introduces one untagged storage value with several named,
  offset-zero typed lenses. It has no selected alternative and no member
  lifetime to switch. Safe unions admit only lens sets whose legal writes keep
  every lens representation valid; `union unsafe` admits a wider low-level
  relationship under unresolved unsafe responsibility.
- A `variant` introduces one managed wrapper with hidden selection state and
  zero or one active named alternative under the latest maintainer notes.
  Changing the selected alternative ends one conditional life path and begins
  another.

The most important unresolved conflict is the variant model. The maintainer
notes demonstrate an absent default state, runtime-panicking unchecked
alternative access, and `switch` cases that both select and bind alternatives.
Current owners instead say that a managed variant has exactly one active path,
that conditionally live storage requires static proof before access, and that
current `case` alternatives are Boolean tests that introduce no bindings.
Those are not wording differences; they change valid source, runtime checks,
control-flow facts, and the relationship between variants and optionals.

The union direction is clearer: the legacy active-member/manual-lifecycle model
is superseded. The remaining hard question is how the intended jagged
offset-zero overlays relate to the current, equal-extent `as coercive` relation.
A union-specific overlay relation appears necessary if differently sized safe
lenses are retained.

The first maintainer review should therefore decide:

1. whether a default variant is absent and hence has zero or one active
   alternative;
2. whether unproved direct alternative access is a checked panic or a static
   error;
3. whether variant selection introduces a narrow variant-specific binding form
   or activates generalized pattern work;
4. whether jagged safe unions are intended to use a new overlay-validity rule
   rather than the current coercive-view rule; and
5. where unsafe-union responsibility must be written at each access.

### Aligned findings after maintainer review

The maintainer reviewed the reconstruction and aligned the following conceptual
direction. These findings remain non-authoritative working material.

#### Ordinary types and `outer tracked`

- An ordinary named `type` has one identity, one direct stored shape, and one
  complete instance lifetime. Type-body declarations can contribute storage,
  behavior, type-owned state, routes, or metadata without all becoming stored
  members.
- `Name :: type outer tracked { ... }` remains the settled spelling and current
  placement. No present pressure justifies changing it.
- The relative ordering of possible future type-header properties remains for
  the feature that introduces them. That future work must integrate with
  `type outer tracked`; it does not reopen the existing words merely because
  more properties become possible.
- Every authorized partial fragment that can affect stored shape, union lenses,
  variant alternatives, hidden components, or lifecycle operations must merge
  into one reproducible, order-independent complete definition before layout,
  construction, compatibility, union admissibility, variant storage, or
  exhaustive selection is finalized.
- A later partial cannot add such shape- or lifecycle-affecting material after
  completion. This is explicit pressure on future partial-type design, not a
  current partial syntax decision.
- Specialized declarations have category-specific forward anchors:
  `forward union` and `forward variant`. Completion must preserve that category,
  including completion through an exact alias to an existing union or variant.
- Anonymous `:: union { ... }` and `:: variant { ... }` declarations are
  available wherever the corresponding anonymous type declaration context is
  accepted. Each declaration creates one stable anonymous identity discoverable
  through `type of`. Anonymous recursive self-syntax remains unavailable.

#### Union terminology and admissibility

`trivial` is too broad and overloaded for the complete requirement. The aligned
working terms and tests are:

- A type is **union-admissible** when its representation may be overwritten,
  copied, and abandoned without invoking or bypassing required construction,
  destruction, replacement, resource disposition, reference binding, ownership
  accounting, or placement-repair work.
- A type is **all-bit-pattern-valid** when every possible pattern in its
  complete storage envelope represents an ordinary valid value. Padding and
  non-value bits count as ignored by that type and may have any contents.
- A plain union is **overlay-safe** as a consequence of every lens being both
  union-admissible and all-bit-pattern-valid.

This deliberately conservative rule avoids pairwise code-body or invariant
analysis. `union unsafe` requires only union admissibility; a plain `union`
requires both properties from every lens.

A union-admissibility diagnostic should recursively identify the first blocking
obligation rather than merely report that a type is “nontrivial.” Future
reflection may expose the property, but no source keyword or general-purpose
`trivial` concept is accepted here.

An aggregate derives these properties from its resolved stored representation:

- every stored component must satisfy the applicable property;
- layout padding is ignored and therefore accepts arbitrary contents;
- required custom construction, destruction, copy, replacement, ownership, or
  placement work prevents union admissibility; and
- a semantic invariant not represented by a language-visible identity,
  admission policy, lifecycle operation, or component is not inferred from
  function bodies.

This rule can reject a theoretically safe pair of restricted types with exactly
the same value subset. Such a pair uses `union unsafe`; a future explicit
pairwise contract may recover it only if real pressure justifies the added
complexity.

#### Untagged overlay semantics

- A union is one untagged backing value with named offset-zero typed lenses.
- It has no active lens, selected alternative, discriminant, or member lifetime
  to construct or destroy.
- Jagged safe unions are intentional. A smaller lens modifies only the bytes in
  its extent at the beginning of the union backing. The remaining suffix keeps
  its previous zero-filled or written bits.
- Concrete endianness determines which part of a wider scalar those starting
  bytes represent.
- Union size accommodates the largest lens extent. Union alignment satisfies
  every lens's required alignment.
- A write through a lens may leave any permitted non-value or padding bits
  unspecified. A plain union remains safe because every pattern is valid for
  every lens; an unsafe union must prove the requested lens or assert its
  validity locally.
- A plain `union` accepts only lenses that are both union-admissible and
  all-bit-pattern-valid.
- `union unsafe` accepts union-admissible lenses without requiring global
  overlay safety. It still cannot admit a type whose lifecycle or placement
  obligations make raw overlay impossible.

Consequently:

- managed `unique`, `strong`, `weak`, anchored, or atomic pointer forms are
  never union-admissible;
- references are never union-admissible;
- callables carrying a bound receiver lifetime are never union-admissible;
- `String` and other allocation- or lifetime-managing values are never
  union-admissible;
- arrays are never union-admissible, including arrays using `Flat` storage;
- `outer tracked` values are never union-admissible because arbitrary lens
  writes cannot establish or preserve valid placement metadata;
- raw pointers are union-admissible only in `union unsafe`; and
- unbound callable representation may be union-admissible only in
  `union unsafe`. Zero-filled backing may be an invalid callable state, so no
  access is justified until the required lens validity is established.

The deciding distinction is not whether a value has a conceptual lifetime.
Every value does. A float lifetime can end by abandoning its bits with no
observable hook or relationship to repair. An `outer tracked` value cannot,
because its valid hidden state depends on placement establishment and
maintenance.

#### Union initialization, copy, transfer, and destruction

- Default union initialization zero-fills the complete backing.
- Zero-fill must be valid through every lens of a plain union.
- A `union unsafe` may contain a lens for which zero-fill is not valid; that
  lens cannot be accessed without applicable proof or unsafe responsibility.
- Ordinary source default-zeroes a union and then writes a lens. A union
  construction packet is unavailable because it would falsely suggest
  member-lifetime construction.
- One-expression immutable or compile-time formation uses an ordinary unbound
  factory: construct a zero-filled local union, write a lens, and return the
  complete union value. The factory may execute at compile time under ordinary
  compile-time rules. No raw representation literal or special union
  constructor is required.
- Whole-union construction or assignment from the same union type copies every
  backing bit. The compiler cannot choose a selective lens because no lens is
  active.
- A programmer can deliberately perform a selective copy by reading and
  writing a particular justified lens.
- `move` and `last` resolve to union `copy`; the source remains unchanged.
- `deep` is unavailable: an unsafe union may contain passive raw pointers or
  other representations for which all-bit copy does not establish independent
  resources.
- No equality, ordering, or hashing is generated from the backing bits. Padding
  and lens interpretation make such behavior domain-specific.
- A union cannot declare custom constructors, replacement constructors,
  destructors, or whole-value copy/move hooks. Ordinary fixed functions and
  operators may explicitly use lenses.
- Ending the complete union lifetime invokes no union or lens destructor hook.
  A union's backing is abandoned after its lifetime ends.

Representative factory:

```zax
MyUnion :: union {
  byte : U8
  word : U32

  fromWord final : (
    result : MyUnion
  )(
    value : U32
  ) unbound = {
    resultValue : MyUnion
    resultValue.word = value
    return resultValue
  }
}

value final : MyUnion immutable = MyUnion.fromWord(4995)
```

#### Profile-selected floating lenses

`Float` may participate in a plain union when its profile-resolved exact
representation is union-admissible and all-bit-pattern-valid. A profile
selecting an incompatible representation makes that declaration invalid for
the profile.

This target dependence is acceptable because `Float` already states
profile-selected intent. Source requiring a portable exact relationship should
name an exact format such as `Binary32` and an exact peer such as `U32`.
`Float` remains available in `union unsafe` when it is union-admissible but not
all-bit-pattern-valid.

#### Unsafe-union proof and source stability

A plain union's declaration proves global overlay safety, so ordinary lens
access needs no per-use unsafe control.

For `union unsafe`:

- zero-fill establishes validity only for lenses whose zero representation is
  accepted;
- writing one valid source through a passive lens establishes validity of that
  lens;
- another lens is valid without unsafe only when the selected language contract
  proves that fact from the representation and flow reaching the access;
- a direct complete copy preserves the validity facts known at that source
  point without interpreting another lens;
- branch convergence retains only facts true on every path;
- readonly calls preserve facts, while a call or alias that may write the union
  invalidates them;
- an opaque function result carries no lens-validity fact until a future
  callable contract says otherwise; and
- an unproved lens read, operation, or reference formation uses the aligned
  provisional category
  `unsafe<union-lens-validity>{...}`.

A compiler may perform stronger private analysis, optimize checks, or advise.
Private cleverness alone cannot make omission of a required assertion portable
or make a portable assertion invalid. Stronger source-validity behavior belongs
to an explicitly selected analysis contract.

`union-lens-validity` is now inventoried in the
[raw analysis-control registry](../raw/analysis-control-registry.md). The
registry is non-authoritative future-work input.

#### Variant cardinality and reset

- A variant wrapper has zero or one active named alternative.
- When present, exactly one named payload path is resident.
- Default construction produces absence. An explicit zero-entry construction
  packet also invokes that default and produces absence.
- `reset myVariant` destroys the active payload when present and leaves the same
  wrapper absent. Reset on an absent wrapper is a no-op and returns access to
  that wrapper without increasing authority.
- Alternative names distinguish states even when several alternatives have the
  same payload type.

This corrects the earlier sloppy “exactly one” wording in current lifetime and
raw-variant input; it does not change the intended one-when-present invariant.

#### Protected contained reconstruction with `.=`

`.=` is the aligned, non-overloadable protected symbolic form for re-delivering
construction inputs to an existing semantic wrapper. The wrapper lifetime
continues while the operation ends old contained state and establishes fresh
contained state:

```zax
myVariant.text .= "first" // select/reconstruct `text`
myVariant.text = "second" // ordinary assignment through checked `text` access
myVariant = otherVariant  // replace the complete wrapper
reset myVariant           // destroy the payload and become absent
```

The same operation clarifies optional intent:

```zax
myOptional .= value       // construct/reconstruct a present payload
myOptional. = value       // assign through proven-live payload access
myOptional = otherOptional // replace the complete wrapper
reset myOptional           // destroy the payload and become absent
```

Construction packets can receive their destination from `.=`:

```zax
myVariant.connection .= [{ endpoint }]
myOptional .= [{}]
```

The direct and packet forms correspond to ordinary construction:

```zax
myOptional .= value
myOptional .= [{ constructorArguments }]

myVariant.text .= value
myVariant.myType .= [{ constructorArguments }]
```

The first form directly constructs from one source. The second supplies several
constructor inputs. The left side is a destination designator, not a read of an
old payload. An inactive named variant path therefore does not panic before the
operation selects and constructs it.

A variant wrapper can instead receive a variant construction packet:

```zax
myVariant .= [{}] // reconstruct absent state

myVariant .= [{
  .myType = (: MyType = [{ "test", myValue }])
}]
```

A wrapper packet has zero alternative entries for absence or exactly one named
alternative entry for presence. More than one is an error. A bare
`myVariant .= value` is invalid because equal-typed alternatives still have
different names; whole-wrapper assignment from another equal variant uses `=`.

Packet-targeted variant `.=` returns wrapper access because the zero-entry form
has no payload. Named-alternative and optional-payload `.=` return access to the
freshly constructed payload with its resolved qualifications.

`.=` has assignment precedence and right association. It evaluates the wrapper
and target designator once, then evaluates and binds every source input before
ending the old payload. It constructs the new payload and publishes presence or
the active name only after construction completes. A possible alias into the
old payload must be resolved or use the applicable `replacement-alias`
responsibility. A panic leaves the same operation blocked and exposes no
partial payload or rollback path.

The wrapper must be mutable and the path writable. A final or varying wrapper
place may use `.=` because the complete wrapper lifetime is not replaced.
Payload qualifications govern returned access. The form applies only to
optional payloads, named variant alternatives, and variant wrapper packets; it
does not become a general delayed-construction operator.

#### Variant direct access and panic

Direct named alternative access follows four cases:

1. proven active: access succeeds and the check may be omitted;
2. proven inactive: mandatory compile-time error;
3. not proved either way: perform the required runtime check and panic when the
   requested name is inactive; and
4. check explicitly disabled: the programmer promises the requested name is
   active, and violation has undefined consequences.

The aligned provisional panic category is
`inactive-variant-access`, enabled by default. It applies to reads, reference
formation, ordinary assignment, compound operations, and calls through a named
alternative. It does not apply to `.=` or to a variant case binding whose route
establishes the active name.

Static proof can remove an enabled check. Disabling the category never
suppresses a statically proved error and does not alter a reporting operation.
The category is inventoried in the
[raw analysis-control registry](../raw/analysis-control-registry.md).

High-confidence but unproved findings remain explicit pressure on future safety
and diagnostics work. Variant access supplies a concrete distinction among a
proved invalid access, runtime-checkable uncertainty, an uncheckable proof gap,
and compiler suspicion suitable for warning policy. This work does not make
portable source validity depend on one compiler's confidence heuristic.

#### Variant-specific `switch` mode and binding

When a `switch` selector's static type is a variant, its `case` entries enter a
variant-specific mode:

- bare case names resolve in the variant's alternative namespace, not as
  lexical expressions or ordinary Boolean tests;
- a single named case selects and binds that same alternative name;
- comma-separated names can share one body;
- the contextual `bind` form gives several alternatives one common
  body-local binding;
- the body is checked separately for every alternative type that can select it;
- `case !` selects absence;
- `case ? bind value` can select any present alternative and specialize the
  body for every reachable payload type; and
- `default` remains positional fallback for any state not selected earlier. It
  means absence only when every present alternative has already been handled.

Representative source:

```zax
switch myVariant {
  case text {
    print(text)
  }

  case number, count bind value {
    print(value)
  }

  case ! {
    handleAbsent()
  }
}
```

`bind` is preferred over `as` because it introduces a local binding rather than
converting or requalifying a value. A case flow label remains a separate
switch-wide transfer target:

```zax
case retry: number, count bind value {
}
```

The label cannot double as the payload binding. Flow labels and ordinary
variables have different spelling, namespace, visibility, and transfer
semantics. In particular, `goto retry:` cannot manufacture the payload binding.

This is a finite variant-specific selection facility. It does not activate
recursive patterns, structural destructuring, guards, optional pattern
flattening, or general value-producing pattern selection.

#### Variant control flow and payload transfer

Control-flow transfer and payload transfer are separate concerns:

- direct `goto` into an alternative-routed body is illegal because it bypasses
  routing and binding;
- a `continue` to an alternative test entry may be legal when it reruns routing
  and establishes the binding normally;
- direct entry into a transfer-only or otherwise unbound body remains governed
  by current common-facts rules;
- `next`, `break`, and outward transfers retain their ordinary meanings; and
- no incoming path may use facts or bindings that its route did not establish.

A payload may separately be transferred through its checked active access:

```zax
consume(myVariant.owner as last)
```

The source variant remains present with a destruction-valid terminal payload
until `reset`, `.=` selection, whole-wrapper replacement, or destruction ends
that payload. Transferring into another variant therefore does not silently
reset the source. Whole variant-to-variant assignment otherwise requires equal
variant types.

#### Variant construction and whole-wrapper operations

Direct declaration construction accepts either the absent default or one named
alternative:

```zax
absent : MyVariant = [{}]

present : MyVariant = [{
  .text = "ready"
}]
```

A variant construction packet has zero alternative entries for absence or one
for presence. It cannot name several alternatives. Unlike a union packet, this
is genuine managed payload construction.

Whole-wrapper operations preserve the active name:

- `copy` preserves absence or copies the active payload;
- `deep` preserves absence and requires exact `deep` support from every payload
  the wrapper may contain;
- `move` preserves the active name and leaves the source payload moved-from;
- `last` preserves the active name and leaves the source payload terminal but
  destruction-valid;
- same-type `=` destroys the old destination payload and establishes the source
  wrapper state;
- `reset` destroys the active payload and establishes absence; and
- wrapper destruction destroys the active payload when present.

A whole-wrapper operation is available only when every alternative it may
encounter supports the required operation. Flow that proves one alternative
can operate on that payload directly. Selecting the same alternative with `.=`
still renews its payload lifetime and invalidates every reference to the former
payload.

#### Variant case coverage and binding stance

A case binding is reference-shaped access to the resident payload. It performs
no copy. It preserves origin, qualifications, and the transfer stance projected
from the retained selector; a later selected consumer performs any actual
transfer.

The body is checked independently for every alternative that can reach it.
Diagnostics identify the failing alternative and operation. Bare `case ?`
introduces no binding, while `case ? bind value` does.

Coverage separates:

- **alternative coverage**: every declared name is handled by a named case or
  by `case ?`; and
- **state coverage**: absence is handled by `case !` or positional `default`.

Ordered selection remains in force. Named cases before `case ?` handle their
specific alternatives first; `case ?` then specializes over every remaining
present alternative. `default` is fallback but does not silently count as
explicit alternative coverage. Deliberately omitting alternatives uses the
aligned `intent<partial-variant-selection>{...}` category.

A same-name case binding follows ordinary local shadowing rules. `bind` renames
the payload when the alternative name would collide:

```zax
case text bind selectedText {
  use(selectedText)
}
```

#### Variant body organization and protected lifecycle

A variant body begins with its alternative prologue. Every per-instance stored
declaration is an alternative. The first no-instance-storage definition begins
ordinary definition mode, after which alternatives cannot resume.

Allowed later definitions include:

- `final` functions and operators, which add no replaceable per-instance slot;
- `once` functions or callable slots, whose state belongs to the complete type
  rather than each variant instance; and
- nested types, aliases, and other genuinely no-instance-storage metadata.

A `final` value remains storage and therefore remains an alternative. A direct
varying callable declaration is rejected because it would create hidden
per-instance storage outside variant selection. Store a callable explicitly as
an alternative instead.

Programmer-declared constructors, replacement constructors, destructors, and
replacements for the exact wrapper `=`, `.=` or reset/destruction contracts are
prohibited. The protected wrapper operations own discriminant publication,
absence, payload dispatch, reference invalidation, and exactly-once
destruction. Ordinary final factories and methods can compose those protected
operations, while each payload type retains its own lifecycle customization.

#### Documentation ownership direction

The likely lasting teaching structure is a common ordinary type-definition
owner plus specialized union and variant owners. This is documentation
architecture, not a semantic distinction, and remains subject to the required
pre-promotion documentation-fit dry run.

The accepted intent registry already exists in
`language/intent-acknowledgements.md`. Unsafe and panic names previously lacked
a centralized live inventory, so the authorized capture added
`project/raw/analysis-control-registry.md` and indexed it from the raw router.
The legacy registry in `compiler-directives.md` remains evidence only.

### Initial candidate reconstruction retained for review history

The examples and analysis in this section preserve the initial review probes.
Later aligned findings above supersede them where they differ. Variant and
jagged-union source remains explicitly illustrative where it conflicts with or
extends current owners.

#### Ordinary named type

```zax
MyPoint :: type {
  x : I32
  y : I32

  translate final : ()(
    dx : I32,
    dy : I32
  ) writable = {
    _.x += dx
    _.y += dy
  }
}

myPoint : MyPoint
myPoint.translate(3, 4)
```

Candidate reading:

- `MyPoint` is a distinct named identity.
- Its direct stored shape is `x` followed by `y`.
- `translate` contributes behavior but, because it is `final`, no replaceable
  callable slot is stored in each `MyPoint`.
- Default construction establishes `x` and then `y`; ordinary destruction ends
  them in reverse declaration order.
- `_.x` and `_.y` are direct member places reached through the current
  instance's qualifications.
- A transparent alias can add another name for `MyPoint`; an equal-looking type
  remains a different identity.

#### Finite self-reference

```zax
MyNode :: type {
  value : Integer
  next : MyNode * unique
}
```

`MyNode` is visible by its incomplete self-name while its body resolves. The
pointer has finite representation, so `next` does not recursively inline
another complete `MyNode`. Its default value is a vacant owner. If it later owns
a node, pointer destruction follows the pointer's ownership contract.

Direct storage remains invalid:

```zax
MyBrokenNode :: type {
  next : MyBrokenNode // error: infinitely recursive stored layout
}
```

Mutually recursive named types use `forward` where source order requires an
earlier root. No current anonymous recursive type syntax exists.

#### Composition preserves the member boundary

```zax
MyEngine :: type {
  rpm : Integer

  start final : ()() = {
  }
}

MyCar :: type {
  engine own expose : MyEngine
  wheels : Integer
}

myCar : MyCar = [{
  .engine = makeMyEngine()
}]

myCar.rpm = 2000
myCar.start()
```

`myCar.rpm` is a published path to `myCar.engine.rpm`, and `myCar.start()` is a
generated behavior adapter. Neither flattens physical storage or lifecycle.
`engine` is still constructed and destroyed as one complete `MyEngine`; the
construction packet must initialize `.engine`, not the published `.rpm`.

#### `outer tracked` is a type capability

```zax
MyTrackedEngine :: type outer tracked {
  rpm : Integer
}

MyTrackedCar :: type {
  engine : MyTrackedEngine
}

recoverMyCar final : (
  result : MyTrackedCar & ?
)(
  engine : MyTrackedEngine &
) = {
  return engine tracked outer cast MyTrackedCar.engine
}
```

The current composition owner already uses `type outer tracked`, despite the
fixed initiating input listing its placement and spelling as unresolved. No
concrete pressure found in this reconstruction justifies changing that current
form.

The capability permits hidden root representation and lifecycle bookkeeping.
A standalone engine and an engine resident at `MyTrackedCar.engine` have
different placement relationships. Construction, copy, move, and replacement
establish destination-correct tracking; raw relocation cannot be assumed to
preserve it. The checked cast returns an optional immediate container and may
perform runtime work.

#### Untagged union lenses

```zax
MyRegister :: union {
  signedValue : I32
  unsignedValue : U32
}

myRegister : MyRegister
myRegister.signedValue = -1
print(myRegister.unsignedValue)
```

Candidate reading:

- the complete union storage begins zero-filled;
- both lenses begin at the same address;
- no discriminant records which lens was written;
- writing through one lens does not construct that lens or destroy another;
- both `I32` and `U32` accept every 32-bit pattern, so reading through either
  lens remains representation-valid; and
- the interpreted numeric values can differ.

`switch myRegister` cannot select a lens because no selection state exists.

The maintainer notes also permit jagged unions:

```zax
MyJaggedRegister :: union {
  lowByte : U8
  wholeWord : U32
}
```

This form is illustrative pending a rule. Both scalar domains accept every bit
pattern, but the current coercive-view contract requires equal logical width and
storage extent. A union-specific rule would have to say that `lowByte` covers
the first byte in the active environment's layout, that writing it preserves
the other three bytes, and that every resulting whole-storage state remains
valid for both lenses. Endianness then affects which part of `wholeWord` the
byte denotes.

#### Managed variant

```zax
MyMessage :: variant {
  number : U32
  text : String
  alternateText : String
  owner : MyPayload * unique
}

myMessage : MyMessage
if !myMessage
  print("no active alternative")

myMessage.text = "ready"

switch myMessage {
  case text { // illustrative variant-specific selection and binding
    print(text)
  }

  default {
    print("another alternative or absence")
  }
}

myMessage.number = 42
// The text path ended; number is now the active path.
```

Candidate reading from the maintainer notes:

- alternative identity is its declared name, so `text` and `alternateText`
  remain distinct despite both storing `String`;
- default construction produces an absent wrapper;
- `myMessage.text = "ready"` directly constructs the `text` alternative;
- selecting `number` destroys `text`, constructs `number`, and updates hidden
  selection state;
- selecting the already active name still reconstructs it rather than invoking
  ordinary in-lifetime assignment;
- `?myMessage` reports whether any alternative is active; and
- a variant's size and alignment must accommodate its largest alternative plus
  whatever hidden state its contract requires, without promising one layout.

The shown `case text` binding is not current `switch` behavior. It is retained
as maintainer evidence and as the main syntax/selection question for review.

### Candidate ordinary-type model

#### What a `type` definition contributes

`Name :: type { ... }` introduces a named identity, an incomplete self-name
during body resolution, and one declaration scope. The body can contribute
different kinds of declarations:

- Direct stored value members add per-instance places, stored shape, member
  lifetimes, construction work, and destruction work.
- A varying callable declared directly in the type adds a replaceable callable
  slot according to its bound/unbound and receiver-lifetime capacity.
- A `once varying` callable slot, when applicable, belongs to the complete type
  rather than each instance.
- A `final` function contributes one fixed implementation and no replaceable
  per-instance callable slot. A bound function receives an instance; an unbound
  function is type-owned but receiverless.
- A `once final bound` function contributes one type-callable implementation
  with both instance-qualified and receiverless Nothing-instance routes. It
  does not add per-instance storage.
- Nested types, aliases, reshapes, abstract roles, fulfillments, data routes,
  preferred routes, exposure metadata, operator declarations, and lifecycle
  declarations contribute definitions, behavior, metadata, or generated
  surfaces according to their owners; they are not ordinary resident members.
- `outer tracked` adds a hidden structural component and lifecycle work even
  though it is not a source-declared ordinary member.

This is not one flat “everything in braces is a member” model. Reflection must
eventually preserve these categories instead of reporting them all as stored
fields.

#### Identity and anonymous definitions

Two separately declared named types remain distinct even when their direct
stored shape is equal. Explicit structural conversion can cross a compatible
shape boundary; it does not erase identity.

An anonymous declaration such as:

```zax
myPair : :: type {
  left : Integer
  right : Integer
}
```

creates one stable compile-time anonymous identity for that declaration. Values
produced from that declaration reuse the identity. `type of myPair` may be used
to create a transparent alias. Repeating equal anonymous source elsewhere does
not by itself prove the same identity. Anonymous recursive syntax remains
unestablished.

#### Completion, size, and forwards

A named type's self-name resolves before its body is complete, but operations
requiring final size, layout, member set, generated operation set, or
initialization behavior remain pending.

`forward type` establishes the root and category for source-order or mutual
recursion. It supplies no body, layout, members, or initialization. Completion
must occur exactly once through the matching direct definition or exact alias.

Future partial definitions cannot be allowed to add stored members after layout
or compatible views have been finalized. If stored-member additions are ever
authorized, all pieces must merge into one order-independent complete shape
before construction, layout, compatibility, union admissibility, or variant
payload storage is checked.

#### Construction, replacement, and destruction

For ordinary direct members:

- automatic construction follows declaration order;
- explicit member construction can select another control-flow order;
- normal constructor completion requires every required member to be live or
  covered by explicit `unsafe ???` responsibility;
- enclosing destruction runs first while remaining members are live, then
  automatic member destruction follows reverse declaration order;
- complete replacement renews every direct member resident instance even when a
  custom replacement constructor retains its resources or bytes; and
- composition never turns a member lifecycle operation into the container's
  lifecycle operation.

Stored-member initializers affect construction, not completed stored shape.
Functions, defaults, and metadata do not become bytes merely because they occur
inside the type body.

#### Qualification and member paths

The container, each direct member, and every indirection layer retain their
resolved place, value, and access qualifications. A readonly container path
ordinarily produces readonly direct-member paths. Deep immutability follows
direct structural containment but does not silently rewrite pointer,
reference, optional, or variant payload qualifications.

`own` and data `via` create alternate paths to the same physical member place.
They create no new lifetime. `preferred` and `expose` do not add stored shape.

### Candidate union model

#### One value, several lenses, no active member

The candidate union is one resident union instance. A named lens is a
same-storage typed access path into that instance, not an independently
constructed resident member. This preserves the current principle that shared
bytes alone do not establish several independent object lifetimes while still
allowing several meaningful typed views.

Consequences:

- A union has no active-member tag, presence state, selected alternative, or
  selection exhaustiveness.
- A lens access does not start or end a member life path.
- A write through one lens changes the common representation observed through
  every other lens.
- A reference obtained through a safe lens remains tied to the union storage.
  A later write through another safe lens changes what it observes rather than
  rebinding it.
- The union itself still has a complete lifetime and qualification. Its lenses
  derive access authority from the union path and cannot increase it.
- Structural flattening stops at the union. The union is one semantic overlay
  leaf unless a future explicit bridge says otherwise.

This rejects the legacy model in which code manually invokes `+++` and `---` on
one active union member. It also supersedes the raw union candidate that allowed
manual member lifecycle under unsafe responsibility.

#### Candidate safe-union admission rule

A default `union` needs a mechanically checkable relation stronger than
“members have the same size.” A candidate rule is:

> Zero-filled backing is valid for every lens, and every write permitted through
> any lens leaves every other lens with a valid representation and preserves the
> union's complete qualification and lifetime contract.

For equal-extent scalars this resembles mutual safe writable coercion:

- `I32` and `U32` are suitable because every 32-bit pattern is valid under both
  exact formats.
- Exact `Binary32` and matching same-endian `U32` are also bidirectionally safe
  under current scalar owners. Binary16/32/64/128 define every unsigned bit
  pattern as a floating representation, including NaNs.
- The profile-selected `Float` identity is not simply `Binary32`; even on a
  target selecting Binary32 storage, they remain distinct identities. A
  `Float`/`U32` union therefore cannot be justified by the Binary32 fact without
  resolving `Float`'s selected exact representation and identity boundary.
- `X87Extended80` to an unsigned representation is only directionally safe; an
  arbitrary unsigned write can create an invalid extended-float encoding.
- A strict enum does not admit every backing representation, so an arbitrary
  backing lens paired with that enum is not a safe mutual lens set. A relaxed
  enum may be eligible when its exact backing and every other condition match.

Aggregates require the same reasoning across their complete representation,
including nested identities, hidden components, padding, custom invariants, and
lifecycle hooks. “Contains only integers” may be sufficient evidence for a
plain generated aggregate, but the language still needs an explicit
union-admissibility rule rather than inferring away type-owned invariants.

#### Jagged overlays need a distinct relation

The maintainer notes permit different lens extents and set union size to the
largest contained contiguous aligned type extent. All direct lenses begin at
offset zero; union alignment satisfies every lens.

That cannot reuse the current `as coercive layout T &` relation unchanged. The
current scalar coercion requires matching logical width and selected storage
extent. A jagged union additionally needs rules for:

- which bytes a smaller lens covers under concrete endianness;
- what happens to the untouched suffix after a smaller write;
- whether writes normalize non-value bits or padding;
- whether a larger lens may interpret bytes that are padding for the smaller
  lens;
- whether every reachable retained-suffix state remains valid;
- how alignment and zero-size lenses participate; and
- whether a reference to a smaller lens remains usable after a larger write.

The `U8`/`U32` example appears representation-safe if a smaller assignment
updates only the first byte and retains the other zero-filled or previously
valid bytes. It is nevertheless observably endian-sensitive. This appears to be
the intended low-level behavior, but it needs maintainer confirmation before
becoming the basis of the general rule.

#### Initialization and operations

The latest notes require default zero-fill of the complete union storage.
Unlike ordinary aggregate default construction, this cannot call a lens type's
constructor. Every admitted safe lens must therefore accept the resulting
representation without construction.

Still unresolved:

- whether source can directly initialize a union by naming one lens or must
  default-zero then write a lens;
- whether whole-union `copy` copies its backing representation, performs a
  generated union-specific operation, or is unavailable for some lens sets;
- whether `move` and `last` merely fall back to representation-preserving copy
  because safe union lenses own no transferable resources;
- how assignment through an aggregate lens treats padding and custom assignment
  behavior;
- whether unions can be constant-initialized from a representation; and
- what destruction operation, if any, the complete trivial union exposes.

No answer may silently reintroduce active-member construction or destruction.

#### Admissible and inadmissible lens families

The candidate consequences of the notes are:

- ordinary generated scalar and plain aggregate representations may be
  eligible when the mutual validity rule succeeds;
- managed pointers (`unique`, `strong`, `weak`, anchored forms, and their atomic
  forms) are ineligible because overwriting storage would bypass ownership,
  observation, or release behavior;
- references are ineligible because their fixed binding and referent lifetime
  cannot be treated as arbitrary overlay bits;
- raw pointers can appear only under a suitably unsafe relationship because an
  arbitrary overlay write does not prove a valid address, provenance,
  alignment, pointee lifetime, or destination Nothing representation;
- callable storage with bound receiver lifetime is ineligible;
- an unbound callable may be representable in `union unsafe` only if its
  unavailable representation, replacement, and reset behavior are explicitly
  reconciled; zero bytes are not assumed to mean unavailable;
- `String` and other allocation/lifetime-managing values are ineligible;
- arrays remain ineligible even with `Flat` storage because the array owns
  element lifetimes and possibly provider participation and unique backing
  state; contiguity is not trivial lifecycle; and
- `outer tracked` values are ineligible unless a future specific rule can
  establish correct placement metadata for every lens operation. Raw overlay
  writes cannot preserve it by assumption.

The language currently has no accepted “trivial” or “union-admissible”
capability that captures these constraints. This work must either define the
programmer-visible property or state a structural generated-operation test that
diagnostics can explain.

#### `union unsafe` and locality of responsibility

Illustrative source from the maintainer notes is:

```zax
MyUnsafeValue :: union unsafe {
  bits : U32
  code : MyStrictCode
}
```

This can hold a representation that is valid as `U32` but outside
`MyStrictCode`'s domain. The programmer may track informational meaning, but the
union stores no tag and the language does not know which interpretation is
currently justified.

The unresolved safety issue is whether `unsafe` on the declaration alone makes
every later lens read an unmarked unsafe operation. Current safety guidance says
unsafe responsibility should be narrow and local. A safer integration would
make `union unsafe` declare that unchecked lenses are available while requiring
the particular unproved read, write, reference escape, or handoff to carry its
applicable local unsafe responsibility. The declaration marker would classify
the type rather than grant ambient unsafe authority.

The maintainer notes do not settle that operation-site rule. It needs explicit
review.

### Candidate variant model

#### Wrapper and named alternatives

A `variant` is a distinct managed wrapper identity. Each declaration in its
alternative prologue introduces one alternative name and payload type. The
names, not just payload identities, distinguish alternatives:

```zax
MyTextChoice :: variant {
  primary : String
  secondary : String
}
```

`primary` and `secondary` remain separately selectable states.

The wrapper owns hidden selection state and storage suitable for its admitted
alternatives. The representation may use a discriminant, niche, or another
strategy, but it must preserve absence separately from every active alternative
if default absence is accepted. No current source promises exact size,
discriminant width, offset, ABI, or niche choice.

Composition stops at the wrapper. `own`, `preferred`, `expose`, and outer
casting apply to the declared variant value and cannot silently enter whichever
payload happens to be active.

#### Zero or one active path

The maintainer notes show:

```zax
myVariant : MyVariant

if ?myVariant {
  print("contains a value")
}
```

and only later assign an alternative. The candidate therefore treats default
construction as absence:

- the wrapper lifetime is live;
- no alternative path is live;
- `?myVariant` is false; and
- wrapper destruction has no payload to destroy.

After selecting an alternative, exactly one named payload path is live.
Changing selection ends that path and begins another. Reset syntax and whether a
named empty alternative exists remain open.

This directly conflicts with current lifetime and raw-variant wording that says
exactly one alternative is resident. If absence is accepted, those owners must
change to “zero or one,” and exhaustiveness must include absence.

#### Alternative construction and replacement

Maintainer source uses:

```zax
myVariant.owner = myOwner as last
myVariant.number = 42
myVariant.text = "ready"
myVariant.text = "replacement"
```

Candidate semantics:

1. evaluate and bind the right-hand source under its written transfer stance;
2. preserve any source values needed across the transition;
3. destroy the old active payload when present;
4. construct the named destination payload directly;
5. publish the new active name only after construction completes; and
6. leave every other alternative absent.

Selecting the already active `text` still ends and reconstructs `text`. It does
not invoke `String`'s ordinary in-lifetime assignment. This is a
variant-specific lifecycle operation spelled with member-like `=`.

That spelling conflicts with the current general rule that `=` operates on an
already live destination and with the optional owner's deliberate rejection of
`optional = payload`. The variant can still choose it, but the design must
classify it as a protected selection/construction form and make diagnostics
explain why `variant.alternative = source` is not ordinary member assignment.

Self- and interior-alias cases need the same care as optional packet
construction and reconstructive replacement. Evaluating a reference first does
not preserve the old payload after its lifetime ends.

A panic during the transition does not expose a partially active variant to
ordinary code or roll the old payload back. It leaves the lifecycle operation
blocked for the ordinary panic model and otherwise crashes gracefully.

#### Active access: direct panic versus required proof

The maintainer notes explicitly permit:

```zax
myString : String & = myVariant.text
// panics if `text` is not active
```

and:

```zax
myVariant.number += 1
// panics if `number` is not active unless analysis proves it
```

Current lifecycle guidance instead says access through conditionally live
storage requires proof and makes unproved optional access a semantic error.

Three coherent layers are possible, but only one should own the ordinary
spelling:

- proof-only access, matching optionals;
- checked direct access that panics on a name mismatch, matching the latest
  notes; or
- distinct proved and explicitly checked forms.

The initial candidate preserves the maintainer note's checked-panic meaning for
ordinary named access. Recognized switch selection or a preceding construction
can prove the active name and let the implementation omit the check. This is a
candidate, not alignment; accepting it creates an intentional difference from
optional postfix access that must be taught and integrated into safety,
lifetime, and panic documentation.

A reference produced from an active alternative remains tied to that exact
conditional life path. Selecting any alternative again, including the same
name, invalidates it:

```zax
myVariant.text = "first"
oldText : String readonly & = myVariant.text

myVariant.text = "second"
use(oldText) // error: the first text lifetime ended
```

#### Selection and binding

The notes use:

```zax
switch myVariant {
  case text {
    print(text)
  }
}
```

The intended operation appears to:

- test the retained variant's discriminant for alternative `text`;
- enter the body only while that exact payload path is live;
- introduce `text` as a body binding to the payload, preserving its
  qualifications and origin rather than copying it; and
- make direct access through that binding check-free.

Current `switch` cannot express this. `case text` currently means a direct
selector-relative Boolean operation when one applies, otherwise equality with
an expression named `text`; cases introduce no bindings. The raw pattern input
also requires future patterns to remain visibly distinct from those Boolean
cases.

Silently making `case text` type-directed variant syntax would create a source
stability hazard and a lexical question when an ordinary `text` expression is
also in scope. This work needs to decide whether:

- variant selection has a distinct case form;
- a complete switch over a variant enters a visibly declared
  alternative-selection mode; or
- the narrow binding requirement is accepted as the first generalized pattern
  form.

The initial reconstruction does not invent replacement punctuation. It retains
the note's source as illustrative evidence until that integration is reviewed.

The notes also prohibit `goto`, `continue`, and other transfers to another
variant case. That prohibition is semantically justified for direct body entry:
`goto` would bypass the discriminant test and could not create the payload
binding. Current `continue case_label:` reruns tests and could theoretically
establish a binding, but the notes still reject it. Exact treatment of bare
`continue`, labeled `continue`, `goto`, transfer-only cases, `next`, and
variant-switch restart must be stated rather than summarized as “no jumps.”

Pattern-bound bodies cannot be direct-entry targets unless every incoming path
establishes the same binding and active-path proof. The simplest candidate is
to make variant-alternative clauses ineligible for `goto` and for every
cross-clause transfer.

#### Exhaustiveness and absence

A closed variant declaration supplies a finite set of alternative names, but
default absence adds another state. Exhaustiveness therefore needs two
dimensions:

- every declared alternative name is handled; and
- absence has a selecting body or is proved unreachable at that switch.

If variant selection follows enum precedent, a `default` can cover current
runtime remainder without silently counting as explicit handling of newly added
alternatives. Adding an alternative would then be a visible source-compatibility
event for a member-complete switch.

The exact syntax for absence—`case !`, `default`, or another variant-specific
form—remains open. An unknown future alternative is not part of the current
closed identity unless open variants are later designed.

#### Qualification and transfer

The variant wrapper and active payload need separate qualification layers,
analogous to optional wrapper and boxed value:

- wrapper `mutable` plus writable access permits changing active state within
  one wrapper lifetime;
- wrapper `immutable` keeps absence/selection stable for that wrapper lifetime;
- a varying wrapper place may still receive a complete replacement wrapper
  under ordinary replacement authority;
- active access carries the selected payload's own resolved qualifications;
  and
- ending a payload through authorized wrapper mutation is not mutation through
  a readonly payload path.

Whole-variant `copy`, `deep`, `move`, and `last` should preserve absence or
apply the selected payload operation. A present payload can own strings,
managed pointers, arrays, callables, and other nontrivial values because the
variant owns real conditional lifecycle. Each operation needs to state source
state, failure, and whether changing selection is permitted through the current
wrapper path.

Default construction, whole-wrapper replacement, active-alternative
construction, in-place payload operations, and reset must remain distinct.

#### Recursion and nested wrappers

A direct variant alternative containing the complete variant identity creates
the same infinite-layout pressure as a directly recursive ordinary member:

```zax
MyRecursive :: variant {
  next : MyRecursive // expected error: no finite payload storage
}
```

Finite indirection is coherent:

```zax
MyRecursive :: variant {
  end : MyEnd
  next : MyRecursive * unique
}
```

A variant containing an optional, another variant, pointer, or comparable
wrapper retains each semantic boundary. Selection never flattens nested
absence, vacancy, or active-alternative state.

### Union and variant must not share one lifecycle story

The candidate distinction is:

- A union has one complete untagged representation and several lenses. Lens
  changes do not start or end member lifetimes.
- A variant has a managed wrapper and conditional payload paths. Selection
  changes run construction and destruction.
- A union cannot be switched by the language because there is no selection
  state.
- A variant can be selected because hidden state identifies one name or
  absence.
- A union excludes values requiring managed lifetime work.
- A variant exists specifically to contain such values safely.
- Equal storage address in a union means overlay; reused payload address in a
  variant does not preserve the old payload lifetime.

Using one keyword, one “active member” term, or one generated-operation model
for both would erase the deciding distinction.

### Conflicts and corrections discovered in the evidence

#### Legacy union behavior is superseded

The root legacy page presents unions as storage in which the programmer
constructs and destroys one selected member manually. That is the rejected
variant-like understanding identified by the maintainer notes. Useful legacy
evidence that survives is limited to shared starting storage, maximum
size/alignment pressure, low-level reinterpretation motivation, and the need to
make endian and layout consequences visible.

The examples that place `String`-owning aggregates in a union and call member
`+++`/`---` should not survive promotion as current union teaching.

#### Raw union manual-lifecycle allowance is superseded

The raw variants/unions input tentatively allowed either trivial views or
manual member lifecycle under unsafe responsibility. The maintainer notes reject
the latter: types stored in a union cannot construct, destruct, allocate, or
deallocate. The raw input's broader concern about aliasing, provenance,
alignment, initialization, and qualification remains useful.

#### Variant cardinality conflicts with current owners

Current lifetime and raw-variant text says exactly one active alternative.
Maintainer source shows default absence and `?variant`. The candidate uses zero
or one and flags the current text for eventual coordinated correction if the
maintainer confirms that reading.

#### Variant access conflicts with current proof-first safety

The current construction owner states the reusable rule that conditionally live
storage requires proof before access. The notes instead assign defined panic
behavior to an unproved alternative access. Accepting the notes requires either
narrowing that reusable rule or explicitly classifying variant named access as
a checked operation rather than ordinary conditional-path access.

#### Variant cases conflict with current Boolean cases

Current switch cases do not bind names. The notes require an alternative
binding and prohibit transfers that current switch otherwise supports. This is
a real selection-language extension, not documentation-only integration.

#### The float example needs exact types

The maintainer note asks whether a 32-bit float is only directionally coercive
with `U32`. Current scalar design answers:

- exact Binary16/32/64/128 formats accept every matching unsigned bit pattern
  and are safely bidirectional, including writable views;
- Extended80 has invalid unsigned patterns and is directional; and
- `Float` is a profile-selected identity, not a spelling for Binary32.

Union examples should therefore use exact scalar identities and should not teach
`Float`/`U32` as an inherently unsafe pair.

#### `outer tracked` spelling is already current

Current composition, structural-shape, lifetime, and terms owners consistently
use `Name :: type outer tracked { ... }`. Unless this work finds a concrete
cross-feature failure, reopening its placement only because the fixed input
listed it as unresolved would create unnecessary churn.

### Initial candidate holes

These were the holes identified before maintainer review. The aligned findings
and pre-dry-run status above resolve or disposition them; this list remains as
review history rather than the current open-question set.

#### Ordinary types

- Whether a dedicated ordinary-type owner should name a formal
  union-admissible/trivial property or leave that test structural and
  operation-based.
- Exact treatment of local anonymous identity in reflection and diagnostics.
- Which type-body declaration categories are permitted in unions and variants
  in addition to their lens/alternative prologues.
- Whether union and variant bodies transition from a declaration prologue to
  ordinary definition mode as enums do.
- Exact generated-operation sets for ordinary types after union/variant rules
  are integrated.

#### Unions

- Exact declaration grammar and whether `union unsafe` is the final ordering.
- The complete safe overlay-validity relation, especially for jagged extents,
  padding, unusual integer widths, enums, identities, hidden components, and
  user invariants.
- Whether safe union lens writes are limited to assignment/representation
  adoption or may invoke arbitrary type operations proved to preserve every
  lens.
- Whole-union construction, assignment, transfer, comparison, hashing,
  destruction, and reset.
- Whether a lens can escape by reference and which later writes preserve that
  reference's valid typed use.
- Local unsafe syntax and consequences for invalid reads, writes, and escaped
  references.
- Whether any raw pointer or unbound callable lens can be mechanically valid
  rather than merely permitted by `union unsafe`.
- Exact size/alignment and padding facts that are conceptual promises rather
  than future ABI contracts.
- Interaction with compile-time values and representation literals.

#### Variants

- Confirmation of default absence and zero-or-one cardinality.
- Exact declaration and alternative-selection syntax.
- Whether an alternative can declare a default and whether one alternative can
  be the default active state.
- Reset spelling and result.
- Whether ordinary direct alternative access panics, requires proof, or has two
  forms.
- The exact type and declaration stance of a case payload binding.
- Collision and shadowing behavior when an alternative name is already visible
  lexically.
- Transfer behavior for by-value, reference, pointer, and optional payloads.
- Alternative construction from construction packets and multi-result sources.
- Self/interior aliases during reselection.
- Generated `copy`, `deep`, `move`, `last`, assignment, replacement, and
  destruction families.
- Wrapper and payload qualification syntax.
- Exhaustiveness including absence, unreachable alternatives, and duplicate
  payload types.
- Which switch transfers remain available.
- Recursive alternatives, generic alternatives, and incomplete payload types.
- Layout/discriminant cost promises versus future implementation freedom.
- Concurrency and atomic replacement boundaries.

### Alignment status before the documentation-fit dry run

Maintainer review resolved the conceptual model for ordinary types, unions,
variants, `.=` contained reconstruction, variant-specific selection, safety
categories, and `outer tracked` integration.

No unresolved semantic contradiction blocks promotion. Promotion still has to
perform reader-facing refinement rather than transcribe this record:

- derive union eligibility from passive storage and all-bit-pattern validity
  without presenting a compiler algorithm;
- teach union factories, all-bit copy, unsafe proof loss, and diagnostics with
  focused examples;
- teach the three-way distinction among complete-wrapper `=`, contained
  reconstruction `.=` and ordinary payload assignment through existing access;
- layer variant presence, direct construction, reset, checked access, transfer,
  switch specialization, and lifecycle invalidation in dependency order;
- keep exact unsafe/panic control syntax deferred while preserving the aligned
  category names and consequences;
- preserve recursive, generic, reflection, partial, ABI/FFI, generalized
  pattern, and concurrency pressure in their live destinations; and
- verify the final owner split and complete integration through the required
  documentation-fit dry run.

### Adjacent findings captured for later work

These are candidate deferrals, not authorization to update their destination
files.

#### Generalized pattern matching

Live destination: `project/raw/pattern-matching.md`.

Variant selection creates concrete payload-binding pressure, but recursive
destructuring, guards, optional pattern flattening, structural patterns, and
value-producing selection can wait. Current work constrains that future design:
it must preserve active-path lifetimes, exact payload qualifications, and the
fact that direct body entry cannot manufacture a binding.

If a narrow variant alternative clause is accepted now, that behavior should be
promoted to variant and switch owners while the genuinely generalized remainder
stays in the raw pattern destination.

#### Generics and type parameters

Live destination: `project/raw/type-parameters-and-generics.md`.

Complete generic syntax and constraints can wait. Any future generic
instantiation must nevertheless produce one complete concrete type before size,
recursive layout, generated lifecycle operations, union admissibility, variant
maximum storage, or alternative exhaustiveness is decided. An open-ended
generic union cannot defer representation validity to runtime.

#### Partial types

Live destination: `project/raw/partial-types.md`.

External completion authority can wait. The current constraint is that no
partial may add stored members, union lenses, variant alternatives, hidden
tracking components, or lifecycle operations after the complete shape and
operation family has been used to validate layout, compatibility, construction,
or exhaustive selection. If such additions are ever authorized, all pieces
must close in one reproducible order-independent definition before those checks.

#### Reflection

Live destination: `project/raw/reflection.md`.

The future reflection schema must distinguish at least:

- physical stored members;
- fixed behavior and varying callable storage;
- type-owned `once` declarations;
- no-storage routes, published paths, roles, and fulfillments;
- union lenses from ordinary resident members;
- safe versus unsafe union classification and lens extents;
- variant alternatives, current value state, and declaration metadata;
- hidden variant selection state without promising its representation;
- `outer tracked` capability and placement state;
- declared, generated, defaulted, existing, bodyless, and forbidden lifecycle
  operations; and
- named, anonymous, alias, union, and variant identities.

Runtime active alternative is a value fact, not canonical variant type identity.
No exact reflection spelling or schema is decided here.

#### ABI and interoperability

Live destination: `project/raw/interop.md`.

Zax unions may resemble C/C++ offset-zero overlays, but ordinary size,
alignment, zero-fill, lens validity, and lifecycle rules do not establish C or
C++ layout, calling convention, active-member rules, aliasing, or ABI stability.
A foreign union or variant needs a named contract or adapter. Exact
profile-selected `Float`, enum, pointer, callable, and hidden discriminant or
tracking representations remain especially unsuitable for accidental ABI
claims.

#### Pointer provenance and raw representations

Live owner: `language/pointers-and-arenas.md`, with broader cast/provenance work
remaining in its stated future boundary.

A raw pointer lens owns no pointee lifetime. Reading it after unrelated overlay
writes requires address, provenance, alignment, target-lifetime, and
qualification facts that zero-fill and non-vacancy cannot prove. Managed
pointer roles remain excluded from unions because their ownership accounting is
not representationally trivial.

#### Callable representation

Live owner: `language/lambdas-and-callable-composition.md`.

An unbound callable has no receiver lifetime, but it still has an unavailable
state, installed implementation, transfer, replacement, and reset behavior.
It is not union-safe merely because it is receiverless. Bound borrowed and
managed receiver modes add direct lifetime reasons for exclusion.

#### Array storage

Live owner: `language/arrays-and-slices.md`.

`Flat` guarantees contiguous element storage, not absence of array or element
lifecycle. Arrays remain excluded from union lenses under the maintainer notes.
A future raw byte-block or fixed representation type would be a separate
low-level facility, not an array exception inferred from contiguity.

### Likely lasting ownership boundaries

This is an early ownership hypothesis, not a documentation-fit dry run.

- A cohesive ordinary type-definition owner should teach identity, body
  categories, stored shape, self-reference, forwards, anonymous identity, and
  the handoffs to lifecycle, qualifiers, composition, and structure.
- Union teaching needs enough independent depth for lens validity, jagged
  overlay behavior, unsafe locality, admissibility, representation cost, and
  diagnostics. It may warrant a specialized owner linked from the ordinary type
  owner.
- Variant teaching needs enough independent depth for alternatives, wrapper and
  payload lifetimes, selection, binding, transfer, replacement, exhaustiveness,
  and cost. It may warrant another specialized owner.
- Declarations should continue to own introduction syntax and body declaration
  categories rather than duplicating complete union/variant semantics.
- Construction should own shared lifecycle mechanics and state transitions;
  variant owners should apply them locally. Union owners should state explicitly
  that lenses do not use member lifecycle.
- Lifetimes should own conditional paths and reference invalidation.
- Qualifiers should own wrapper/payload and access propagation.
- Transfer should own general stance meaning, with variant forwarding and union
  restrictions applied locally.
- Composition should retain the semantic-indirection boundary and
  `outer tracked`.
- Structural shape should treat unions and variants as semantic leaves and own
  their explicit compatibility relationships.
- Switch should own the final clause mechanics, proof flow, transfer
  eligibility, and exhaustiveness integration for any accepted variant
  selection form.
- Safety should own the reusable distinction among proved access, checked panic,
  local unsafe assertion/permission, and known-invalid use.
- Terms should eventually gain concise union-lens, variant-wrapper,
  alternative-path, and active-alternative terminology only after those terms
  are aligned.

Promotion would also need to consume useful value from the root legacy
`type-definition.md`, retire or narrow the reached raw union/variant input,
disposition the maintainer notes completely, and update every current owner
whose present statement conflicts with the accepted model. No such promotion is
authorized by this reconstruction.

## Dispositions and promotion dry run

### Aligned disposition summary

The maintainer aligned:

- the ordinary type-definition mental model and body storage categories;
- incomplete self-names, forwards, recursion, anonymous identity, construction,
  member order, qualifications, composition, and `outer tracked` integration;
- unmanaged unions as zero-filled untagged offset-zero overlays rather than
  active-member storage;
- union admissibility from passive storage and plain-union safety from
  all-bit-pattern-valid lenses;
- intentional jagged overlays, maximum extent/alignment, concrete-endian
  interpretation, all-bit whole copy, `move`/`last` fallback, unavailable
  `deep`, and no union lifecycle hooks;
- `union unsafe`, flow-sensitive lens validity, and
  `union-lens-validity`;
- managed variants with zero or one active named payload, absent default
  construction, named selection, reset, direct checked access, payload
  lifetime invalidation, wrapper transfer, and protected lifecycle;
- protected, non-overloadable `.=` as contained reconstruction for optional
  payloads and variant payload/selection state;
- variant-specific switch routing, reference-shaped payload bindings,
  polymorphic `bind`, presence/absence cases, coverage, control-flow entry, and
  `partial-variant-selection`;
- `inactive-variant-access` as the independently controllable checked panic;
- variant body prologue/definition-mode behavior and prohibition of custom
  wrapper lifecycle hooks; and
- partial, generic, reflection, pattern, interoperability, safety-control, and
  implementation constraints and deferrals.

No aligned finding depends on compiler lowering, one runtime representation, a
formal ABI, or a formal specification.

### Evidence disposition

- The maintainer notes are completely represented by the aligned findings:
  ordinary type evidence is retained; legacy union active-member behavior is
  rejected; jagged overlay, zero-fill, admissibility, raw-pointer/callable
  boundaries, variant presence, selection, transfer, and switch behavior are
  retained with the reviewed refinements.
- Useful ordinary type-definition evidence from root
  `type-definition.md` moves to the new ordinary type owner and existing
  declaration/construction owners.
- The root page's active-member union examples, manual lens `+++`/`---`, and
  nontrivial `String`-owning union payloads are superseded and must not remain
  public teaching.
- Its function-type material is already owned by declarations, invocation, and
  callable composition; no unique function-type behavior remains there.
- `project/raw/variants-and-unions.md` is consumed by the new union and variant
  owners plus lifecycle, switch, qualifier, transfer, composition, structure,
  and safety integration.
- The general recursive-pattern, guard, and structural-binding remainder stays
  in `project/raw/pattern-matching.md`; accepted variant routing is removed from
  that future obligation.
- Generic, partial, reflection, and interoperability consequences remain in
  their indexed raw owners with the constraints recorded below.
- The staged maintainer-note file is not modified or unstaged during promotion.
  It remains provenance until closure disposition preserves the staged review
  boundary.

### Ownership map

The lasting teaching structure retains the flat `language/` directory and adds
three cohesive owners:

1. `language/type-definitions.md`
   - owns the ordinary type mental model, identity, body categories, direct
     stored shape, self-reference, forwards, anonymous definitions, lifecycle
     handoffs, composition boundaries, `outer tracked` integration, and routes
     to unions and variants;
   - does not duplicate complete declaration, lifecycle, qualifier,
     composition, compatibility, union, or variant rules.
2. `language/unions.md`
   - owns union lenses, passive storage, all-bit-pattern validity,
     plain/unsafe admission, zero-fill, jagged overlap, padding, alignment,
     bit-copy transfer, lens proof, costs, diagnostics, and body restrictions.
3. `language/variants.md`
   - owns variant identity, absence/presence, alternatives, packets, `.=` and
     reset application, checked access, wrapper/payload qualifications and
     transfer, references, recursive pressure, body restrictions, and the
     handoff to variant switch routing.

Existing owners retain:

- declaration forms and body declaration categories:
  `language/declarations-and-bindings.md`;
- shared construction packets, operation order, aliasing, panic, and lifecycle
  machinery: `language/construction-and-destruction.md`;
- conditional life paths and reference invalidation:
  `language/lifetimes-and-references.md`;
- wrapper, payload, lens, place, value, and access qualification:
  `language/qualifiers.md`;
- general `copy`/`deep`/`move`/`last` meaning:
  `language/transfer-stances.md`;
- composition's semantic-indirection and `outer tracked` rules:
  `language/composition.md`;
- structural leaves and compatibility boundaries:
  `language/structural-shapes-and-compatibility.md`;
- optional-specific `.=` and reset behavior:
  `language/optional-values.md`;
- exact variant case mechanics, flow entry, and coverage:
  `language/switch.md`;
- proof, unsafe, panic-disablement, and compiler-analysis stability:
  `language/safety-and-analysis.md`;
- exact `.=` catalog, precedence, association, and protection:
  `language/operator-catalog.md`;
- tokenization, presentation, packet, and variant-clause layout:
  `language/source-structure.md`;
- `partial-variant-selection` registry behavior:
  `language/intent-acknowledgements.md`; and
- concise cross-cutting vocabulary: `language/terms.md`.

### Human reading path

The public `index.md` routes ordinary readers first to type definitions, then to
specialized unions or variants when those concepts are needed. Each new owner
opens with representative source and a plain-language outcome before terms,
rules, diagnostics, costs, and reference detail.

Direct-entry readers receive:

- enough ordinary declaration/lifecycle context in the type owner to understand
  one type definition without reconstructing declarations and construction;
- enough bit-pattern and lifecycle context in the union owner to distinguish a
  safe overlay from `union unsafe` before seeing edge cases;
- enough absence/selection/lifetime context in the variant owner to use packets,
  `.=` and `switch` before transfer and control-flow detail; and
- focused links to complete foreign mechanisms rather than duplicate
  definitions.

The root `type-definition.md` legacy content is replaced by a concise migration
router while active work still links to that live path. It preserves no
duplicate teaching. Closure can retire the route after the working record is
archived and live-path impact is reviewed.

### Deferred destinations and activation pressure

- `project/raw/pattern-matching.md`: recursive destructuring, guards,
  structural patterns, optional flattening, and general pattern-aware
  value selection; activate when behavior beyond finite variant routing is
  required.
- `project/raw/type-parameters-and-generics.md`: generic union/variant
  formation and constraints; every specialization must close before union
  admissibility, storage, recursion, lifecycle, or variant coverage is checked.
- `project/raw/partial-types.md`: all shape- and lifecycle-affecting pieces must
  merge before completion; activate when external/open completion authority is
  designed.
- `project/raw/reflection.md`: distinguish stored members, union lenses,
  variant alternatives and value state, generated/protected operations, hidden
  discriminant/tracking facts, and source presentation without promising one
  layout.
- `project/raw/interop.md`: foreign union/variant layout, tags, calling
  conventions, pointer/function representations, and ABI stability require a
  named contract or adapter.
- `project/raw/analysis-controls.md` and
  `project/raw/analysis-control-registry.md`: final unsafe/panic control syntax,
  registry ownership, selected-contract proof sets, and extension provenance.
- Existing pointer, callable, array, scalar, concurrency, and compiler owners
  retain their stated future boundaries; no implementation mapping enters
  language documentation.

Every material deferral therefore has a live owner, activation pressure, and a
constraint on current work.

### Exact promotion change set

Create:

- `language/type-definitions.md`;
- `language/unions.md`;
- `language/variants.md`.

Rewrite or integrate current/public owners:

- `index.md`;
- `type-definition.md`;
- `language/declarations-and-bindings.md`;
- `language/construction-and-destruction.md`;
- `language/lifetimes-and-references.md`;
- `language/qualifiers.md`;
- `language/transfer-stances.md`;
- `language/composition.md`;
- `language/structural-shapes-and-compatibility.md`;
- `language/optional-values.md`;
- `language/switch.md`;
- `language/safety-and-analysis.md`;
- `language/operator-catalog.md`;
- `language/source-structure.md`;
- `language/intent-acknowledgements.md`;
- `language/terms.md`.

Update reached live raw destinations:

- `project/raw/variants-and-unions.md`;
- `project/raw/pattern-matching.md`;
- `project/raw/type-parameters-and-generics.md`;
- `project/raw/partial-types.md`;
- `project/raw/reflection.md`;
- `project/raw/interop.md`;
- `project/raw/analysis-control-registry.md`;
- `project/raw/README.md`.

Update this work record with the promotion result and any material issue found
during validation. Do not edit, stage, or unstage the staged maintainer-note
file. Do not archive work item `028`, create work item `029`, or change
`project/README.md` during this promotion.

### Dry-run result: PASS

**PASS.**

The aligned findings have one coherent owner structure, a cold-reader path,
live deferred destinations, explicit legacy/raw disposition, and an exact
promotion set. No unresolved semantic contradiction, orphaned finding, duplicate
authority, or missing owner blocks promotion.

The structure remains minimal: three concept owners are added to the existing
flat `language/` layout, no speculative directory family or specification area
is created, and every cross-cutting edit is limited to behavior the affected
owner must teach. Passing this dry run does not itself authorize unrelated
changes; the maintainer's conditional authorization permits the exact promotion
set above.

Promotion review exposed one declaration integration question after the initial
PASS: category-specific forwards and anonymous union/variant declarations. The
maintainer aligned `forward union`, `forward variant`, `:: union { ... }`, and
`:: variant { ... }`, including exact-alias category preservation and no
anonymous recursive self-syntax. The issue changes no owner or file in the exact
promotion set. The dry-run result remains **PASS**.

A later integration audit found that `language/operators.md`, not only the exact
operator catalog, owns the shared protected `?` presence mental model. The
maintainer explicitly authorized adding that file to the promotion set so
variant presence does not remain orphaned. This is an owner-integration
correction, not a new semantic decision or structural change. The dry-run result
remains **PASS**.

### Promotion result

The authorized promotion completed:

- `language/type-definitions.md`, `language/unions.md`, and
  `language/variants.md` now provide the cold-reader teaching path;
- declarations, lifecycle, lifetime, qualifier, transfer, composition,
  structural, optional, switch, safety, operator, source, intent, and
  terminology owners contain their required local integration;
- `index.md` routes readers to the new owners;
- root `type-definition.md` is now a migration router rather than duplicate
  legacy teaching;
- reached pattern, generic, partial, reflection, interoperability, and
  analysis-control inputs preserve only their remaining future pressure; and
- the reached variants/unions raw input is marked consumed pending closure
  retirement.

Promotion review also aligned category-specific forwards and anonymous
union/variant declarations and added `language/operators.md` to the integration
set. Both revisions are recorded above and leave the dry-run structure
coherent.

Validation confirmed:

- whitespace-clean diffs;
- balanced fenced examples in all three new owners;
- resolving local Markdown paths and anchors throughout every changed live
  Markdown file;
- a separate cold-reader review whose material findings were repaired:
  opening variant switches now satisfy their own coverage/reachability rules,
  early examples explain implicit case bindings, type-owned `once` storage is
  classified, the public route includes optional prerequisites, and current
  owners do not route readers into raw interoperability input;
- no live current-owner statement retaining exactly-one variant cardinality,
  future active-member union transitions, or the superseded existing-optional
  `= [{...}]` reconstruction spelling; and
- preservation of the staged maintainer-note boundary.

This result does not authorize closure, archival, raw-note retirement, staging,
commit, push, or creation of work item `029`.

### Post-promotion aligned refinement: complete reconstruction uses `.=`

The maintainer's review of the promoted material exposed and aligned a clearer
general role for `.=`. This finding supersedes the earlier promoted use of
compiler-generated reconstructive `=` but is intentionally not yet promoted
while the maintainer completes the broader documentation review.

#### Source operation versus lifecycle hook

Two concerns remain separate:

- protected `.=` is the source operation requesting complete reconstructive
  replacement; and
- `replacement +++` remains the type-owned lifecycle hook selected inside the
  compiler-owned replacement skeleton.

An ordinary `operator binary '='` body never gains construction, destruction, or
replacement authority merely from its token or receiver qualifications. It
operates within the current resident lifetime:

```zax
MyType :: type {
  value : Integer

  operator binary '=' final : ()(
    newValue : MyType
  ) immutable writable varying = {
    _.value = 5 // error: the current instance is immutable
    audit(newValue) // legal external effect
  }

  replacement +++ final : ()(
    newValue : MyType
  ) = {
    // Transitional construction authority establishes the successor.
    _.value = newValue.value
  }
}
```

At the use:

```zax
value varying :
  MyType immutable writable varying

value = replacement
// Ordinary operator selection; cannot replace the resident lifetime.

value .= replacement
// Compiler-owned complete replacement; may select replacement +++.
```

#### Availability for mutable and immutable values

Complete reconstruction is not inherently an immutable-only operation. Any
ordinary concrete type can offer it when:

- the destination place is type-side `varying`;
- this declaration has varying replacement permission;
- the access path is writable;
- same-storage size/alignment remains suitable;
- replacement is not forbidden; and
- a matching `replacement +++` or ordinary destruction/construction fallback
  is viable.

Value mutability decides whether ordinary in-lifetime mutation is also
available:

```zax
mutableValue varying :
  MyType mutable writable varying

mutableValue = source
// Ordinary assignment; current resident lifetime continues.

mutableValue .= source
// Explicit complete reconstruction; successor lifetime begins.
```

For an immutable value, `.=` is the only ordinary operation that changes the
represented value in the same place:

```zax
immutableValue varying :
  MyType immutable writable varying

immutableValue = source
// Ordinary `=` has no lifecycle authority.

immutableValue .= source
// Complete reconstructive replacement.
```

`readonly` removes change permission, declaration-side `final` removes this
path's replacement permission, and type-side `final` forbids a successor
resident lifetime. None can use complete `.=` reconstruction:

```zax
stable final :
  MyType immutable writable final

stable .= source // error: final place

reader :
  MyType immutable readonly varying &

reader .= source // error: readonly path
```

#### Replacement packets and fallback

`.=` can re-deliver a full positional/named constructor-input packet:

```zax
value .= [{
  firstInput,
  mode: selectedMode
}]
```

The compiler:

1. evaluates and binds every packet input;
2. selects a matching `replacement +++`;
3. lets that hook recycle the previous representation and resources in the
   continuing storage; or
4. falls back to old `---` followed by ordinary `+++` using the same inputs.

Direct call-site `.member = ...` entries are initially unavailable for complete
replacement packets. They would compete with the replacement hook's authority
to carry, renew, or reconstruct old members. Positional and named constructor
parameters remain available.

Existing replacement-result behavior remains: `.=` forwards results explicitly
declared by the selected `replacement +++`; a resultless hook or generated
fallback produces no implicit destination result.

#### Nested optional and variant payloads

Postfix optional access naturally selects complete replacement of an already
present payload:

```zax
if ?myOptional
  myOptional. .= [{ newValue }]
```

This differs from wrapper-owned payload reconstruction:

```zax
myOptional .= [{ newValue }]
```

The first requires a present payload and targets that payload type's
`replacement +++`; the second ends optional contained state and performs fresh
payload construction under the wrapper.

The concise variant alternative form remains wrapper-owned reselection:

```zax
myVariant.myReplaceable .= [{ newValue }]
// Select/reselect the alternative and perform fresh payload construction.
```

To invoke the active payload type's complete replacement, first establish the
payload binding:

```zax
switch myVariant {
  case myReplaceable {
    myReplaceable .= [{ newValue }]
    // Binding targets the active payload place; may select replacement +++.
  }
}
```

An explicit writable varying payload reference provides the same distinction.
No extra trailing-dot variant syntax is introduced.

#### Required later promotion integration

When separately authorized after the maintainer's current review, this
refinement must replace reconstructive `=` with complete reconstructive `.=` in
the construction, qualifier, declaration, operator, lifetime, structural,
array, mixfix, transfer, optional, variant, source, and terminology
owners. Examples must lead with the immutable ordinary `=` failure above,
followed by successful `.=` and then the mutable choice between assignment and
reconstruction.

The owner structure remains coherent, but the recorded promotion result is now
known to require this follow-up before work item `028` can close.

### Final maintainer review alignment

The maintainer completed review and aligned these remaining refinements:

- Type-side `final`, declaration-side `final`, and `readonly` are barriers to
  complete replacement and therefore to selecting `replacement +++` at that
  layer.
- A final mutable wrapper can still use wrapper-owned contained `.=` because
  the wrapper lifetime continues; that operation performs fresh payload
  construction and does not select the wrapper or old payload's
  `replacement +++`.
- Postfix optional payload access or a variant case/reference binding can target
  the contained value's complete `.=` reconstruction. That contained place must
  itself be varying and reached through writable access.
- Same-type complete optional reconstruction uses `.=` with an optional source;
  payload-source `.=` remains wrapper-owned contained reconstruction. Resolved
  optional depth distinguishes the target.
- Optional markers attach to the completed inner type without preceding
  whitespace. Canonical source uses `MyType immutable readonly final? mutable
  writable varying`; nested layers remain `MyType? ?`.
- Exact aliases preserve specialized categories:
  `alias union ExistingUnion` and `alias variant ExistingVariant`, including
  `alias union type of value` and `alias variant type of value`.
- A variant alternative is not an `anchor` target. A physical member containing
  a complete variant can be anchored only as the atomic wrapper value.
- Reset teaching shows plain `reset value` before its returned wrapper access or
  chained reconstruction.
- General future-property ordering commentary around `type outer tracked` is
  unnecessary and is removed.
- Defunct root `type-definition.md` and consumed
  `project/raw/variants-and-unions.md` are deleted; Git history retains their
  provenance, and all live routes/references are updated.
- The cohesive lifecycle owner remains
  `language/construction-and-destruction.md`; replacement teaching is
  substantially reorganized there rather than split into another document.

### Follow-up documentation-fit dry run

The owner structure remains unchanged. Complete reconstructive replacement and
contained wrapper reconstruction share the existing lifecycle owner but receive
separate teaching sections and examples.

The follow-up promotion updates:

- `language/construction-and-destruction.md`;
- `language/qualifiers.md`;
- `language/declarations-and-bindings.md`;
- `language/operator-catalog.md`;
- `language/operators.md`;
- `language/terms.md`;
- `language/lifetimes-and-references.md`;
- `language/structural-shapes-and-compatibility.md`;
- `language/arrays-and-slices.md`;
- `language/mixfix-operators.md`;
- `language/transfer-stances.md`;
- `language/optional-values.md`;
- `language/variants.md`;
- `language/source-structure.md`;
- `language/type-definitions.md`;
- `language/unions.md`;
- `index.md`;
- `project/raw/README.md`;
- `project/raw/reflection.md`; and
- this work record.

It deletes:

- `type-definition.md`; and
- `project/raw/variants-and-unions.md`.

No new owner, directory, raw placeholder, specification surface, or
implementation mapping is required. Existing generalized pattern, generic,
partial, reflection, interoperability, and analysis-control deferrals remain
valid.

**PASS.**

The follow-up has a coherent owner and reading path, resolves the known
post-promotion mismatch, removes defunct routes, and introduces no orphaned
finding or duplicate authority. The maintainer explicitly authorized promotion
after this review.

### Follow-up promotion result

The authorized follow-up promotion completed:

- protected complete `.=` replacement now applies to mutable or immutable
  writable varying residents;
- ordinary `=` retains in-lifetime authority and never silently selects
  `replacement +++`;
- replacement packets carry positional/named constructor inputs while direct
  member entries remain unavailable;
- type-side/declaration-side `final` and readonly access are explicit barriers
  at the targeted replacement layer;
- optional and variant owners distinguish same-wrapper assignment,
  complete-wrapper reconstruction, wrapper-owned payload reconstruction, and
  complete active-payload reconstruction;
- the variant case-binding example demonstrates how to invoke a payload type's
  `replacement +++` without adding trailing-dot syntax;
- canonical optional spacing, category-specific aliases, plain reset teaching,
  and variant anchor boundaries are integrated;
- defunct `type-definition.md` and
  `project/raw/variants-and-unions.md` were deleted with their live routes
  removed; and
- the cohesive construction/replacement/destruction owner was retained and
  reorganized rather than split.

A second cold-reader audit found five stale or ambiguous integration points in
operator reset, reconstruction diagnostics, mutable successor wording, optional
boxed assignment, and operator-catalog classification. All were corrected.

Validation again confirmed balanced examples, resolving live Markdown paths and
anchors, whitespace-clean staged and unstaged diffs, and no live current-owner
wording that lets ordinary `=` select reconstructive replacement.

At that promotion point, closure, archival, staging, commit, push, and creation
of work item `029` still required separate authorization.

### Closure and continuation

Work item `028` is complete:

- every aligned type, union, variant, replacement, selection, qualification,
  safety, syntax, and documentation finding was promoted to its current owner;
- every generalized pattern, generic, partial, reflection, interoperability,
  analysis-control, concurrency, and implementation consequence has a live
  owner and activation pressure;
- exhausted legacy and raw type/union/variant files were removed;
- the maintainer-note input was completely dispositioned and is retired at
  closure; and
- validation and two cold-reader reviews found no remaining blocker.

The next work item is `029: except`. Its initiating input distinguishes
function-result flow controls that intentionally follow an exceptional result
path from exceptions, catch/throw mechanisms, panic, and stack unwinding.
Maintainer-refreshed notes are the preferred primary input; legacy `except.md`
remains useful evidence rather than authority.

This archived record owns no current language meaning. Work item `029` must
begin in a separately assigned session or explicit reassignment and was not
analyzed during this closure.
