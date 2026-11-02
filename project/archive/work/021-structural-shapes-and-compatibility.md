# 021: Structural shapes and compatibility

| Field | Value |
| --- | --- |
| Status | Historical / non-normative / audit-only / closed |
| Work Item | `021` |
| Created | 2026-09-12 |
| Owns | The bounded review defined by the fixed initiating input |
| Does Not Own | Complete generics, reflection, partial types, general casting, ABI/FFI, inheritance, runtime dispatch, or compiler lowering |

## Non-authority notice

This file is a collaborative working record. Existing statements, new
observations, candidate wording, and aligned findings remain non-authoritative
until a separately discussed, aligned, and explicitly authorized promotion
incorporates them into their lasting owners.

## Fixed initiating input

This section records the information aligned when work item `021` was created.
It is intentionally incomplete and must not be rewritten as work develops.

### Initiating concern

Establish a coherent programmer-facing model for structural shape and
compatibility without collapsing distinct type identity, storage layout,
conversion authority, or whole-type conformance into one relation.

The language maintainer is preparing
[structural-shape and compatibility notes](../raw/structural-shapes-and-compatibility-maintainer-notes.md)
as the intended primary input for this work. The notes may be revised before
`021` is assigned. At assignment, treat their complete contents as maintainer
input to understand and test, not as automatically accepted language design.
That assignment freezes the initiating version; later clarifications belong in
the working record rather than silently changing the anchor.

The existing [raw structural-typing proposal](../raw/structural-typing.md) is
supplemental candidate evidence. Its agent-authored recommendations and syntax
have never been accepted and must not override the maintainer's refreshed
input.

### Motivating pressure

Current design already depends on several structural facts without one accepted
model connecting them:

- named aggregate types can have similar stored members while retaining
  distinct identities;
- endian and identity behavior distinguish semantic type identity from storage
  or shape compatibility;
- construction packets are not anonymous structural values;
- arithmetic operators produce anonymous report values;
- composition distinguishes stored declarations from published paths, routes,
  generated declarations, and fulfillments;
- `outer tracked` may add representation and lifecycle pressure;
- directional projection or truncating transfer must not silently weaken an
  exact symmetric relation; and
- future generic constraints, whole-type contracts, reflection, and partial
  types all need a stable meaning for structural shape.

If these concerns are left unresolved, each later feature may invent an
incompatible meaning for “same shape.”

### Known assumptions

- Type identity, structural shape, storage/layout compatibility, conversion or
  projection permission, and whole-type conformance are potentially distinct
  relations until this review establishes otherwise.
- Structural similarity does not introduce class inheritance, hidden base
  objects, vtables, or implicit runtime dispatch.
- Current identity-type behavior remains owned by
  `language/identity-types.md`; this work must not erase an accepted distinct
  identity boundary accidentally.
- Current composition behavior remains owned by `language/composition.md`.
  Published paths remain access paths to existing places, and generated or
  fulfilled declarations remain distinguishable from physical stored
  declarations.
- Current construction packets remain constructor/member input mappings rather
  than anonymous structural values.
- Implementation feasibility and ABI pressure may test the model, but compiler
  lowering and backend representation do not belong in promoted language
  teaching.
- No compiler implementation exists in this repository.

### Known inclusions

- What contributes to the structural shape of a completed value type.
- Whether stored-member names, declaration order, complete types,
  qualifications, defaults, lifecycle declarations, and other attributes
  participate.
- Exact structural equivalence between distinct named type identities.
- Recursive and anonymous structural values, including arithmetic-report
  results.
- The distinction among semantic shape, exact storage compatibility, layout
  compatibility, and ABI compatibility.
- Whether equal shape grants any assignment, binding, conversion, reference
  view, or substitution behavior.
- Exact shape versus directional width/prefix projection and truncating
  transfer.
- The structural effect, if any, of composition publication, data routes,
  preferred projection, generated exposure/adoption, fulfillments, fences, and
  `outer tracked` metadata.
- Pressure for a whole-type no-storage contract and its relationship to
  member-level `abstract` roles.
- Qualification, identity, lifetime, transfer, source-evolution, diagnostic,
  reflection, and generic consequences at the depth necessary to keep the
  structural model coherent.
- Lasting documentation ownership and complete disposition of both structural
  raw inputs.

### Known starting boundaries

- Complete generic declaration, inference, specialization, and constraint
  syntax.
- A complete reflection API or metadata representation.
- Partial/open type authority and merge mechanics.
- General conversion/casting syntax beyond direct structural consequences.
- ABI, FFI, packing, alignment-control syntax, or platform calling convention.
- Class inheritance, nominal subtyping hierarchies, virtual dispatch, or
  runtime interface objects.
- Complete pattern matching or destructuring syntax.
- Compiler data structures, lowering, hashing, canonicalization, or optimizer
  implementation.

These boundaries do not erase consequences. Preserve every material pressure in
a live owner or indexed raw destination rather than leaving it only in the
eventual archive of this work item.

### Intentionally unresolved framing

- Whether two named types can be structurally equivalent while retaining
  distinct identities.
- Whether member names and declaration order participate in exact shape.
- Which qualifier and declaration attributes are shape-relevant.
- Whether exact shape is merely descriptive or grants any operation.
- Whether storage/layout compatibility is independent from semantic shape.
- Whether extra-member relationships exist and, if so, whether they are
  explicit projection, same-storage views, conversion, or truncating transfer.
- How anonymous structural values acquire and compare type identity.
- Whether a whole-type no-storage contract is part of structural design and how
  a type demonstrates conformance.
- Which composition surface categories participate in shape, conformance, or
  reflection without becoming duplicate storage.

### Initial stopping guidance

Stop when the work has:

- established a coherent mental model and vocabulary for identity, shape,
  storage/layout compatibility, operation permission, and conformance;
- decided the shape contribution of relevant declaration and composition
  categories;
- explained exact and directional relationships with representative valid and
  invalid source;
- dispositioned anonymous structural values and whole-type contract pressure;
- traced material qualifier, lifetime, transfer, construction, identity,
  generic, reflection, partial-type, and source-evolution consequences;
- assigned every deferred consequence a live owner or indexed raw destination;
- identified exact lasting owners and raw/legacy dispositions; and
- completed the required documentation-fit dry run.

Do not promote findings, archive this work item, begin work item `022`, or
design complete generics, reflection, partial types, general casting, ABI, or
pattern matching without the separately required discussion, alignment, and
authorization.

## Reading scope

### Required

- [Documentation architecture](../documentation.md) - governs focused reading,
  decision maturity, promotion, deferral, and closure.
- [Structural-shape and compatibility maintainer notes](../raw/structural-shapes-and-compatibility-maintainer-notes.md)
  - supplies the maintainer's intended primary questions, examples, and design
  input once preparation is complete.
- [Raw structural typing](../raw/structural-typing.md) - supplies the
  non-authoritative candidate proposal and accumulated compatibility pressure
  that this work must disposition.
- [Language vision](../../language/vision.md) - supplies Zax's data-oriented,
  explicit-cost, composition-over-inheritance, and structural-relationship
  direction.
- [Declarations and bindings](../../language/declarations-and-bindings.md) -
  owns stored declarations, complete declaration categories, qualifiers, and
  initialization framing whose structural participation must be decided.
- [Identity types](../../language/identity-types.md) - owns transparent aliases,
  distinct identities, underlying relationships, admission, projection, and
  current same-storage pressure.
- [Composition](../../language/composition.md) - owns the distinctions among
  physical storage, published paths, preferred routes, generated declarations,
  fulfillments, and outer-tracking capability.
- [Qualifiers](../../language/qualifiers.md) - owns place, value, and access
  qualification axes whose role in shape and compatibility must be tested.
- [Construction and destruction](../../language/construction-and-destruction.md)
  - owns physical member construction and the construction-packet boundary.
- [Endianness](../../language/endianness.md) - provides a current concrete use
  of storage- and shape-compatibility language without equating compatible
  types.

### Consequence-driven

- [Integer operator catalog](../../language/integer-operator-catalog.md) when
  anonymous arithmetic-report type identity or compatibility is evaluated.
- [Function invocation](../../language/function-invocation.md) when one
  structural value must be distinguished from several result slots or expected
  type creates binding pressure.
- [Lifetimes and references](../../language/lifetimes-and-references.md) when
  same-storage reference views, aliasing, or place origin become material.
- [Transfer stances](../../language/transfer-stances.md) when structural
  projection may copy, move, consume, or discard source members.
- [Operators](../../language/operators.md) and the
  [operator catalog](../../language/operator-catalog.md) when conversion,
  projection, or compatibility needs a programmer-visible operation form.
- [Raw type parameters and generics](../raw/type-parameters-and-generics.md)
  when whole-type contracts, generic admission, deduction, or specialization
  pressure becomes concrete.
- [Raw reflection](../raw/reflection.md) when shape enumeration, generated
  surfaces, source identity, or canonical metadata becomes material.
- [Raw partial types](../raw/partial-types.md) when external additions could
  change shape or invalidate compatibility.
- [Raw callable selection](../raw/callable-selection.md) when whole-type
  callable requirements or result-origin contracts cross the structural
  boundary.
- Legacy casting or type-definition material only when a concrete structural
  question cannot be resolved from current owners and indexed raw input.

### Audit-only

- Archived work only when a concrete provenance or regression question cannot
  be answered from current owners and live raw input.

## Initial working posture

Begin with the completed maintainer notes and reconstruct each intended
structural relationship in plain language with concrete Zax values and
operations. Treat the older structural proposal as a challenge to coverage, not
as the default answer.

Keep distinct questions separate: describing equal shape is not automatically
permission to assign, convert, project, alias, or substitute. Follow each
material consequence far enough to determine its owner and constraints without
prematurely designing complete generics, reflection, partial types, or ABI.

## Working record

### Initial reconstruction for review

The later
[aligned synthesis after maintainer review](#aligned-synthesis-after-maintainer-review)
supersedes this initial candidate wherever terminology, relationships, or
operation boundaries differ.

Everything in this working record is candidate analysis. It reconstructs the
maintainer notes against the current owners and legacy evidence; it does not
record an aligned finding.

### Review entry point

The primary notes appear to want this ordinary rule:

```zax
draw final : ()(point : Point readonly &) = {
}

coordinates : Coordinates
draw(coordinates) // error: Point was requested, not Coordinates
```

Strict identity remains the default even if `Point` and `Coordinates` have
identical stored members. A programmer can separately request an operation that
constructs, projects, or views another type when the applicable relationship and
authority exist.

The initial candidate model therefore has five independent questions:

1. **Identity:** What exact type does this value have?
2. **Stored shape:** Which direct resident members make up a completed value,
   in what declaration order, and with which complete member types and
   shape-relevant attributes?
3. **Representation:** What size, alignment, offsets, padding, hidden
   representation, and target-dependent layout does this concrete type have?
4. **Operation permission:** May this source be constructed, transformed,
   projected, transferred, or viewed as a requested destination, and what does
   that operation cost or discard?
5. **Conformance:** Does the complete type satisfy a separately declared
   no-storage requirement?

The first three describe facts. The fourth is executable or alias-forming
authority. The fifth is a contract relationship. No fact silently grants an
operation, and no operation proves equal identity, shape, or representation.

The most important terminological contradiction to resolve first is the primary
notes' answer that separately declared anonymous types with the same members are
not structurally equivalent but “might be compatible.” If a compared type's own
identity participates in “shape,” two distinct named types can never have the
same shape and shape supplies no useful relation independent from identity. The
candidate model instead treats a type's own identity as separate: two types may
have equal stored shape while retaining distinct identities and no implicit
interchangeability. If the intended relation is deliberately identity-sensitive,
it needs another name and the work still needs an identity-independent shape
relation.

The most important feasibility pressure is an inline shape-accepting callable:

```zax
measure final : ()(
  point : <some accepted x/y structural contract>
) = {
  use(point.x, point.y)
}
```

Unrelated arguments may order, align, or qualify `x` and `y` differently. One
ordinary callable body cannot merely “duck type” over arbitrary offsets while
also assuming one fixed parameter representation. The programmer-visible model
must choose among:

- requiring an exact representation relationship for a no-copy binding;
- constructing one canonical parameter value at the call boundary; or
- treating the callable as a generic shape-constrained declaration whose body
  is checked for each concrete admitted type.

Those choices have different identity, cost, lifetime, and source-evolution
behavior. “Compatible” alone does not choose one.

### Candidate vocabulary and relations

The legacy corpus and primary notes use “compatible” for facts, operations,
permissions, and overload viability. The following candidate vocabulary keeps
those roles separate.

#### Type identity

A transparent alias has the same identity as its target. A named ordinary type,
an identity type, and an anonymous type each introduce a distinct identity
unless another current owner says otherwise.

Identity controls the strict baseline for typed initialization, arguments,
results, assignment, and overload discovery. Equal representation never erases
an identity boundary.

#### Stored-member shape

A candidate **stored-member shape** is the ordered declaration-level structure
of one completed value. At minimum it records:

- every direct resident stored member;
- each member's name;
- declaration order;
- its resolved complete type use after transparent-alias normalization; and
- attributes later determined to affect the member's structural contract.

The outer type's own identity is not one of its members and is not part of this
shape comparison. A nested named member's type identity remains part of that
member's complete type use unless this review deliberately adopts recursive
structural substitution. That choice matters: recursively ignoring nested
identities would allow a structural operation to cross semantic boundaries at
every depth rather than only at the explicitly requested outer boundary.

Declaration order is a plausible shape fact even apart from byte layout because
it currently controls automatic construction and reverse destruction. It also
gives positional decomposition and recomposition a deterministic order. Exact
layout remains separate because packing, alignment, hidden state, and target
rules can change offsets without changing source member order.

#### Exact shape match

An **exact shape match** is a symmetric fact between two completed types whose
stored-member shapes match under the final attribute rules. It does not grant
assignment, argument binding, conversion, construction, or a reference view.

This candidate is intentionally exact. A source with an additional member does
not exactly match a smaller destination.

#### Exact layout match

An **exact layout match** is a symmetric, target-specific fact requiring equal
complete instance size, alignment, representation boundaries, member offsets
and extents, padding obligations, and relevant hidden representation.

Equal gross size and alignment are insufficient. Equal layout also does not say
that a live value of one type may be accessed as another type. Lifetime,
qualification, alias, invariant, and type-owner authority remain separate.

“Memory compatible” is too easy to read as permission to reinterpret. If that
legacy phrase survives, documentation must say whether it means only exact
layout match or also includes a separately declared same-storage view.

#### Positional representation match

The primary notes describe same-layout types whose member names differ as
“memory compatible.” A narrower candidate fact is **positional representation
match**: corresponding resident members have matching offsets, extents, and
representations even though their declared names or semantic type identities
prevent exact shape match.

This fact may help validate a low-level operation. It is not evidence that
pixels and meters, signed and unsigned values, or independently owned resources
mean the same thing.

#### Prefix relationship

A **layout-prefix relationship** is directional: a requested destination
representation matches the initial representation of a larger source. It is not
exact layout match and does not itself authorize either:

- a same-storage prefix reference; or
- a new by-value destination constructed from the prefix.

Those two operations have different lifetime and destruction consequences.
“Slice compatible” currently hides that distinction.

#### Named transformability

A **named transformation** is an explicit directional operation, not a
compatibility fact. It maps source members or result slots to destination
members, may reorder them, may use a declared renaming map, and constructs or
assigns the destination under ordinary lifecycle and transfer rules.

Because it operates member by member, it need not require source and destination
layout to match. Its cost is visible even when optimization later removes some
work.

#### Whole-type conformance

Whole-type conformance is a no-storage contract relation over required values,
callables, operators, qualifications, and provenance. It neither adds a base
object nor implies layout equality. Current member-level `abstract` roles are
not this facility.

The primary notes deliberately hand this concern to future `abstract` and
generic work. Work item `021` still needs to leave those owners an unambiguous
meaning for stored shape and structural admission.

### Concrete relationship tests

Consider:

```zax
Point :: type {
  x : I32
  y : I32
}

Coordinates :: type {
  x : I32
  y : I32
}

SwappedCoordinates :: type {
  y : I32
  x : I32
}

NamedCoordinates :: type {
  horizontal : I32
  vertical : I32
}

Point3D :: type {
  x : I32
  y : I32
  z : I32
}
```

Under the candidate model:

- `Point` and `Coordinates` have distinct identities and are candidates for
  exact shape match. Exact layout still needs a separate proof. Neither can be
  passed where the other identity is requested without an authorized operation.
- `SwappedCoordinates` does not exactly shape-match `Point` when declaration
  order participates. Equal gross size does not repair the different named
  offsets. A named transform can construct one from the other by reordering
  values.
- `NamedCoordinates` does not exactly shape-match `Point` because names
  participate. It may positionally match one target layout, but a semantic
  transform requires an explicit remapping or positional request.
- `Point3D` does not exactly shape- or layout-match `Point`. It may have a
  layout-prefix relationship to `Point`; a value projection and a same-storage
  prefix view still require different authorization.

The model deliberately does not attempt to infer “logic compatibility.” The
compiler cannot know whether two `I32` members represent pixels, meters, or
account balances. Distinct identities and explicit operations are how source
preserves that semantic decision.

### What contributes to which shape

The initial evidence supports the following candidate disposition. Entries
marked open need maintainer review rather than silent acceptance.

| Declaration fact | Stored-member shape | Exact layout | Named transform / conformance |
| --- | --- | --- | --- |
| Direct resident member | Yes | Yes | Eligible subject to access and operation rules |
| Member name | Yes | No by itself | Primary same-name mapping key |
| Declaration order | Candidate yes | Reflected through actual offsets | Positional mapping order |
| Resolved member base type identity | Candidate yes | Representation checked separately | A transform needs an accepted member operation |
| Transparent alias spelling | No; normalize to one identity | No | May remain useful in diagnostics/reflection |
| Type-side `final`/`varying` and value mutability | Open semantic-shape question | Only if representation changes | Must be preserved by a same-place view and checked by binding |
| Access qualification or declaration-side replacement permission | No resident-state duplication | No | Belongs to the particular path and operation |
| Member transfer stance | Probably not resident shape | No | Affects decomposition, projection, and transform transfer |
| Default or explicit initializer | No completed-value shape | No | Affects whether a destination can be constructed |
| Constructor, replacement constructor, or destructor | No instance member | No direct bytes | May make raw copy, truncation, or a view invalid |
| Type-owned `final` function | No | No | May participate in a future whole-type callable contract |
| Captured function value stored as a member | Its function value is an ordinary stored member | Its concrete representation matters | Capture origin and copyability remain part of that member type |
| `once` declaration | No per-instance member | No per-instance storage | Separate type/global surface; not silently erased from reflection |
| `private` visibility | Physical member still exists | Yes | External name mapping must not bypass access authority |
| Incomplete `forward` state | No; compare completed types | Layout remains pending | Not an independent shape attribute |
| `own`-published path | No duplicate member | No | Possible explicit path-mapping input, never another resident value |
| Data `via` route | No duplicate member | No | Possible explicit path-mapping input if place preservation and access allow |
| `preferred` route | No | No | Separate expected-type projection, not shape |
| `expose`, callable `via`, or adopted family | No | No | Callable surface/conformance and reflection only |
| `abstract` role or fulfillment | No | No | Member-level conformance metadata, not whole-type storage shape |
| Exact or family fence | No | No | Callable-surface and reflection fact |
| `outer tracked` | No declared stored member | Representation-relevant and potentially layout-breaking | Capability/lifecycle requirement for tracked operations |

Two cautions follow from this table:

1. “Non-storage declarations are ignored” is safe only for resident shape and
   layout. It is not a general instruction to erase those declarations from
   callable conformance, reflection, source compatibility, or construction
   viability.
2. Qualifiers cannot be answered with one yes/no. A layout comparison may erase
   non-representational qualifiers while a safe same-storage view must preserve
   actual place truth and may only narrow authority. A structural callable
   contract may need qualifications even when stored bytes are identical.

### Construction is not structural compatibility

The primary notes intend a constructor packet to be usable at an argument:

```zax
draw final : ()(point : Point &) = {
}

draw([{ .x = 4, .y = 5 }])
```

That operation, if accepted, constructs the exact expected `Point`. It does not
pass an anonymous structural value and does not depend on `Point` being
compatible with another type.

Current owners clearly establish that a packet is constructor input rather than
an expression value and use an anonymous typed declaration when a packet needs
an explicit destination:

```zax
draw((: Point = [{ .x = 4, .y = 5 }]))
```

They also reject at least the ambiguous nested-optional case in which a bare
packet has no destination. The short argument form is therefore a contextual
packet-construction question for invocation and construction, not a structural
compatibility decision. It needs a separate determination of when an expected
parameter type supplies a sufficiently clear destination.

Nested packets should remain ordinary recursive construction after each nested
destination is known. They must not cause packet entries to become anonymous
structural members.

### Decomposition is explicit result/input mapping

The proposed `>-` operation turns one value into a set of member sources for a
mapping-capable destination:

```zax
point : Point

drawCoordinates(
  x:,
  y: >- point // illustrative proposed syntax
)
```

This is not proof that `Point` is compatible with the callable. It is one
explicit operation that:

1. evaluates its source once;
2. selects eligible member paths;
3. maps those member values or places to callable inputs under the current
   source/destination cursor rules;
4. applies ordinary qualification and transfer viability per mapped member; and
5. leaves, moves from, or destroys the source according to the selected member
   operations and source stance.

The primary notes propose matching names first and then assigning every
remaining member positionally. That is deterministic for one callable but
creates source-evolution and overload pressure:

```zax
StrangePoint :: type {
  x : Integer
  y : Integer
  foo : Integer
  bar : Integer
}

consume final : ()(
  what : Integer,
  ever : Integer,
  x : Integer,
  y : Integer
) = {
}

consume(>- strangePoint) // illustrative
```

Here `x` and `y` map by name, then `foo` and `bar` apparently map positionally
to `what` and `ever`. Renaming `foo`, adding an earlier member, adding a default,
or adding a callable overload can change viability or make the source
ambiguous.

“More matching names” and “longest match” must not become preference scores.
Every candidate should independently obtain one complete deterministic mapping;
ordinary partial-order selection then chooses a unique dominating callable or
reports ambiguity. The review still needs to decide whether automatic
name-then-position fallback is sufficiently clear or whether positional
fallback requires explicit positional intent.

Other unresolved decomposition rules are:

- whether only direct physical members participate by default;
- whether an explicitly named published `own` path or data `via` route may be
  selected without pretending it is storage;
- whether private members are ineligible outside their permitted context;
- how duplicate published names are diagnosed;
- whether decomposition through a pointer, reference, optional, or wrapper
  stops at the same semantic-indirection boundary as composition publication;
- whether a member is offered by value or as a same-place reference;
- how source and member transfer stances combine;
- whether an unmatched source member is merely unused, is explicitly
  discarded, or prevents an exact decomposition; and
- how a temporary returned by `getPoint()` survives while all mapped consumers
  complete.

The existing invocation distinction remains useful: a callable's several result
slots already map directly; a returned `Point` is one value until `>-`
explicitly decomposes it.

### Recomposition creates one value

The proposed `-<` operation consumes remaining result slots and creates one
structural value:

```zax
first:, remaining : -< produce() // illustrative proposed syntax
```

Current invocation rules say that several result slots are not one expression
value and parentheses do not combine them. Recomposition is therefore an
explicit structural-promotion operation at the producer boundary.

For an anonymous destination, the initial candidate is:

- every unconsumed result becomes one direct resident member;
- the result label becomes the member name;
- member order is the producer's declared result order after earlier routing;
- the complete result type, qualifications, reference shape, and transfer
  obligations are preserved rather than inferred from the stored value later;
- duplicate or unusable labels require explicit renaming rather than compiler
  invention; and
- zero remaining results forms a zero-member anonymous type only if that type
  and its use are otherwise meaningful.

For a named destination, recomposition is construction, not layout
reinterpretation. Result expressions are already evaluated in producer result
order; their values can be treated like named packet inputs, while actual member
construction retains destination member order. A destination constructor or
member operation must accept every mapped value. Equal shape alone does not
manufacture copyability or conversion.

The proposed assignment form:

```zax
destination -< produce() // illustrative proposed syntax
```

is observably different from construction. It performs member assignments on an
already-live destination. Unless a future operation explicitly makes the group
transactional, earlier assignments remain visible if a later assignment panics,
matching current result-routing behavior. The operation must not be documented
as though it atomically replaces one structural value.

One producer invocation may feed ordinary routed destinations and at most one
recomposition of the remaining slots. A second textual invocation is a second
execution; no syntax should imply shared evaluation accidentally.

### Anonymous type identity

An anonymous type still needs one stable compile-time identity. “Unique per
instance” would make ordinary static typing impossible. The candidate
interpretation of the primary notes is:

- each anonymous type-producing declaration, generated operation schema, or
  concrete generic specialization establishes one distinct type identity; and
- repeated runtime evaluations of that source produce values of that same
  identity.

The review must decide the exact identity boundary for arithmetic reports. For
example, reports from two uses of the same concrete `I32 +! I32` operation could
share the one result identity declared by that operation, while another
operator or integer identity could produce a distinct anonymous report type.
Backend representation deduplication would not merge those language identities.

Two distinct report identities can still exactly shape-match:

```zax
// Conceptual shapes from different generated operations.
<first anonymous> :: type {
  value : I32
  overflowed : Boolean
}

<second anonymous> :: type {
  value : I32
  overflowed : Boolean
}
```

Equal shape would not grant assignment. A named report destination would need an
explicit recomposition, transform, or declared construction route. `?report` and
`!report` remain operations associated with the concrete report identity rather
than properties inferred from any coincidentally matching two-member type.

Reflection must eventually expose distinct identity, optional lack of source
name, stored shape, qualifications, and layout without infinitely expanding
recursive types. The raw reflection owner preserves that pressure; this work
should decide the domain facts to be reflected, not a metadata API.

### Transformation is construction or assignment, not reinterpretation

The proposed `-<>-` operation performs decomposition followed by recomposition:

```zax
destination -<>- source // illustrative proposed assignment transform

created : DestinationType -<>- source
// illustrative proposed construction transform
```

The primary notes say this operation always performs copying of some kind. That
is coherent if it is a memberwise construction/assignment transform rather than
a same-storage cast. A transformation can reorder source members, change the
outer identity, and use a renaming schema while keeping costs visible.

A safe construction transform should initially require:

1. every required destination member is established exactly once;
2. every implicit same-name match is unique;
3. every mapped member has an accepted construction or transfer;
4. destination construction and lifetime rules are preserved;
5. inaccessible declarations are not exposed by structural matching; and
6. extra source members have a stated disposition.

The last rule needs a decision. Candidate alternatives are:

- extra members are harmless when copying from a still-live source because
  nothing is discarded from the source;
- a consuming or truncating transform must explicitly acknowledge every source
  member whose resource or state is not transferred; and
- a destination with missing members may use only explicit destination defaults
  or constructor policy, never guessed values.

This is more precise than “works so long as it is compatible,” which makes
compatibility depend circularly on whatever the operation happens to do.

For an assignment transform, the destination already has live member values.
The operation needs selected `=` candidates, destination qualifications,
source-order evaluation, failure behavior, and alias handling. A self or
interior-aliasing transform can otherwise end a member before another mapping
reads it.

The current operator catalog preserves legacy `<-` and `->` only as
result-shape transformation evidence. The primary notes propose `>-`, `-<`, and
`-<>-`. No spelling is accepted merely by appearing in either source. Before
promotion, source integration must determine each form's fixity, precedence,
candidate-tree interaction, mapping boundary, and whether it is protected and
non-overloadable.

### A reshaper is not presently a runtime type

The proposed declaration:

```zax
MyReshaper :: type reshape {
  x: myX:
  y: myY:
}
```

contains name-to-name mapping rules but no complete member types or storage.
The primary notes also say it cannot be instantiated without a transformation
source and that its resulting concrete member types come from that source. Those
properties make it a compile-time mapping schema or dependent adapter, not an
ordinary completed value type.

Treating it as a type creates contradictions:

- `myReshaper : MyReshaper` has no source from which to obtain member types;
- requiring an “actual instance” implies runtime storage even though the schema
  defines none;
- one schema used with two differently typed sources would have two concrete
  runtime shapes under one nominal identity; and
- permitting it as a result but not an input makes identity depend on flow
  direction.

The candidate direction is to describe the feature as a named transformation
map. Applying the map produces an ordinary named or anonymous destination value;
the map itself is not that value. Future generic work may later express a map
parameter, but work item `021` can define the non-generic transformation
behavior without pretending the schema is a runtime type.

Same-name members omitted from the map could pass through as a deliberate
default rule. Members named by one side of a map but missing on the other should
be errors unless an explicit destination default or source-discard rule applies.
The primary notes' broader “bypass” behavior needs examples showing conflicts,
duplicate targets, cycles, and source evolution before it can be accepted.

### Same-storage views need more than matching bytes

The primary notes propose reference views such as:

```zax
source become directly Destination &
source become memory Destination &
```

The exact spelling is unsettled, but the semantic distinction is essential. A
same-storage view:

- creates no destination value lifetime;
- schedules no destination destruction;
- aliases the source's exact place and origin;
- must preserve actual final/varying truth, mutability, and lifetime;
- may narrow but never increase access or replacement authority;
- must account for hidden representation such as `outer tracked`; and
- must not let unrelated destination lifecycle or invariant assumptions operate
  on storage whose live resident instance is still the source type.

Exact layout match is therefore necessary but not sufficient. The current
same-storage identity-view pressure has an explicit identity/underlying
relationship. Unrelated named types have no comparable owner-declared bridge
yet. A candidate safe rule is that ordinary same-storage views require both:

1. a proved exact or permitted prefix representation relationship; and
2. a declared bridge or operation owned by an applicable type authority.

Otherwise the operation is an unsafe representation assertion even if the
compiler can verify offsets.

Prefix views are especially sensitive. A `Point3D &` viewed as `Point &` must
not permit whole-value replacement or destruction of the apparent `Point` while
leaving `z` and the enclosing `Point3D` lifetime incoherent. A copied
`Point3D`-to-`Point` projection instead creates an independent `Point`; its
source `z` is not “lost” when the source remains live, although the new value
does not contain it.

The primary notes' “coerced memory compatibility” is better modeled as
target-specific layout coincidence plus an unsafe semantic assertion.
`UChar` matching `U8` on one target does not create a portable relationship.
Likewise equal-width `U8` and `I8` storage does not erase their value semantics.

An unsafe operation should not mean that the compiler ignores every known fact.
Known insufficient size, impossible alignment, expired provenance, or a
contradicted placement relationship can remain errors. Unsafe responsibility
is for an unproved but mechanically possible claim, not permission to make an
already disproved fact true.

### Compatibility posture needs a clearer authority model

The primary notes propose value postures such as:

```zax
myPoint as directly
myPoint as sliced memory
```

and propose that anonymous values receive a posture automatically. This offers
an ergonomic route to a local anonymous input type, but it currently has three
problems.

First, the notes say input compatibility postures do not affect selection. If a
posture makes an otherwise invalid argument bind to a parameter, it necessarily
changes candidate viability and may change the selected overload even if it is
not itself a preference score.

Second, an instance-level posture has to be part of the static source type or
declaration metadata for a compiled call to use it. If it can differ only at
runtime, overload viability would become runtime dispatch. If it is static, its
identity, propagation, aliasing, inference, reflection, and greedy stripping
need the same care as transfer stance and qualifiers.

Third, a caller's willingness alone does not establish what a callee means by
accepting another representation. A named `Point` parameter promises exact
`Point` body-entry facts. An unrelated source can satisfy it safely only by:

- explicitly constructing or transforming a real `Point`; or
- invoking a parameter contract that explicitly accepts structural sources and
  defines canonicalization or specialization.

The initial recommendation is not to make broad compatibility postures the
foundation. Preserve strict parameters, use explicit transformation or
same-storage operations at the call site, and design a distinct
shape-accepting parameter contract if repeated structural APIs need one. A
later convenience posture can abbreviate one already-defined operation without
becoming a second conversion and overload system.

Automatically giving every anonymous value a permissive posture also weakens
the principle that intent is explicit. Contextual construction into a known
anonymous parameter type may provide the desired literal ergonomics without
making every anonymous value generally convertible.

### Endian and identity pressure

Current endianness teaching says an endian enum and its backing fundamental type
are both storage- and shape-compatible while remaining independent types. The
exact-storage claim fits this reconstruction for a representation-trivial enum.
The shape claim needs a precise scalar/identity rule: neither side is an
ordinary record with matching named stored members.

Possible coherent readings are:

- an identity's immediate underlying payload is a structural leaf whose shape
  can match the underlying type while outer identity remains separate; or
- endianness needs only exact storage match plus its explicitly declared enum
  boundary operations, and “shape-compatible” should be removed there.

Either way, endian conversion remains explicitly owned by `from`, `as`, and
`underlying value`. Structural facts must not add another implicit route.

Representation-trivial identity exposure is another special declared
relationship. Its ability to substitute copied underlying inputs and results
must not generalize to every pair of equal-shape named types. An identity with
additional resident data ceases to have that complete representation
relationship.

### Source evolution and diagnostics

Structural relationships deliberately create source-compatibility coupling.
Programmers should be able to predict at least these events:

- renaming a stored member breaks exact shape and implicit same-name mapping;
- reordering members breaks the candidate ordered shape and can change layout,
  construction order, destruction order, and positional mapping;
- adding or removing a member breaks exact shape, may add or remove prefix
  relationships, and can change decomposition or transform completeness;
- changing a member type, qualifier, transfer stance, or representation
  attribute may change different relations independently;
- changing `outer tracked` can change hidden representation and lifecycle;
- adding a published path or data route can create a mapping ambiguity only if
  the chosen mapping operation admits those paths;
- adding callable behavior does not change resident shape but may change
  whole-type conformance and callable selection;
- adding a partial stored member, if ever authorized, must invalidate and
  recompute all completed shape and layout relationships independent of import
  order; and
- a target-dependent coerced layout may compile on one target and fail on
  another without becoming portable source.

Diagnostics should name both the failed fact and the requested operation. Useful
distinctions include:

- identity mismatch with no requested conversion;
- exact shape mismatch at member name, order, type, or attribute;
- exact layout mismatch at size, alignment, offset, extent, padding, or hidden
  representation;
- directional prefix rather than exact relationship;
- transform missing a destination member or producing duplicate mappings;
- unmatched source member requiring discard intent;
- construction or assignment unavailable for one mapped member;
- same-storage view lacking owner authority, qualification, lifetime, or
  provenance;
- target-specific rather than portable layout;
- decomposition/recomposition unable to form one deterministic mapping; and
- several callable candidates remaining nondominated after independent mapping.

### Captured adjacent consequences and proposed deferrals

These consequences are material but need not expand work item `021` into their
complete designs:

- **Whole-type contracts and generic structural admission:** preserve in
  `project/raw/type-parameters-and-generics.md`. Reactivate when a callable needs
  to accept an unknown concrete type without canonicalizing it, or a generic
  body needs a named shape constraint. Constraint: member-level `abstract`,
  `preferred`, and equal layout do not already supply this contract.
- **Reflection:** preserve in `project/raw/reflection.md`. Reactivate when code
  must enumerate stored shape, compare relations, inspect anonymous identities,
  or distinguish physical, published, routed, generated, fulfilled, and hidden
  representation categories. Constraint: reflection reports the relations
  established here rather than defining them.
- **Partial/open types:** preserve in `project/raw/partial-types.md`. Reactivate
  if an authorized external piece may add stored members or mapping behavior.
  Constraint: every stored contribution must be known before shape, layout, and
  same-storage operations are validated; order of imports cannot decide the
  completed shape.
- **General callable preference:** preserve in
  `project/raw/callable-selection.md`. Reactivate when decomposition or
  structural constraints require new per-slot comparisons. Constraint:
  name-match counts, path length, declaration order, and layout cheapness do not
  become overload scores; unresolved nondominance remains ambiguity.
- **General reflection metadata for recursive and anonymous types:** remains in
  the reflection input. Work item `021` should establish stable semantic
  identity and finite comparison behavior, not a metadata encoding or compiler
  canonicalization algorithm.
- **General ABI/FFI and packing controls:** defer until an actual foreign or
  layout-control boundary needs them. Constraint: exact Zax layout match is not
  an ABI promise, and no current relation should be named or taught as one.
- **General unsafe casting:** legacy `casting.md` remains evidence for the wider
  conversion lattice. Work item `021` should decide only the structural
  preconditions and distinction between value construction and same-storage
  viewing needed here.

### Likely lasting owner boundaries

If a coherent model is later aligned, one dedicated programmer-facing
structural-shape and compatibility owner is likely warranted. It would teach:

- identity versus stored shape versus representation;
- exact and directional structural facts;
- explicit transform, projection, and same-storage operation categories;
- anonymous structural values; and
- structural costs, failures, diagnostics, and source evolution.

Existing owners would still need focused integration:

- `language/declarations-and-bindings.md` for which declaration facts enter a
  completed stored shape;
- `language/identity-types.md` for identity/underlying structural leaves,
  same-storage identity bridges, and additional resident data;
- `language/composition.md` for physical members versus paths and generated
  surfaces, plus `outer tracked`;
- `language/qualifiers.md` for shape participation and authority-preserving
  views;
- `language/construction-and-destruction.md` for packets versus anonymous values
  and memberwise construction;
- `language/function-invocation.md` for decomposition, recomposition, result
  routing, callable viability, and temporary completion;
- `language/lifetimes-and-references.md` and
  `language/transfer-stances.md` for same-place views and per-member transfer;
- `language/operator-catalog.md` and `language/operators.md` for accepted
  operation forms and protected/non-overloadable status;
- `language/integer-operator-catalog.md` for the selected arithmetic-report
  identity model;
- `language/endianness.md` for precise replacement of its provisional
  shape/storage terminology;
- legacy `casting.md`, `functions.md`, and `type-definition.md` for disposition
  of still-useful structural evidence; and
- both structural raw inputs for retirement or extraction after every useful
  question has a lasting owner.

This owner map is only a candidate boundary. No promotion or structural change
is implied.

### Aligned synthesis after maintainer review

The findings in this section are aligned for the current review scope. They
remain non-authoritative working material until separately authorized promotion
incorporates them into their lasting owners.

This section replaces incompatible alternatives and terminology in the initial
reconstruction. Earlier material remains useful evidence for why the aligned
boundaries were selected, but it is not a competing model.

#### Panic is never unwinding

Panic is graceful crash behavior, not an exception or alternate control-flow
path:

- the operation encountering the panic remains blocked;
- a narrowly applicable helper may repair the condition and let that same
  operation resume and complete;
- otherwise the process crashes gracefully;
- panic never skips the failed operation, returns a substitute, unwinds scopes
  or completed members, rolls back work, or returns partial lifecycle state to
  ordinary execution.

This is already substantially owned by
`language/safety-and-analysis.md#panic-boundary` and repeated correctly in
construction and allocation teaching, but it is not routed consistently enough
to prevent recurring exception-shaped interpretations. `language/terms.md`
lacks a canonical `panic` entry, `language/principles.md` lacks the reusable
“panic is not alternate control flow” principle, and some local wording can
still suggest that execution continues after a panic or that a future
recoverable panic unwinds partial work.

This cross-cutting documentation finding is broader than structural conversion.
The eventual dry run must give it a lasting owner and identify focused local
corrections without repeating the complete panic model everywhere.

#### Identity remains outside shape

Every type-defining declaration or generated anonymous type site establishes a
distinct type identity unless an accepted mechanism says otherwise. A
transparent alias names an existing identity rather than creating another one.

Two named or anonymous types may have the same structural shape while remaining
different identities. Equal shape never makes them interchangeable. This
preserves semantic distinctions such as pixels versus kilometers even when both
types store members named `x` and `y` with identical integer types.

An inferred declaration reuses the initializer's exact concrete type identity;
it does not create or “borrow” another type:

```zax
original : :: type {
  x : Integer
  y : Integer
}

copy := original
OriginalType :: alias type type of original
another : OriginalType = original
```

The three values use the same anonymous type identity. “Borrow” remains lifetime
and reference vocabulary rather than type-inference vocabulary.

Each anonymous type-producing declaration, generated operation schema, or
concrete generic specialization establishes one stable compile-time identity.
Repeated runtime evaluation produces more instances of that identity rather
than a new type identity per value.

#### Direct and flattened structure remain distinguishable

Exact stored shape records the direct resident-member tree:

- direct member names;
- declaration order;
- containment boundaries;
- complete nested type identities after transparent-alias normalization;
- complete type-side qualifications at every represented layer; and
- hidden structural components that affect the value's representation.

The compared outer identity is not part of this shape. Declaration order remains
material because it participates in physical order, automatic construction, and
reverse destruction.

Flattened shape recursively removes only eligible by-value containment
boundaries and compares the resulting ordered leaves. It does not descend
through semantic-indirection or atomic lifecycle boundaries merely because
their eventual bytes could be inspected.

For:

```zax
Point2D :: type {
  x : Integer
  y : Integer
}

Point3D :: type {
  x : Integer
  y : Integer
  z : Integer
}

A :: type {
  coord : Point2D
  z : Integer
}

B :: type {
  coord : Point3D
}

C :: type {
  x : Integer
  y : Integer
  z : Integer
}
```

`A`, `B`, and `C` do not have the same exact stored shape. Under compatible
ordinary integer representation they may have the same flattened leaf shape and
layout: three consecutive `Integer` leaves named `x`, `y`, and `z`. That fact
does not erase their different direct resident-member boundaries.

#### Scalar shape has a semantic format

A scalar's structural leaf records the properties that determine how its bits
represent a value, separately from outer nominal identity and physical storage
layout.

The provisional safe-compatibility property list for integer and fixed-point
scalars is:

- logical bit width;
- signedness;
- value encoding, including two's-complement signed representation;
- fixed fractional-bit position;
- endianness role; and
- storage extent, alignment, non-value bits, and normalization requirements.

Every meaning-bearing property must match for safe scalar shape or layout
compatibility. Different outer identities may still match when their complete
scalar formats agree; the posture or explicit operation controls whether that
fact grants a recast.

Coercion compatibility deliberately uses a reduced family-specific property
list. For integer and fixed-point values, equal logical bit width and a
sufficient compatible target storage envelope are the provisional deciding
requirements. Signedness, fixed fractional position, and endian interpretation
may differ under local unsafe coercive responsibility:

```zax
signed unsafe as coercive layout U32
fixedPoint unsafe as coercive layout I32
```

Enums remain directional. An enum may serve as a source for its compatible
immediate underlying scalar type, but an underlying scalar does not
automatically become the enum, and one enum never safely or coercively becomes
another enum merely because their backing representations agree.

Floating-point safe compatibility and coercion require more properties than
integer bit width. The provisional future list includes total format width,
exponent width, fraction or mantissa width, explicit versus implicit integer
bit, endianness, and every concrete family rule affecting representation.
Different exponent partitioning is not another coercive viewpoint on the same
number and remains incompatible even when total storage size agrees.

`project/raw/floating-point-maintainer-notes.md` and
`project/raw/numeric-type-families.md` must preserve these provisional safe and
coercive lists as explicit review pressure. Future floating-point work will
finalize them and recheck the integer list rather than treating this work item
as the lasting numeric-format owner.

A representation-trivial identity retains its underlying scalar format while
adding a distinct outer identity. Additional resident data makes it an
aggregate. Current endian teaching must distinguish equal storage envelope from
equal scalar format: a semantic endian type and the native backing scalar can
share storage while requiring explicit endian-owned conversion because their
endianness roles differ.

#### Compatibility postures are safe binary-recast permission

Use **compatibility posture**, not “compatibility stance.” Transfer stances
describe resource transfer; compatibility postures describe willingness to
cross a type-identity boundary through a low-overhead binary-compatible recast.

Every completed declaration resolves one effective posture. Omission resolves
to:

```zax
compatible strict
```

The aligned posture family is:

```zax
compatible strict
compatible shape
compatible flattened shape
compatible layout
compatible flattened layout
```

All non-strict postures require one contiguous binary-compatible source region.
They permit a fixed compile-time offset adjustment and type reinterpretation,
plus whatever ordinary value transfer or reference binding the destination
already requires. They never gather discontiguous members, reorder values, or
perform arbitrary reshaping.

- `compatible strict` permits only exact identity and transparent aliases to
  bind contextually.
- `compatible shape` requires compatible direct names, order, nested identity,
  qualifications, resident boundaries, and binary layout.
- `compatible flattened shape` may remove eligible containment boundaries but
  still compares ordered leaf names, complete leaf types, qualifications, and
  binary layout.
- `compatible layout` preserves direct resident boundaries and complete
  qualified member types while ignoring member names.
- `compatible flattened layout` may remove eligible containment boundaries and
  ignores leaf names while preserving complete qualified leaf types and binary
  layout.

A posture grants permission to test the named relation against an independently
known expected type. It does not assert compatibility with every possible
destination and never chooses or invents a destination.

Exact identity remains preferable to any posture-based adaptation. A posture can
change candidate viability, but source order, number of matching names, shortest
offset, or apparent conversion closeness never chooses among several remaining
destinations.

#### Posture is declaration metadata and does not propagate implicitly

Compatibility posture is static declaration or type-use metadata rather than
runtime state or canonical type identity. Declaration and value reflection must
eventually report the effective posture, including defaulted
`compatible strict`; canonical type identity reflection must not absorb it.

A result may deliberately publish structural flexibility:

```zax
makeInternalPoint final : (
  result : InternalPoint copy compatible shape
)() = {
}

publicPoint : PublicPoint = makeInternalPoint()
```

The selected function still returns exactly `InternalPoint`. The result posture
permits contextual adaptation during its immediate result-mapping boundary when
`PublicPoint` is already expected.

Posture does not propagate through an ordinary inferred declaration:

```zax
captured := makeInternalPoint()
// captured has the exact InternalPoint identity and compatible strict.
```

This prevents implicit conversion authority from travelling arbitrarily far
from the API that granted it. A future explicit inferred-posture declaration may
retain local flexibility, but its exact syntax is unsettled. An explicit local
posture restatement can authorize one use:

```zax
consume(captured as compatible shape)
```

An explicitly typed destination, transform result, parameter, or forwarding
result establishes its own posture. A parameter posture governs later uses of
the bound local parameter; it does not independently loosen the caller-side
binding requirement. Anonymous declarations, arithmetic-report types, and
anonymous values produced by recomposition receive `compatible strict` unless
their owning declaration or operation explicitly states another posture.

No coercive or unsafe posture may be declared, including on a function result.
Unsafe responsibility must remain local to the operation performing it.

#### Anchor selects the source-relative start

An optional `anchor .path` selects the starting place of the one contiguous
source region used by a compatibility operation. The path always resolves
within the source, never within the destination. The independently expected
destination supplies the required extent, alignment, and resulting type.

An anchor must resolve to a resident storage place. A physical path or an
accessible `own`-published or place-preserving data-`via` path may identify that
place. The compiler canonicalizes an alternate path to its physical resident
place before checking offset, alignment, tracking components, and extent.

An anchor cannot select a `final` function, a type-owned `once` value, an
`abstract` declaration, callable `via`, exposed or generated behavior, an alias
without a place, or another metadata-only declaration.

A zero-size stored value remains a real conceptual place even when it shares a
machine address with another zero-size place or the following nonzero member.
Its structural path, origin, and lifetime distinguish it; numeric address
equality does not merge places. A zero-size anchor may supply only a compatible
zero-size destination and cannot use its shared address to consume bytes from a
following member. This remains categorically different from a no-storage
declaration, which has no anchorable place at all.

Without an anchor, matching starts at the source root and uses the complete
source extent. With an anchor, matching starts at that exact source path:

```zax
source : MyType compatible layout

consume(source anchor .payload)

converted :=
  source as layout Destination anchor .payload
```

An anchor replaces automatic searching and the earlier candidate
`prefix`/`subset`/`sliced` posture families. The compiler never searches for the
first, closest, or cheapest compatible block. A full source path chooses one
start deterministically; an absent, inaccessible, ambiguous, misaligned, or
insufficient region is an error.

A result may declare a default source anchor:

```zax
produce final : (
  result : Packet compatible layout anchor .payload
)() = {
}

destination : PayloadView = produce()
```

The producer knows the path inside its own result without knowing the caller's
destination type. A use-site anchor may select another source path when the
posture permits it.

Posture and default anchor belong to the visible result contract. They are not
part of the returned type identity.

#### Anchor applies independently to one result slot

A bare multiple-result sequence is not one structural value and cannot be
anchored collectively. Result selection chooses one source slot first; an
anchor then selects a place inside that result; the destination parameter or
declaration supplies the expected type.

Illustrative result-routing integration:

```zax
produce final : (
  packet : Packet compatible flattened layout,
  status : Status
)() = {
}

consume(
  packet anchor .payload.point: point:,
  status: = produce()
)
```

The extended source selector reads:

```text
<source-result label> anchor <source-path>: <destination>:
```

A declared default result anchor allows ordinary source/destination routing to
omit the use-site anchor. Every result slot retains its independent completion,
posture, anchor, stance, transfer, and discard obligations.

One result slot may be consumed at most once. Two different anchors do not
permit one slot to supply two destinations implicitly; an explicit copy,
decomposition, or second producer invocation is required.

Anchoring a by-value result temporary is valid only through its complete
synchronous consumer. An anchored reference cannot escape the source
temporary's life path. Anchoring a reference result preserves its original
referent origin and lifetime.

#### Protected compatibility conversions

Explicit protected conversion forms mirror the safe posture relationships:

```zax
source as shape Destination
source as flattened shape Destination
source as layout Destination
source as flattened layout Destination
```

Each may carry a source anchor:

```zax
source as shape Destination anchor .path
source as flattened shape Destination anchor .path
source as layout Destination anchor .path
source as flattened layout Destination anchor .path
```

These forms are protected and non-overloadable. Ordinary:

```zax
source as Destination
```

continues to select applicable type-owned conversion behavior and does not
silently opt into structural recasting.

A protected compatibility operation has three distinct destination contexts:

```zax
copy : Destination =
  source as layout Destination

existing =
  source as layout Destination

view : Destination & =
  source as layout Destination &
```

- A by-value destination invokes applicable destination construction using the
  compatible source view.
- An existing destination invokes applicable destination assignment.
- A reference destination creates a same-storage view and by itself invokes
  neither construction nor assignment.

The compatibility adaptation adds no arbitrary mapping cost. Ordinary
destination construction, assignment, `copy`, `deep`, `move`, `last`, allocation,
or lifecycle behavior retains its own visible cost.

#### Coercive conversion and unsafe cast are separate

Coercive conversion remains a checked-layout but unsafe local operation:

```zax
source unsafe as coercive layout Destination
source unsafe as coercive flattened layout Destination

source unsafe as coercive layout Destination anchor .path
source unsafe as coercive flattened layout Destination anchor .path
```

The compiler still requires a sufficiently sized, aligned, contiguous
representation on the selected target. Coercion may ignore semantic leaf type
identity and may weaken qualifications under explicit unsafe responsibility. A
target-specific coincidence is not a portable compatibility promise.

`unsafe cast` is a separate general unchecked reinterpretation mechanism:

```zax
pointer unsafe cast Destination *
value unsafe cast Destination &
reference unsafe cast Destination &
```

These forms reinterpret existing storage or an existing address. They perform
no compatibility, qualification, invariant, lifetime, bounds, or target-layout
guarantee. Raw pointer casting is an especially dangerous application, not the
complete extent of `unsafe cast`.

A bare by-value result is rejected:

```zax
copy := value unsafe cast Destination // error
```

That spelling cannot say coherently whether to establish a destination lifetime
without construction, copy an arbitrary byte extent, invoke destination copy
construction, truncate, overread, or preserve invalid placement metadata. A
programmer who wants a new value first forms the explicit unsafe view and then
requests ordinary destination construction:

```zax
copy : Destination =
  value unsafe cast Destination &
```

The cast owns unchecked reinterpretation of the source place. Destination
construction owns copying, resource behavior, tracking repair, and the new value
lifetime. Checked-layout by-value reinterpretation instead uses coercive
`unsafe as`.

No `unsafe cast` or coercive permission enters a compatibility posture or
function result contract.

#### Safe compatibility never weakens qualifications

Every participating direct or flattened leaf must preserve compatible
type-side qualifications at every optional, pointer, reference, ownership, and
other represented layer. Flattening never excuses one incompatible leaf.

Ordinary access may narrow authority, but safe compatibility cannot present:

- immutable state as mutable;
- readonly access as writable;
- a final place as varying;
- a path without replacement permission as one that has it; or
- a nested pointer or reference with stronger authority than its source.

Construction of a distinct destination value is not requalification of the
source. A destination constructor may establish its independently declared
state when its source access and construction contract permit it. Unsafe
coercion and `unsafe cast` may weaken qualifications because their local unsafe
responsibility explicitly leaves the safe compatibility family.

#### Same-storage compatible references mutate the same storage

A same-storage compatibility reference creates another typed access path to the
source resident storage:

```zax
PixelPoint :: type {
  x : Integer
  y : Integer
}

Coordinates :: type {
  horizontal : Integer
  vertical : Integer
}

point : PixelPoint

coordinates final :
  Coordinates mutable writable final & =
    point as layout Coordinates &

coordinates.horizontal = 20
// Mutates point.x in the same storage.
```

Mutation through a writable compatible view affecting the source is intended.
The source posture or explicit compatibility operation grants that semantic
permission, while qualifications decide whether the path can exercise it.

Creating the reference does not begin a separate destination value lifetime.
Direct destination construction or destruction through that view is therefore
unavailable. Ordinary destination functions and operators may act through the
view when their qualification and in-lifetime behavior preserve a valid source
representation.

An anchored region does not inherit the complete containing place's
whole-value replacement authority. It denotes a final resident subregion for
the current containing instance:

```zax
a : A mutable

c final :
  C mutable writable final & =
    a as flattened layout C & anchor .coord.x

c.y = 10      // content mutation is permitted
c = makeC()   // canonical whole-value replacement is unavailable
```

For an immutable outer value, deep immutability applies to its composed values.
Even when the outer place is writable and varying for complete reconstructive
replacement, an anchored region cannot become mutable or independently varying:

```zax
a varying : A immutable writable varying

c final :
  C immutable readonly final & =
    a as flattened layout C & anchor .coord.x
```

Replacing the complete `A` invalidates the anchored reference rather than
making that reference observe a renewed interior region. A domain-specific `=`
that explicitly accepts a final receiver and performs coherent in-lifetime
mutation may remain selectable; compiler-recognized whole-value or
reconstructive replacement does not arise through the anchored view.

A whole-root compatible reference is different. Without an anchor, the view may
retain the actual outer place's `immutable writable varying &` capability only
when compatibility covers the complete root and also proves:

- equal extent and alignment;
- corresponding resident-lifetime partitions;
- compatible complete qualifications;
- compatible tracking components; and
- compatible construction, replacement, and destruction obligations.

For two simple structurally generated record types, complete shape/layout
compatibility can establish those facts and permit reconstructive replacement
through the compatible root view. The shared root place renews as one complete
place and remains valid under either identity's promised representation.

Equal bytes alone are insufficient. If either identity has incompatible custom
replacement, destruction, or source-only resource obligations, the
`immutable writable varying &` conversion is an error unless a future explicit
bridge or local unsafe operation accepts the stronger responsibility.

#### Transfer stance through a compatible region

A whole-source compatible recast preserves the source transfer stance. An
anchored region instead receives its own default `copy` stance, like a newly
declared reference alias. The containing source's `last` does not prove that
terminally consuming an arbitrary interior region leaves excluded state valid
for destruction.

If a conversion explicitly selects `copy`, the region receives `copy` even when
the outer declaration ordinarily offers `last`.

Explicit terminal use may be requested:

```zax
(source anchor .payload) as last
```

It is viable only when the applicable source contract establishes that
terminally consuming the anchored region leaves the complete source
destruction-valid. A result declaration combining `last` with a declared default
anchor can establish that API-wide contract for that one anchor:

```zax
result :
  Container last
  compatible layout
  anchor .payload
```

A caller-selected different anchor does not inherit that guarantee.

#### `outer tracked` is a hidden root structural component

`outer tracked` contributes one hidden root representation component with
placement and lifecycle meaning. It participates in exact and flattened shape
and layout comparison even though it is not an ordinary source-declared member.

The language model does not require that component to be one pointer or one
particular metadata-block implementation. It does require:

- a tracked destination to find a compatible tracking component at its own
  relative root;
- two aligned tracking components with the same metadata contract to be
  compatible even when their outer nominal type identities differ;
- a tracking component never to match ordinary user data merely because its
  size agrees;
- flattening not to enter the tracking component's internal representation;
- a same-storage compatible reference to share the source resident's tracking
  state; and
- destination copy construction to repair or regenerate tracking state for the
  destination placement rather than blindly preserving source placement data.

An anchor may begin at the first visible member after an outer tracking
component when the destination is untracked. If an anchored region contains a
nested tracking component, the destination must expect a compatible tracking
component at the same relative position. Missing, misaligned, or semantically
different tracking components prevent safe compatibility.

Tracking metadata must remain interpretable for a compatible recast rather than
being valid only under the original nominal type name. A tracked query still
tests the actual requested outer/member placement relationship; recasting does
not make an incorrect relationship succeed.

#### Contextual packets remain construction, not compatibility

A construction packet supplied where one concrete parameter type is already
expected may construct that exact parameter type if invocation adopts the short
contextual form:

```zax
draw([{ .x = 4, .y = 5 }])
```

The unambiguous explicit form remains:

```zax
draw((: Point = [{ .x = 4, .y = 5 }]))
```

Neither packet is an anonymous structural value. This is a construction and
invocation integration question, not posture-based compatibility. Nested packets
remain recursively destination-directed construction.

#### The structural operators carry visual meaning

The punctuation for the three structural operators is not arbitrary. Its visual
shape teaches the direction of opening and packing:

- In `>-`, the right-hand `-` is the packed source shape. The open side of `>`
  releases its members toward mapping destinations on the left.
- In `-<`, open values on the right flow into `<`, which closes them into the
  packed destination represented by the left-hand `-`.
- In `-<>-`, the right packed source opens through `>-`; the resulting open
  values then close through `-<` into the left packed destination.

Programmer-facing promotion must explain this visual model beside the first
representative examples. The operator shapes are mnemonic language design, not
historical lore to discard after the semantics are recovered.

#### Decomposition `>-` is name-only

`>-` explicitly decomposes one structural value into member sources for a
mapping-capable destination. It does not perform automatic positional fallback.

The operation:

- evaluates its source once;
- considers eligible source members by name;
- maps each selected member under ordinary qualification and transfer rules;
- leaves unmatched source members untouched;
- requires every destination input to be supplied by another explicit argument,
  one matching member, or its declared default; and
- never ranks overloads by number of matching names.

Every callable candidate must independently obtain one complete deterministic
mapping. Several remaining nondominated candidates are ambiguous.

An unmatched member of a source value is not an unconsumed callable result. It
remains part of the still-live source and later receives ordinary transfer and
destruction behavior. A decomposed temporary retains every unmatched member
until its complete consumer finishes.

Private or inaccessible members do not become eligible merely because the
compiler can see them. Semantic indirection remains atomic.

Automatic name mapping may use ordinary composition data lookup. An accessible
`own`-published path or place-preserving data-`via` route may therefore supply a
source member. The selected path canonicalizes to its resident physical place,
and every alias to that same place shares one consumption identity. `>-` does
not treat publication or routing as another stored member.

#### Recomposition `-<` creates one value

`-<` explicitly turns remaining result slots into one value. It does not mean
assignment into an existing destination and does not reinterpret the callable's
bare multiple-result sequence as a value without the operator.

For an anonymous destination:

- every remaining selected result becomes one direct member;
- the source-result label becomes the member name;
- producer result order becomes declaration order;
- duplicate or unusable labels require explicit renaming; and
- the produced anonymous value receives `compatible strict` unless explicitly
  declared otherwise.

For a named destination, exact result names map to destination members and
ordinary destination construction establishes the complete value. Unmapped
destination members may use already declared construction defaults; no value is
guessed.

Every non-discardable result must be routed, recomposed, or otherwise consumed.
A result explicitly permitting discard may remain unused. Named recomposition
does not silently absorb a result for which it has no destination member.

Named recomposition targets the physical construction shape. It does not use an
`own`-published or data-`via` destination path to flatten construction
implicitly. Results that need explicit routing into another nested shape use
`-<>-` and, when needed, a reshape declaration.

#### Transformation `-<>-` performs actual mapping

`-<>-` is the protected transformation operation for cases requiring real
member mapping rather than one contiguous binary recast. It may rename,
reorder, gather noncontiguous members, change nesting, construct a destination,
or assign an existing destination.

For a newly declared destination, ordinary destination construction establishes
every resident member. Mapped inputs supply selected members; established
defaults may complete the rest.

For an existing destination, transformation performs ordered memberwise
assignment. Unmapped destination members retain their existing values. Unmapped
source members remain untouched. Panic never rolls the operation back or returns
partial state: a repairing helper resumes the blocked operation, otherwise the
process crashes gracefully.

Every explicit reshape mapping consumes both its source and destination
endpoints before automatic matching. Remaining unconsumed source and destination
members then receive same-name automatic matching. A swap is therefore valid:

```zax
SwapCoordinates :: reshape {
  x: y:
  y: x:
}
```

The explicit `x: y:` and `y: x:` entries prevent automatic `x: x:` or `y: y:`
mapping.

#### Transform overlap uses static conservative capture

Transformation defines overlap-safe by-value mapping without runtime range
tests.

The compiler first resolves the complete mapping. It then:

1. captures every mapped by-value source needed before a potentially
   overlapping destination write;
2. applies destination operations in their defined order; and
3. preserves reference binding rather than snapshotting a referenced pointee.

A statically visible swap or dependency cycle requires the corresponding
intermediate values. Proven-disjoint source and destination storage needs no
intermediate. When references or other aliases make overlap possible but do not
establish the exact relation, the operation uses a complete source temporary
unconditionally rather than emitting a runtime address-range test.

If the required source cannot be copied, moved, or otherwise captured, that
transformation is unavailable unless future work supplies an explicit
non-overlap contract. A direct same-member mapping requires no intermediate but
still invokes its selected assignment when that assignment has observable
behavior.

#### A reshape is a no-storage directional mapping declaration

A reshape is not an ordinary runtime type and contains no anchor:

```zax
MyPointMapping :: reshape {
  x: myX:
  y: myY:
}
```

Entries preserve Zax's source-to-destination mapping order:

```text
source path: destination path:
```

Nested mapping uses complete relative paths:

```zax
MyFlattening :: reshape {
  coord.x: x:
  coord.y: y:
  z: z:
}
```

A reshape has no instance storage, runtime identity, constructor, or direction
inference. It is checked when applied to concrete source and destination types.
Reversing a transformation requires another reshape declaration; the compiler
does not assume that one map is invertible.

`anchor` remains exclusive to contiguous compatibility recasting. A reshape
already names arbitrary semantic source paths, so embedding an anchor would be
redundant and would incorrectly imply binary-recast semantics. A future
source-path-prefix shorthand may abbreviate repeated reshape paths without being
called an anchor.

#### Composition data paths participate in mapping, not shape

`own` publication and place-preserving data `via` create alternate access paths
to resident places. They never add stored members, change direct or flattened
shape, alter layout, or create a second consumable value.

Accessible `own` and data-`via` paths may be used:

- as source paths for `>-`, explicit reshape entries, `-<>-`, and `anchor`;
- as destination paths for explicit reshape entries and `-<>-` assignment; and
- as destination designators for `-<>-` construction only when the
  transformation can establish the complete underlying physical member and
  every enclosing destination lifetime correctly.

Callable `via`, `preferred`, `expose`, abstract roles themselves, and generated
callable surfaces are not place-preserving data paths and do not participate.
Private access and complete-path visibility remain ordinary composition
requirements; structural mapping grants no friendship.

Automatic same-name transformation uses ordinary composition data lookup:

```zax
Engine :: type {
  rpm : Integer
}

Car :: type {
  engine own : Engine
}

Snapshot :: type {
  rpm : Integer
}

snapshot : Snapshot -<>- car
```

`Snapshot.rpm` may obtain its source through `car.rpm`, which canonicalizes to
`car.engine.rpm`. A renamed data route can similarly supply the visible name:

```zax
Car :: type {
  engine : Engine
  speed via engine.rpm
}

SpeedReading :: type {
  speed : Integer
}

reading : SpeedReading -<>- car
```

Path aliases canonicalize before consumption is tracked. If `car.rpm` and
`car.speed` both reach `car.engine.rpm`, using either consumes that physical
endpoint for this ordinary one-to-one transformation; the other alias cannot
automatically supply another destination. A future explicit duplication
operation may request multiple copies, but reshape does not infer it.

For a newly constructed destination, a published or routed destination name is
only a designator for its physical target. Transformation never silently
default-constructs a containing member and then assigns through the route. It
must construct that complete physical member, including nested state, from the
mapped inputs and established defaults or reject the mapping.

An anchor may use an alternate path to select an address, but a name-aware shape
comparison still uses the underlying physical declaration names. A renamed
data-`via` path cannot manufacture shape equality; use reshape when renaming is
the intended semantic operation.

#### Multiple transformed results use a transforming group boundary

The earlier candidate source-result form:

```zax
oldPoint: -<>- point:
```

is discarded. It reverses the standalone operator's visual source/destination
model and adds a per-entry operator where the complete routing group already has
one producer boundary.

Use `-<>-` in place of the routing group's ordinary `=` when the selected
results may require transformation:

```zax
produce final : (
  oldPoint : LegacyPoint,
  oldSize : LegacySize,
  status : Status
)() = {
}

consume(
  oldPoint: point:,
  oldSize: size:,
  status: -<>- produce()
)
```

The right side is the packed producer result shape. `-<>-` opens that shape and
transforms each independently selected result into its mapped destination.
Exact-identity entries such as `status` transfer directly without invented
reshaping. The producer executes once and every result retains its independent
transfer, posture, anchor, completion, and discard rules.

Typed capture uses the same boundary:

```zax
oldPoint: point : Point,
oldSize: size : Size,
status: -<>- produce()
```

A named reshape applies only to its result entry. The aligned representative
form preserves source-to-destination routing order:

```zax
oldPoint: reshape PointMapping point:,
oldSize: reshape SizeMapping size:,
status: -<>- produce()
```

An ordinary group whose entries need no transformation retains `=`.

For one ordinary expression value, transformed declaration initialization may
retain the concise destination/source form:

```zax
point : Point -<>- legacyPoint
```

A bare several-result invocation cannot occupy that one-value source position.
Select the intended result through routing. `>-`, `-<`, and `-<>-` therefore
retain distinct cardinalities:

- `>-`: one structural value supplies several mapping destinations;
- `-<`: several result slots form one structural value; and
- `-<>-`: one structural value transforms into one destination.

At a multiple-result routing boundary, the one `-<>-` applies that final
one-source/one-destination transform independently to every mapped pair; it does
not combine the producer's result slots into one implicit value.

#### Arithmetic reports use shared hidden identities

“Anonymous” describes the caller-facing name surface of generated arithmetic
reports, not a fresh identity for every operator or invocation.

The protected integer family defines one compiler-hidden report identity per
concrete integer identity and report schema. Conceptually:

```zax
I32WrappedOverflowReport :: type {
  value : I32
  overflowed : Boolean
}
```

The relevant `I32` addition, subtraction, and multiplication wrapped-report
operators may all return that same hidden identity through a compiler-internal
alias. `U64` receives another identity because its contained scalar type differs.
Combined, saturated, and transition reports use their own shared identities
when their member schemas differ.

The generated report identity owns protected `?report` and `!report` behavior.
An unrelated programmer-defined or anonymous value with coincidentally equal
members does not acquire those operations through shape alone. Callers may use
`type of` to create a source-visible alias to the hidden identity when needed.
Every report defaults to `compatible strict`.

#### Pointer-bearing slices remain pointer-design pressure

Pointers, references, optionals, unions, tracking components, and similar
semantic boundaries may participate as atomic leaves when their complete types,
qualifications, and representations match. Flattening never descends through
them.

A compatible copy or anchored region containing a pointer is not prohibited
merely because a pointer may refer into excluded source state. Destination copy
construction or assignment owns whether that pointer is copied, repaired,
retained, or rejected. A generated copy exists only when its member contracts
make the operation available.

The possibility that a copied pointer becomes dangling is general pointer-copy
and lifetime pressure made more visible by structural slicing. Whether such an
operation is safe, unavailable, or requires local unsafe responsibility belongs
to pointer and lifetime work. Structural compatibility must preserve that
pressure without inventing a second pointer model or prohibiting every
pointer-bearing conversion.

This issue is explicitly deferred rather than left as an unresolved structural
rule. Before archival, preserve in the existing pointer/lifetime raw owner:

> Structural slicing can separate a copied pointer from storage it references;
> destination copy behavior and future pointer-lifetime rules decide whether
> that copy is proved safe, unavailable, or locally unsafe.

A type with a custom lifecycle remains an atomic leaf and may participate
through exact matching. Flattening through its internals could bypass its copy,
ownership, or lifecycle contract and therefore requires a future explicit
type-owned bridge. No new general `flattenable` keyword is established here.

#### Promotion and evidence-consumption obligations

The active record intentionally retains legacy proposals and superseded
candidate reasoning as evidence. Lasting programmer-facing teaching must not
present those older forms as current alternatives after the aligned design has
replaced them.

Promotion must:

- begin with a usable programmer model and representative valid source rather
  than transcribing this material-dense finding ledger;
- explain the operators' visual mnemonic where it first helps a programmer;
- distinguish compatibility posture, anchor, coercive conversion, unsafe cast,
  decomposition, recomposition, transformation, and reshape by concrete effect
  and cost;
- remove superseded `directly`, memory/sliced/coerced posture, `become`, legacy
  split/combine, positional-decomposition, and runtime-reshape-type teaching
  once their useful evidence is fully dispositioned;
- retire each structural raw input after every useful idea has been promoted,
  deferred to a live owner, rejected, or superseded;
- move newly discovered pointer, reflection, analysis, generic, partial-type,
  and other future pressure into its applicable current owner or indexed raw
  destination before archival; and
- preserve review quality if any later task is delegated by using a
  task-suitable high-capability model for nuance-heavy reasoning and a genuinely
  separable scope.

Consumed raw material serves no continuing current purpose merely because it
records provenance. Historical provenance belongs in the eventual archived work
record after live meaning and future pressure have moved elsewhere.

#### Alignment-complete checks before the dry run

The core model, vocabulary, posture family, anchor model, safe/coercive/raw
boundaries, qualification behavior, tracking model, scalar boundary, generated
report identity, and three structural operators are aligned. The remaining
items are validation, exact disposition, or explicitly bounded future syntax
rather than unresolved competing semantic models:

- test recursive types and ensure flattening terminates at pointer/reference or
  another atomic boundary without requiring compiler-algorithm documentation;
- test private visibility, duplicate flattened leaf names, nested anchors,
  alternate paths to the same resident place, and inaccessible source paths;
- verify the aligned representative syntax for explicit retained local posture,
  default and overridden result anchors, named per-result reshape selection,
  source discard, and destination-default requests remains internally
  unambiguous;
- reconcile contextual packet argument shorthand with the current rule that a
  packet is not independently an expression value;
- trace source-evolution consequences for posture, default anchors, flattening,
  tracking components, scalar-format lists, shared report identities, and
  reshape maps;
- add the aligned pointer-copy pressure to the existing indexed pointer/lifetime
  raw destination;
- add the provisional scalar safe/coercive property lists to floating-point and
  numeric-family future input;
- preserve whole-type conformance, reflection, generics, partial types, ABI/FFI,
  and complete casting as explicit deferrals without letting them redefine the
  aligned model;
- determine the exact lasting owner set and whether one dedicated structural
  owner remains the best human reading path;
- disposition every useful part of both structural raw inputs and the relevant
  legacy casting, function split/combine, and type-definition material; and
- run the required complete documentation-fit dry run only after these
  remaining findings are reviewed and aligned.

## Dispositions and promotion dry run

### Dry-run result: PASS

The documentation-fit dry run completed on 2026-09-13.

PASS means the aligned findings have one coherent primary owner, focused local
integration points, live destinations for every deferred consequence, a
human-developer reading path, complete raw and legacy dispositions, and an exact
proposed promotion change set. It does not authorize promotion, archival,
closure, staging, committing, or any other repository change.

### Structure proposal

Retain the current flat `language/` directory and add one cohesive current
concept owner:

```text
language/structural-shapes-and-compatibility.md
```

Do not add a structural subdirectory, separate layout hierarchy, formal
specification area, compatibility matrix file, or additional router. The
concept is cohesive for programmers: identity-versus-shape, binary-compatible
postures, anchors, same-storage views, protected conversion, and the three
shape-changing operators need one mental model before their domain-specific
interactions make sense.

`index.md` remains the public router. It will add the new owner to the
task-oriented opening list and current-conceptual-design catalog.
`language/vision.md` will replace its “future structural rules” statement with a
concise route to the new current owner.

No current programmer-facing owner will cite this work item, its eventual
archive, or `project/raw/`. Deferred material remains discoverable only through
the project raw index and the future owner that activates it.

### Primary owner and teaching plan

`language/structural-shapes-and-compatibility.md` will own:

- the separation among type identity, direct stored shape, flattened shape,
  semantic scalar format, physical layout, operation permission, and whole-type
  conformance;
- the complete compatibility-posture family and default
  `compatible strict`;
- source-relative `anchor`, including zero-size and non-storage boundaries;
- safe protected shape/layout conversion, coercive `unsafe as`, and view-shaped
  `unsafe cast`;
- same-storage whole-root and anchored-reference behavior;
- qualification, transfer-stance, tracking-component, construction, assignment,
  and lifetime consequences at the structural boundary;
- decomposition `>-`, recomposition `-<`, transformation `-<>-`, and no-storage
  directional `reshape`;
- result-posture, result-anchor, and transforming result-routing behavior at the
  conceptual depth needed to use the feature;
- composition data-path participation without treating alternate paths as
  storage;
- anonymous structural values and shared hidden arithmetic-report identities;
  and
- programmer-visible costs, diagnostics, source evolution, corner cases, and
  explicit deferrals.

The owner will teach rather than transcribe the work record:

1. Open with distinct `Point`/`Coordinates` identities, equal physical shape,
   strict ordinary binding, and one explicit compatible use.
2. Establish identity, direct shape, flattened shape, scalar format, and layout
   in plain language before presenting the complete posture family.
3. Show posture-bearing result APIs and source-relative anchors with one
   single-result example before multiple-result routing.
4. Distinguish by-value construction, existing-destination assignment, and
   same-storage reference views with observable mutation and qualification
   examples.
5. Introduce `>-`, `-<`, and `-<>-` together with their visual mnemonic and
   one-to-many, many-to-one, and one-to-one roles.
6. Layer reshape, overlap capture, composition paths, tracking components,
   scalar families, generated reports, diagnostics, costs, and evolution after
   the ordinary model.
7. End with explicit future boundaries rather than interleaving raw generic,
   reflection, pointer, float, partial-type, ABI, or implementation speculation
   into common use.

Every invalid sample will carry an inline `// error`. Illustrative unresolved
formal grammar will be marked inline without weakening the aligned conceptual
source forms. The owner will not teach superseded `directly`,
memory/sliced/coerced postures, `become`, legacy `<-`/`->` split/combine,
positional decomposition, runtime reshape instances, or automatic structural
interchangeability.

### Local current-owner integration

The primary owner remains the only complete definition. Existing owners receive
the following bounded local material:

- `language/declarations-and-bindings.md` — declare compatibility-posture
  attachment and defaulting, explicit retained local posture, `:: reshape` as a
  non-value declaration, direct stored-member shape contribution, inferred
  posture non-propagation, and reflection handoff.
- `language/identity-types.md` — preserve outer identity independently from
  compatible underlying scalar/aggregate format; integrate whole-root
  same-storage views and additional-data constraints without duplicating the
  general compatibility model.
- `language/enums.md` — define directional enum-to-immediate-underlying
  compatibility, prohibit enum-to-enum safe or coercive structural recasting,
  and preserve admission/projection as separate operations.
- `language/composition.md` — define physical storage versus
  `own`/data-`via` mapping paths, physical-place canonicalization, construction
  viability, and aligned hidden root tracking-component behavior. `preferred`,
  callable `via`, `expose`, and abstract metadata remain nonparticipants.
- `language/qualifiers.md` — replace the deferred structural boundary with
  qualifier-exact leaf matching, safe non-weakening, anchored final-subregion
  behavior, whole-root varying-view conditions, and local unsafe coercion/cast
  boundaries.
- `language/lifetimes-and-references.md` — teach source-relative anchored life
  paths, zero-size conceptual places, whole-root versus interior compatible
  references, replacement invalidation, and temporary-result anchor lifetime.
- `language/transfer-stances.md` — teach whole-source stance preservation,
  anchored default `copy`, explicit terminal-region requirements, and
  transform-overlap capture without redefining compatibility.
- `language/construction-and-destruction.md` — integrate destination
  construction for compatible values, contextual packet arguments,
  recomposition/transform construction, physical nested-member completion,
  custom-lifecycle flattening boundaries, and rejection of direct lifecycle
  operations through same-storage views.
- `language/function-invocation.md` — own call/result mapping grammar for
  posture, anchor, `>-`, `-<`, transforming `-<>-` groups, named per-entry
  reshape, one-result consumption, contextual packet arguments, exact-identity
  preference, and ambiguity.
- `language/operators.md` — integrate protected structural forms with existing
  discovery, viability, expected-type, once-only evaluation, no-guessing, and
  result-flow behavior. Structural posture changes viability after discovery;
  it never widens receiver/operator discovery.
- `language/operator-catalog.md` — replace deferred legacy `<-`/`->` evidence
  with the exact protected `>-`, `-<`, `-<>-`, safe structural `as`, coercive
  `unsafe as`, `unsafe cast`, and `anchor` form catalog. Mapping-bound forms need
  no invented ordinary-expression precedence.
- `language/integers.md` — own current integer scalar safe/coercive properties
  at the accepted finite-integer depth and link future fixed-point/float
  refinement to its raw owner.
- `language/integer-operator-catalog.md` — replace unresolved anonymous-report
  identity with shared compiler-hidden identities per concrete integer and
  report schema, default strict posture, and report-owned `?`/`!`.
- `language/endianness.md` — replace provisional shape/storage terminology with
  scalar-format versus storage-envelope language while retaining explicit
  encode, decode, transcode, raw extraction, and raw adoption.
- `language/pointers-and-arenas.md` — distinguish structural pointer-leaf copying
  from view-shaped raw `unsafe cast` and hand unresolved pointer-copy validity
  to the indexed pointer mechanics input.
- `language/safety-and-analysis.md` — remain the exact panic owner and add only a
  useful handoff to the promoted reusable principle/term.
- `language/core-flow-control.md`,
  `language/declarations-and-bindings.md`,
  `language/function-invocation.md`, and `language/qualifiers.md` — correct local
  panic wording that could suggest exception-style continuation, rollback, or
  recoverable unwinding.
- `language/principles.md` — replace “future” structural-combination wording
  with the current explicit operators and add the reusable principle that panic
  is not an alternate control-flow path.
- `language/terms.md` — add concise cross-cutting entries for compatibility
  posture, compatibility anchor, reshape, and panic; distinguish compatibility
  anchor from receiver, type, and ownership anchors.

These integrations explain the local consequence and link to the complete
structural owner. They do not duplicate its full relation catalog.

### Deferred consequence destinations

Every material future pressure has a live destination:

- Whole-type no-storage conformance, generic structural constraints, deduction,
  and specialization remain in
  `project/raw/type-parameters-and-generics.md`.
- Reflection of posture, default anchor, direct/flattened shape, physical versus
  alternate paths, reshape declarations, tracking components, scalar formats,
  and shared hidden report identity remains in `project/raw/reflection.md`.
- Callable comparison beyond exact-identity preference and posture-enabled
  viability remains in `project/raw/callable-selection.md`.
- An explicit non-overlap contract for transforms that cannot use conservative
  capture remains in `project/raw/analysis-controls.md`.
- Pointer copies separated from referenced source storage remain in
  `project/raw/pointer-and-arena-mechanics.md`.
- Final fixed-point, integer recheck, and binary floating safe/coercive property
  lists remain in `project/raw/floating-point-maintainer-notes.md` and
  `project/raw/numeric-type-families.md`.
- Stored-member additions, layout closure, and compatibility invalidation by
  external type pieces remain in `project/raw/partial-types.md` and
  `project/raw/mutability-indexed-type-families.md`.
- ABI, FFI, packing, calling convention, and foreign layout contracts remain in
  `project/raw/interop.md`; exact Zax compatibility is not an ABI promise.
- General casting beyond the protected structural and raw-view forms remains
  indexed by `project/raw/feature-catalog.md` and evidenced by the retained
  nonstructural parts of legacy `casting.md`.
- Complete recursive-type metadata, generic canonicalization, compiler
  flattening algorithms, backend lowering, and optimizer representation remain
  future reflection/generic or implementation work; no implementation owner is
  created in this repository.

No deferred consequence depends on the eventual archived `021` record for live
meaning.

### Raw-input disposition

Both structural raw inputs are fully consumed by the aligned owner, focused
integrations, and deferred destinations:

- `project/raw/structural-shapes-and-compatibility-maintainer-notes.md` has no
  remaining design or deferred ownership role;
- `project/raw/structural-typing.md` has no remaining design or deferred
  ownership role; and
- `project/raw/README.md` marks both consumed pending work-item closure.

Promotion review found that the immutable initiating and reading-scope sections
of active `021` still link to both inputs. Deleting them before archival would
break the live working record. They therefore remain only as temporary
provenance until closure. In the same authorized closure change that archives
`021`, remove both files and both raw-index rows; the archived record may retain
its historical links under project guidance.

Every other live reference now routes current meaning elsewhere:

- `project/raw/reflection.md` will route explicit structural promotion to the
  new current owner and retain only reflection questions;
- `project/raw/feature-catalog.md` will route structural casting, same-storage
  views, and anonymous structural values to the new current owner while keeping
  complete general casting future;
- `project/raw/type-parameters-and-generics.md`,
  `project/raw/callable-selection.md`, `project/raw/partial-types.md`, and
  `project/raw/mutability-indexed-type-families.md` will state only the
  constraints the accepted structural model places on their future work; and
- the current raw index will continue to route the pointer, floating, numeric,
  reflection, analysis, generic, partial, mutability-family, and interop
  destinations.

No current language or future-design owner depends on either consumed raw file.
Their temporary retention is not a compatibility stub or duplicate current
teaching.

### Legacy-page disposition

Consume legacy material by value:

- `casting.md` — remove the superseded structural type-casting rules and
  `unsafe as` structural examples; replace them with a concise route to the new
  owner. Retain unresolved string conversion, nonstructural casting, and custom
  conversion evidence that the structural owner does not absorb.
- `functions.md` — remove the legacy split/combine sections and examples after
  their useful decomposition, recomposition, and multi-result behavior is
  taught by current owners; add a concise disposition route rather than
  retaining `<-`/`->` as alternate syntax.
- `type-definition.md` — remove or directly disposition only the
  compatibility-relevant claims that trivial structural values may be cloned by
  raw memory copy and route current anonymous structural behavior to the new
  owner. Preserve unique union, function-type, and other still-unreviewed type
  evidence.

No current owner will teach old forms merely to say they are obsolete.

### Exact proposed promotion change set

Add:

- `language/structural-shapes-and-compatibility.md`

Modify current public owners and routers:

- `index.md`
- `language/vision.md`
- `language/principles.md`
- `language/terms.md`
- `language/declarations-and-bindings.md`
- `language/identity-types.md`
- `language/enums.md`
- `language/composition.md`
- `language/qualifiers.md`
- `language/lifetimes-and-references.md`
- `language/transfer-stances.md`
- `language/construction-and-destruction.md`
- `language/function-invocation.md`
- `language/operators.md`
- `language/operator-catalog.md`
- `language/integers.md`
- `language/integer-operator-catalog.md`
- `language/endianness.md`
- `language/pointers-and-arenas.md`
- `language/safety-and-analysis.md`
- `language/core-flow-control.md`

Modify future-input routing and destinations:

- `project/raw/README.md`
- `project/raw/analysis-controls.md`
- `project/raw/callable-selection.md`
- `project/raw/feature-catalog.md`
- `project/raw/floating-point-maintainer-notes.md`
- `project/raw/interop.md`
- `project/raw/mutability-indexed-type-families.md`
- `project/raw/numeric-type-families.md`
- `project/raw/partial-types.md`
- `project/raw/pointer-and-arena-mechanics.md`
- `project/raw/reflection.md`
- `project/raw/type-parameters-and-generics.md`

Modify legacy evidence:

- `casting.md`
- `functions.md`
- `type-definition.md`

Retain consumed raw provenance until closure:

- `project/raw/structural-shapes-and-compatibility-maintainer-notes.md`
- `project/raw/structural-typing.md`

The promotion modifies neither file. Their deletion and raw-index row removal
move to the exact later closure set.

Update the active work record during promotion:

- `project/work/021-structural-shapes-and-compatibility.md` — record finding
  disposition and post-promotion validation without changing the immutable
  initiating input.

No other file is part of the proposed promotion. `README.md` already routes
public readers through `index.md`; `project/README.md`, archival movement, and
work item `022` belong to later closure rather than promotion.

### Promotion validation plan

After separately authorized promotion:

- verify every aligned finding appears in its primary or bounded local owner;
- verify the new owner opens with a usable programmer model and does not read as
  this ledger;
- verify every code example uses established declaration ordering, exact
  two-space structural indentation, and inline failure comments;
- verify no public/current page cites numbered work or raw input;
- verify only active `021` provenance and the temporary consumed raw-index rows
  reference the retained structural raw files;
- search live public documentation for superseded `directly`,
  sliced/coerced-memory posture, `become`, structural `<-`/`->`, positional
  decomposition, and runtime `type reshape` teaching and disposition each
  occurrence by value;
- verify compatibility `anchor` is distinguished from receiver, type, and
  ownership anchors;
- verify panic is never described as unwinding, rollback, catchable return, or
  continuation after partial state;
- verify current concept ownership remains unique across the structural owner,
  invocation, composition, identity, qualifier, lifecycle, and numeric owners;
- verify every deferred pressure remains indexed and has an activation
  condition;
- validate all live Markdown links and heading anchors, excluding historical
  archive contents as required by project guidance;
- run Markdown/whitespace checks;
- preserve the staged/unstaged review boundary; and
- confirm the final changed-file set exactly matches the separately authorized
  promotion set.

### Promotion applied and validated: PASS

The authorized promotion was applied on 2026-09-13.

The lasting human reading path now begins at `index.md`, reaches
`language/structural-shapes-and-compatibility.md`, and hands local consequences
to the declaration, identity, enum, composition, qualifier, lifetime, transfer,
construction, invocation, operator, numeric, endian, pointer, and safety owners.
The new structural owner teaches the programmer model and common source before
layering relation details, same-storage behavior, structural operators, mapping,
costs, diagnostics, evolution, and future boundaries.

Promotion incorporated:

- identity outside direct and flattened shape;
- default `compatible strict` and the complete safe posture family;
- non-propagating posture and posture-bearing result APIs;
- source-relative anchors, multiple-result anchoring, zero-size places, and
  non-storage rejection;
- safe shape/layout conversion, local coercive conversion, and view-shaped
  `unsafe cast`;
- qualification-exact same-storage whole-root and anchored reference behavior;
- transfer stance and tracking-component integration;
- contextual packet arguments without turning packets into values;
- visual and semantic teaching for `>-`, `-<`, `-<>-`, and `reshape`;
- transforming multiple-result groups;
- physical-place canonicalization for `own` and data-`via` mapping paths;
- integer scalar safe/coercive properties, directional enum participation,
  endian scalar-format distinctions, and shared compiler-hidden report
  identities; and
- repair-or-crash panic behavior without unwinding.

Deferred pressure moved to live indexed owners for pointer copying,
floating/fixed numeric formats, reflection, generics, callable comparison,
analysis non-overlap contracts, partial/mutability-indexed types, interop, and
remaining general casting.

Legacy structural casting and function split/combine teaching was removed and
routed to current owners. Compatibility-relevant type-definition wording no
longer claims that structural values may be cloned by raw memory copy.

#### Promotion-time dry-run correction

The two structural raw inputs are fully consumed, but the immutable initiating
and reading-scope sections of active `021` still link to them. Deleting them
during promotion would break the live work record. They and their consumed
raw-index rows therefore remain only until the separately authorized closure
archives `021`; that closure change will remove both files and rows. No current
language or future-work owner depends on their meaning.

This corrects the dry run's original deletion timing without changing the owner
map or PASS result.

#### Validation result

PASS:

- the promoted concern has one complete structural owner;
- current local owners contain only bounded integration and link to that owner;
- authority and implementation state remain conceptual and unimplemented;
- the new owner begins with concrete strict-identity and explicit-conversion
  examples rather than a finding ledger;
- invalid promoted examples carry inline `// error` comments;
- the three structural operators retain their visual mnemonic;
- no current owner cites numbered work or raw input;
- consumed structural raw files are referenced only by active-work provenance
  and their temporary consumed index rows;
- live relative Markdown files and heading anchors resolve;
- Markdown code fences and whitespace checks pass;
- superseded structural posture, `become`, positional decomposition, runtime
  reshape-type, and legacy split/combine teaching is absent from current
  language owners;
- panic is consistently repair-or-crash and never an unwinding path;
- every deferred consequence has a live indexed destination;
- no implementation mapping or ABI promise was introduced;
- the changed-file set matches the corrected authorized promotion scope; and
- the pre-existing staged baseline remains staged while this promotion remains
  unstaged for review.

Promotion does not authorize archival, work item `022`, staging, committing, or
pushing.

### Corrective promoted review

Human review of the promoted reading path exposed teachability and source-form
problems that the finding ledger had hidden. The following aligned corrections
supersede incompatible earlier working-record examples and were promoted under
the maintainer's corrective authorization.

#### Examples establish the programmer's problem first

Compatibility, posture, anchor, coercion, decomposition, recomposition, and
transformation teaching now:

1. defines the source and destination types;
2. shows the consumer prototype and its required type;
3. shows the ordinary call failing with an inline `// error`;
4. shows the smallest compatible or transforming operation succeeding; and
5. explains whether the operation constructs, assigns, aliases, adjusts one
   address, or performs member mapping.

The primary owner no longer asks readers to infer why an abstract
`source`/`Destination` operation is needed. The operator catalog uses explicit
`<source>` and `<DestinationType>` substitution patterns in tables rather than
making `source` appear to be a protected keyword.

Anchor teaching begins with a tagged point whose compatible `x`/`y` region
starts after `tag`, then shows a value containing two point regions so the
programmer must choose `.start.x` or `.end.x`. Posture non-propagation now shows
an immediate compatible result succeeding, an inferred capture failing under
default strict posture, and explicit one-use or retained posture succeeding.
Coercion now shows equal-width signed/unsigned reinterpretation succeeding while
safe layout and unequal-width coercion fail.

The structural owner links the existing
“Expressiveness serves clear intent” and
“Preference must be defensible, not guessed” principles and states their local
consequence:

> Zax does not guess intent. A compatible source still needs one concrete
> destination and one unambiguous source region or mapping.

#### Enum coercion is destination-directed

Safe enum-to-enum structural recasting remains unavailable. Coercive conversion
may target a relaxed or other enum whose ordinary admission accepts every value
of its compatible backing representation. A strict, flags, or otherwise
restricted destination remains ineligible; universal `unsafe from` does not
make its ordinary admission open.

#### Named packet mapping is explicit

The short named packet form is:

```zax
draw(point: [{ .x = 4, .y = 5 }])
```

The selected `point : Point &` parameter supplies the destination. The explicit
typed named form is:

```zax
draw(point: (: Point = [{ .x = 4, .y = 5 }]))
```

`point:` is named parameter mapping. `(: Point = ...)` is an anonymous typed
declaration expression. The positional forms remain valid but no longer obscure
that distinction in the opening example.

#### `reshape` is a declaration keyword only

`reshape` appears as a keyword only in:

```zax
MyMapping :: reshape {
  sourceName: destinationName:
}
```

Use sites name `MyMapping` as the no-storage middle operand of `-<>-`; no
`reshape MyMapping` use-site phrase exists.

#### Normalized `-<>-` forms

When both operands exist, `-<>-` is a complete update expression:

```text
<destination> -<>- <source>
<destination> -<>- <reshape> -<>- <source>
```

It updates the existing left destination, retains unmapped destination members,
and returns a reference without increasing authority.

When a declaration, assignment, parameter, or result supplies an unconstructed
destination hole, `=` remains visible:

```text
<destination context> = -<>- <source>
<destination context> = -<>- <reshape> -<>- <source>
```

That form constructs a complete transformed destination before ordinary
assignment/replacement when applicable; unmapped members use destination
defaults rather than retaining old state.

Transformation never applies to a complete multiple-result sequence. The final
producer boundary remains ordinary `= <producer>`, and each transformed result
is marked independently while preserving ordinary source-to-destination result
routing:

```text
<source-result label>: -<>- [<reshape> -<>-] <destination>
: -<>- [<reshape> -<>-] <destination declaration>
```

The leading bare `:` selects the current positional source-result cursor.
Unmarked scalar or other results continue to map normally. The rejected
`= -<>- <producer>` form and promoted
`<source>: reshape <mapping> <destination>:` form are removed.

#### Transfer teaching defines an interior region

Transfer-stance integration now defines a `Packet` with `header`,
`payloadSize`, `payloadChecksum`, and `trailer`. It uses adjacent
`payloadSize`/`payloadChecksum` members with no enclosing `PayloadSummary`
value:

```zax
consumePayloadSummary(packet) // error: complete Packet has another shape
consumePayloadSummary(packet.payloadSize) // error: one U32 is insufficient
consumePayloadSummary(packet anchor .payloadSize) // spans both sibling members
```

The structural owner similarly contrasts `drawPoint(tagged.x)` with
`drawPoint(tagged anchor .x)` and uses flat start/end coordinate fields to show
why an explicit anchor chooses among several possible regions. This establishes
why `anchor` exists rather than presenting it as another spelling for ordinary
member projection.

Coercion teaching also replaces the single `I32`/`U32` example with complete
`SignedRegisterBlock` and `RawRegisterBlock` types plus a consumer requiring one
aggregate raw-register view. The example now distinguishes failed strict
binding, failed safe layout compatibility, successful equal-width coercive
layout, and failed unequal-width coercion. This demonstrates aggregate
bit-preserving reinterpretation rather than making coercion look like an
overcomplicated scalar cast.

#### Corrective validation result

PASS:

- the structural owner now gives every unfamiliar mechanism a complete source
  and destination context, consumer prototype, failing ordinary use, and
  succeeding structural form where that sequence teaches the distinction;
- posture non-propagation, anchor, coercion, reshape, and transformation have
  representative failure/success examples;
- the operator catalog uses substitution patterns in explanatory tables rather
  than pseudo-keyword identifiers;
- enum coercion is destination-directed through ordinary open admission;
- construction-packet teaching distinguishes named parameter mapping from an
  anonymous typed declaration expression;
- `reshape` appears as a keyword only in `:: reshape` declarations;
- full-operand and contextual `-<>-` forms preserve their destination/source
  roles, while named and positional result entries preserve the established
  source-to-destination routing order;
- no group-wide transforming producer form remains;
- the existing clear-intent/no-guessing principles are linked from the
  structural owner;
- transfer teaching defines the selected interior region before applying
  `last`;
- live Markdown links, heading anchors, code fences, and whitespace pass;
- no current owner cites numbered work or raw input; and
- the pre-existing staged review boundary remains unchanged while these
  corrective edits remain unstaged.

### Closure

Work item `021` closed after maintainer review, promotion, corrective teaching
review, and validation PASS.

The promoted structural model is owned by
`language/structural-shapes-and-compatibility.md` and its bounded current-owner
integrations. Every future consequence has an indexed raw destination.

Promotion was preserved in commit `68c2e0c` before closure so the original
maintainer notes and supplemental raw proposal remain recoverable from Git
history. Both inputs are fully consumed and retire with this work item.

Work item `022` begins the separately bounded review of floating and fixed-point
scalar families. This record contains no analysis of that next concern.
