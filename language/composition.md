# Zax composition

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers building types from stored components and choosing which component surfaces the container presents |
| Applies To | Named containment; independent `own`, `preferred`, and `expose`; semantic-indirection boundaries; published data paths; singular and family composition routing and filtering; abstract roles and fulfillment; outer casting and exact-origin proof; costs, diagnostics, formatting, and source stability; not a formal grammar or specification |
| Implementation State | Not established by this repository |
| Owns | The complete programmer-facing composition model; data publication and collisions; expected-type projection; generated behavior exposure and unchanged results; `via`, `tracked via`, `unsafe via`, `via family`, and `existing`; exact and outer-family fences; composition-specific mapping eligibility; `abstract`, `abstract optional`, `abstract relaxed`, `abstract optional relaxed`, and `fulfill`; the two activations of abstract roles, through `own` and through hook points; the shared mechanical filter used by identity exposure; `outer`, `outer tracked`, `outer cast`, `tracked outer cast`, `unsafe outer cast` (including role-preserving outer casts of managed pointers and their operand-shaped failure), and composition-specific exact-origin proof |
| Does Not Own | Ordinary declarations and member lookup ([declarations and bindings](declarations-and-bindings.md)); callable selection and compatible visible prototypes ([function invocation](function-invocation.md)); shared operator discovery and selection ([operators](operators.md)); [structural shape and compatibility](structural-shapes-and-compatibility.md); qualification meaning ([qualifiers](qualifiers.md)); general transfer semantics ([transfer stances](transfer-stances.md)); complete semantic-wrapper behavior ([optional values](optional-values.md), [variants](variants.md), and [unions](unions.md)); reference origin and lifetime ([lifetimes and references](lifetimes-and-references.md)); ordinary lifecycle behavior ([construction and destruction](construction-and-destruction.md)); identity admission and projection ([identity types](identity-types.md)); pointer ownership ([pointers and arenas](pointers-and-arenas.md)); or the reusable unsafe model ([safety and analysis](safety-and-analysis.md)) |
| Source / Provenance | Legacy composition intent, reconciled with current declaration, invocation, operator, transfer, lifetime, construction, identity, and safety design |
| Supersedes | Legacy composition design formerly published at the repository root |

## Mental model

Ordinary named containment is the foundation of Zax composition:

```zax
Engine :: type {
  rpm : Integer

  start final : ()() = {
  }
}

Car :: type {
  engine : Engine
  wheels : Integer
}

prepare final : ()(car : Car mutable writable &) = {
  car.engine.rpm = 2000
  car.engine.start()
  car.wheels = 4
}
```

`Car` contains an `Engine`. The stored member has its own name, place, value,
and lifecycle. Nothing implies that a `Car` *is an* `Engine`.

The `own` word can make selected data paths more convenient:

```zax
Car :: type {
  engine own : Engine
  wheels : Integer
}

prepare final : ()(car : Car mutable writable &) = {
  car.engine.rpm = 2000 // physical path
  car.rpm = 2000        // published path to the same place
}
```

`car.rpm` does not name another declaration or another integer. It is a
published access path to `car.engine.rpm`. Both paths have the same storage,
identity, lifetime, qualifications, and access cost.

Composition offers three independent choices:

1. Should stored data names be available by a shorter path? Use `own`.
2. Should an outer value supply this member when that member type is already
   expected? Use `preferred`.
3. Should mechanically safe member behavior appear on the outer type? Use
   `expose`.

Each word can stand alone:

```zax
Assembly :: type {
  publicData own : PublicData
  engine preferred : Engine
  controls expose : Controls
}
```

`Assembly` publishes names from `publicData`, may project to `engine` in an
`Engine`-typed context, and exposes eligible `controls` behavior. None of those
choices silently enables either of the others. In particular, `engine` remains
available as a physically named member rather than publishing its data, and
`controls` behavior can be called through `Assembly` without publishing
`controls` data.

Any subset may be combined:

| Form | Selected conveniences |
| --- | --- |
| `member own : Type` | Stored instance-member names |
| `member preferred : Type` | Expected-type projection |
| `member expose : Type` | Mechanically eligible instance behavior |
| `member own preferred expose : Type` | All three |

When words are combined, their canonical relative order is
`own preferred expose`. Placement of this modifier group among unrelated
declaration words awaits the general grammar and formatting sweep.

Examples below reuse short type names such as `Engine` and `Car` independently.
Each block supplies the declarations relevant to that example rather than
extending every earlier block.

These conveniences remain composition:

- physical member paths always survive;
- published data creates no storage;
- generated behavior is statically selected forwarding, not virtual dispatch;
- expected-type projection is one-way and context-bounded;
- no form creates a class hierarchy, vtable, or hidden outer reference; and
- ambiguity is diagnosed rather than resolved by source order.

## Publishing stored data with `own`

### What is published

Bare `own` publishes the contained type's stored instance-member names:

```zax
Address :: type {
  city : String
  postalCode : String
}

Customer :: type {
  address own : Address
}

printDestination final : ()(customer : Customer readonly &) = {
  print(customer.city)
  print(customer.address.postalCode)
}
```

It does not publish:

- instance functions;
- operators;
- nested types;
- aliases;
- constants; or
- `once` declarations.

Behavior crosses a composition boundary only when the boundary explicitly uses
`expose` or the container declares a forwarding operation.

`own` is not a local-variable or flow-scope projection facility. The separate
`shadowable` word remains a one-level lexical-name permission and has no effect
on publication, precedence, or collisions. See
[declarations and bindings](declarations-and-bindings.md#shadowing).

### Direct names and collisions

A stored member declared directly in the container owns its short name:

```zax
Payload :: type {
  id : PayloadId
  bytes : Buffer
}

Envelope :: type {
  payload own : Payload
  id : EnvelopeId
}

inspect final : ()(envelope : Envelope readonly &) = {
  use(envelope.id)         // Envelope.id
  use(envelope.payload.id) // Payload.id
  use(envelope.bytes)      // Payload.bytes
}
```

The direct `Envelope.id` prevents publication of the short `payload.id` path.
It does not hide or alter the physical member.

Two owned paths publishing the same name make that short name ambiguous:

```zax
Reading :: type {
  value : Float
  timestamp : Integer
}

Sensor :: type {
  primary own : Reading
  backup own : Reading
}

inspect final : ()(sensor : Sensor readonly &) = {
  use(sensor.primary.value)
  use(sensor.backup.value)
  use(sensor.value) // error: two owned paths publish `value`
}
```

There is no first-declaration or shortest-path winner.

### Selecting and renaming data with `via`

A data `via` declaration creates a no-storage route to one exact stored path:

```zax
Sensor :: type {
  primary own : Reading
  backup own : Reading

  value via primary.value
  age via backup.timestamp
}

inspect final : ()(sensor : Sensor readonly &) = {
  use(sensor.value)             // primary.value
  use(sensor.age)               // backup.timestamp
  use(sensor.primary.timestamp) // physical path still available
}
```

The route may preserve the target name or give it a new name. It retains the
target place's type truth, qualification, lifetime, transfer stance, and access.
It cannot widen authority or create storage.

A direct `via` route owns its declared outer name just like a direct stored
member. It therefore selects that name over any automatically published short
path. Two direct routes, or a route and another direct declaration, cannot claim
the same outer name; that is a declaration error.

### Visibility follows the complete physical path

A private member of the contained type never enters the `own`-published set. A
published route also cannot be more visible than any physical segment it
abbreviates. A public inner member behind a private carrier therefore does not
acquire a public short path.

`own`, `expose`, and `via` grant no friendship and no private access. A `via`
target must already be accessible from the declaration's context.

A private carrier may nevertheless use `own` to activate its
[abstract roles](#abstract-roles-and-explicit-fulfillment). That does not
publish the carrier or its fields: the immediate container may inspect its own
private path, and only the container's explicitly written `fulfill`
declarations acquire whatever visibility they declare.

### Nested composition

Data publication follows owned data paths transitively:

```zax
Coordinates :: type {
  x : Float
  y : Float
}

Position :: type {
  coordinates own : Coordinates
}

Sprite :: type {
  position own : Position
}

moveToOrigin final : ()(sprite : Sprite mutable writable &) = {
  sprite.x = 0
  sprite.position.y = 0
  sprite.position.coordinates.x = 0
}
```

Every physical path survives. Collisions at any depth remain ambiguous.
Behavior is different: it crosses only boundaries that independently say
`expose`.

### Publication stops at semantic indirection

Recursive publication follows resident by-value containment. It does not
silently enter a pointer, reference, optional, variant, or another wrapper whose
payload has separate presence, selection, alias, or lifetime meaning.

```zax
Coordinates :: type {
  x : Float
  y : Float
}

Position :: type {
  coordinates : Coordinates * = @
}

Sprite :: type {
  position own : Position
}

draw final : ()(sprite : Sprite readonly &) = {
  use(sprite.coordinates) // published path to the stored pointer
  use(sprite.x)           // error: publication does not enter the pointee

  if !sprite.position.coordinates
    return

  use(sprite.position.coordinates.x) // presence and dereference stay explicit
}
```

`Sprite.position` is resident, so `own` publishes its stored `coordinates`
member. That member's value is a pointer. The allocated `Coordinates` is not
resident inside either `Position` or `Sprite`, even when `@` allocated it during
initialization. Consequently, `own` does not manufacture `sprite.x`, and
ordinary outer casting cannot travel from the pointee back to the object that
stores its pointer.

The same principle applies to the other modifiers. `preferred` selects the
declared member value when that exact value type is expected. `expose` considers
behavior of the declared pointer, optional, variant, or wrapper value; it does
not synthesize delegation to a present or selected payload. A future indirect
delegation feature would need to expose its presence, aliasing, lifetime, and
runtime costs explicitly rather than weakening this boundary.

The complete managed-alternative boundary is taught by
[Zax variants](variants.md#composition-stops-at-the-variant-wrapper). An
unmanaged union is likewise one complete stored union value; composition does
not publish or select one of its offset-zero lenses. See
[Zax unions](unions.md#composition-and-foreign-layout-boundaries).

### Construction remains whole-member construction

Published fields are not flattened into the container's construction shape:

```zax
AnimalData :: type {
  animal : String = "bear"
}

Container :: type {
  data own : AnimalData
}

container : Container = [{
  .animal = "mouse"
  // error: `animal` is a published path, not a direct Container member
}]
```

The container can supply constructor arguments or a complete value for its
direct `data` member:

```zax
container : Container = [{
  .data = makeMouseData()
}]
```

The `.data` entry suppresses default initialization for that direct member.
`makeMouseData()` is evaluated in packet order, and its result constructs
`data` once when member-order construction reaches that place. This is not
default construction followed by assignment. Selected member initialization
finishes before the enclosing constructor body begins.

It cannot initialize `data.animal` as though `animal` were declared directly in
`Container`. The `AnimalData` value must be established by its own selected
constructor. A constructor body that also calls `_.data.+++()` conflicts with
the packet entry because both request construction of the same place:

```zax
Container :: type {
  data own : AnimalData

  +++ final : ()(data : AnimalData) = [{
    .data = data
  }] {
    _.data.+++() // error: `.data = data` already constructs this member
  }
}
```

Construction packets, constructors, replacement, and destruction continue to
target the physical stored shape described by
[construction and destruction](construction-and-destruction.md).

### Structural mapping may use place-preserving paths

Publication and routing do not change stored shape, but explicit structural
mapping may use their accessible data paths.

An `own`-published path or place-preserving data `via` route may be:

- a source for decomposition, reshape, transformation, or a compatibility
  anchor;
- a destination for explicit reshape or existing-value transformation; or
- a new-value transformation designator when construction can establish the
  complete underlying physical member and every enclosing lifetime.

Every alternate path canonicalizes to its resident physical place before
one-to-one consumption is tracked. Two names for one place do not create two
source values. Ordinary direct-name precedence, route precedence, ambiguity,
privacy, and complete-path visibility continue to apply.

Named recomposition and ordinary construction packets remain narrower: they
target physical construction shape and do not flatten through an `own` or
data-`via` path. `preferred`, callable `via`, `expose`, abstract metadata, and
generated behavior are not resident data paths.

Name-aware compatibility still compares physical declaration names. A renamed
route can aid transformation but cannot manufacture equal shape. Complete
behavior is defined by
[Zax structural shapes and compatibility](structural-shapes-and-compatibility.md#composition-paths-can-map-without-becoming-shape).

## Expected-type projection with `preferred`

`preferred` lets an outer value supply one member when a destination type is
already known:

```zax
Car :: type {
  engine preferred : Engine
  wheels : Integer
}

tune final : ()(target : Engine readonly &) = {
}

service final : ()() = {
  car : Car

  tune(car)        // projects car.engine
  tune(car.engine) // explicit path remains available
}
```

Preferred projection participates in:

- call-input binding;
- explicitly typed initialization; and
- other typed binding where the destination is already established.

It does not perform open-ended member, receiver, or operator discovery. The
direction is outer to member only. An exact type match beats projection.

A reference projection preserves the physical member path, lifetime, and
qualifications. A by-value projection constructs only the selected member value
under the applicable stance rather than pretending the outer value and member
are identical.

`own` and `preferred` can therefore select different aspects of the same
physical relationship:

```zax
PublishedCar :: type {
  engine own : Engine
}

ProjectedCar :: type {
  engine preferred : Engine
}

compare final : ()(
  published : PublishedCar readonly &,
  projected : ProjectedCar readonly &
) = {
  use(published.rpm) // data publication
  tune(published)    // error: `own` creates no Engine projection

  use(projected.rpm) // error: `preferred` publishes no data
  tune(projected)    // projects projected.engine
}
```

### More than one preferred member

Several preferred members with different target types are legal:

```zax
Rig :: type {
  engine preferred : Engine
  radio preferred : Radio
}

configure final : ()(target : Engine readonly &) = {
}

configure final : ()(target : Radio readonly &) = {
}

configureRig final : ()(rig : Rig readonly &) = {
  configure(rig)        // error: both projected overloads are viable
  configure(rig.engine) // explicit path resolves the choice
}
```

Qualification-distinct routes to the same base type may also coexist. Ordinary
viability and partial-order comparison can select one when its complete
capabilities uniquely fit. Cross-axis or nondominated overlap remains a
use-site ambiguity; projection introduces no scoring system.

```zax
BufferViews :: type {
  snapshot preferred : Buffer immutable
  working preferred : Buffer mutable
}

archive final : ()(input : Buffer immutable &) = {
}

edit final : ()(input : Buffer mutable writable &) = {
}

useViews final : ()(views : BufferViews mutable writable &) = {
  archive(views) // selects snapshot
  edit(views)    // selects working
}
```

The routes are distinguishable because the expected complete types require
different capabilities. If two routes remain equally viable after ordinary
qualification comparison, the use is ambiguous.

Two direct preferred routes with the same normalized type and same complete
defaulted shape are indistinguishable. They are rejected when the containing
type completes rather than waiting for a use:

```zax
TwinBuffer :: type {
  first preferred : Buffer
  second preferred : Buffer
  // error: indistinguishable direct preferred projections
}
```

Transparent aliases do not make the routes distinct.

### Nested and operator projection

Every boundary in a nested projected route must independently use `preferred`.
Converging nested routes remain ambiguous; path length and declaration order do
not rank them.

```zax
Car :: type {
  engine preferred : Engine
}

FleetEntry :: type {
  car preferred : Car
}

tuneEntry final : ()(entry : FleetEntry readonly &) = {
  tune(entry) // FleetEntry -> Car -> Engine
}
```

Removing `preferred` from either `FleetEntry.car` or `Car.engine` makes that
projected call unavailable. An `own` on either boundary may shorten a data path,
but it does not repair the missing expected-type route.

Operator discovery completes before preferred projection participates. Once a
discovered candidate provides an expected operand type, the operand may project
through a preferred member. `preferred` never:

- discovers an operator through the outer receiver;
- widens a candidate tree;
- swaps operands;
- rescans source; or
- beats a direct operand match.

Generic deduction and structural matching do not currently acquire targets from
preferred routes. Their future integration must begin with an independently
established expected type or shape.

## Exposing behavior

`expose` asks the compiler to generate thin forwarding for mechanically eligible
instance functions and operators:

```zax
Engine :: type {
  start final : ()() = {
  }

  isRunning final : (result : Boolean)() readonly = {
  }
}

Car :: type {
  engine expose : Engine
}

drive final : ()(car : Car mutable writable &) = {
  car.start()
  if car.isRunning()
    travel(car)
}
```

The visible calls belong to `Car`'s surface. Their implementations forward to
`car.engine`. A compiler may optimize a trivial wrapper away, but the language
does not promise one backend representation or zero pointer adjustment.
`car.rpm` remains unavailable because `expose` does not publish data.

### Mechanical eligibility

Automatic exposure is intentionally narrower than arbitrary handwritten
wrapping:

| Contained operation | Automatic exposure |
| --- | --- |
| Resultless instance function or operator | Eligible when inputs map unambiguously |
| Result in an orthogonal domain, such as `Boolean` | Eligible; result passes through unchanged |
| Contained value, reference, or pointer result | Eligible when the exact contained result type passes through unchanged |
| Receiver reference or pointer result explicitly declared `self` | Eligible; receiver origin is proved |
| Mapping a copied contained result to a complete outer value | Ineligible; the outer's remaining state cannot be invented |
| Constructor, replacement constructor, destructor, generated copy, or generated assignment | Ineligible; lifecycle targets the physical stored shape |
| Mapping requiring a checked or unsafe outer cast | Omitted |
| Ambiguous input, result, qualification, or provenance mapping | Omitted |

Exposure cannot strengthen or launder:

- visibility;
- access capability;
- place or referent qualifications;
- transfer stance;
- result provenance; or
- lifetime.

Automatic exposure also keeps by-copy input types unchanged. An `Engine`
parameter does not silently become a `Car` parameter merely because the
operation is exposed. Representation-trivial identity exposure is the one case
that may additionally substitute by-copy inputs and copied self results; see
[identity exposure](#identity-exposure).

Unchanged contained results are useful and do not require an outer cast:

```zax
Engine :: type {
  snapshot final : (result : Engine)() = {
    return copy _
  }

  selected final : (result : Engine &)() = {
    return locateSelectedEngine()
  }

  selectedPointer final : (result : Engine *)() = {
    return locateSelectedEnginePointer()
  }

  tune final : (result self : Engine &)() = {
    calibrate(_)
    return _
  }
}

Car :: type {
  engine expose : Engine
  wheels : Integer
}

inspectResults final : ()(car : Car mutable writable &) = {
  snapshot : Engine = car.snapshot()
  selected : Engine & = car.selected()
  selectedPointer : Engine * = car.selectedPointer()
  sameCar : Car & = car.tune()
}
```

The first three wrappers return exactly the contained operation's declared
result type. The `Engine` copy does not become a `Car`; no value exists for
`wheels`. The ordinary `Engine &` and `Engine *` also stay ordinary contained
results even if they happen to refer to a resident engine.

`tune` is different. Its verified reference `self` contract proves that the
result is exactly the receiver used for that invocation. The wrapper may
therefore return the original `Car` receiver. This narrow proof-backed
substitution does not authorize checked or unsafe outer casting for other
results.

Passing through safe unchanged results removes the need for broad
`expose everything` or `expose copy` modes. It does not authorize wrappers that
invent missing outer state, add copies, or assert provenance.

Callable viability, overload comparison, and visible-prototype adaptation are
defined by [function invocation](function-invocation.md). Operator discovery
and selection are defined by [operators](operators.md).

### The `self` result contract

`self` on a reference or pointer result means that every returning path returns
exactly the invocation receiver `_`:

```zax
Engine :: type {
  tune final : (
    result self : Engine &
  )(profile : Profile readonly &) = {
    apply(_, profile)
    return _
  }
}
```

The compiler verifies the contract. It is never inferred into a public
declaration, and it never applies to a copied result. This receiver-origin proof
lets an exposed wrapper map a contained receiver reference back to its outer
receiver without performing a runtime outer cast.

Reference origin and escape remain governed by
[lifetimes and references](lifetimes-and-references.md#returned-references).
When a true provenance claim cannot be proved, only a localized unsafe
assertion can accept that responsibility. An `intent` acknowledgement cannot.

### Direct declarations, ambiguity, and `once`

A direct outer declaration with an exact generated shape wins over exposure.
It is ordinary container behavior, not an override relationship. This includes
a bodyless direct declaration: its exact shape remains unavailable rather than
falling through to an exposed implementation. Use `= existing`, `via`, or a
body when the direct declaration should supply behavior.

Equal generated mappings and ordinary overload sets never use declaration or
generation order as a tiebreaker. An unresolved best set is an ambiguity error.

A `once` function is never exposed automatically. Its receiverless type-call
mode uses a `copy`-stanced Nothing receiver while its instance-call mode uses
the actual receiver source, so one automatic wrapper cannot preserve both
meanings. A programmer may deliberately provide a mode-specific `via`, select a
unique existing mapping, or write a
wrapper body.

Complete receiverless and Nothing-instance behavior is defined by
[Zax Nothing instances](nothing-instances.md#receiverless-and-instance-calls).

Lifecycle operations are also excluded. `expose`, `via`, and `= existing`
cannot turn a member constructor, replacement constructor, destructor,
generated `copy`, or generated assignment family into container lifecycle.
Container lifecycle targets the physical stored shape. A container lifecycle
body may invoke member lifecycle explicitly when its ordinary lifecycle
authority permits.

## Explicit behavior routing and filtering

### `via` selects one target

A function or operator `via` declaration names the exact route it adapts:

```zax
Car :: type {
  engine : Engine

  start final : ()() = via engine.start
  ignite final : ()() = via Car.engine.start
}
```

The relative path is ordinary inside the type. A full type-qualified path is
also available. There is no `_.engine.start` selector form.

Unlike a plain compatible visible prototype, `via` may generate a thin adapter
that performs fixed-offset receiver adjustment and mechanically permitted input
projection. It cannot invent policy, construction, conversion, capability, or
provenance. A written wrapper body is required when those decisions are needed.

The data form `name via member.path` is a no-storage declaration. The callable
form keeps a complete visible prototype and uses `= via member.operation` as
its definition.

An operator target repeats the operator's structured name:

```zax
PoweredCar :: type {
  engine : Engine

  operator binary '+' final : (
    result : Power
  )(
    rhs : Fuel
  ) readonly =
    via engine.operator binary '+'
}
```

The visible prototype selects one operation in that family. Zax does not add a
punctuation-only selector or a separate overload identifier.

Singular `via` must obtain one source operation and one complete mapping. If
permitted adaptation leaves several source overloads viable, the declaration is
ambiguous. An exact private helper can make the intended source prototype
explicit:

```zax
StartRequest :: type {
  fuel preferred : Fuel
  code preferred : IgnitionCode
}

Engine :: type {
  start final : ()(fuel : Fuel readonly &) = {
  }

  start final : ()(code : IgnitionCode readonly &) = {
  }
}

AmbiguousCar :: type {
  engine : Engine

  start final : ()(request : StartRequest readonly &) =
    via engine.start // error: either preferred route could fit an overload
}

Car :: type {
  engine : Engine

  startExact private final : ()(fuel : Fuel readonly &) =
    via engine.start

  start final : ()(request : StartRequest readonly &) =
    via startExact
}
```

The helper's exact `Fuel` prototype selects one `engine.start`. The public
declaration then targets that helper and projects `request` only through
`StartRequest.fuel`. This is legal for the same reason a public written body may
call an accessible private helper: the public declaration is a new API surface,
not publication of the helper. The rule does not let `own`, automatic `expose`,
or data `via` publish private data or behavior.

### `= existing` selects a unique mapping

`= existing` may locate one unique mechanically compatible existing operation
and one unique complete mapping through the permitted member surfaces. Unlike
`via`, it does not name the intended target; failure to find one unique mapping
is an error.

```zax
Engine :: type {
  stop final : ()() = {
  }
}

Car :: type {
  engine expose : Engine

  stop final : ()() = existing
}
```

No match or several matches is an error. Source order and overload ranking do
not guess which relationship the declaration meant.

### Adopting a callable family

The singular forms above select one operation. Their `family` counterparts
deliberately adopt a complete source callable family:

```zax
Engine :: type {
  reset final : ()() = {
  }

  reset final : ()(profile : Profile readonly &) = {
  }
}

Controls :: type {
  engine expose : Engine

  reset final : ()() = existing family
}

RenamedControls :: type {
  engine : Engine

  restart final : ()() = via family engine.reset
}
```

`= existing family` must discover one unique source family from the exact
written anchor. `= via family` names that family and may give it another outer
name. Operator families use the same structured target:

```zax
PoweredCar :: type {
  engine : Engine

  operator binary '+' final : (
    result : Power
  )(
    rhs : Fuel
  ) readonly =
    via family engine.operator binary '+'
}
```

The written prototype is an exact anchor, not a wildcard. After it identifies a
family, each source member must independently obtain one mechanical outer
mapping. A conversion or other one-off adaptation that made the anchor viable
does not become a family-wide rule.

Family adoption is intentionally evolution-sensitive. If a later source
overload cannot map uniquely, the outer family becomes an error; that overload
is not silently omitted. Selecting only one singular operation avoids taking on
that source-family compatibility commitment.

### Fencing generated behavior

An exact fence permanently blocks one generated signature:

```zax
Car :: type {
  engine expose : Engine

  start final : ()() = forbidden
}
```

Generation and compatibility fallback cannot restore that signature.

`= forbidden family` blocks the complete outer callable family:

```zax
Engine :: type {
  reset final : ()() = {
  }

  reset final : ()(profile : Profile readonly &) = {
  }
}

Car :: type {
  engine expose : Engine

  reset final : ()() = forbidden family
}
```

The complete written prototype is an exact anchor for the outer name or
structured operator form. `family` broadens the prohibition to every generated,
exposed, adopted, or later direct signature in that outer family, regardless of
which contained source could have supplied it. A later direct `reset`
declaration therefore conflicts with the fence rather than bypassing it.

This is an outer-surface name fence, not an originating-family filter. A
same-spelled callable does not escape because it came from another member, and
no unrelated differently named family is affected. The fence introduces no
wildcard prototype, callable, or runtime state.

## Forwarding values, stances, and results

Automatic exposure does not rewrite contained by-copy inputs. Explicit `via` or
`= existing` declarations may instead present an outer by-value parameter and
project one member declared `preferred` to the selected contained input:

```zax
Fuel :: type {
  refill final : ()(source : Fuel mutable writable & move) = {
  }
}

Tank :: type {
  fuel preferred : Fuel
  shell : Shell

  refill final : ()(source : Tank) = via fuel.refill
  // `source` is established as a complete Tank. The wrapper offers
  // `source.fuel` as last, then destroys the remaining terminal Tank.
}
```

The visible outer parameter is constructed according to its declared stance.
The composition-specific consequence is that the selected member then reaches
the contained input through its preferred route rather than by changing the
visible outer contract. `own` alone cannot authorize this automatic input
projection. A written body remains free to name `source.fuel` directly and
therefore does not need `preferred`.

For outer `copy`, `move`, and `last`, the projected member is offered as `last`;
outer `deep` remains `deep`; and reference-shaped mappings preserve the caller's
exact authorization.

The complete fallback, terminal-member destruction requirement, and cost model
are owned by
[transfer stances](transfer-stances.md#explicit-composition-wrappers). In
particular, the mapping can still perform an outer copy followed by another
member copy; composition does not pretend it is free.

For results:

- orthogonal results pass through unchanged;
- a declared `self` reference or pointer may map to the outer receiver;
- a contained value, reference, or pointer may pass through with its exact
  declared result type;
- a copied contained result cannot become a complete outer value;
- an explicit `via` may retain the contained result type when its visible
  prototype declares that type;
- `tracked via` may explicitly produce an optional outer result through checked
  outer casting;
- ordinary `via` may produce a non-optional outer result when the selected
  contract proves its origin; and
- an unproved non-optional outer remapping requires localized `unsafe via`.

The last form is a provenance assertion, not an intent acknowledgement.

For example, suppose `Engine.selectedPeer()` returns an `Engine &` whose
container relationship is not its invocation receiver:

```zax
Engine :: type outer tracked {
  selectedPeer final : (result : Engine &)() = {
    return locatePeer(_)
  }
}

Car :: type {
  engine own : Engine

  checkedPeerOwner final : (result : Car & ?)() =
    tracked via engine.selectedPeer

  assertedPeerOwner final : (result : Car &)() =
    unsafe via engine.selectedPeer
}
```

The `tracked via` declaration visibly selects checked immediate outer casting
and therefore keeps an optional result. The non-optional `unsafe via`
declaration instead asserts that the returned engine really resides at
`Car.engine`; a false claim has unsafe consequences. When the selected language
contract proves that result origin, ordinary unmarked `via` supplies the
non-optional form without tracking or unsafe responsibility. Deliberately
retaining `tracked via` under that proof requires
`intent<redundant-outer-tracking>`.

## Abstract roles and explicit fulfillment

A component type used by an `own` member may describe declarations that its
immediate container must supply, or optional roles whose signatures the
container may choose to verify.

Inside an abstract required type or prototype, `outer` means the immediate
containing type whose `own` activates the requirement. It is a bounded
contextual type spelling, not a generic parameter and not runtime outer access.

```zax
WindowContract :: type {
  start abstract : ()()
  label abstract : String
  handle abstract : (result self : outer &)()
}

Window :: type {
  contract own : WindowContract

  begin fulfill contract.start final : ()() = {
  }

  caption fulfill contract.label : String

  onEvent fulfill contract.handle final : (
    result self : Window &
  )() = {
    return _
  }
}
```

An `abstract` declaration is compile-time role metadata. When an `own` member
activates it, as here, it creates:

- no storage;
- no implementation;
- no callable hook;
- no vtable or ABI entry;
- no hidden reference to the container; and
- no ability for inner code to call outward.

A type can also use a contract of roles as a hook point that its partials
fulfill; see [hook points](#hook-points-for-partials). Everything else in this
section describes activation through `own`.

The immediate `own` activates the role. Unless the role is explicitly optional,
the immediate container must write a compatible declaration that names the role
with `fulfill`. A name published from another member declared `own` does not
satisfy anything accidentally.
After fulfillment, the requirement does not propagate through another outer
composition boundary.

Declaration category, placement, and qualification rules are integrated with
[declarations and bindings](declarations-and-bindings.md#composition-facing-member-declarations).

The role's qualified abstract path is its identity. The fulfilling declaration
may have another name, as `caption` does above. One declaration may fulfill
several compatible roles by naming all of them:

```zax
start fulfill primaryContract.start, backupContract.start final : ()() = {
}
```

Every named path must be an active compatible role. Naming no real role is an
error. Identical requirements do not merge silently; both paths must appear in
the `fulfill` list. Incompatible requirements cannot share one declaration.

### Optional roles verify without requiring

An optional abstract role offers a named conformance target without requiring
every activating container to implement it:

```zax
TelemetryContract :: type {
  report abstract optional : ()(event : Event readonly &)
}

SilentService :: type {
  contract own private : TelemetryContract
  // No report implementation is required.
}

ObservedService :: type {
  contract own private : TelemetryContract

  report fulfill contract.report final : (
    event : Event readonly &
  )() = {
  }
}
```

`abstract optional` supplies no default body, storage, callable hook, dispatch,
or runtime presence query. It changes only whether zero fulfillments are valid.
If a declaration names the role with `fulfill`, all ordinary path, category,
type, qualification, access, and provenance checks still apply. A compatible
declaration that does not name the role is not silently treated as its
fulfillment.

The stored carrier may be private even though its roles must be visible to the
immediate container:

```zax
Service :: type {
  contract own private : ServiceContract

  start fulfill contract.start final : ()() = {
  }
}
```

`Service` can name its own private physical path while checking fulfillment.
The carrier and any data it holds remain private; `own` does not launder them
into a public data surface. The direct `start` declaration independently chooses
its visibility, just as a written public declaration may use other private
implementation details.

Abstract metadata has no declaration place. Declaration-side words therefore
do not apply, and `private` is invalid because the immediate container must be
able to inspect the role:

```zax
Contract :: type {
  start abstract final : String // error: declaration-side `final`
  label abstract : String final // valid type qualification
}
```

Qualifications belonging to the complete required type or prototype remain
meaningful.

### `final` fulfillment of a callable role

A role has no declaration side, so its type-side stance follows the ordinary
default: `start abstract : ()()` asks for a `varying` callable. A `final`
declaration may still fulfill it, for the same reason `foo final : Foo varying`
is legal: declaration-side `final` restricts only that declaration's own
replacement authority, which is compatible with a `varying` place. That is why
the examples above fulfill `start abstract : ()()` with a fixed `final`
implementation. A `varying` fulfillment is equally valid and provides a
replaceable slot.

A role that writes `final` on its type side requires a final place:

```zax
StartContract :: type {
  start abstract : ()() final
}

MyStarter :: type {
  contract own : StartContract

  begin fulfill contract.start final : ()() = {
  }
}
```

Fulfilling `contract.start` with `begin fulfill contract.start varying : ()()`
would be an error for the same reason `foo varying : Foo final` is: a
declaration cannot claim more replacement authority than its place provides.

### Leaving defaulted qualifier axes open

Omitting a qualifier normally chooses the language default for that axis. An
ordinary abstract role follows that rule and therefore asks for one exact
defaulted shape. `abstract relaxed` is the explicit exception: it leaves only
the otherwise defaulted role-matching axes open.

```zax
ObserverContract :: type {
  observeExact abstract : ()()
  observe abstract relaxed : ()()
}

Observer :: type {
  contract own private : ObserverContract

  observeExact fulfill contract.observeExact final : ()() = {
  }

  observeReadonly fulfill contract.observe final : ()() readonly = {
  }

  observeWritable fulfill contract.observe final : ()() mutable writable = {
  }
}
```

The exact role has one fulfillment with the ordinary defaulted receiver shape.
The relaxed role has two distinct qualification-specialized fulfillments. Each
declaration explicitly names and independently satisfies `contract.observe`;
the declarations do not become a dispatch table, merge into one implementation,
or make fulfillment implicit.

For a value role, relaxation applies only to the outer required value layer. For
a callable role, it applies only to omitted receiver qualifier axes. Any
explicitly written qualifier remains mandatory. Relaxation does not recurse
into parameter or result types and does not loosen:

- the base type;
- arity or labels;
- indirection;
- transfer stance; or
- result provenance.

Duplicate normalized fulfillment signatures remain errors. An ordinary exact
role cannot acquire several implementations merely because qualification could
distinguish them.

Optionality and relaxation are independent:

| Role form | Valid fulfillment count |
| --- | --- |
| `abstract` | Exactly one |
| `abstract relaxed` | One or more distinct qualification-specialized fulfillments |
| `abstract optional` | Zero or one |
| `abstract optional relaxed` | Zero or more distinct qualification-specialized fulfillments |

The canonical order is `abstract optional relaxed`. `optional` does not produce
an optional value and does not relax any part of the role signature.

### Compatibility and routed value fulfillment

Fulfillment uses ordinary safe same-place or compatible-prototype rules.
Transparent aliases may normalize and a same-place view may safely narrow
authority. Fulfillment cannot manufacture compatibility through:

- construction;
- conversion;
- preferred projection;
- capability strengthening;
- a `tracked outer cast`; or
- a computed temporary.

A direct data `via` declaration may fulfill a value role with resident storage
provided by another contained implementation:

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

Pipeline :: type {
  contract own : PipelineContract
  implementation : BufferedPipeline

  buffer fulfill contract.buffer via implementation.buffer
  flush fulfill contract.flush final : ()() = via implementation.flush
}
```

The value route must:

- be declared directly in the fulfilling container;
- name the abstract role explicitly;
- resolve statically and uniquely to one accessible resident stored place;
- preserve type truth, qualification, lifetime, and access;
- perform no construction, copy, conversion, preferred projection, checked outer
  cast, or temporary creation; and
- contain no route cycle.

The physical target may be nested. An automatically published short name is
never sufficient by itself.

### Hook points for partials

A type cannot call functions that its [partials](partials.md) add, because its
body never sees them. When a type needs its partials to take part in its own
operations, it declares a **hook point**: a member whose contract roles any
number of partials may fulfill, and which the type calls.

```zax
MyCatalog :: forward type

MyCatalogHooks :: type {
  assign abstract : ()(rhs : MyCatalog readonly &) writable
}

MyCatalog :: type seal open storage {
  hooks partial : MyCatalogHooks

  operator binary '=' final : (
    result self : MyCatalog &
  )(
    rhs : MyCatalog readonly &
  ) writable = {
    _.hooks.assign(rhs)   // calls every partial's fulfillment of assign
    return _
  }
}
```

The roles are still abstract: the type does not know how many partials fulfill
them. If no partial fulfills a role, the compiler removes the call. The two
activations differ as follows:

| Activation | Fulfilled by | Fulfillments | Called by |
| --- | --- | --- | --- |
| `own` member | The immediate container | As the role form allows, for example exactly one for plain `abstract` | Nobody: the role is conformance metadata |
| Hook point | Partials that name the hook point's roles | Each participating partial fulfills every required role and at most once each optional role | The type, which fans out to every fulfillment |

`hooks partial : MyCatalogHooks` declares the hook point. A partial takes part
by naming its roles with `fulfill`, for example `fulfill hooks.assign`, just as
an `own`-activated fulfillment names its role's path. Because the path
identifies the role, a type may declare several hook points with the same
contract, and each is fulfilled separately.

A hook call returns nothing. Calling a role that has a result through a hook
point is an error, because several answers cannot be combined; for the same
reason, a hook-point contract cannot contain value roles. Complete hook behavior, including order,
private state, and the intent error for a hook role the type never calls, is
defined by [Zax partials](partials.md#hooks).

### Partials keep their own scope

A partial's declarations do not join the type's own composition surface.
Within the type, a directly declared name still takes precedence over a name
published through `own`, and a family fence still applies to the type's
generated, exposed, adopted, and direct declarations. Neither rule reaches a
partial. If a type publishes `label` from an `own` member and a granted partial
declares its own `label`, uses of that name outside the type are ambiguous. A fence does not prevent a
partial from adding a name. A type that must accept no added functions writes
`seal close callable`. See
[names inside and outside](partials.md#names-inside-the-type-inside-the-partial-and-outside).

## Identity exposure

Identity-specific application of the shared mechanical filter is owned by
[identity types](identity-types.md#exposed-and-opaque-behavior). The essential
composition distinction is that a representation-trivial, offset-zero identity
consists of exactly its underlying value. Unlike an ordinary container, it may
therefore substitute the identity type for mechanically compatible by-copy
inputs and copied underlying-self results:

```text
Integer + Integer -> Integer
```

can appear as:

```text
MyInteger + MyInteger -> MyInteger
```

When an identity carries additional resident data, by-copy substitution cannot
manufacture that state and this exception is unavailable. An eligible operation
may still remain exposed with its unchanged underlying result type; it simply
cannot claim to have produced the complete identity. The identity owner defines
the remaining eligible subset, invariant responsibility, and source-evolution
consequences.

Identity admission, projection, and underlying-operation behavior are defined
by [identity types](identity-types.md).

## Outer casting to an immediate container

Ordinary forwarding already knows its receiver relationship and needs no cast.
Other code may hold only a reference or pointer to a stored member. An
**outer cast** starts from that member access and casts outward to its immediate
container.

### Exact stored-member targets

All outer-cast forms name an exact resident stored-member path:

```zax
Engine :: type outer tracked {
  rpm : Integer
}

Car :: type {
  engine own : Engine
  spare : Engine
}

inspectContainer final : ()(input : Engine &) = {
  asserted : Car & = input unsafe outer cast Car.engine

  checked : Car & ? = input tracked outer cast Car.engine
  if ?checked
    use(checked.)

  invalid := input outer cast Car
  // error: target must be an exact resident stored-member path
}
```

The path resolves duplicate same-typed members and supplies the relationship
being claimed or tested. A target is never:

- a bare type;
- a function, including `final` or `once`;
- an alias;
- a published short path;
- a data `via` route; or
- a generated behavior surface.

The operations are protected language forms, not overloadable operators.
Their exact recognition and precedence are recorded by the
[operator catalog](operator-catalog.md#outer-cast-forms).

### Proved, tracked, and unsafe outer casting

`unsafe outer cast` uses the named member offset under programmer-asserted
provenance. A false claim has undefined behavior.

Plain `outer cast` is the proof-required safe form. It returns a non-optional
container reference only when the selected language contract proves that every
origin reaching the operand is the exact named member path. It requires neither
tracking metadata nor a runtime relationship check. If that contract does not
establish the proof at this site, the declaration is rejected rather than
silently changing result type or runtime mechanism.

`tracked outer cast` is the checked runtime form. It validates one immediate
relationship at runtime and reports failure in the shape of its operand:

```zax
checked : Car & ? = engineReference tracked outer cast Car.engine       // reference in, optional out
checkedOwner : Car * strong = engineOwner tracked outer cast Car.engine  // pointer in, vacant on failure
```

- A reference operand produces an optional reference, because a reference
  cannot be vacant.
- A pointer operand produces a pointer of the same role that is vacant on
  failure, like a failed ownership claim or weak acquisition.

The check needs a way to know where the member lives. Ordinarily the member type
supplies it by being declared `outer tracked`. A managed pointer whose outward
step reaches its allocation root needs no such declaration: its control block
already knows the root and its type, so the check compares the target with the
root's member offset.

The operation remains a tracked, checked operation even when static analysis
happens to know that the result is present.

### Managed pointers keep their role

An outer cast on a `strong` or `weak` pointer produces a pointer of the same
role that shares the operand's control block:

```zax
car : Car * strong = makeCar()
engine : Engine * strong = inner car.engine

sameCar : Car * strong = engine outer cast Car.engine   // one more strong owner of the same Car
```

This is how an [interior pointer](pointers-and-arenas.md#interior-pointers)
returns to its container without losing ownership. The proved, tracked, and
unsafe forms keep their usual meaning. A false `unsafe outer cast` assertion
cannot corrupt ownership accounting, because the result shares the operand's
real control block; only its target place would be wrong.

Temporaries and references whose origin has expired are ineligible for all
forms. No outer cast revives an ended resident instance.

These operations apply the reusable unsafe and proof model in
[safety and analysis](safety-and-analysis.md). Their reference origins and
lifetimes remain governed by
[lifetimes and references](lifetimes-and-references.md#published-paths-and-outer-casts).

An outer cast crosses one immediate boundary at a time. Reaching a
grand-container requires another cast from the newly obtained immediate
container.
Possible nested-path sugar is future work and must not imply arbitrary ancestor
discovery.

### Static exact-origin proof

Runtime tracking is unnecessary when the selected language contract mandates a
proof that every origin reaching this cast site is the exact named member path:

```zax
Engine :: type {
  rpm : Integer
}

Car :: type {
  engine : Engine
}

inspectKnownCar final : ()(car : Car readonly &) = {
  engine : Engine readonly & = car.engine
  sameCar : Car readonly & = engine outer cast Car.engine
  use(sameCar)
}
```

At this site, `engine` can only have come from `car.engine`. Under a selected
contract that requires this analysis, the safe unmarked cast produces a
non-optional result without `Engine outer tracked` or a runtime relationship
check.

Proof is about origins that can reach the cast site, not every value of the
member type in the universe. An importer constructing an unrelated standalone
`Engine` does not matter if that value cannot flow into `inspectKnownCar`.
Conversely, same-typed sibling members, arbitrary `Engine &` parameters,
callbacks, raw pointers, opaque ingress, or an exported API that accepts
standalone engines may prevent the exact-path proof because a different origin
can reach the cast.

This initial site-specific analysis is a conceptual proof model rather than a
finished formal algorithm. Source validity follows the selected contract:

| Situation | `outer cast` | `tracked outer cast` | `unsafe outer cast` |
| --- | --- | --- | --- |
| Selected-contract exact-origin proof succeeds | Produces the non-optional container reference | Requires `intent<redundant-outer-tracking>` to retain the optional tracked operation | Redundant assertion is an error |
| Selected contract does not mandate or establish that proof | Error | Performs the checked operation | Retains the required programmer assertion |
| Source selects a stronger extension contract whose proof succeeds | Produces the non-optional result under that selected contract | Requires the same intent acknowledgement | Redundant assertion is an error under that contract |
| The exact relationship is proved false | Error | Produces absence or vacancy under its checked semantics | Error; unsafe cannot contradict a known fact |

A compiler may use extra private analysis to optimize or advise. Without a
selected contract that makes the proof part of source semantics, it cannot make
plain `outer cast` portable or make one program reject `unsafe` while another
conforming compiler still requires that word. For the same reason, private
proof cannot require an intent acknowledgement around `tracked outer cast`.

### Deliberately retaining redundant tracking

When selected-contract proof makes the plain operation available, `tracked`
strongly resembles a mistaken request for runtime work and an optional result.
The ordinary repair is to use the proved non-optional operation:

```zax
TrackedEngine :: type outer tracked {
}

TrackedCar :: type {
  engine : TrackedEngine
}

inspectKnownTrackedCar final : ()(car : TrackedCar &) = {
  engine : TrackedEngine & = car.engine
  knownCar : TrackedCar & = engine outer cast TrackedCar.engine
  use(knownCar)
}
```

A programmer may instead deliberately preserve the tracked optional contract:

```zax
inspectPossiblyKnownCar final : ()(car : TrackedCar &) = {
  engine : TrackedEngine & = car.engine

  possibleCar : TrackedCar & ? =
    intent<redundant-outer-tracking>{
      engine tracked outer cast TrackedCar.engine
    }
}
```

Without that acknowledgement, the redundant tracked expression is an intent
error. The acknowledgement preserves the optional result and accepts the
`outer tracked` representation and lifecycle costs. Like every intent
acknowledgement, it does not force a physical metadata read; ordinary as-if
optimization remains available.

If the selected contract does not establish exact origin, tracking is not
redundant: write `tracked outer cast` directly, and the acknowledgement is
inapplicable. A compiler's stronger private proof may optimize or advise, but
cannot make otherwise portable tracked source require intent.

The same three-way distinction applies to outer result provenance in `via`:

- when the selected contract proves the outer result origin, ordinary unmarked
  `via` produces the non-optional outer result;
- `tracked via` explicitly selects tracked checking and an optional outer result;
- without contract-required proof, an unchecked non-optional outer result
  requires `unsafe via`, even if this compiler privately proves the origin;
- an explicitly selected extension may make its stronger proof canonical; and
- a `via` provenance assertion that is proved false is always an error.

When selected-contract proof makes ordinary `via` available, deliberately
retaining the optional tracked route uses the same category around the complete
declaration:

```zax
intent<redundant-outer-tracking>{
  possibleOwner final : (result : Car & ?)() =
    tracked via engine.selectedPeer
}
```

`tracked via` applies only to a route whose result mapping performs this checked
immediate outer cast. It is not a general alternate routing mode. `= existing`
and automatic `expose` do not silently discover or generate tracked outer-result
work; a programmer who wants it must name the route explicitly.

### `outer tracked` costs and lifecycle

`outer tracked` explicitly permits compiler-owned hidden representation and
lifecycle bookkeeping:

```zax
Engine :: type outer tracked {
}
```

The metadata describes the current resident instance's placement. It is not
ordinary user state copied unchanged from a source value. A standalone `Engine`
and an engine resident at `Car.engine` have different placement relationships.

For structural reasoning, `outer tracked` contributes one hidden component at
the tracked value's relative root. The component is atomic: flattening never
enters its implementation. Aligned components with the same placement-metadata
contract may participate in safe compatibility even when the outer nominal
identities differ. A missing, misaligned, or semantically different component
prevents safe shape/layout compatibility.

An anchor may begin after an outer tracking component when the destination is
untracked. If an anchored region contains a nested tracking component, the
destination must expect a compatible component at that same relative position.
A same-storage compatible reference shares the resident tracking state; a
by-value destination establishes corrected state for its new placement. See
[Zax structural shapes and compatibility](structural-shapes-and-compatibility.md#outer-tracked-participates-in-structure).

Ordinary construction, copy, move, and replacement establish the destination's
current metadata. Moving a complete container preserves its internal immediate
relationships in the destination. Removing a member from a container does not
retain the source relationship. Replacement ends the old resident relationship
and establishes the successor's relationship; references to the old resident
remain invalid.

A `tracked outer cast` or `tracked via` route has checked optional semantics and
may perform runtime work. A contract-proved cast or route does not require
tracking when written in its ordinary plain form. An intent-acknowledged tracked
operation retains the optional contract and permission for tracking costs. The
design does not promise a single stored pointer, a particular metadata block, or
any other backend representation. Ordinary as-if optimization may remove a
redundant metadata read only when doing so cannot change the optional result or
other observable behavior. Raw byte relocation cannot be assumed to preserve a
valid tracked relationship.

## Costs

Composition syntax makes relationships shorter or generates statically selected
adapters; it does not make their consequences free.

Programmers must be able to discover:

- the unchanged cost of following a published data path;
- pointer adjustment performed by a generated wrapper;
- by-value member extraction performed by preferred projection;
- outer and member copies performed by explicit wrappers;
- especially expensive `deep` member mapping;
- tracked runtime work and hidden state permitted by `outer tracked`;
- additional callable surface generated by `expose`; and
- source compatibility deliberately coupled through family adoption; and
- source compatibility risk introduced by published names and preferred routes.

A compiler may inline or eliminate a wrapper when ordinary optimization permits.
That possibility does not change the language-level transfer, lifetime,
qualification, or failure behavior.

## Diagnostics

Required composition diagnostics include:

- `own` on a local or flow-scope declaration;
- ambiguous published data names;
- attempted publication or automatic forwarding through a pointer, reference,
  optional, variant, or comparable semantic wrapper;
- an inaccessible or non-place-preserving data `via`;
- cyclic data routes;
- indistinguishable direct preferred routes;
- ambiguous projected binding;
- attempting operator or member discovery through `preferred`;
- unavailable or ambiguous generated behavior;
- attempted exposure or routing of a member lifecycle operation as container
  lifecycle;
- exposing a mapping that requires capability or provenance invention;
- `self` on a copied result or a returning path that does not return `_`;
- singular `via` with no unique source operation and complete mapping;
- `= existing` without one unique complete mapping;
- family adoption without one unique source family or with a source member that
  has no unique mechanical outer mapping;
- a direct declaration conflicting with an outer `= forbidden family` fence;
- an exact or family fence without one exact anchor;
- a `fulfill` target that is absent, inactive, incompatible, or already
  fulfilled inconsistently;
- an activated required abstract role left unfulfilled by its immediate
  container;
- several fulfillments of an exact role, including an optional exact role, or
  duplicate normalized fulfillments of a relaxed role;
- a hook-point call to a role that has a result;
- declaration-side or private words on abstract metadata;
- reach-through construction of a member declared `own`;
- an outer-wrapper mapping whose remaining container cannot be destroyed in a
  valid terminal-member state;
- an outer cast whose target is not an exact resident stored-member path;
- a plain `outer cast` for which the selected contract does not establish
  exact-origin proof;
- `tracked outer cast` or `tracked via` without the required `outer tracked`
  capability, or with a result shape that does not match its operand (an
  optional reference for a reference operand, a same-role pointer for a pointer
  operand);
- selected-contract proof making `tracked outer cast` or `tracked via`
  redundant without `intent<redundant-outer-tracking>`;
- redundant `unsafe` when selected-contract proof requires the safe form;
- omitted `unsafe` when the selected contract does not authorize the asserted
  provenance;
- a provenance assertion proved false; and
- an outer cast from a temporary or expired origin.

Exact diagnostic identifiers and presentation remain future diagnostics work.

## Formatting

Canonical formatting:

- orders the local modifier group as `own preferred expose`;
- preserves explicit physical paths rather than replacing them with published
  short paths;
- preserves every role path in a `fulfill` list;
- presents callable `via` targets as ordinary member paths and operator targets
  with their structured operator names;
- writes checked outer-result routing as `tracked via`;
- keeps `existing family`, `via family`, and `forbidden family` together as
  declaration states; and
- writes the type capability as `outer tracked` and the operation forms as
  `outer cast`, `tracked outer cast`, and `unsafe outer cast`;
- writes optional relaxed roles as `abstract optional relaxed`.

The wider declaration grammar and formatter own placement of the complete
composition-modifier group relative to unrelated declaration and qualifier
words.

## Source stability

The following changes are source- or behavior-visible:

- adding a stored member to the type of an `own` member may create a
  published-name collision;
- adding a direct outer member may take over a published short name;
- adding or changing contained behavior may change an exposed surface;
- adding a preferred route may introduce a new use-site ambiguity;
- changing qualification may alter preferred-route viability;
- adding a required abstract role may break immediate containers;
- changing a role may invalidate an existing fulfillment;
- adding a qualification-specialized fulfillment may conflict with an existing
  relaxed-role fulfillment;
- adding an overload to an exposed family may create new candidates;
- adding an overload to an adopted family changes the promised outer surface
  and may make the family declaration invalid if no unique mapping exists;
- adding an outer family fence conflicts with every declaration in that visible
  family, regardless of source provenance;
- changing a physical path may break `via`, `fulfill`, and outer-cast targets;
- adding identity data may make some exposure mappings ineligible;
- adding or removing `outer tracked` changes representation and lifecycle cost;
  and
- selecting a contract that newly requires exact-origin proof may require a
  tracked operation to become plain or gain
  `intent<redundant-outer-tracking>`.

None of these changes is resolved by declaration or source order.

## Boundaries and maturity

This document describes current conceptual design, not a formal grammar,
specification, ABI, compiler lowering, reflection contract, or implementation
guarantee.

General structural equivalence, layout compatibility, postures, anchors, and
transformation are defined by
[Zax structural shapes and compatibility](structural-shapes-and-compatibility.md).

It does not establish:

- class inheritance or virtual dispatch;
- a general interface, trait, concept, or structural-subtyping facility;
- a whole-type `abstract` contract or required structural shape;
- generic deduction through preferred projection;
- external authority to change an owner's exposure fences;
- generalized parameter-origin result contracts beyond receiver `self`;
- local or flow-scope `own`;
- generalized delegation through semantic indirection;
- automatic nested outer casting;
- a final mandatory set of static outer-origin proof algorithms;
- reflection identities for published, generated, or fulfilled surfaces; or
- one representation for generated wrappers or `outer tracked`.

Future work may add those facilities only without turning publication into
storage, making projection drive discovery, weakening explicit fulfillment,
inventing provenance, or resolving ambiguity by source order.
