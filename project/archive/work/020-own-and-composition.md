# 020: `own` and composition

| Field | Value |
| --- | --- |
| Status | Historical working material / non-normative / audit-only |
| Work Item | `020` |
| Created | 2026-09-10 |
| Completed | 2026-09-12 |
| Owns | The bounded review defined by the fixed initiating input |
| Does Not Own | Class inheritance, a complete interface or trait system, generalized structural subtyping, complete generic or partial-type behavior, reflection, callable capture, or compiler lowering |

## Non-authority notice

This file is the historical record of completed work item `020`. It is
non-normative and audit-only. Current language owners, not this record, define
the promoted conceptual design.

## Fixed initiating input

This section records the information aligned when work item `020` was created.
It is intentionally incomplete and must not be rewritten as work develops.

### Initiating concern

Recover and refine a coherent, data-oriented model for `own` and composition
without introducing class inheritance or hiding the contained relationship.

The language maintainer supplied a
[refreshed `own` and composition mental model](../raw/owned-composition-new-mental-model.md)
as the intended primary conceptual starting point for this work. Treat that
material as the refreshed candidate model to understand, test, and refine. Its
role as the initiating model does not make every detail accepted language
design; promotion still requires ordinary discussion, alignment, documentation
fit, and explicit authorization.

Earlier composition material is superseded as a mental model. Read it as
supplemental evidence to identify concrete requirements, examples, failure
cases, or consequences absent from the refreshed model. Do not preserve a
conflicting legacy rule merely because it was previously documented, and do not
silently merge old behavior into the refreshed model.

The review should establish whether and how a separately stored member can
participate in its containing value's programmer-visible surface while
preserving explicit composition, identity, lifetime, and cost.

### Motivating pressure

Zax's foundational direction favors composition over inheritance, explicit data
shape and representation, structural relationships, and visible cost. Legacy
material proposes `own` as a way to expose contained state and behavior through
an outer value, but it mixes several potentially distinct ideas:

- ordinary named containment;
- member and operation exposure;
- implicit selection of one contained value;
- ambiguity resolution across several contained values;
- identity and representation relationships;
- interface-like substitution;
- outer and inner conversion; and
- local lexical member exposure.

Those ideas must not be accepted or rejected as one package. The refreshed
mental model supplies the primary reconstruction. Older material may reveal
additional pressure, but it no longer supplies the default model.

### Known assumptions

- Zax favors explicit composition and does not gain class inheritance through
  `own`.
- A contained value has ordinary construction, lifetime, qualification,
  transfer, replacement, and destruction behavior unless this review aligns a
  concrete additional rule.
- Current identity-type behavior remains owned by
  `language/identity-types.md`; composition must not silently erase a distinct
  identity boundary.
- Current function selection, result routing, operator resolution, and
  declaration lookup remain in force unless a concrete composition consequence
  requires an aligned integration change.
- The refreshed mental model is the primary candidate input. Legacy composition
  material is supplemental and superseded.
- No compiler implementation exists in this repository.

### Known inclusions

- The ordinary named-containment baseline and the exact additional purpose, if
  any, served by `own`.
- The stored-place and identity relationship between an outer value and each
  owned member.
- Whether the contained member remains directly and explicitly nameable.
- Which stored members, functions, operators, phrases, type-level operations,
  or other declarations may participate in an exposed surface.
- Visibility, filtering, suppression, and owner authority.
- Precedence, ambiguity, and explicit disambiguation when an outer value or
  several owned members provide matching names or operations.
- Qualification, access capability, transfer stance, source-state, and
  mutability consequences.
- Construction, replacement, lifetime, destruction, and disposal consequences.
- Function-input, function-result, operator-resolution, and conversion pressure
  created by an exposed surface.
- Interaction with current transparent aliases and distinct identity types.
- Multiple owned members, nested composition, and source evolution.
- Cost visibility, diagnostics, source stability, and formatting.
- Complete disposition of the refreshed model, the legacy composition page,
  the older indexed owned-composition input, and the surviving
  `using value own` member-exposure evidence.
- Lasting documentation ownership and legacy/raw disposition.

### Known starting boundaries

- Class inheritance, virtual base classes, vtables, or compiler-hidden object
  hierarchy.
- A complete generalized interface, trait, or runtime-polymorphism facility.
- General structural equivalence, layout compatibility, subtyping, or
  truncating structural transfer beyond direct consequences of the reviewed
  composition relationship.
- Complete type-parameter, generic-specialization, partial/open-type, or
  reflection behavior.
- General callable capture, closure representation, or function composition.
- General local lexical member injection. The legacy `using value own` proposal
  receives a disposition but does not reopen resource lifetime or grant
  implicit local projection.
- Compiler lowering, ABI implementation, or optimizer strategy.

These boundaries do not erase consequences. Preserve concrete pressure in a
live owner or indexed raw destination rather than leaving it only in the
eventual archive of this work item.

### Intentionally unresolved framing

- Whether `own` denotes a semantic relationship, surface-generation sugar, an
  explicit forwarding declaration, or some combination.
- Whether the named contained place always remains directly accessible.
- Whether exposure includes data, behavior, type-level declarations, or only an
  explicitly selected subset.
- Whether an outer value can satisfy a parameter or result expecting an owned
  member's type, and whether doing so is projection, conversion, or selection.
- How locally declared outer behavior interacts with exposed behavior.
- How ambiguity is diagnosed and explicitly resolved.
- Whether exposure is fixed when the outer type is completed or can change when
  a source type changes.
- Which relationships are representation-preserving and which require ordinary
  construction or transfer.
- Whether existing `own` spelling remains coherent after the model is tested
  across qualifiers, transfer, lifetime, identity, and operation selection.

### Initial stopping guidance

Stop when the work has:

- established a coherent ordinary-composition and `own` mental model;
- dispositioned every material claim in the refreshed and legacy inputs;
- defined storage, identity, exposure, lookup, ambiguity, access, transfer, and
  lifecycle consequences;
- integrated every accepted consequence with current invocation, operator,
  declaration, identity, qualifier, transfer, lifetime, and construction
  owners;
- preserved structural typing, generics, partial types, reflection, callable,
  casting, and implementation pressure without prematurely designing those
  complete facilities;
- identified exact lasting owners and legacy/raw changes; and
- completed the required documentation-fit dry run.

Do not promote findings, archive this work item, begin work item `021`, or
redesign generalized structural typing, interfaces, generics, partial types,
reflection, or callable capture without the separately required discussion,
alignment, and authorization.

## Reading scope

### Required

- [Documentation architecture](../documentation.md) - governs focused reading,
  decision maturity, promotion, deferral, and closure.
- [Refreshed `own` and composition mental model](../raw/owned-composition-new-mental-model.md)
  - supplies the maintainer's intended primary candidate model and must be read
  before reconstructing `own`.
- [Language vision](../../language/vision.md#data-oriented-organization-and-composition)
  - owns the accepted foundational preference for composition over inheritance.
- [Declarations and bindings](../../language/declarations-and-bindings.md) -
  owns stored declarations, lookup, visibility, shadowing, and initialization
  framing.
- [Qualifiers](../../language/qualifiers.md) - owns access capabilities,
  qualification, mutability, and place behavior.
- [Construction and destruction](../../language/construction-and-destruction.md)
  - owns construction packets, replacement, lifecycle tracking, and terminal
  destruction.
- [Transfer stances](../../language/transfer-stances.md) - owns copy, deep, move,
  last, and source-state behavior.
- [Lifetimes and references](../../language/lifetimes-and-references.md) - owns
  life paths, references, places, and storage-duration consequences.
- [Function invocation](../../language/function-invocation.md) - owns callable
  selection, input and result binding, complete prototypes, routing, and
  elision.
- [Identity types](../../language/identity-types.md) - owns transparent aliases,
  distinct identities, projection, admission, and current exposed/opaque
  behavior.
- Legacy root `composition.md` (now removed) - supplied superseded public
  composition, `own`, ambiguity, interface, slicing, and conversion claims; its
  history remains evidence, not the current mental model.
- Older [owned-composition input](../raw/owned-composition.md) - supplies
  superseded identity-exposure, signature-transformation, filtering,
  same-storage-view, casting, and reflection pressure; retain only independently
  justified additional insight.

### Consequence-driven

Read these when a concrete question reaches their ownership boundary:

- [Operators](../../language/operators.md),
  [operator phrases](../../language/operator-phrases.md), and
  [mixfix operators](../../language/mixfix-operators.md) for exposed-operation
  declarations, lookup, fencing, and ambiguity.
- [Source structure](../../language/source-structure.md) and
  [terms](../../language/terms.md) for type-body placement, parsing, naming, and
  vocabulary.
- Legacy [type definitions](../../type-definition.md) and
  [casting](../../casting.md) for concrete type-body or conversion evidence not
  already preserved by the required inputs.
- Raw [callable selection](../raw/callable-selection.md) for exact-prototype or
  `own` surface-selection pressure.
- Raw [structural typing](../raw/structural-typing.md) for shape, equivalence,
  layout, conversion, or subtyping pressure.
- Raw [partial types](../raw/partial-types.md) for suppression, extension, or
  owner-authority pressure.
- Raw [reflection](../raw/reflection.md) for exposed-versus-local declaration
  identity and source-evolution visibility.
- Raw [mutability-indexed type families](../raw/mutability-indexed-type-families.md)
  for qualifier-specialized representation or surface pressure.
- Raw [function composition and chaining](../raw/function-composition-and-chaining.md)
  only when callable capture or generated-callable pressure cannot remain
  separate.
- Raw [cross-cutting audit](../raw/cross-cutting-audit.md) when an aligned
  finding creates or resolves a repository-wide audit entry.

### Audit-only

- Archived [work item `019`](../archive/work/019-using-and-scoped-resource-lifetimes.md)
  only when targeted provenance for the retired `using value own` spelling is
  necessary beyond the live raw inputs.
- Earlier archived work only when a concrete provenance or regression question
  cannot be answered from current owners and live raw input.

## Initial working posture

Begin with the refreshed mental model and reconstruct its programmer-visible
claims in plain language. Test each claim against ordinary composition and the
current type, identity, declaration, qualifier, transfer, lifetime,
construction, invocation, and operator models.

Use legacy material to challenge coverage, not to restore superseded framing.
For each additional legacy insight, record why the refreshed model does not
already cover it and whether it belongs in `020`, a current owner, another raw
destination, or retirement.

Do not promote candidate behavior while reconstructing it. Discussion,
alignment, documentation-fit dry run, and explicit promotion authorization
remain separate steps.

## Working record

### Current programmer model

The reconstruction is complete enough to state plainly. Ordinary named
containment remains the baseline. Three independent declaration-side words
extend a member relationship, and each is separately opt-in:

| Word | What it adds |
| --- | --- |
| `own` | Publishes the member's *stored instance-member names* on the outer value |
| `preferred` | Lets the outer value satisfy a place where the member's type is already expected |
| `expose` | Publishes mechanically eligible instance functions and operators through generated thin forwarding |

Any subset is legal. `own` is not the price of requesting projection or
forwarding, and neither projection nor forwarding publishes data. When two or
three words appear together, their canonical relative order is
`own preferred expose`.

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

myDrive final : ()() = {
  myCar : MyCar

  myCar.engine.rpm = 2000   // physical path, always available
  myCar.rpm = 2000          // published name: same place, same cost
  myCar.start()             // generated thin forwarding to engine.start()
  myCar.wheels = 4
}
```

Two further mechanisms serve composition without publishing anything:
`abstract` roles fulfilled by `fulfill` declarations let a contained type
require or optionally verify declarations from its immediate container, and
`outer cast` / `tracked outer cast` / `unsafe outer cast` cast outward to a
container from one of its member places.

Nothing published is new storage. Every published name is an access path to the
same stored place, with the same identity, lifetime, qualification, and cost as
the physical path it abbreviates. Zax gains no inheritance, no vtables, and no
hidden object hierarchy.

### Status of this record

The findings below are **aligned** for the current review scope and are not
implemented. Their promotion and subsequent post-promotion corrections are
recorded below. Current conceptual owners now incorporate the complete model,
including optional roles and explicit proof, tracked, and unsafe
outer-provenance operations. This file remains non-authoritative working
material.

Superseded alternatives from earlier passes have been removed rather than kept
as history; see *Corrections to earlier entries in this record* for the ones a
reviewer might otherwise expect to find. The final review questions are closed
below; deliberately future work is collected under *Deferred adjacent
concerns*.

Provenance markers: **[refreshed]** for
[the refreshed mental model](../raw/owned-composition-new-mental-model.md),
**[legacy]** for the removed root `composition.md`, and **[raw-older]** for
[the older owned-composition input](../raw/owned-composition.md). Named current
owners carry their own authority.

The selected source spellings for this work are `own`, `preferred`, `expose`,
`via`, `tracked via`, `unsafe via`, `via family`, `abstract`,
`abstract optional`, `abstract relaxed`, `abstract optional relaxed`, `fulfill`,
`= existing`, `= existing family`, `= forbidden`, `= forbidden family`,
`outer`, `outer tracked`, `outer cast`, `tracked outer cast`, and
`unsafe outer cast`, plus `intent<redundant-outer-tracking>` for deliberately
retaining tracked semantics under selected-contract proof.

### Aligned: `own` and the published data surface

**Baseline.** Ordinary named containment is unchanged and remains sufficient on
its own. `own` adds exactly one thing: publication of the member's stored
instance-member names into the containing type's member surface. It creates no
storage, no identity relationship, and no separate lifetime.

**Data only.** `own` publishes stored instance members. It publishes no
functions, no operators, no nested types, no aliases, no constants, and no
`once` declarations. Behavior crosses a composition boundary only through an
explicit `expose` (below).

**Physical paths always survive.** Publication never removes or renames the
physical route. `myCar.engine.rpm` stays legal and denotes the same place as
`myCar.rpm`.

**A direct outer member wins unconditionally.** A stored member declared
directly in the container owns its outer name outright and suppresses the
published short path. It does not change the inner member's storage, value, or
reachability.

```zax
MyPayload :: type {
  id : MyPayloadId
  bytes : MyBuffer
}

MyEnvelope :: type {
  payload own : MyPayload
  id : MyEnvelopeId       // wins outright; MyPayload.id publishes no short path
}

myInspect final : ()(myEnvelope : MyEnvelope readonly &) = {
  use(myEnvelope.id)            // MyEnvelope.id
  use(myEnvelope.payload.id)    // unchanged inner storage
  use(myEnvelope.bytes)         // published from MyPayload
}
```

**Collisions are ambiguous, never resolved by order.** Two or more owned routes
publishing one name make that short name ambiguous at use. There is no
first-wins, declaration-order, or source-order rule. Physical paths remain
available, and an explicit route may be selected.

**`via` data routes.** An explicit no-storage data surface route selects, and
may rename, exactly one physical path. It introduces no storage and preserves
the target's qualification, lifetime, transfer stance, and access rather than
widening any of them:

```zax
MyReading :: type {
  value : Float
  timestamp : Integer
}

MySensor :: type {
  primary own : MyReading
  backup own : MyReading      // `value` and `timestamp` collide

  value via primary.value     // select one physical route
  age via backup.timestamp    // select and rename another
}

myRead final : ()(mySensor : MySensor readonly &) = {
  use(mySensor.value)              // primary.value
  use(mySensor.age)                // backup.timestamp
  use(mySensor.primary.timestamp)  // always available

  use(mySensor.timestamp)          // error: ambiguous between primary and
                                   // backup; no source-order winner
}
```

**Visibility is never laundered.** A private stored member of the contained
type never enters the set published by `own`. More generally, a published route
cannot be more visible than every physical path segment it abbreviates. A
public inner member behind a private owned carrier therefore does not acquire a
public short path, and `via` cannot target a place inaccessible to its declaring
context. Neither mechanism grants friendship or private access.

A private carrier may nevertheless use `own` for its other compile-time job:
activating that member's abstract roles. Public declarations written directly
in the container may explicitly fulfill those private role paths. Their own
visibility does not reveal the carrier, its stored data, or any otherwise
private route; this is explicit role satisfaction rather than publication.

**Nesting is transitive.** An owned member that itself owns a member publishes
transitively. Every explicit path at every depth remains. A collision at any
depth is ambiguous at that depth. Behavior still crosses only where an explicit
`expose` is written on each boundary.

**Publication never walks semantic indirection.** `own` publishes through
resident by-value containment, not through a pointer, reference, optional,
variant, or another wrapper whose payload has a distinct presence, lifetime, or
selection operation. It may publish a direct stored member whose value happens
to be a pointer, but recursive publication stops at that pointer rather than
entering its pointee. Auto-allocation does not make the pointee a resident part
of the pointer-containing object:

```zax
Position :: type {
  coordinates : Coordinates * = @
}

Sprite :: type {
  position own : Position
}

myUse final : ()(mySprite : Sprite readonly &) = {
  if !mySprite.position.coordinates
    return

  use(mySprite.coordinates)            // published pointer member
  use(mySprite.position.coordinates.x) // explicit checked pointer traversal
  use(mySprite.x)                      // error: `own` does not hide traversal
}
```

The same boundary prevents ordinary outer casting from travelling from the
pointee back to the pointer-containing object. `expose` likewise operates on
the declared member value; it does not automatically delegate through a pointer
or other semantic wrapper to its payload. A future explicitly indirect
delegation facility would be a separate feature with visible presence,
aliasing, lifetime, and cost rules.

**Local and flow-scope `own` is rejected.** `own` applies to stored type
members. It is not available on a local declaration or in a flow-control scope,
and `expose` is never legal in ordinary local or flow scope. The refreshed
input's opening local-scope sugar **[refreshed]** is deliberately excluded from
the current model and recorded as a separate future scoped
projection/destructuring concern.

**`shadowable` is unrelated to `own`.** It plays no part in publication,
precedence, or collision. It keeps its existing meaning: a one-level forward
lexical permission, which here lets a nested or inline type scope deliberately
reuse an outer lexical name. Without permission, that reuse is an error,
because an unmarked reuse makes `_.name` source misleading to a reader:

```zax
MyOuter :: type {
  count shadowable : Integer
  total : Integer

  MyInner :: type {
    count : Integer   // legal: the outer declaration granted one level

    report final : ()() readonly = {
      use(_.count)    // unambiguously MyInner.count
    }
  }

  MyOther :: type {
    total : Integer   // error: MyOuter.total is not shadowable
  }
}
```

**Legacy `override` is retired completely.** **[legacy]** `override` conflated
four distinct jobs: replacing a contained default, declaring a requirement,
replacing behavior, and disambiguating between two sources. Requirements move
to `abstract`/`fulfill`; behavior replacement is an ordinary direct declaration
that wins over any exposed one; disambiguation uses qualified `fulfill` paths
or `via`. The default-replacement role is **rejected outright**, not relocated.

**No construction reaches through `own`.** A container may not treat published
fields as part of its own construction shape:

```zax
MyAnimalData :: type {
  animal : String = "bear"
}

MyContainer :: type {
  data own : MyAnimalData
}

myContainer : MyContainer = [{
  .animal = "mouse"
  // error: `animal` is a published path, not a direct MyContainer member
}]
```

The container packet may supply constructor arguments or a complete value for
its direct `data` member. The contained value must be established through
`MyAnimalData`'s selected constructor rather than by reaching through `data`:

```zax
myContainer : MyContainer = [{
  .data = makeMouseData()
}]
```

This supersedes the member-initializer proposal recorded in the first pass.
It does not alter ordinary direct-member packet initialization. For an actual
direct member, `.text = "mouse"` is one direct initialization performed when
member-order construction reaches `text`; it is not default construction
followed by assignment. It suppresses ordinary default initialization and
completes before the enclosing constructor body. If the selected constructor
also explicitly invokes `_.text.+++()`, the two construction requests conflict
and the declaration is an error.

### Aligned: `expose` and the published behavior surface

**Form and canonical local ordering.** `own`, `preferred`, and `expose` are
independent. `own` adds data publication and abstract-role activation,
`preferred` adds expected-type projection, and `expose` adds behavior
forwarding. Any one, any pair, or all three may appear. Within this
composition-modifier group, the canonical relative order is:

```zax
member own preferred expose : Type
```

The later grammar and formatting sweep may place this complete group relative
to unrelated declaration and qualifier words; it does not reopen the group's
internal order.

`engine expose : MyEngine` therefore forwards eligible behavior without
publishing `MyEngine`'s stored data, while `engine own : MyEngine` publishes
eligible data without forwarding behavior. `engine preferred : MyEngine`
provides expected-type projection without either publication. This independence
also explains identity exposure: behavior forwarding does not require an
ordinary data-publication relationship.

**What it does.** `expose` automatically publishes only *mechanically eligible*
instance functions and operators, through compiler-generated thin forwarding.
Fixed-offset pointer math, unambiguous input projection, and zero-offset
wrappers that optimize away entirely are all acceptable implementations. This
is not class inheritance, not a vtable, not callable capture, and not an ABI
claim.

**Eligibility filter.** A contained operation is automatically exposed only
when its mapping is mechanically decidable:

- resultless functions are eligible;
- results in an orthogonal domain pass through unchanged (a `Boolean`
  comparison stays `Boolean`);
- a contained value, reference, or pointer result may pass through unchanged as
  that contained type, including a non-`self` reference;
- a receiver-reference or receiver-pointer result may instead map to the outer
  receiver only with an explicit, verified `self` contract;
- reference inputs are eligible when any required expected-type projection is
  supplied by an unambiguous `preferred` route;
- a copied contained result may pass through unchanged, but is never rewritten
  into a complete outer value because no wrapper may invent the outer's other
  resident state;
- constructors, replacement constructors, destructors, generated `copy`, and
  generated assignment families are ineligible because container lifecycle
  targets the physical stored shape;
- no exposure performs runtime outer casting;
- an ambiguous slot mapping is not eligible.

An unchanged by-value result is returned directly as the contained operation's
declared type; the wrapper does not construct a complete outer value and then
slice it. Any later expected-type projection belongs to the caller's separate
binding. These pass-through cases make an `expose everything` or `expose copy`
mode unnecessary in the current model. Private, lifecycle, `once`, ambiguous,
and unsafe mappings remain excluded rather than becoming opt-in automatic
exposure.

Ordinary overload ambiguity remains a demand-time error rather than a
declaration-time one. Two generated mappings that are equally good are an
error, not a ranking exercise. A direct outer declaration with the exact shape
always wins over an exposed one. Exposure can never strengthen or launder
visibility, access, qualifiers, transfer stances, or provenance.

```zax
MyEngine :: type {
  start final : ()() = {                                   // eligible
  }

  isRunning final : (result : Boolean)() readonly = {      // eligible
  }

  tuneTo final : (result self : MyEngine &)(
    profile : MyProfile readonly &
  ) = {                                                    // eligible: self
  }

  cloned final : (result : MyEngine)() readonly = {        // eligible unchanged:
  }                                                        // outer result is MyEngine
}
```

**`once` is never automatically exposed.** A `once` function has two call
modes - a type call where `_` is `Nothing` and an instance call where `_` is
present - so no single generated wrapper is mode-correct. A programmer may
still expose one deliberately with a mode-clear `via`, a unique `= existing`,
or a written body.

**Lifecycle is never exposed or routed as container lifecycle.** `expose`,
`via`, and `= existing` cannot turn a member constructor, replacement
constructor, destructor, generated `copy`, or generated assignment family into
container lifecycle. The container's own lifecycle body may invoke member
lifecycle explicitly under ordinary lifecycle authority.

**`self` result contracts.** `self` on a reference or pointer result is an
explicit alias and provenance contract: every returning path returns exactly
the invocation receiver `_`. The compiler verifies it. It is never inferred
into an undeclared public contract, and declaration-side `self` concerns
reference or pointer place identity, never a copied result. When a claim is
true but unprovable, the repair is a localized `unsafe` assertion, not an
`intent` acknowledgement - intent does not authorize provenance.

### Aligned: explicit forwarding

**`via` names a target.** The selected form is:

```zax
MyCar :: type {
  engine own : MyEngine

  start final : ()() = via engine.start
  ignite final : ()() = via MyCar.engine.start   // full path also legal
}
```

The relative member path is ordinary inside the type; the fully type-qualified
path stays available where it reads better. There is no `_.member.operation`
selector form.

`via` is a statically selected **thin adapter**, deliberately distinct from
today's plain compatible-visible-prototype aliasing in
[function invocation](../../language/function-invocation.md): the adapter may
perform fixed-offset adjustment and input projection, which prototype aliasing
does not. A full written body remains the fallback whenever the mapping needs
non-mechanical policy or construction.

An operator target uses the same structured name as an operator declaration:

```zax
operator binary '+' final : (
  result : Power
)(
  rhs : Fuel
) readonly =
  via engine.operator binary '+'
```

The visible prototype still selects one overload within that operator family.
There is no separate string-like or punctuation-only operator selector.

**Singular selection stays singular.** `via` must select one source operation
and one complete mechanical mapping. If permitted adaptation leaves several
source overloads viable, the declaration is ambiguous rather than choosing by
order or implementation preference. No additional overload-id syntax is added.
A private exact helper in the same container is the deliberate escape hatch:

```zax
startExact private final : ()(fuel : Fuel readonly &) =
  via engine.start

start final : ()(tank : Tank readonly &) =
  via startExact
```

The helper's exact visible prototype selects the source operation; the public
declaration then targets that exact helper. This requires no modification to an
external contained package, introduces no callable storage or dispatch, and may
optimize to the same code as a direct route. Zax does not promise an exact
instruction count. A callable `via` declaration is an explicit new API
declaration, like a written public wrapper body that calls a private helper, so
its own visibility may be broader than that helper's visibility when the target
is accessible in the declaration context. It does not make the helper itself
visible. This deliberate callable rule does not permit `own`, automatic
`expose`, or a data `via` to publish a private stored path.

**`= existing`.** May locate one unique mechanically compatible existing
operation and one unique complete mapping through permitted member surfaces.
If more than one candidate qualifies, it is an error; no order or ranking guess
is made. It may generate the same thin wrapper `via` would.

**Family adoption.** The singular forms have family counterparts:

```zax
reset final : ()() = existing family
restart final : ()() = via family engine.reset
```

`= existing family` discovers and adopts one unique source callable family. No
family or several qualifying source families is an error. `= via family`
explicitly names the source family and may rename it on the outer surface.
Operator families use the same structured target:

```zax
operator binary '+' final : (
  result : Power
)(
  rhs : Fuel
) readonly =
  via family engine.operator binary '+'
```

The written prototype is an exact anchor for family selection, not a wildcard
prototype. After the source family is selected, each eligible member is mapped
independently under the ordinary mechanical rules. A one-off conversion or
other special adaptation that happens to make the anchor viable is not
generalized across the family. A source-family overload that has no unique
mechanical outer mapping is diagnosed rather than guessed or silently omitted.

Adopting a family deliberately makes later additions to that source family
outer-surface compatibility events. This is stronger and more evolution-
sensitive than selecting one operation, so it must be written explicitly.

**`= forbidden`.** Permanently fences exactly one generated or exposed
signature. It cannot be bypassed by fallback:

```zax
MyCar :: type {
  engine own expose : MyEngine

  stop final : ()() = existing
  start final : ()() = forbidden   // this exact signature is never available
  reset final : ()() = forbidden family
}
```

**`= forbidden family`.** The written prototype is an exact anchor that
establishes the outer callable name or structured operator form being fenced.
The `family` word broadens the prohibition to that complete outer family: every
generated, exposed, or adopted signature with that visible name or operator
form is blocked, regardless of which contained source family could have
supplied it. A same-spelled source is therefore not exempt merely because it has
different provenance.

The fence is name-wide on the outer surface, not a hidden originating-family
filter. This is the intended reason to choose `family` over repeated exact
`= forbidden` declarations. A later direct declaration with that same outer
family name or structured operator form conflicts with the fence rather than
bypassing it. The fence remains compile-time surface control and creates no
callable, vtable entry, or runtime behavior.

### Aligned: forwarding inputs, results, and transfer stances

**Automatic exposure does not rewrite input types.** An exposed operation keeps
the contained type's by-copy inputs. Automatic `expose` never silently turns a
`MyEngine` parameter into a `MyCar` parameter.

**Explicit forwarding may project an outer parameter.** A declared `via` or
`= existing` may accept an outer by-value parameter and project it to the
selected contained input only through a `preferred` route, or through an
explicitly written path in a body. `own` alone never supplies expected-type
input projection. The visible outer `copy`, `move`, or `last` construction
happens normally at the boundary. Once the wrapper owns the complete outer
parameter, its selected member projection is offered as `last`, so ordinary
`last` to `move` to `copy` fallback and resource reuse apply. The wrapper then
destroys the remaining outer parameter in a valid terminal-member state.

This can still cost one extra member copy, and that cost is visible at the
declaration. A custom destructor that is incompatible with terminal member
state is diagnosed; such a type needs a custom wrapper.

```zax
MyFuel :: type {
  refill final : ()(source : MyFuel mutable writable & move) = {
  }
}

MyTank :: type {
  fuel preferred : MyFuel
  shell : MyShell

  refill final : ()(source : MyTank) = via fuel.refill
  // outer MyTank parameter constructed normally, projected to MyFuel as
  // `last`, remaining MyTank destroyed in valid terminal-member state
}
```

**`deep` is never upgraded.** An outer by-value `deep` parameter keeps the
member mapping at `deep`. It is never silently promoted to `last`, and it may
be extremely costly.

**Reference-shaped mappings preserve the caller's authorization exactly.** A
`move` mapping stays `move`; it does not become `last`.

**Cost location.** Full-container copy occurs at the visible boundary; the
member mapping is wrapper work. No promise is made about whether the copy is
performed on the caller or callee side.

### Aligned: `preferred` projection

**Separate mechanism, separate word.** Ambient expected-type projection is
separated from `own` and named **preferred projection**, spelled `preferred`.
It is legal with or without `own` or `expose`.

**What it does.** When an outer value is offered where the member's type or
complete shape is *already expected*, preferred projection may select that
member. It applies to expected-type binding - call inputs, typed
initialization, and typed binding - and not to open-ended receiver, member, or
operator discovery. An exact type match always beats projection. The direction
is outer to member only. A reference projection preserves path, lifetime, and
qualifications; a by-value projection slices under the applicable stance. The
explicit path is always available.

```zax
MyCar :: type {
  engine preferred : MyEngine
  wheels : Integer
}

tune final : ()(target : MyEngine readonly &) = {
}

myTune final : ()() = {
  myCar : MyCar

  tune(myCar)          // preferred projection selects myCar.engine
  tune(myCar.engine)   // explicit path, always available
}
```

**Several preferred members are legal.** Different target types are fine, and
overloads expecting those different types may become ambiguous at a use:

```zax
MyRig :: type {
  engine preferred : MyEngine
  radio preferred : MyRadio
}

configure final : ()(target : MyEngine readonly &) = { }
configure final : ()(target : MyRadio readonly &) = { }

myConfigure final : ()(myRig : MyRig readonly &) = {
  configure(myRig)          // error: ambiguous; two preferred routes are viable
  configure(myRig.engine)   // explicit route resolves it
}
```

**Same base type, different capabilities, is legal.** Ordinary viability and
partial-order selection decide when one route is uniquely viable or dominant;
cross-axis or nondominated overlap is a use-site ambiguity. This includes
mutable/immutable and readonly/writable pressure. No score is invented.

**Indistinguishable routes are an early error.** Two direct preferred
projections with the same normalized type - transparent aliases included - and
the same complete defaulted shape are an error when the containing type
completes, not at a later use:

```zax
MyTwin :: type {
  first preferred : MyBuffer
  second preferred : MyBuffer
  // error at MyTwin completion: indistinguishable preferred projections
}
```

**Nested projection is opt-in at every boundary.** A transitive route projects
only when every boundary on it is marked `preferred`. Converging transitive
routes are a use-site ambiguity; there is no shortest-path and no
declaration-order winner. An `own` boundary may publish an inner data name, but
it does not create a preferred route. Conversely, `preferred` may project
through a physically named member whose data is not published.

**Operator participation begins after discovery.** `preferred` may participate
in operand mapping once an already discovered operator candidate supplies an
expected operand type. It never causes the outer receiver to discover
operators on the preferred member, widens candidate-tree formation, swaps
operands, or rescans source. A direct operand match still beats projection, and
several viable projected mappings remain subject to the ordinary partial order
and ambiguity rules. Generic deduction and structural matching remain separate
future integration work.

**Bounded effect.** `preferred` affects expected-type projection only. It never
influences published-name collisions, behavior exposure, reverse outer
casting, or exact-match priority. Every automatic expected-type mapping,
including one used by an explicit forwarding wrapper, follows `preferred`
rather than treating `own` as an implicit conversion request.

### Aligned: `abstract` roles and `fulfill`

**What `abstract` is.** Compile-time role-requirement metadata, activated only
by an immediate `own`. It creates no storage, no implementation, no callable
hook, no vtable, no ABI, no outer reference, and no ability for inner code to
call its container. After the immediate container fulfills a role, the
requirement does not propagate to an outer owner of that container.

**Direct fulfillment only.** A role must be fulfilled by a declaration written
directly in the immediate container. Directness records responsibility and
attribution; for a value role it does not require the declaration to introduce
new storage. A name merely published from another owned member never fulfills a
role accidentally.

**`fulfill` names the roles.** Each named target must be an active abstract
role and must be compatible. Every role must be explicitly named; a declaration
that fulfills none is an error. One declaration may fulfill several compatible
roles; incompatible roles conflict.

```zax
MyContract :: type {
  start abstract : ()()
  label abstract : String
  handle abstract : (result self : outer &)()
}

MyWindow :: type {
  contract own : MyContract

  begin fulfill contract.start final : ()() = {
  }

  caption fulfill contract.label : String

  onEvent fulfill contract.handle final : (result self : MyWindow &)() = {
    return _
  }
}
```

Several roles at once use the settled comma list:

```zax
  start fulfill primaryContract.start, backupContract.start final : ()() = {
  }
```

The activating carrier may be private:

```zax
MyType :: type {
  contract own private : MyContract

  start fulfill contract.start final : ()() = {
  }
}
```

The container may name its own private physical path while checking
fulfillment. The carrier and its data remain private, while the fulfilling
declaration chooses its own visibility. This does not make `private abstract`
legal and does not publish the carrier through `own`.

**A data `via` may fulfill a value role.** A direct no-storage route may name a
role explicitly and fulfill it with resident storage supplied by another
contained implementation:

```zax
PipelineContract :: type {
  buffer abstract : Buffer
  flush abstract : ()()
}

BufferedPipeline :: type {
  buffer : Buffer

  flush final : ()() = {
  }
}

MyPipeline :: type {
  contract own : PipelineContract
  implementation : BufferedPipeline

  buffer fulfill contract.buffer via implementation.buffer
  flush fulfill contract.flush final : ()() = via implementation.flush
}
```

The data route must resolve statically and unambiguously to one accessible
resident stored place. It may select, rename, and safely narrow a same-place
view, but it cannot construct, copy, convert, use `preferred`, perform an outer
cast at runtime, or produce a computed temporary. Its complete type,
qualifications, lifetime, and access must satisfy the role without inventing
capability or falsifying type-side truth, and route cycles are errors. The
target may lie on a nested explicit stored path; an automatically published
short name alone never supplies fulfillment.

**Renaming is deliberate.** The abstract qualified path is the role identity;
the fulfilling declaration's own name is the actual outer surface. `caption`
above fulfills `contract.label` and publishes `caption`. This is not a
name-sensitive ABI or structural lookup contract; any future literal
structural-name requirement is a separate concern.

**`outer` is a bounded contextual type spelling.** Inside abstract requirement
types and prototypes only, `outer` denotes the immediate containing type whose
`own` activates the requirement. It is not generics, and it provides no runtime
access to the container. The event-handler role above is the motivating case.
The general bare untyped wildcard form `e abstract :` **[refreshed]** is
rejected for the current model; its motivating use is covered by `outer`.

**No ordinary declaration-side posture or private visibility on abstract
metadata.** Abstract metadata has no declaration place, so ordinary
declaration-side words such as `final` do not apply. An abstract role must also
be inspectable by the immediate container whose `own` activates it, so
`private` is invalid rather than a narrower role visibility. Qualifications
belonging to the required complete type are fine:

```zax
MyContract :: type {
  start abstract final : String   // error: abstract metadata has no
                                  // declaration place
  label abstract : String final   // legal: qualification of the required type
}
```

Compatibility uses the ordinary safe same-place and compatible-prototype
rules. Transparent aliases may normalize and a view may safely narrow
authority, but fulfillment cannot invent qualifier capability or use a
constructor, conversion, `preferred` projection, or other value-producing
adaptation to manufacture compatibility. A data `via` is therefore
place-preserving, while a function `via` may perform only the already permitted
thin mechanical adaptation. The fulfilling declaration chooses its own
visibility and declaration-side posture and must still meet the complete
required type or prototype.

**`abstract relaxed` opens omitted qualifier axes deliberately.** An ordinary
abstract requirement resolves omitted qualifier axes normally, so omission
still chooses the language defaults. A relaxed requirement instead leaves only
its otherwise defaulted qualifier axes open for fulfillment. `relaxed` is
requirement-matching metadata, not declaration-place posture:

```zax
ObserverContract :: type {
  observe abstract relaxed : ()()
}

MyObserver :: type {
  contract own private : ObserverContract

  observeReadonly fulfill contract.observe final : ()() readonly = {
  }

  observeWritable fulfill contract.observe final : ()() mutable writable = {
  }
}
```

For a value role, relaxation applies only to the role's outer required value
layer. For a callable role, it applies only to omitted receiver qualifier axes.
Any explicitly written qualifier remains required. Relaxation does not recurse
into parameter or result types and does not loosen the base type, arity,
indirection, transfer stance, labels, or provenance.

An ordinary exact role has exactly one fulfillment. A relaxed role may have
several distinct qualification-specialized fulfillments, as above, so a
container can explicitly demonstrate several safe views of the same required
shape. Every declaration must name the role itself and satisfy it
independently; duplicate normalized signatures remain an error. Multiple
fulfillments do not create runtime dispatch or merge several callables into one
implementation.

**`abstract optional` changes only the minimum fulfillment count.** An optional
role remains a named signature-verification target, but its activating
container may leave it unfulfilled. If a declaration uses `fulfill`, the normal
path, category, type, qualification, access, and provenance checks all apply.
The role supplies no default body, storage, hook, dispatch, or runtime presence
query, and a compatible declaration without `fulfill` does not satisfy it
accidentally.

Optionality and relaxation are independent:

- `abstract` requires exactly one fulfillment;
- `abstract relaxed` requires one or more distinct
  qualification-specialized fulfillments;
- `abstract optional` permits zero or one; and
- `abstract optional relaxed` permits zero or more distinct
  qualification-specialized fulfillments.

The canonical combined order is `abstract optional relaxed`.

**Requirements never merge silently.** One declaration may satisfy two
identical roles only when both paths appear in its `fulfill` list. Conversely,
no declaration satisfies an unlisted role merely because its shape matches.

### Aligned: identities

Identity `expose` uses the same eligibility filter as member `expose`. A
representation-trivial identity receives every eligible mapping. An identity
carrying additional resident data receives only the subset that an analogous
member container could safely expose; carrying extra data alongside `expose`
is not banned outright.

The boxed underlying value sits at byte offset zero. Additional resident data
prevents a copied underlying result from being rewritten as a complete identity
result. The underlying value may still pass through unchanged as its own type,
just as non-`self` references and pointers may. Resultless operations,
orthogonal-domain results, and proven reference or pointer `self` mappings also
remain eligible.

Exposure verifies mechanical mapping only. It does not verify user-defined
cross-member or cache invariants; the owner accepts those invariant and
source-evolution responsibilities and may fence a signature with `forbidden` or
supply a custom wrapper.

This replaces the concern recorded in the first pass that
[identity types](../../language/identity-types.md) cites owned composition for
`expose`: the analogy holds, because `expose` is exactly the mechanism both
use.

### Aligned: outer casting and `outer tracked`

**Spelling family.** Contextual `outer`; the operations `outer cast`,
`tracked outer cast`, and `unsafe outer cast`; and the type capability
`outer tracked`:

```zax
MyEngine :: type outer tracked {
  rpm : Integer
}
```

`managed` is retired for this meaning because it collides with managed pointer
ownership in
[pointers and arenas](../../language/pointers-and-arenas.md) and with managed
variants in [switch](../../language/switch.md). The RTTI spelling is avoided so
no implementation representation is promised.

**The target is a member path.** A cast target must be an exact resident stored
member path. It is never a bare type, a function, a `final` function, a `once`
declaration, an alias, or a generated surface route. The path both resolves
duplicate members and supplies the offset:

```zax
MyCar :: type {
  engine own : MyEngine
  spare : MyEngine
}

myOuterCast final : ()(input : MyEngine &) = {
  myFast : MyCar & = input unsafe outer cast MyCar.engine

  myChecked : MyCar & ? = input tracked outer cast MyCar.engine
  if ?myChecked
    use(myChecked.)

  myBad := input outer cast MyCar   // error: target must be an exact resident
                                    // stored member path
}
```

**Three operations, three contracts.** Plain `outer cast` crosses one immediate
boundary and returns a non-optional reference only when the selected language
contract proves the exact named origin at that site. `tracked outer cast`
explicitly uses the contained type's `outer tracked` placement capability and
returns an optional result. `unsafe outer cast` uses the fixed member offset
under programmer-asserted provenance; a false claim is undefined behavior.
Temporaries and expired origins are ineligible for all three.

**Contract-required static proof may remove tracking.** A selected language
contract must define which proof classes every conforming compiler recognizes.
The initial composition proof candidate is site-specific: every origin that can
reach the cast operand is proven to be the exact resident member path named by
the target. When the selected contract requires and establishes that proof,
plain `outer cast` produces a non-optional result and needs neither placement
metadata nor a runtime check. Without that proof, it is rejected rather than
silently becoming a tracked optional operation.

Whole-program proof that a type is instantiated only as one particular member
is sufficient, but not necessary. Two same-typed members, arbitrary external
parameters, callbacks, raw-pointer ingress, opaque code, or an exported API that
accepts the standalone member type may prevent the proof. External code's
unrelated standalone values do not matter when no such value can reach the cast
site. A library may therefore prove an internal cast even when importers can
construct the member type separately, provided its public boundary never lets
those values enter the relevant flow.

This is an initial language-contract model to test against implementation
experience, not a claim that the eventual specification already contains a
complete analysis algorithm.

**The selected contract controls whether `unsafe` is required.** Compiler
cleverness alone cannot change source validity:

- if the selected mainline language contract classifies this case as proved,
  every conforming compiler must accept the non-optional unmarked operation,
  `unsafe` is a redundant hard error that must be removed, and deliberately
  retaining the optional tracked operation requires
  `intent<redundant-outer-tracking>`;
- if that contract does not classify the case as proved, the unchecked
  non-optional operation still requires `unsafe` for mainline validity even when
  one compiler independently proves the claim true; the checked optional
  operation is instead written `tracked outer cast`; that compiler may optimize
  or issue an advisory, but it may neither reject the retained assertion merely
  as redundant, accept its omission as mainline source, nor require intent
  around the tracked operation; and
- a compiler may offer a stronger private or shared extension contract, but it
  may make the additional proof canonical and reject `unsafe` only when source
  explicitly selects that extension contract.

A compiler that demands `unsafe` for a case the selected contract requires it
to prove is nonconforming with that contract. A compiler that silently demands
removal because of an unselected stronger analysis would likewise create an
unportable dialect. These limits do not protect a false assertion: an
implementation that proves the asserted provenance impossible must still reject
known-invalid source. The same contract-relative rule applies to statically
proven result provenance through ordinary, `tracked`, and `unsafe via`.

**One boundary at a time.** An outer cast crosses a single immediate boundary.
Nested outer casting repeats the cast. Possible full-path sugar remains future
work and must never imply arbitrary ancestor discovery.

**Outer tracking is compiler-owned placement state.** Where
`tracked outer cast` or `tracked via` is selected, the metadata records
resident placement; it is not user payload copied unchanged. A standalone
instance and a `Container.member` instance have different placements.
Copying or moving into a destination establishes the destination's placement;
moving a complete container preserves its internal immediate relationships;
replacement ends the old resident and establishes new metadata, and references
to the old resident remain invalid. This entails representation plus
construction, copy, move, replacement, and destruction bookkeeping rather than
a single stored pointer. The implementation representation is deliberately
unspecified here. `outer tracked` explicitly permits those hidden storage and
lifecycle costs. A tracked operation has checked optional semantics and may
perform runtime work; ordinary as-if optimization may remove a redundant
metadata read only when no observable behavior changes. A contract-proved plain
operation requires neither tracking nor runtime checking. Retaining the tracked
operation under selected-contract proof requires
`intent<redundant-outer-tracking>`, which preserves its optional contract but
does not force a physical metadata read. Unsafe raw byte relocation cannot be
assumed to preserve a valid tracked relationship; ordinary lifecycle rules must
establish the destination placement. No additional composition-specific
lifecycle restriction is presently required.

**Provenance in forwarding.** Proven `self` forwarding needs no cast at all.
Ordinary `via` may produce a non-optional outer result when the selected
language or extension contract proves its origin. `tracked via` explicitly
generates checked outer casting, retains an optional outer result, and accepts
the tracked cost and failure mode. When selected-contract proof makes ordinary
`via` available, the tracked form requires
`intent<redundant-outer-tracking>`. Automatic exposure and `= existing` never
generate tracked outer casting. An arbitrary non-self reference to an unproved
non-optional outer result requires localized `unsafe via`. A merely smarter
compiler may advise that the assertion is redundant but cannot change the
portable spelling or require the intent category. Intent acknowledges the
defined tracked choice but cannot authorize provenance. A future intent
diagnostic for deliberately choosing the unsafe form when a tracked alternative
exists is safety policy, not composition semantics.

### Aligned: shared lifecycle, evolution, and principles

**Places and lifecycle.** A published path is the same stored place with the
same identity and lifetime as its physical path. Ordinary construction,
destruction, replacement, and transfer rules apply unchanged. Construction
packets and lifecycle operations target the physical stored shape, never
published aliases - which is the same rule that rejects reach-through
construction above.

**Source evolution risks are real and must stay visible.** Adding a stored
member can create a published-name collision. Adding behavior changes the
exposed set. Adding a preferred route can create a use-site ambiguity. Adding
an overload to a family adopted by `existing family` or `via family` is an
outer-surface compatibility event. Changing a role can invalidate existing
fulfillments. Adding a required role can break a container that does not fulfill
it. None of these is ever resolved by source order; each surfaces as a
diagnostic.

**Broader principles this work confirms**, recorded without inventing umbrella
doctrine:

- no hidden inheritance is introduced by any part of this model;
- cost stays explicit, including wrapper and projection cost;
- ambiguity is an error rather than a guess; and
- a compile-time wrapper may still perform runtime pointer adjustment, so
  "compile-time" does not mean "free."

### Final review closure

No conceptual composition-model question remains open within the bounded scope
of `020`. The original review and the post-promotion boundary review
established:

1. `own`, `preferred`, and `expose` are independent member modifiers; any
   subset is legal, and `own preferred expose` remains their canonical relative
   order while placement among unrelated declaration words awaits the general
   grammar and formatting sweep.
2. `own` publishes only resident by-value containment. It does not traverse
   pointers, references, optionals, variants, or other semantic indirection.
3. `preferred` alone controls expected-type projection, including automatic
   input mapping for forwarding. `own` does not create preferred candidates.
4. `expose` forwards mechanically eligible behavior without requiring data
   publication. Safe contained values, references, and pointers may pass
   through unchanged; only a verified reference or pointer `self` contract may
   remap a result to the outer receiver.
5. Singular `via` and `= existing` require one operation and one complete
   mapping. Ambiguous explicit routing may be split through a private exact
   helper rather than gaining another overload-id syntax.
6. `via` may target structured operator names. `= existing family` adopts one
   uniquely discovered source family, while `= via family path` explicitly
   selects and may rename one source family. Each member must map
   mechanically; family evolution is intentionally an outer compatibility
   event.
7. Exact `= forbidden` fences one signature. `= forbidden family` is a
   name-wide outer-surface fence, not an originating-source-family filter.
8. Direct, explicit, place-preserving data `via` may fulfill abstract value
   roles; automatic publication cannot fulfill anything.
9. Ordinary abstract roles resolve omitted qualifier axes normally and require
   one fulfillment. `abstract optional` permits that role to remain unfulfilled
   while retaining explicit signature verification when used.
   `abstract relaxed` independently leaves only otherwise defaulted outer value
   or receiver qualifier axes open and may have distinct
   qualification-specialized fulfillments.
10. A private `own` carrier may activate roles that public direct declarations
    fulfill, without publishing the carrier or making private abstract metadata
    legal.
11. Direct-member packet initialization constructs the member once, suppresses
    its ordinary default initialization, and conflicts with an explicit
    constructor-body construction of that member. Published paths never flatten
    the outer packet.
12. Preferred operand mapping occurs after operator discovery, never by
    discovering operators through the preferred member.
13. Generalized parameter-origin result contracts remain future lifetime and
    callable-contract work rather than a prerequisite for receiver-specific
    `self`.
14. Outer casting remains one boundary at a time. Plain `outer cast` requires
    selected-contract exact-origin proof and returns a non-optional result.
    `tracked outer cast` explicitly uses the programmer-visible `outer tracked`
    capability and returns an optional result; under selected-contract proof it
    requires `intent<redundant-outer-tracking>`. `unsafe outer cast` remains the
    unchecked provenance assertion; `via` uses the same three-way distinction
    for outer-result mapping.
15. Portable `unsafe` classification belongs to the explicitly selected
    language or extension contract, not to incidental compiler cleverness.
    Stronger unselected proof may optimize or advise but cannot make portable
    source ill-formed or require an intent acknowledgement.

### Deferred adjacent concerns

Each records a destination, what reactivates it, and the constraint it places
on `020`.

- **Scoped projection and destructuring** (the rejected local/flow-scope
  `own`).
  *Destination:* a future scoped-projection concern; the nearest existing raw
  index entry is [pattern matching](../raw/pattern-matching.md), and a
  dedicated raw placeholder may be preferable.
  *Activation:* concrete pressure for local member projection, including the
  retired `using value own` spelling **[raw-older]**.
  *Constraint:* `020` must not reintroduce local publication implicitly through
  `own`, `expose`, or `using`.

- **Structural shape, layout, and equivalence**, including offset-zero boxing
  and outer-tracking layout effects.
  *Destination:* [raw structural typing](../raw/structural-typing.md).
  *Activation:* any promise about size, alignment, base address, or
  substitutability.
  *Constraint:* `020` states programmer-visible behavior only; it settles no
  layout contract.

- **Family adoption and owner extension authority.**
  *Destination:* [raw partial types](../raw/partial-types.md).
  *Activation:* partial or external declarations attempting to extend,
  suppress, or replace an owner's exposed or adopted family.
  *Constraint:* `020` may not invent a partial or extension mechanism;
  `= forbidden` fences one exact signature, `= forbidden family` fences one
  complete outer visible-name or operator-form family, and family adoption
  never uses source order.

- **Reflection identity of published, exposed, fulfilled, and directly declared
  operations.**
  *Destination:* [raw reflection](../raw/reflection.md).
  *Activation:* any tool needing to enumerate a type's surface.
  *Constraint:* `020` should say what a published name *is* - an access path,
  not a declaration - because reflection cannot decide that itself.

- **Callable-selection integration for `preferred` projection and generated
  wrappers.**
  *Destination:* [raw callable selection](../raw/callable-selection.md), with
  eventual behavior in
  [function invocation](../../language/function-invocation.md).
  *Activation:* promotion of preferred projection or any preference-table work.
  *Constraint:* projection must fit the existing partial order without a
  score, exact match must keep beating projection, and operator discovery must
  complete before preferred operand mapping.

- **Preferred projection in generic and structural contexts.**
  *Destination:*
  [raw type parameters and generics](../raw/type-parameters-and-generics.md)
  and [raw structural typing](../raw/structural-typing.md).
  *Activation:* generic deduction, specialization, or structural matching that
  receives an outer value with one or more preferred routes.
  *Constraint:* `020` permits projection only after a concrete expected
  type or shape exists; it does not let projection invent a deduction target or
  widen discovery.

- **Whole-type abstract contracts.**
  *Destination:*
  [raw type parameters and generics](../raw/type-parameters-and-generics.md)
  and [raw structural typing](../raw/structural-typing.md).
  *Activation:* pressure to declare a type as a no-storage required shape and
  use that contract in constraints, structural matching, or composition.
  *Constraint:* member-level `abstract` remains compile-time role metadata;
  `020` does not turn an abstract type into inheritance, runtime interface
  storage, or a vtable.

- **Qualifier-specialized surfaces.**
  *Destination:*
  [raw mutability-indexed type families](../raw/mutability-indexed-type-families.md).
  *Activation:* exposing an operation family whose exact qualifier-complete
  prototypes differ.
  *Constraint:* `020` must not assume one prototype per operation.

- **Generalized result-origin contracts.**
  *Destination:*
  [lifetimes and references](../../language/lifetimes-and-references.md) and
  future callable-contract work.
  *Activation:* an opaque or separately expressed callable must promise that a
  reference result originates from a particular input rather than receiver
  `self`.
  *Constraint:* `020` proves receiver remapping only through `self`; other
  non-optional outer remapping remains explicit and `unsafe` until a broader
  origin contract exists.

- **Nested checked outer-cast sugar.**
  *Destination:* the future-work boundary in
  [current composition](../../language/composition.md).
  *Activation:* repeated immediate `outer cast` operations become a material
  usability problem.
  *Constraint:* outer casting remains one immediate boundary at a time, and any
  sugar must not imply arbitrary ancestor discovery.

- **Indirect delegation through pointers or other wrappers.**
  *Destination:* a future composition and pointer/optional integration review.
  *Activation:* concrete demand to publish or forward through a pointee,
  optional payload, variant selection, or another semantically indirect place.
  *Constraint:* ordinary `own` and `expose` do not hide presence checks,
  dereference, allocation, aliasing, payload selection, lifetime, or
  pointer-replacement effects.

- **Composition proof classes and unsafe controls** (`unsafe via`,
  statically proven `outer cast`, and redundant-unsafe diagnostics).
  *Destination:*
  [raw safety](../raw/safety.md) and
  [safety and analysis](../../language/safety-and-analysis.md).
  *Activation:* formalizing the initial site-specific proof candidate, revising
  it from implementation evidence, or defining a standard or extension
  language contract that mandates more proof.
  *Constraint:* provenance is authorized by `unsafe`, never by `intent`;
  portable source validity follows the explicitly selected contract rather than
  unselected compiler analysis.

- **Terminology load on the word "own."**
  *Destination:* [raw cross-cutting audit](../raw/cross-cutting-audit.md).
  *Activation:* promotion touching `transfer-stances.md`,
  `declarations-and-bindings.md`, `using.md`, or `pointers-and-arenas.md`,
  which already use "owned" for by-value storage, life paths, resource
  entries, and pointer ownership.
  *Constraint:* `own` is kept as the member keyword; `020` may not rename
  anything in a current owner.

- **Forward references already promised by current owners.**
  `identity-types.md` (several places, including `underlying place` and
  "complete owned-composition transformation"), `terms.md` (the
  "owned-composition boundary" in its `underlying place` entry),
  `construction-and-destruction.md` (identity admission boundary), and
  `integers.md` all defer to this work.
  *Activation:* immediate.
  *Constraint:* those references must be satisfied or explicitly redirected
  before this work closes.

### Legacy and raw disposition status

- **Behavior auto-publishing by bare `own`** **[legacy]** and its
  `own-relationship-access-ambiguous` identifier: superseded by filtered
  explicit `expose` plus ordinary demand-time ambiguity.
- **`override` obligation role** **[legacy]**: superseded by
  `abstract` + `fulfill`.
- **`override` default-replacement role** **[legacy]**: rejected, not
  relocated.
- **Bodyless declarations as composition requirements**: superseded by explicit
  `abstract` roles and `fulfill`. A bodyless direct callable remains an
  unavailable declared shape and does not become a composition role
  implicitly.
- **Qualified `myInterface1.start override := { }` disambiguation**
  **[legacy]**: superseded by qualified `fulfill` paths and `via`.
- **`unsafe outer of` / `outer of` spelling and `unsafe-outer-of-ambiguous`**
  **[legacy]**: superseded by member-path `unsafe outer cast` / `outer cast`.
  The ambiguity diagnostic is eliminated by construction, because the exact
  member path resolves duplicates.
- **Signature-transformation pressure** **[raw-older]**: partly resolved by the
  filtered expose eligibility rules, `self`, `via`, and `preferred`. What
  remains live there is the qualifier, provenance, and filtering material.
- **Local `own` sugar** **[refreshed]**: deferred out of the current type
  composition model.
- **Data-only `own`** **[refreshed]**: retained, and augmented by the separate
  `preferred` and `expose` opt-ins.

### Defects in the refreshed input needing repair

Recorded for repair of the raw file rather than silently reinterpreted. Several
of these examples are superseded by the aligned model regardless of their
defects.

- `anotherType own : :: Type { ... }` uses `Type` where anonymous type syntax is
  `:: type`.
- `myContainer.b = "hello" // the MyContainer.b is really MyContainer.c` - the
  comment contradicts the example.
- `a = 1 // ... due to `myContainer ow`n`` - broken inline code span.
- In the downcast example, several result member paths do not match their
  types: `dB1Upcast.a.a`, `dC1Upcast.b.b`, `dBar1Upcast.b.b`, and
  `dManaged1Upcast.b.b`.
- `doDowncast` passes ten arguments but omits a comma before `myManagedPtr`.
- `myBBCRefRef` and `myBBCAltRefRef` forward an `A` operation from a `B`-shaped
  prototype while their comments discuss `C.b`.
- The intent category is spelled both `container-downcast-return-results` and
  `container-downcast-return-result`. Both are superseded: provenance now
  requires `unsafe via`, not an acknowledgement.
- `Bar bar1;` is C++ declaration syntax; `makeMeABar` is declared returning
  `BarType` rather than `MyBarType`; and the final `unsafe outer cast` example
  omits its operand.
- Two identical `### reference `unsafe outer cast` and `outer cast`` headings;
  the second appears to be the `outer cast` section.
- The `shadowable` explanation paragraph is duplicated verbatim under
  *shadowing solution 2* and *shadowing solution 3*, and the *solution 3* copy
  discusses `own`, which that example does not use.

### Corrections to earlier entries in this record

Removed rather than retained, and listed so a reviewer does not look for them:

- **The A/B/C behavior-publication framing is withdrawn.** The question is
  answered: bare `own` publishes no behavior, and behavior publication is the
  separate, filtered, explicit `expose` opt-in.
- **The speculation that `final` was a mistaken `once` reference is wrong.**
  The exclusion of behavior from bare `own` was deliberate, motivated by
  slicing, provenance, and wrapper constraints. Those constraints are now
  handled by the expose eligibility filter rather than by a blanket exclusion.
  The separate `once` exclusion has its own reason: dual call modes.
- **The first-wins publication candidate is withdrawn**; collisions are
  ambiguous, and `via` supplies the explicit route.
- **The member-initializer proposal for legacy `override` defaults is
  withdrawn** as reach-through construction overreach.
- **The "implicit by-value slicing" open question is resolved**: nothing
  projects implicitly without `preferred`, and even then only into an already
  expected type.
- **The earlier D1-D10 decision list is superseded** by the aligned findings and
  the final review closure.

### Teaching-debt observation and feedback correction

The first pass found that *owned declaration* in
[transfer stances](../../language/transfer-stances.md) and
[declarations and bindings](../../language/declarations-and-bindings.md) could
be mistaken for the `own` composition keyword. The initial promotion attempted
to preserve and define that phrase. Maintainer review found the definition more
confusing rather than more explanatory.

- both affected passages now explain stance propagation directly in terms of a
  by-value declaration and contrast it with a reference alias;
- [terms](../../language/terms.md) no longer invents *owned declaration* as a
  glossary term; and
- [raw cross-cutting audit](../raw/cross-cutting-audit.md) retains the wider
  terminology audit across lifetime, pointer, resource, `using`, and composition
  owners.

The original observation therefore does not enter the teaching-debt log.

### Pre-promotion documentation-fit dry run

**Result: PASS.**

The aligned model has a coherent lasting owner structure, human reading path,
deferred destinations, and exact promotion set. No remaining design,
documentation-ownership, or integration question blocks promotion.

#### Structure and teaching proposal

Create one cohesive current owner at `language/composition.md`. Composition
cannot be taught coherently by distributing its pieces among declaration,
invocation, operator, lifetime, construction, and identity owners. A reader
should reach the feature through:

```text
index.md
-> language/composition.md
-> focused links to declaration, invocation, operator, transfer, lifetime,
   construction, identity, qualifier, pointer, and safety owners
```

The composition owner must not transcribe this working record. It should teach:

1. ordinary named containment;
2. the data convenience added by bare `own`;
3. physical and published paths, same-place identity, collisions, nesting,
   visibility, and explicit data `via`;
4. the four local forms `own`, `own preferred`, `own expose`, and
   `own preferred expose`;
5. expected-type projection through `preferred`;
6. mechanically eligible behavior forwarding through `expose`;
7. explicit forwarding and filtering;
8. transfer-stance and result-provenance consequences;
9. abstract roles and explicit fulfillment;
10. outer casting and its costs; and
11. diagnostics, source evolution, and deferred boundaries.

Representative valid source must precede rule matrices and failure cases.
Prerequisite vocabulary must appear before provenance, filtering, and
outer-tracking detail. Design chronology, rejected alternatives, legacy C++
comparison, disposition labels, and review bookkeeping do not belong in the
current owner.

#### Finding-to-owner map

- `language/composition.md` owns ordinary composition; data publication;
  surviving physical paths; name precedence, ambiguity, visibility, and
  nesting; data `via`; `preferred`; `expose`; explicit forwarding and
  filtering; abstract roles and fulfillment; outer casting; composition
  costs, diagnostics, and evolution; and the complete human reading path.
- `language/declarations-and-bindings.md` locally owns the declaration-facing
  forms, canonical local composition-modifier order, the non-lexical nature of
  published names, `shadowable` separation, and abstract metadata's
  declaration-category consequences.
- `language/function-invocation.md` locally owns expected-input mapping,
  compatible visible prototypes, generated wrapper participation, and the
  callable-facing `self` result contract.
- `language/operators.md` owns discovery and candidate selection integration:
  exposed operators are outer candidates, while preferred projection occurs
  only after discovery.
- `language/operator-catalog.md` recognizes `outer cast` and
  `unsafe outer cast` as protected composition forms.
- `language/transfer-stances.md` owns the general stance ladder and the
  explicit outer-wrapper projection consequence: outer `copy`, `move`, and
  `last` offer the selected member as `last`, while `deep` remains `deep`.
- `language/lifetimes-and-references.md` owns receiver-origin meaning for
  `self`, same-place reference consequences, and outer-cast origin and
  lifetime constraints.
- `language/construction-and-destruction.md` owns complete-member
  construction, lifecycle, terminal-source consequences, and the distinction
  between bodyless unavailable declarations and explicit abstract composition
  roles; published paths do not flatten construction packets.
- `language/identity-types.md` owns identity-specific application of the
  shared composition exposure filter and the offset-zero underlying
  relationship.
- `language/safety-and-analysis.md` owns the reusable unsafe model applied by
  `unsafe via` and `unsafe outer cast`; intent acknowledgement grants no
  provenance.
- `language/pointers-and-arenas.md` preserves the distinction between the
  composition keyword and the retired pointer qualifier.
- `language/terms.md` owns concise cross-cutting composition terminology.
- `language/vision.md`, `index.md`, and the temporary root `composition.md`
  route own public discovery during promotion.

`language/qualifiers.md` needs no new rule: composition applies its existing
qualification and promise-strengthening model. `language/intent-
acknowledgements.md` needs no new category because intent does not authorize
composition provenance.

#### Exact promotion change set

1. Create `language/composition.md` as the cohesive, progressively taught
   current owner.
2. Update `index.md` and `language/vision.md` to route readers to it.
3. Remove the superseded root `composition.md` after maintainer review confirms
   that no temporary route is needed.
4. Integrate the local rules described above into:
   - `language/declarations-and-bindings.md`;
   - `language/function-invocation.md`;
   - `language/operators.md`;
   - `language/operator-catalog.md`;
   - `language/transfer-stances.md`;
   - `language/lifetimes-and-references.md`;
   - `language/construction-and-destruction.md`;
   - `language/identity-types.md`;
   - `language/safety-and-analysis.md`;
   - `language/pointers-and-arenas.md`;
   - `language/terms.md`; and
   - `language/integers.md`.
5. Relocate deferred pressure into live destinations:
   - exact generated-surface and origin-contract pressure into
     `project/raw/callable-selection.md`;
   - preferred projection and finite specialization pressure into
     `project/raw/type-parameters-and-generics.md`;
   - access-path, layout, and structural-equivalence distinctions into
     `project/raw/structural-typing.md`;
   - exposure-fence extension authority into `project/raw/partial-types.md`;
   - enumeration identity into `project/raw/reflection.md`;
   - qualifier-specialized composition examples into
     `project/raw/mutability-indexed-type-families.md`;
   - rejected local `own` pressure into
     `project/raw/pattern-matching.md`;
   - future checked-versus-unsafe outer-cast diagnostic pressure into
     `project/raw/safety.md`;
   - the terminology collision into `project/raw/cross-cutting-audit.md`; and
   - the accepted composition disposition into
     `project/raw/feature-catalog.md`.
   Update `project/raw/README.md` so its callable-selection route describes the
   remaining integration pressure rather than treating the complete `own`
   surface as future work.
6. Validate links, Markdown structure, current-owner consistency, legacy
   retirement, raw discoverability, changed-file scope, and the preserved
   staged/unstaged comparison boundary.

#### Deferred and closure effects

The complete generic, structural, partial, reflection, and generalized
result-origin systems remain future design. The listed raw owners preserve the
composition constraints without making this archived work item a future source
of meaning.

The two raw composition inputs remain available during promotion because this
active record cites them. Separately authorized closure should archive this work
item, retire those fully consumed raw inputs and their index rows, update the
project and archive routers, and avoid assuming or creating work item `021`.

### Promotion result

After the PASS result was reviewed, the language maintainer explicitly
authorized promotion. The aligned model is now incorporated into current
documentation:

- `language/composition.md` is the cohesive current owner;
- `index.md` and `language/vision.md` route readers to it;
- declaration, invocation, operator, transfer, lifetime, construction,
  identity, safety, pointer, terminology, and integer owners contain their
  focused integration rules;
- every deferred concern has a live current or raw destination; and
- the former root `composition.md` content has been consumed and the legacy file
  has been removed.

The original legacy text named by the reading scope is therefore no longer
present at the root path. It remains recoverable through Git history as evidence
for this work record.

### Post-promotion teaching revision

Maintainer review found that several initial cross-guide integrations recorded
the right facts without teaching why those facts matter. It also rejected
the earlier name for the outer-cast family because it suggested panic recovery
or stack unwinding, and rejected *owned declaration* as an unhelpful attempt to
distinguish ordinary by-value storage from the `own` keyword.

The feedback revision:

- consistently calls the cast family *outer casting*;
- removes the invented *owned declaration* glossary term and explains stance
  propagation directly in terms of by-value declarations and reference aliases;
- replaces the vague *outer relationship* glossary entry with an example-led
  `outer cast` entry;
- expands the declaration, construction, invocation, operator, transfer,
  lifetime, and identity integrations with local examples or contrasts; and
- leaves raw files as future-work inputs except where the obsolete cast-family
  name would otherwise seed later work.

Validation after that revision confirms:

- all local file and heading links in non-archive Markdown resolve;
- all non-archive Markdown code fences are balanced;
- staged, unstaged, and combined whitespace checks pass;
- no current language owner cites numbered work or raw project input;
- no stale future-owned-composition promise remains in `language/`; and
- the maintainer's staged promotion snapshot remains unchanged while the
  feedback revision remains unstaged.

This working record remains non-authoritative and is not a formal
specification or implementation claim. Closure is not part of this promotion:
the work item has not been archived, the two raw composition inputs have not
been retired, and no successor work item has been created. No staging,
unstaging, commit, or push was performed as part of the feedback revision.

Excluded generalized systems - interfaces as a facility, structural subtyping,
generics, partial types, reflection, callable capture, and compiler lowering -
remain undesigned; each place their pressure appears has a live destination
above.

### Post-promotion composition-boundary review

Maintainer review after the teaching revision introduced new concrete pressure
and aligned the working model on the following corrections and extensions:

- `own`, `preferred`, and `expose` are independent rather than a hierarchy
  grounded by `own`;
- publication and automatic forwarding stop at semantic indirection;
- unchanged contained result types are eligible for automatic exposure;
- singular routing remains exact and ambiguity-rejecting, while explicit and
  discovered whole-family adoption use `via family` and `existing family`;
- `forbidden family` fences the complete outer visible-name or structured
  operator family;
- a private exact helper may disambiguate an adapted singular `via`;
- member-level contracts gain `abstract relaxed`, qualification-specialized
  repeated fulfillment, and private `own` carriers;
- a possible whole-type contract remains deferred to generic, constraint, and
  structural-shape design;
- direct-member packet construction remains one construction and conflicts with
  explicit construction of the same member; and
- static outer-origin proof may avoid `outer tracked`, subject to the selected
  language contract.

The final point received an explicit portability clarification. The proof set
that changes required source spelling must be part of the selected language
contract. A compiler may privately prove more and use that fact for
optimization or an advisory, but under the mainline contract it cannot both
reject the retained `unsafe` and leave another conforming compiler requiring
that same `unsafe`. Stronger mandatory classification is permitted only through
an explicitly selected compiler or shared extension contract, or through a
later mainline contract that requires every conforming compiler to recognize
the proof. The initial site-specific origin analysis above is therefore a
candidate for eventual contract definition and implementation testing, not
permission for compiler-dependent source validity.

Authorization for this review covered updating `020` only. These aligned
findings have not been promoted into `language/composition.md` or its
integration owners. The earlier promotion result and dry-run record above
remain historical descriptions of the version that was promoted; they must not
be read as overriding this later aligned working model. At that point, a further
documentation dry run and explicit promotion authorization remained required
before current language documentation changes.

### Post-boundary-review documentation-fit dry run

**Result: PASS.**

The post-promotion findings have a coherent lasting owner structure, can be
taught without inventing another concept document, and do not expose a remaining
language-design contradiction. Promotion is ready for maintainer review, but
this result does not authorize it.

This dry run is intentionally stricter than checking whether every fact has a
file. The earlier promotion showed that correct facts can still fail readers
when they are distributed as dense integration notes. The next promotion must
reconstruct the teaching path and then perform a separate completeness pass.
The corner cases below are part of the feature model. They must appear in the
body of the relevant teaching, normally with representative source and a
plain-language consequence, rather than surviving only in diagnostics, summary
bullets, or cross-links.

#### Structure proposal

Retain the current document structure:

```text
index.md
-> language/composition.md
-> focused declaration, invocation, operator, transfer, lifetime,
   construction, pointer, optional, qualifier, identity, safety, and
   terminology integrations
```

`language/composition.md` remains the cohesive human-facing owner. The new
findings refine one composition model rather than creating a second feature, so
splitting family routing, relaxed roles, or proof-driven outer casting into new
top-level owners would make readers reconstruct the model across files.

No file move, new directory, new index entry, or navigation route is required.
`index.md` and `language/vision.md` already describe the composition owner
accurately. Existing headings should be retained where practical so current
cross-links remain stable; new focused subsections may be added within those
headings.

#### Teaching reconstruction in the composition owner

The opening should continue to begin with ordinary named containment and the
fact that composition does not create an inheritance relationship. It should
then replace the current modifier hierarchy with three independent questions:

1. Should stored data names be shorter? Use `own`.
2. Should an expected member type be selectable from the outer value? Use
   `preferred`.
3. Should eligible behavior appear on the outer type? Use `expose`.

Source should show each word alone before showing combinations. A compact table
may consolidate the model afterward, including that any subset is legal and
that `own preferred expose` is only their canonical relative order. This
prevents the reader from treating `own` as an umbrella permission and makes
identity `expose` unsurprising.

The existing chapter order remains useful, with the following revisions and
example obligations.

1. **Data publication and the indirection boundary.**
   - Keep the ordinary `Car.engine.rpm` / `Car.rpm` same-place example.
   - Add a first-class pointer example in which `Sprite.position` publishes the
     stored `coordinates` pointer itself, while `Sprite.x` is an error and
     `Sprite.position.coordinates.x` retains explicit presence and dereference.
   - Explain in the same section that references, optionals, variants, and other
     semantic wrappers create the same boundary. The modifiers apply to the
     declared member value, not an automatically discovered payload.
   - State beside the example, not only in a later warning, that ordinary outer
     casting cannot travel from a pointee back to the object that stores its
     pointer.
   - Keep the private-path no-laundering rule, then contrast it with a private
     `own` carrier whose only public consequence is explicitly written
     fulfillment.

2. **Construction.**
   - Keep the invalid reach-through packet example.
   - Follow it with valid `.data = expression` source and explain that a direct
     stored-member entry constructs that member once when member-order
     construction reaches it. It is not default construction followed by
     assignment.
   - Show or directly pair the conflict with an explicit `_.data.+++()` in the
     selected constructor body. The construction owner already contains the
     complete packet-order rule; the composition owner must still explain why
     this consequence matters locally.

3. **Preferred projection.**
   - Use `engine preferred : Engine` without `own` in the opening example.
   - Preserve the distinct-target use-site ambiguity, same-base
     qualification-specialization, indistinguishable-route early error, and
     every-boundary nested opt-in examples.
   - Explicitly contrast `own` and `preferred`: publication may shorten a nested
     data path without creating an expected-type route, while `preferred` may
     select a physically named member whose data is not published.
   - Keep operator discovery before operand projection in this section rather
     than relegating it to the diagnostics list.

4. **Behavior exposure.**
   - Open with `engine expose : Engine` so forwarding without data publication is
     visible immediately.
   - Replace the current copied-result exclusion with a worked result example:
     an `Engine` value, `Engine &`, or `Engine *` may pass through as that exact
     contained result type. A copied `Engine` does not become a `Car`.
   - Contrast that unchanged result with a verified reference or pointer
     `self`, which may map back to the outer receiver. This distinction must be
     visible in source and result types, not only in the eligibility table.
   - Keep lifecycle, `once`, private, ambiguous, and unsafe mappings excluded.
     Explain that the safe unchanged-result cases remove the motivating need for
     broad `expose everything` or `expose copy` modes; they do not authorize
     costly or provenance-inventing wrappers.
   - State that `expose` on an indirect member concerns the pointer, optional, or
     other declared wrapper value. It does not synthesize behavior delegation to
     the payload.

5. **Singular explicit routing.**
   - Teach ordinary callable `via` first, then show the structured operator
     target `via engine.operator binary '+'`.
   - Follow the basic example with the actual ambiguity repair: a private helper
     with an exact visible prototype selects one overload, and the public
     declaration routes to that helper.
   - Explain why the public callable is legal: it is an explicit API declaration
     equivalent in authority to a public body calling an accessible private
     helper. Pair that explanation with the boundary that data `via`, automatic
     `expose`, and `own` still cannot publish a private path.

6. **Callable-family adoption and fences.**
   - Introduce a callable family with at least two overloads before presenting
     the family forms.
   - Compare singular `= existing`, discovered `= existing family`, and explicit
     rename-capable `= via family member.operation`.
   - Include a structured operator-family target rather than implying that the
     operator selector is punctuation alone.
   - Show that the written prototype is an exact anchor and that each eligible
     family member must independently obtain one mechanical outer mapping. A
     one-off adaptation of the anchor is not a family-wide conversion rule.
   - Make source evolution concrete: adding a source-family overload can make
     the outer declaration fail if that overload has no unique mechanical
     mapping. It is never silently omitted.
   - Replace the originating-family explanation of `= forbidden family` with an
     outer-surface explanation. The fence covers the complete visible callable
     name or structured operator form regardless of source provenance, and a
     later same-family direct declaration conflicts rather than bypassing it.

7. **Forwarded inputs and transfer.**
   - Change the outer-parameter example so the selected member is marked
     `preferred`, not merely `own`.
   - Explain at the point of use that explicit forwarding may perform automatic
     outer-to-member input mapping only through a preferred route. A body may
     always spell an explicit member path.
   - Preserve the visible outer construction, member `last` offer, valid
     terminal-member destruction, possible second copy, `deep`, and
     reference-authorization consequences.

8. **Abstract roles.**
   - Retain direct, explicitly named fulfillment and no accidental fulfillment
     through publication.
   - Add the private `own` carrier as a normal worked use case. The example must
     distinguish private contract storage from the independently chosen
     visibility of the direct fulfilling declaration.
   - Introduce `abstract relaxed` only after ordinary exact roles and qualifier
     defaulting are understandable.
   - Use paired exact and relaxed examples to show that omission normally chooses
     defaults, while `relaxed` leaves only those omitted axes open.
   - Show multiple qualification-specialized fulfillments of one relaxed role.
     Explain that each declaration independently names and satisfies the role;
     there is no dispatch table, merged implementation, or implicit overload
     selection.
   - State the non-recursive boundary beside the example: explicit qualifiers
     remain required, and relaxation does not alter parameter or result types,
     base type, arity, indirection, transfer stance, labels, or provenance.

9. **Outer casting and proof.**
   - Preserve the ordinary runtime-checked `outer tracked` example and its
     placement/lifecycle cost before introducing proof elimination.
   - Add a statically known-origin example in which every value reaching the cast
     comes from the exact named member path. Explain that a selected contract
     which mandates this proof permits `outer cast` without tracking or a runtime
     check; the result type may remain optional while flow analysis knows it is
     present.
   - Contrast a closed internal flow with same-typed sibling members, arbitrary
     parameters, callbacks, raw-pointer or opaque ingress, and exported
     standalone-member acceptance. These are proof boundaries, not reasons to
     reject unrelated external construction that cannot reach the cast site.
   - Use a compact source-validity table after the examples:
     - contract-required proof: safe unmarked form required and redundant
       `unsafe` is an error;
     - no contract-required proof: the unchecked form still requires `unsafe`,
       even when one compiler privately proves the fact;
     - explicitly selected extension contract: stronger proof may become
       canonical for that source;
     - proved-false assertion: always an error.
   - Apply the same table to `unsafe via` result provenance. Do not describe
     private compiler proof as silently changing mainline source validity.

The costs, diagnostics, formatting, and source-stability sections should then
consolidate the model. They must not become the only location that mentions
indirection, family evolution, relaxed-role multiplicity, or the safety-contract
distinction.

#### Finding-to-owner map

| Aligned finding | Lasting owner | Required local integrations |
| --- | --- | --- |
| `own`, `preferred`, and `expose` are independent; canonical relative order remains `own preferred expose` | `language/composition.md` | `language/declarations-and-bindings.md`, `language/function-invocation.md`, `language/operators.md`, `language/terms.md`, and stale raw references |
| Publication and automatic forwarding stop at pointers, references, optionals, variants, and comparable wrapper boundaries | `language/composition.md` | `language/pointers-and-arenas.md`, `language/lifetimes-and-references.md`, `language/optional-values.md`, and `project/raw/variants-and-unions.md` |
| Direct packet member initialization constructs once, suppresses default initialization, precedes the body, and conflicts with explicit construction of the same member | `language/construction-and-destruction.md` | Local explanation and link in `language/composition.md`; the complete rule is already present in the construction owner |
| Unchanged contained values, references, and pointers may pass through `expose`; only proven reference/pointer `self` maps to the outer receiver | `language/composition.md` | `language/identity-types.md` and the existing callable `self` handoff in `language/function-invocation.md` |
| Singular `via` uses structured operator targets, rejects ambiguous adapted targets, and can use an exact private helper | `language/composition.md` | `language/function-invocation.md`, `language/operators.md`, and reflection/callable-selection raw inputs |
| `existing family` and rename-capable `via family` adopt whole callable families; every member maps independently | `language/composition.md` | `language/declarations-and-bindings.md`, `language/function-invocation.md`, `language/operators.md`, and callable-family raw inputs |
| `forbidden family` fences the complete outer visible-name or structured-operator family | `language/composition.md` | `language/function-invocation.md`, `project/raw/callable-selection.md`, `project/raw/partial-types.md`, and `project/raw/reflection.md` |
| Automatic outer-to-member input mapping by explicit forwarding requires `preferred`, while explicit body paths remain available | `language/composition.md` | `language/transfer-stances.md` and `language/function-invocation.md` |
| Private `own` carriers may activate roles without publishing data; `abstract relaxed` opens only omitted qualifier axes and permits distinct qualification-specialized fulfillments | `language/composition.md` | `language/declarations-and-bindings.md`, `language/qualifiers.md`, and qualifier/generic/structural raw inputs |
| Contract-required exact-origin proof may eliminate `outer tracked` and runtime checking; unselected compiler cleverness cannot change source validity | Domain proof in `language/composition.md`; reusable contract rule in `language/safety-and-analysis.md` | `language/lifetimes-and-references.md`, `language/operator-catalog.md`, `project/raw/safety.md`, and structural/reflection inputs |
| Whole-type abstract contracts remain future constraint and structural-shape design | `project/raw/type-parameters-and-generics.md` and `project/raw/structural-typing.md` | A concise non-current boundary in `language/composition.md` |

#### Exact promotion change set

1. **`language/composition.md`**
   - Rebuild the modifier introduction around three independent questions.
   - Revise every example and diagnostic that currently says `preferred` or
     `expose` requires `own`.
   - Add the indirection, direct-member construction, unchanged-result,
     private-helper, operator-target, family-adoption, outer-family fence,
     preferred-input, private-carrier, relaxed-role, and contract-relative proof
     teaching described above.
   - Update costs, diagnostics, formatting, source stability, metadata, and
     maturity boundaries for the complete revised model.

2. **`language/declarations-and-bindings.md`**
   - Teach the three member modifiers as independently legal while preserving
     their canonical relative order.
   - Add the callable and operator family definition forms at declaration depth.
   - Add `abstract relaxed`, qualification-specialized repeated fulfillment, and
     the private activating-carrier distinction without pretending the broader
     declaration-word order is settled.

3. **`language/function-invocation.md`**
   - Use a preferred-only member in the expected-input example.
   - Explain that explicit forwarding uses the same preferred projection for
     automatic outer-input mapping.
   - Extend the composition adapter integration to distinguish singular and
     family adoption, ambiguity through permitted adaptation, and the explicit
     public-callable/private-helper rule.
   - Preserve the existing fact that callable `self` never applies to copied
     results.

4. **`language/operators.md`**
   - Replace the `own expose` / `own preferred` comparison with independent
     `expose` / `preferred`.
   - Add a structured singular and family `via` target example after ordinary
     operator declaration syntax is established.
   - Preserve discovery before preferred operand mapping.

5. **`language/operator-catalog.md`**
   - Refine the outer-cast entry so runtime checking and `outer tracked` are the
     fallback when the selected contract does not supply static exact-origin
     proof, rather than unconditional properties of every checked cast.

6. **`language/transfer-stances.md`**
   - Change the explicit wrapper example from `fuel own` to `fuel preferred`.
   - State locally that automatic outer-input projection depends on
     `preferred`; retain the existing stance, fallback, copy-cost, and
     terminal-destruction teaching.

7. **`language/construction-and-destruction.md`**
   - Preserve the already complete mixed call-site member-initialization rules.
   - Remove the incidental `own expose` dependency from lifecycle-forwarding
     wording so independent `expose` is accurate.

8. **`language/lifetimes-and-references.md`**
   - Extend published-path teaching with the fact that publication stops before
     a referenced or pointed-to value.
   - Teach the static exact-origin alternative alongside tracked runtime outer
     casting and explain that unrelated unreachable origins do not defeat a
     site-specific proof.
   - Keep renewal, expiration, same-place identity, and immediate-boundary rules.

9. **`language/pointers-and-arenas.md`**
   - Add a direct-entry example showing that an auto-allocated pointee is still
     distinct from the resident pointer member: composition may publish the
     pointer value but does not publish pointee fields, delegate pointee
     behavior, or permit an outer cast from pointee to pointer-containing object.

10. **`language/optional-values.md`**
    - Add the corresponding wrapper boundary: composition modifiers operate on
      the optional member value and do not silently enter a present payload.
      Presence proof and postfix payload access remain explicit.

11. **`language/qualifiers.md`**
    - Add a focused handoff from ordinary omission defaults to
      `abstract relaxed`: only this role-matching form leaves otherwise defaulted
      axes open, and it does not become a general no-default mode.

12. **`language/identity-types.md`**
    - Replace the analogy to an `own expose` container with independent
      `expose`.
    - Correct the additional-data result example: an underlying copied result
      may remain the underlying type even when it cannot become a complete
      identity value. Offset-zero identities retain their narrower
      representation-trivial substitution exception.

13. **`language/safety-and-analysis.md`**
    - Expand the compiler-analysis section with the complete selected-contract
      matrix: mandatory proof, optional stronger implementation proof,
      explicitly selected extension proof, and proved-false assertion.
    - Use composition provenance as a concrete example while leaving the exact
      origin proof class with the composition owner.

14. **`language/terms.md`**
    - Remove the claim that preferred projection requires `own`.
    - Refine *outer cast* so its checked spelling may be statically proved or
      may use `outer tracked` runtime checking, depending on the selected
      contract.

15. **Existing raw owners**
    - Update `project/raw/callable-selection.md` for independent `expose` and
      `preferred`, singular versus family adoption, and the outer-name meaning
      of a family fence.
    - Update `project/raw/type-parameters-and-generics.md` for independent
      modifiers and preserve whole-type abstract-contract pressure for future
      constraints.
    - Update `project/raw/structural-typing.md` with whole-type required-shape
      pressure and the rule that proof/tracking metadata is not structural
      equivalence.
    - Update `project/raw/safety.md` with formalization pressure for
      site-specific exact-origin proof and selected extension contracts.
    - Update `project/raw/reflection.md` for independent modifiers, singular and
      family adoption identity, outer-name family fences, relaxed roles, and
      repeated fulfillment.
    - Update `project/raw/mutability-indexed-type-families.md` for independent
      exposure and qualifier-specialized relaxed-role pressure.
    - Update `project/raw/partial-types.md` so future additions respect an
      outer-name family fence rather than an originating-family filter.
    - Update `project/raw/variants-and-unions.md` so future variant design
      preserves the no-automatic-payload-traversal boundary.
    - Correct `project/raw/feature-catalog.md` from *immediate container
      recovery* to *outer casting* so obsolete terminology does not seed later
      work.

No change is proposed for `index.md`, `language/vision.md`, or
`project/raw/README.md`: their current routes and activation descriptions remain
accurate. No new owner, router, or glossary family term is needed.

#### Deferred and closure effects

- A whole-type `abstract` contract remains a future way to describe a required
  shape for generic, constraint, or structural work. Promotion records that
  pressure in both raw owners without presenting the facility as current.
- Generic deduction, structural matching, and reflection semantics for
  preferred routes and generated families remain future work. Current
  composition continues to require an already established expected type and a
  finite exact generated surface.
- The exact eventual set of mandatory origin-proof algorithms remains future
  specification and compiler work. Current teaching may present the aligned
  site-specific model and its portability rule without claiming a completed
  formal analysis.
- General indirect delegation through pointers, references, optionals, or
  variants remains unprovided. Future work must use a distinct explicit feature
  with visible presence, aliasing, lifetime, and cost semantics rather than
  weakening current composition.
- Full nested outer-cast sugar remains future work. Current casting remains one
  immediate resident boundary at a time.
- A dedicated `expose everything`, `expose copy`, or overload-identity selector
  is not introduced. Unchanged safe results and exact private helpers address
  the motivating cases without creating those broader mechanisms.
- The two original raw composition inputs remain audit evidence while `020` is
  active. Their retirement, archival of this work item, and any successor work
  remain separate closure actions requiring authorization.

#### Promotion validation gate

An authorized promotion should not be considered complete merely because the
old dependency statements disappear. Validation must confirm:

- a cold reader sees each modifier independently before encountering the
  complete modifier group;
- indirection, unchanged result forwarding, private helper routing, family
  evolution, relaxed repeated fulfillment, and static-origin proof each have
  explanatory source or an equally concrete worked contrast in their owner;
- local integration pages explain the consequence relevant to their reader
  rather than supplying only a link;
- no current owner or retained live raw constraint says `preferred` or `expose`
  requires `own` (the two explicitly superseded composition inputs remain audit
  evidence until closure);
- no current or raw text retains the originating-family interpretation of
  `forbidden family`;
- no text says every checked outer cast unconditionally requires
  `outer tracked` or runtime work;
- no text lets an unselected compiler-specific proof change mainline source
  validity;
- no current owner cites this work record or raw input;
- every local link and heading link resolves, code fences balance, and
  whitespace checks pass; and
- the maintainer's staged comparison boundary is preserved.

### Post-boundary-review promotion result

On 2026-09-11, the language maintainer explicitly authorized the promotion
described by the PASS dry run above. The aligned post-boundary composition model
is now promoted into its current conceptual owners. This promotion does not
claim implementation, formal specification, ABI, or compiler conformance.

`language/composition.md` remains the cohesive programmer-facing owner. Its
teaching path was reconstructed rather than patched with a rules appendix:

- the introduction now presents `own`, `preferred`, and `expose` as three
  independent choices before showing their canonical combined order;
- data publication teaches its pointer, reference, optional, variant, and
  semantic-wrapper boundary with concrete source;
- direct member packet initialization is taught locally as one construction
  before the constructor body;
- behavior exposure contrasts unchanged contained value/reference/pointer
  results with the narrow verified `self` remapping;
- singular callable and structured-operator `via`, exact private-helper
  disambiguation, family adoption, family evolution, and outer-name family
  fences are taught as separate decisions;
- automatic outer-input mapping is tied to `preferred` rather than `own`;
- private role carriers, `abstract relaxed`, and qualification-specialized
  repeated fulfillment are explained with worked source; and
- tracked runtime outer casting is followed by site-specific exact-origin proof
  and the selected-contract source-validity matrix, including `unsafe via`.

The following current language owners received focused local integrations:

- `language/declarations-and-bindings.md`;
- `language/function-invocation.md`;
- `language/operators.md`;
- `language/operator-catalog.md`;
- `language/transfer-stances.md`;
- `language/construction-and-destruction.md`;
- `language/lifetimes-and-references.md`;
- `language/pointers-and-arenas.md`;
- `language/optional-values.md`;
- `language/qualifiers.md`;
- `language/identity-types.md`;
- `language/safety-and-analysis.md`; and
- `language/terms.md`.

The following raw owners now retain the corresponding future-work constraints
without presenting them as accepted behavior:

- `project/raw/callable-selection.md`;
- `project/raw/type-parameters-and-generics.md`;
- `project/raw/structural-typing.md`;
- `project/raw/safety.md`;
- `project/raw/reflection.md`;
- `project/raw/mutability-indexed-type-families.md`;
- `project/raw/partial-types.md`;
- `project/raw/variants-and-unions.md`; and
- `project/raw/feature-catalog.md`.

No navigation, index, vision, router, or new owner change was needed. The two
superseded composition inputs remain unchanged as audit evidence while `020` is
active. Whole-type contracts, generic and structural preferred-route behavior,
general indirect delegation, the final mandatory origin-proof algorithms, and
nested outer-cast sugar remain explicitly deferred.

#### Promotion validation

**Result: PASS.**

- The unstaged promotion contains exactly the twenty-three planned owner edits
  plus this working-record result.
- A separate full-promotion review found one additional-data identity example
  that incorrectly rewrote a by-copy input. The example now preserves that input
  as the underlying type; no other conceptual blocker remained.
- Current and retained live raw text no longer makes `preferred` or `expose`
  depend on `own`, treats a family fence as an originating-family filter, or
  makes tracked runtime checking unconditional for every checked outer cast.
- Local file targets and heading anchors resolve, Markdown fences balance, and
  staged, unstaged, and combined whitespace checks pass.
- The maintainer's staged comparison boundary remains unchanged. Every promotion
  edit and this result remain unstaged.

### Post-promotion optional-role and tracked-provenance correction

After the post-boundary promotion, maintainer review identified and aligned two
remaining gaps:

- **Optional abstract roles.** `abstract optional` retains an explicitly named
  signature-verification target while permitting zero fulfillments.
  `abstract optional relaxed` independently combines that minimum count with
  qualification-specialized fulfillment.
- **Explicit outer-provenance operations.** Plain `outer cast` and ordinary
  outer-result `via` require selected-contract proof and produce non-optional
  results. `tracked outer cast` and `tracked via` explicitly select checked
  optional behavior through `outer tracked`. The existing unsafe forms remain
  unchecked non-optional assertions.

The second correction prevents compiler-private analysis from silently changing
an operation's result shape or runtime mechanism. A tracked operation keeps its
optional source type even when ordinary as-if optimization can remove a
redundant metadata read. When selected-contract proof makes that tracked choice
unnecessary, the later `intent<redundant-outer-tracking>` finding below requires
the programmer to confirm it. Automatic `expose` and `= existing` do not
manufacture tracked outer-result work.

These findings refine the existing composition owner rather than requiring a
new owner, router, or directory. Their current integrations are:

- `language/composition.md`, `language/declarations-and-bindings.md`,
  `language/function-invocation.md`, `language/lifetimes-and-references.md`,
  `language/operator-catalog.md`, `language/qualifiers.md`,
  `language/safety-and-analysis.md`, and `language/terms.md`; and
- raw future-pressure updates in `project/raw/callable-selection.md`,
  `project/raw/mutability-indexed-type-families.md`,
  `project/raw/partial-types.md`, `project/raw/reflection.md`,
  `project/raw/safety.md`, and `project/raw/structural-typing.md`.

Recovery found that the interrupted editing harness had applied those fourteen
owner and raw-file corrections but had not applied its claimed update to this
working record. The reviewed earlier promotion was committed first, those
fourteen corrections were then staged as the preserved comparison boundary, and
the missing record plus targeted consistency repairs were left unstaged for
maintainer review.

Earlier dry-run and promotion passages above remain chronological records of the
model they evaluated. Where they describe plain `outer cast` as an optional
operation with an implicit tracked fallback, or imply that every abstract role
requires fulfillment, this correction supersedes them. The current programmer
model near the start of this record and the current conceptual owners carry the
corrected result.

#### Recovery validation

**Result: PASS.**

- Current owners consistently distinguish proof-required, tracked, and unsafe
  outer casting and the corresponding three `via` result mappings.
- Current role teaching records exact and relaxed required cardinalities,
  optional exact and relaxed cardinalities, explicit fulfillment checks, and no
  default implementation or runtime-presence meaning.
- Relevant raw owners preserve reflection, partial-type, qualifier-family,
  structural, callable-selection, and future safety pressure.
- Local live Markdown link targets and heading anchors resolve, code fences
  balance, and staged, unstaged, and combined whitespace checks pass.
- The fourteen-file interrupted-harness correction remained staged while
  recovery completion edits remained unstaged. No push, closure, archival, or
  successor work was performed.

### Post-recovery redundant outer-tracking intent

Maintainer review aligned one further source-safety refinement. When the
selected language contract, including an explicitly selected extension
contract, establishes exact outer origin, a written `tracked outer cast` or
`tracked via` is defined but suspicious: it requests an optional tracked
operation where the ordinary proved form is available.

The unacknowledged tracked operation is therefore an intent error. The ordinary
repair is plain `outer cast` or ordinary `via` with its non-optional result. A
programmer who deliberately wants the optional tracked contract instead writes:

```zax
possibleCar : Car & ? =
  intent<redundant-outer-tracking>{
    engine tracked outer cast Car.engine
  }
```

For `tracked via`, the intent payload encloses the complete routed declaration.
The category confirms the redundant tracked choice, retains its optional result,
and accepts the applicable representation and lifecycle costs. It grants no
provenance and, like every intent acknowledgement, does not force a physical
metadata read when ordinary as-if optimization can remove one.

The trigger belongs exclusively to selected-contract proof. Stronger private
compiler analysis may optimize or advise, but cannot require the intent
acknowledgement, change source validity, or change the tracked result shape. If
the selected contract does not establish exact origin, tracking is necessary
and `intent<redundant-outer-tracking>` is inapplicable. Selected-contract proof
continues to make redundant `unsafe` non-acknowledgeable; this new category
cannot preserve it.

#### Documentation fit and promotion

**Result: PASS.**

No new owner, router, or directory is needed. Teachability requires each local
path to present the proved plain form first, the acknowledged optional tracked
choice second, and the no-proof direct tracked form separately so readers do not
mistake intent for provenance authority or a mandatory runtime instruction.

The correction is promoted through:

- `language/composition.md` as the complete outer-provenance behavior and cost
  owner;
- `language/intent-acknowledgements.md` as the category registry and
  acknowledgement-semantics owner;
- `language/safety-and-analysis.md` for the reusable selected-contract versus
  private-analysis boundary;
- `language/function-invocation.md` and `language/operator-catalog.md` for
  focused direct-entry teaching; and
- `project/raw/callable-selection.md` and `project/raw/safety.md` for future
  callable and proof-formalization pressure.

This working record receives the corresponding aligned finding and promotion
result. Closure, archival, and successor work remain separate.

#### Promotion validation

**Result: PASS.**

- The composition owner leads with the proved plain operation, then shows the
  acknowledged tracked choice and the no-proof direct tracked form separately.
- The intent owner registers `redundant-outer-tracking`, defines its complete
  expression and declaration payloads, and distinguishes it from provenance
  authority and forced instrumentation.
- Current safety and invocation teaching makes selected-contract proof the only
  intent trigger; compiler-private proof can only optimize or advise.
- Relevant current and raw text contains no surviving rule that permits
  unacknowledged redundant tracking under selected-contract proof.
- Live Markdown links and heading anchors resolve, code fences balance, current
  owners do not cite numbered work or raw input, and staged, unstaged, and
  combined whitespace checks pass.
- The agent did not alter the maintainer's staged boundary while preparing this
  eight-file correction.

### Closure result

Work item `020` is complete. Its aligned composition model, optional-role
cardinality, explicit outer-provenance forms, and redundant-tracking intent
category are promoted into their current owners. Deferred structural, generic,
partial-type, reflection, callable-contract, indirect-delegation, and formal
proof questions have live current or indexed raw destinations.

The two composition-specific raw inputs that initiated this work were fully
consumed and retired during closure. This record moved to the archive, and the
project router no longer identifies `020` as active. Work item `021` remains
uncreated pending separate alignment on its fixed initiating input and reading
scope.
