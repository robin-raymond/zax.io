# Zax optional values

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers reading, writing, or evaluating Zax |
| Applies To | Optional type formation, absence and presence, boxed construction and lifetime, reset, transfer effects, proven access, nested optionals, qualification, and related diagnostics; not a formal specification |
| Implementation State | Not established by this repository |
| Owns | The programmer-facing optional wrapper and boxed-value model; default absence; direct present construction; contained reconstruction with protected `.=`; optional reset; complete-wrapper replacement; optional `copy`/`deep`/`move`/`last` effects; protected `move`/`last` adapters and terminal cleanup; optional swap; proven postfix access; nested optional depth; wrapper-versus-boxed qualification; optional-specific costs, source stability, and diagnostics |
| Does Not Own | General transfer meaning and fallback ([transfer stances](transfer-stances.md)); general constructor mechanics ([construction, replacement, and destruction](construction-and-destruction.md)); [reference lifetime](lifetimes-and-references.md); pointer vacancy, ownership, and Nothing behavior ([Nothing instances](nothing-instances.md), [pointers and arenas](pointers-and-arenas.md)); complete `using` enrollment and disposal ([Zax `using`](using.md)); complete runtime `switch` behavior ([switch, case, and default](switch.md)); function availability; pattern matching; async cancellation; numeric conversion policy; formal layout, ABI, or reflection |
| Source / Provenance | Retired legacy optional design input, refined against current construction, qualifier, operator, invocation, flow, integer, and identity design |

## Start with absence and presence

An optional wrapper either has no boxed value or has exactly one:

```zax
myValue : MyValue

empty : MyValue?
present : MyValue? = myValue

if ?present
  use(present.)
```

`empty` is absent. Its wrapper is a valid live `MyValue?`, but no boxed
`MyValue` lifetime exists.

`present` contains one live `MyValue`. The protected presence operation
`?present` returns exactly `Boolean`. Postfix `present.` crosses the optional
boundary and produces access to the boxed value only where static analysis has
proved that exact boxed lifetime present.

Absence is not uninitialized memory. An implementation may use a tag, a valid
unused representation, or another strategy, but the programmer-visible rule is
the same: an absent wrapper contains no live `MyValue` to access or destroy.

## Reset, move, and last

Reset, terminal transfer, and nonterminal `move` have different wrapper effects:

| Completed operation | Boxed source afterward | Source wrapper afterward |
| --- | --- | --- |
| `reset value` | Lifetime ended when present | Absent immediately |
| Complete consumer of `last value` | Remaining payload state dispositioned | Absent after consumer completion |
| Complete consumer of `move value` | Present payload remains live and moved-from | Preserves absence or presence |

`reset` acts immediately:

```zax
reset present
reset present // no-op: the same live wrapper is already absent
```

### Move and last source adapters

`last value` and `move value` are pre-unary optional phrase operators. They
first change the stance offered to a consumer; they do not transfer a payload by
themselves:

```zax
source : MyValue?
anotherSource : MyValue?

// ...

copied : MyValue? = source
terminallyTransferred : MyValue? = last source
moved : MyValue? = move anotherSource
```

Both forms preserve the complete optional type:

```zax
last source        // MyValue? offered under `last`, plus scheduled cleanup
move anotherSource // MyValue? offered under `move`
```

They do not produce `source.` or expose a bare `MyValue`. A consumer receives an
optional source, may observe absence, and must prove presence before postfix
access. An absent wrapper performs no boxed transfer.

Ordinary `copy` leaves `source` unchanged. When a consumer accepts
`last source`, `source` immediately enters terminal state for analysis. Its boxed
state remains available to that consumer until the complete call or lifecycle
operation finishes. The adapter then removes any remaining boxed value and
leaves `source` absent.

When a consumer accepts `move anotherSource`, an absent wrapper stays absent. A
present wrapper stays present with one live moved-from `MyValue`.

Reference- and by-value consumers use the same `last` cleanup boundary: the
source wrapper becomes absent after the complete singular call, construction,
assignment, or other consumer finishes.

Later ordinary use during that consumer is a terminal-source reuse error even
while the represented payload remains physically live:

```zax
observe(
  last source,
  wasPresent: ?source // error: terminal-source reuse
)
```

This optional adapter is distinct from generic stance restatement:

```zax
source as last // offer `last`; schedule no optional-specific cleanup by itself
last source    // offer `last` and leave this wrapper absent after completion
```

The operator catalog records that these optional forms are protected from
conflicting user declarations. That classification does not change their
ordinary phrase precedence.

Complete stance meaning and fallback are defined by
[Zax transfer stances](transfer-stances.md). The acknowledgement for a defined
terminal-state operation is defined by
[Zax intent acknowledgements](intent-acknowledgements.md).

## Default and present construction

Type-default optional construction is absent:

```zax
empty : MyValue?
```

It does not run `MyValue`'s zero-input constructor.

Direct initialization with a value constructs a present optional:

```zax
myValue : MyValue

present : MyValue? = myValue
```

The declaration directly establishes the `MyValue?` destination. It does not
first construct absence and then assign over it.

### Empty construction packets

An empty construction packet explicitly requests zero-input construction:

```zax
ordinary : MyValue
explicit : MyValue = [{}]
```

For an ordinary `MyValue`, those declarations select the same zero-input
construction when it is available. The packet is an explicit statement of
constructor intent.

For an optional, omission and an empty packet differ:

```zax
empty : MyValue?          // absent
present : MyValue? = [{}] // present, with a zero-input-constructed MyValue
```

`[{}]` is the canonical zero-entry construction packet.

```zax
invalid : MyValue = [{ }] // error: use the contiguous empty packet `[{}]`
```

The spaced form looks like the programmer opened a nonempty packet but forgot
its arguments. Contiguous `[{}]` explicitly acknowledges that zero constructor
inputs are intended.

### Contained reconstruction on an existing wrapper

Protected `.=` explicitly requests a fresh boxed lifetime while retaining the
same optional wrapper:

```zax
connection : MyConnection?

connection .= [{
  endpoint,
  mode: selectedMode
}]
```

This operation:

1. evaluates and immediately binds packet inputs in source order;
2. verifies that the bound inputs survive the transition;
3. destroys the old boxed value when present;
4. invokes the selected boxed constructor; and
5. marks the wrapper present only after construction completes.

It always constructs. It never changes to boxed assignment or boxed replacement
because the wrapper happened to be present.

One direct source is the corresponding single-source construction form:

```zax
connection .= existingConnection
```

The direct and packet forms parallel ordinary declaration construction:

```zax
created : MyConnection = existingConnection
createdFromPacket : MyConnection = [{ endpoint, mode: selectedMode }]
```

`.=` re-delivers those construction inputs one semantic containment layer
inside the existing wrapper. It returns access to the freshly constructed
payload.

Construction failure is an error or panic, not absence:

```zax
myByte := (: U8? = 355) // error: constructing the boxed U8 fails
```

It does not silently select optional conversion, narrow the value, try another
width, or fall back to default absence.

Zax has no exception rollback contract. If runtime construction panics, the
program follows its graceful-crash behavior.

### Packet inputs that alias the old payload

Packet entries bind before the old payload lifetime ends. A `copy` can therefore
establish an independent snapshot, but a reference does not become independent
merely because it was evaluated first:

```zax
if ?connection
  connection .= connection.
  // error when the selected constructor would use this reference after the old
  // boxed lifetime ends
```

The selected operation or lifetime policy must establish an independent value,
retain the exact source lifetime through its final use, handle the alias
explicitly, or reject the operation.

## Construction, wrapper replacement, and boxed assignment

Three similar-looking forms have deliberately different lifetime effects:

```zax
value : MyValue
optional : MyValue?
otherOptional : MyValue?

// ...

optional .= value // construct a fresh boxed value in this wrapper
optional = otherOptional  // assign wrapper state within this lifetime
optional .= otherOptional // reconstruct the complete optional wrapper

if ?optional
  optional. = value    // operate on the proven-live boxed value
```

### Why `optional = value` is an intent error

This source is rejected for an existing optional:

```zax
optional = value // error
```

It is one dot away from:

```zax
optional. = value
```

yet several plausible readings have very different consequences:

- assign into the current boxed lifetime;
- destroy and reconstruct the boxed value;
- replace the complete optional wrapper; or
- construct only when currently absent.

Write the intended operation:

```zax
optional .= value      // fresh boxed construction

if ?optional
  optional. = value    // ordinary in-lifetime boxed assignment
```

A declaration does not have this ambiguity because it is direct construction:

```zax
newOptional : MyValue? = value
```

`optional = [{...}]` is a non-acknowledgeable intent error. Construction
packets for contained reconstruction use `.=`:

```zax
optional .= [{ constructorArguments }]
```

### Same-type wrapper assignment and complete reconstruction

Same-type optional `=` assigns wrapper state while retaining the destination
wrapper lifetime:

```zax
destinationOptional = sourceOptional
```

It requires a mutable wrapper through writable access. The wrapper place may be
final or varying.

Same-type optional `.=` instead reconstructs the complete destination wrapper
lifetime:

```zax
destinationOptional : MyValue?
sourceOptional : MyValue?

// ...

destinationOptional .= sourceOptional
```

Both operations produce the source absence/presence state:

| Old destination | Source optional | New wrapper state |
| --- | --- | --- |
| Absent | Absent | Absent |
| Present | Absent | Destroy the old payload and construct an absent wrapper |
| Absent | Present | Construct a present wrapper and payload |
| Present | Present | Destroy the old payload and construct a present wrapper and fresh payload |

Complete `.=` produces access to the new wrapper. It requires a type-side
varying destination reached through a writable path whose declaration has
replacement permission. Ordinary `=` does not. The source transfer stance selects `copy`, `move`, terminal transfer, or
another compatible construction.

Resolved source depth decides the target:

- a source of boxed type `T`, or a bare constructor packet, requests
  wrapper-owned payload reconstruction and requires a mutable wrapper;
- a source of complete type `T?` requests complete wrapper reconstruction and
  requires a varying wrapper place.

Construct an explicitly typed `T?` source when absence or complete-wrapper
intent must be stated:

```zax
optional .= (: MyValue?) // reconstruct complete wrapper to absence
```

### Assignment through postfix access

Postfix `.` produces boxed access after presence proof:

```zax
if ?optional
  optional. = value
```

This selects the boxed type's own operation. It leaves the wrapper present.
Ordinary assignment retains the current resident lifetime.

Complete payload replacement is explicit:

```zax
if ?optional
  optional. .= [{ replacementInputs }]
```

This requires the boxed place itself to be varying and reached through writable,
declaration-side varying access. It may select the boxed type's
`+++ replacement` while the optional wrapper remains present.

Any reference tied to an ended boxed lifetime becomes invalid even when the
wrapper stays present.

## Reset

`reset optional` ends a present boxed lifetime and leaves the same wrapper
absent:

```zax
reset optional
reset optional // harmless no-op
```

It returns a reference to the same wrapper, never the old boxed value:

```zax
(reset optional) .= replacementInput
```

The returned path preserves the source qualifications. It cannot gain writable
or replacement authority.

Direct boxed destruction is always rejected:

```zax
optional.---() // error: destruction would not update wrapper presence
```

Postfix access does not grant authority to change the owning wrapper's state.
Allowing the call would permit later wrapper destruction to destroy the same
boxed lifetime again.

When alias analysis cannot prove a valid reset boundary, future narrow
`unsafe<...>{...}` assertions may acknowledge an unproved valid case. They
cannot repair a known use of an ended lifetime or make direct boxed destruction
legal.

## Presence proof and postfix access

Static analysis must establish that the exact boxed lifetime is present before
accepting postfix `.`.

A preceding presence test is the ordinary proof:

```zax
if ?optional
  use(optional.)
```

Construction can also establish proof:

```zax
optional : MyValue? = [{}]
use(optional.)
```

Earlier control flow or another recognized presence contract may provide the
same fact. An arbitrary user-defined Boolean-returning `?` does not.

The proof is tied to one boxed lifetime. Reset, contained reconstruction with
`.=`,
complete-wrapper replacement, terminal transfer, or another lifetime-ending
operation invalidates it:

```zax
if ?optional {
  oldValue : MyValue & = optional.
  reset optional
  use(oldValue) // error: oldValue refers to the ended boxed lifetime
}
```

Reset may follow the proven final use of a non-escaping reference. If analysis
cannot establish definitive proof for otherwise valid source, a future narrow
assertion is required:

```zax
if ?optional {
  oldValue : MyValue & = optional.
  opaqueObserve(oldValue)

  unsafe<replacement-alias>{
    reset optional // asserts that opaqueObserve retained no alias
  }
}
```

When the uncertain operation is the access itself, the assertion belongs around
that access. Assume `possiblyValidReference` comes from an opaque lifetime source
whose validity the compiler cannot establish:

```zax
unsafe<lifetime-escape>{
  use(possiblyValidReference)
}
```

The exact categories remain future analysis-control design. These assertions add
no required runtime validity check. If the programmer's presence, alias, or
lifetime claim is false, behavior is undefined; optional debug instrumentation
may detect the violation and panic.

The current distinction among proof, unsafe assertion, defined unsafe
permission, and no valid interpretation is owned by
[Zax safety and analysis](safety-and-analysis.md).

A known absence or unconditionally known-ended lifetime remains invalid inside
an unsafe enclosure. Such an assertion cannot make a value lifetime exist:

```zax
reset optional

unsafe<optional-presence>{
  use(optional.) // error: optional is known absent
}
```

### Why ordinary access remains `.`

Postfix `.` represents statically proven access. It is not a Rust-style
`unwrap()` that performs a runtime check and may panic.

Deep named unwrapping also obscures ordinary layered access:

```zax
// Not the Zax access model:
(unwrap ((unwrap pointer).content)).result

// Layered postfix access:
pointer.content.result
```

Semantic tooling can identify optional and pointer dereference nodes without
requiring textual grep for a word. A future explicitly panicking
`expect`-like operation would be a separate contract.

### Composition stops at the wrapper

Composition modifiers apply to the declared optional member value; they do not
silently enter a present payload:

```zax
Details :: type {
  label : String
}

Slot :: type {
  details : Details?
}

Panel :: type {
  slot own : Slot
}

inspectPanel final : ()(panel : Panel readonly &) = {
  inspect(panel.details) // published path to the optional wrapper

  if ?panel.slot.details {
    details : Details readonly & = panel.slot.details.
    inspect(details.label) // explicit presence proof and postfix payload access
  }

  inspect(panel.label) // error: `own` does not enter the optional payload
}
```

Likewise, `expose` on an optional-valued member can expose only eligible behavior
of that optional wrapper. It does not delegate the boxed value's operations.
This preserves the wrapper's distinct presence, lifetime, and qualification
boundary. Complete composition eligibility belongs to
[Zax composition](composition.md#publication-stops-at-semantic-indirection).

## Nested optionals

Optional wrappers compose:

```zax
single : MyValue?
nested : MyValue? ?
```

The space is required. Compact `MyValue??` contains the conditional-expression
token `??`, not two visibly acknowledged optional layers.

`MyValue? ?` has three states:

1. outer absent;
2. outer present, containing an absent `MyValue?`; and
3. outer present, containing a present `MyValue?`, containing one `MyValue`.

These can represent distinctions such as not evaluated, evaluated with no
result, and evaluated with a result.

### Constructing each nested state

```zax
outerAbsent : MyValue? ?
outerPresentInnerAbsent : MyValue? ? = [{}]

innerPresent : MyValue? = myValue
outerPresentInnerPresent : MyValue? ? = [{ innerPresent }]
```

`[{}]` constructs the outer payload. That payload is a default-constructed
`MyValue?`, so the outer wrapper is present while the inner wrapper is absent.

### Adding optional depth requires a packet

Direct shorthand can add the first optional layer:

```zax
first : MyValue? = myValue
```

Adding a layer around an already-optional value requires explicit intent:

```zax
inner : MyValue?

outer : MyValue? ? = inner       // error: use a construction packet
outer : MyValue? ? = [{ inner }] // construct one outer present layer
```

Without the packet, the source resembles ordinary same-type optional
construction even though it changes wrapper depth.

Same-type construction remains ordinary:

```zax
source : MyValue? ?
copy : MyValue? ? = source
```

The rule follows resolved type structure through aliases and generic
substitution:

```zax
MyInner :: alias type MyValue?
MyOuter :: alias type MyInner?

innerAlias : MyInner
outerAlias : MyOuter = innerAlias // error: resolved construction adds a layer

outerAliasFromPacket : MyOuter = [{ innerAlias }]
```

### Each operation acts on one layer

Presence and postfix access cross one wrapper at a time:

```zax
if ?nested {
  inner : MyValue? & = nested.

  if ?inner
    use(inner.)
}
```

Resetting `nested` removes the outer payload. If the contained inner optional was
present, ordinary nested destruction also destroys its boxed `MyValue`.

Optional type formation does not generate nested conversion or admission
operations. If a declared operator returns `MyValue? ?`, that operator owns its
exact outer and inner state. Type inference receives the declared result without
changing it.

## Participation in `using`

An optional entered into `using` keeps its ordinary wrapper lifetime while
making its immediate payload conditionally available for disposal:

```zax
using (resource := tryObtainResource()) {
  if ?resource
    use(resource.)
}
```

At the disposal phase:

- an absent `resource` performs no disposal call;
- a present `resource` offers `resource.` to ordinary `dispose` selection; and
- the optional wrapper remains alive for its later ordinary destruction.

The possible present path is checked statically. An incompatible `dispose` on
the immediate payload is therefore an error even when one runtime instance could
be absent.

Participation crosses exactly one wrapper layer. For `Resource? ?`, `using`
tests the outer presence and offers its immediate `Resource?` payload; it does
not recursively flatten to `Resource`. Source that wants the inner resource to
participate must explicitly prove and select that inner layer.

This rule does not change optional reset, replacement, qualification, or nested
destruction. Complete resource enrollment and disposal are defined by
[Zax `using`](using.md#optional-values).

## Selecting optional states

Runtime selection can apply the recognized pre-unary presence operation to its
retained selector:

```zax
switch optionalValue {
  case ?
    use(optionalValue.)
  default
    handleAbsent()
}
```

The successful `?optionalValue` interpretation proves the exact boxed lifetime
present on the ordinary body-entry path. The complementary form can establish
presence by failure:

```zax
switch optionalValue {
  case !
    handleAbsent()
  default
    use(optionalValue.) // the recognized absence test failed
}
```

An arbitrary user-defined Boolean operation does not establish optional presence
merely because it appears in a case.

Proof at a shared body must hold for every alternative that can select it:

```zax
case ?, emergencyMode {
  use(optionalValue.) // error if emergencyMode can succeed while absent
}
```

`continue` runs the target tests and may establish their recognized facts.
`goto` enters a case body directly and carries only facts valid on that incoming
path. A body with matched, continued, and direct entries may dereference only
when all applicable paths prove the same boxed lifetime present.

Nested optionals remain explicit and cross one layer at a time:

```zax
switch nested {
  case ? {
    switch nested. {
      case ?
        use(nested..)
      default
        handleInnerAbsent()
    }
  }

  default
    handleOuterAbsent()
}
```

Cases do not bind optional payloads, and postfix `.` is access rather than a
Boolean case test. Use ordinary proven access and nested selection when the
payload itself needs further testing. Complete switch ordering, test
interpretation, and transfers belong to
[Zax switch, case, and default](switch.md#optional-presence-and-nested-layers).

## Conditional convergence

A typed destination can converge a boxed value with explicit absence:

```zax
condition : Boolean
myValue : MyValue

result : MyValue? =
  condition ?? myValue ;; (: MyValue?)
```

The selected arm directly establishes the destination. Inference may likewise
converge `T` with one already concrete `T?` through the same first-layer
construction when no competing convergence is equally valid.

Adding a layer around an already-optional arm requires an explicit typed
construction:

```zax
inner : MyValue?

nestedResult : MyValue? ? =
  condition ??
    (: MyValue? ? = [{ inner }]) ;;
    (: MyValue? ?)
```

This shorthand is rejected:

```zax
nestedResult : MyValue? ? =
  condition ?? inner ;; (: MyValue? ?) // error: true arm adds optional depth
```

A packet is not independently a value or anonymous structure:

```zax
nestedResult : MyValue? ? =
  condition ?? [{ inner }] ;; (: MyValue? ?) // error: packet has no destination
```

The anonymous typed declaration supplies the missing construction boundary.
The same rule applies in arguments, returns, operator operands, aliases, and
generic substitution.

## Parameters and results

Optional parameters use ordinary invocation binding:

```zax
inspect final : ()(input : MyValue?) = {
  if ?input
    use(input.)
}
```

The selected parameter contract determines whether the argument copies, moves,
transfers terminally, or binds by reference. Optional presence alone does not.

An optional result directly establishes absence or presence:

```zax
findMyValue final : (
  result : MyValue?
)(
  shouldReturn : Boolean,
  input : MyValue
) = {
  if shouldReturn
    return input

  return (: MyValue?)
}
```

Neither path default-constructs the result and assigns over it.

An optional-depth transition in an argument or result uses anonymous typed
packet construction:

```zax
consume((: MyValue? ? = [{ innerOptional }]))
```

The packet cannot appear alone because it requires a destination.

## Construction and optional conversion are different

Optional construction promises a present valid payload:

```zax
constructed := (: U8? = 355) // error
```

An explicitly optional conversion reports rejection through absence:

```zax
source : I16 = 355
converted := source as U8? // absent
```

Construction never silently searches for optional conversion after failure.

Validating identity admission is another explicit absence-producing operation:

```zax
handle : MyDocumentHandle? =
  MyDocumentHandle optional from rawHandle
```

The present result contains a real identity value. It does not defer identity
until dereference.

The existence of `T? ?` does not generate `as T? ?`, nested `optional from`, or
another policy. If a source type explicitly declares such a result, that
operation defines the exact nested state.

## Wrapper and boxed qualifications

Optional qualification reads outward:

```zax
T q0 ? q1 * q2
```

This is a `q2` pointer to a `q1` optional containing `q0 T`.

Wrapper and boxed qualifications are independent:

```zax
innerReadonly : MyValue readonly?
wrapperReadonly : MyValue? readonly
```

The first has a wrapper with ordinary defaults but exposes a readonly boxed
value. The second exposes a readonly wrapper but does not rewrite the boxed
qualifications.

The optional marker attaches directly to the complete qualified inner type:

```zax
simple : MyValue?
qualified :
  MyValue immutable readonly final? mutable writable varying

spaced :
  MyValue immutable readonly final ? mutable writable varying
// error: `?` must attach to the inner type layer
```

Nested optional layers remain visibly separated because compact `??` is the
conditional token:

```zax
nested : MyValue? ?
```

After proven `optional.`, wrapper qualifications are gone from view. The result
has the boxed qualifications.

> Qualification does not propagate across the optional boundary, but lifetime
> ownership does.

This makes `?` semantic indirection despite inline storage. An immutable wrapper
may have a separately qualified mutable boxed value:

```zax
stablePresence : MyValue mutable writable? immutable readonly
```

The wrapper's presence is stable through this path, while a proven boxed access
has the declared mutable/writable qualifications. Stabilizing both layers
requires both to say so:

```zax
stable : MyValue immutable? immutable
```

### Wrapper qualification meanings

| Qualifier | Optional-wrapper meaning |
| --- | --- |
| `mutable` | Presence may change during this wrapper lifetime through an authorized operation |
| `immutable` | Presence remains stable for this wrapper lifetime |
| `writable` | This path may perform an otherwise permitted wrapper change or replacement |
| `readonly` | This path may observe but not change wrapper state |
| Type-side `varying` | The place may receive another complete optional-wrapper lifetime |
| Type-side `final` | The place may not receive another complete wrapper lifetime |
| Declaration-side `varying` | This declaration may exercise complete-wrapper replacement |
| Declaration-side `final` | This declaration may not exercise complete-wrapper replacement |

Wrapper `final` does not freeze mutable presence:

```zax
optional final : MyValue? mutable final = value

reset optional
reset optional
optional .= [{}]
optional = otherOptional  // legal: assign state within final wrapper lifetime
optional .= otherOptional // error: complete wrapper place is final
```

An immutable varying wrapper has the opposite boundary:

```zax
optional varying :
  MyValue? immutable writable varying = first

reset optional    // error: would mutate this immutable wrapper
optional .= [{}]  // error: would mutate this immutable wrapper
optional = second // error: would mutate this immutable wrapper
optional .= second // legal: reconstruct the complete wrapper lifetime
```

A fully qualified example keeps the two target layers visible:

```zax
myOptional :
  MyType immutable readonly final? immutable writable varying

replacementOptional :
  MyType immutable readonly final? immutable = makeReplacementOptional()

myOptional .= newValue
// error: payload-source `.=` would mutate this immutable wrapper lifetime

myOptional .= [{ newValue }]
// error: same contained-reconstruction boundary

myOptional = replacementOptional
// error: ordinary `=` would mutate this immutable wrapper lifetime

myOptional .= replacementOptional
// legal: reconstruct the complete varying wrapper lifetime

if ?myOptional {
  myOptional. = newValue  // error: payload is immutable and readonly
  myOptional. .= newValue // error: payload path is readonly and final
}
```

### Ending qualified boxed lifetimes

Boxed qualifications govern boxed use through `.`. They do not prevent an
authorized wrapper owner from ending the conditional lifetime:

```zax
optional : MyValue immutable readonly final? mutable writable

reset optional
optional .= [{}]
```

Here wrapper-owned `.=` is legal even though the payload is immutable,
readonly, and final. The mutable/writable wrapper owns ending that entire
conditional path. Direct postfix assignment or replacement through the payload
remains unavailable.

Those operations structurally remove and create boxed places. They do not
mutate or independently replace the old boxed value through postfix access.

## Operation qualification reference

`T` below means the qualifications permit the operation when its ordinary
constructor or transfer candidate exists. `N` means the stated form is never
available. `S` means proof or selected transfer behavior decides it.

| Operation | Wrapper requirements | Wrapper-place requirements | Boxed requirements | Allow | Wrapper effect | Boxed effect |
| --- | --- | --- | --- | --- | --- | --- |
| `?value` | Live readable wrapper | - | - | T | None | None |
| `value.` | Live readable wrapper | - | Preserve boxed qualifications | S | None | Produce access with presence proof |
| `reset value` | `mutable` + `writable` | `final` or `varying` | - | T | Same wrapper becomes/remains absent; return wrapper reference | Destroy once when present |
| `value .= sourceOfT` or `value .= [{...}]` | `mutable` + `writable` | `final` or `varying` | Applicable constructor | T | Same wrapper becomes present; return payload access | End old lifetime if present; construct fresh payload |
| `value = otherOptional` | `mutable` + `writable` | `final` or `varying` | Compatible optional transfer | T | Retain wrapper lifetime; assign absence/presence state | End old payload if needed; establish assigned payload state |
| `value .= otherOptional` | Writable destination | Type-side and declaration-side `varying` | Compatible optional transfer | T | Reconstruct complete wrapper lifetime | Old payload ends; new state comes from source |
| `value = valueOfT` | - | - | - | N | - | Intent error; use `value .= valueOfT` |
| `value. = source` | Wrapper readable; presence proven | - | Selected boxed operation accepts effective qualifications | S | Presence remains true | Operate on existing boxed value |
| `value. .= source` or `value. .= [{...}]` | Wrapper readable; presence proven | Boxed place type-side and declaration-side `varying` + writable | Applicable replacement/fallback construction | S | Presence remains true | End boxed resident; construct successor; may select boxed `+++ replacement` |
| `value.---()` | - | - | - | N | Would not update presence | Always rejected |

### Transfer stance reference

Generic stance restatement has no state effect until a consumer accepts it:

| Accepted source | Optional-specific source effect |
| --- | --- |
| `value as copy` | Produce the same optional type; wrapper and present payload remain unchanged |
| `value as deep` | Produce the same optional type; wrapper and source payload remain unchanged; a present payload supplies the exact `deep` contract |
| `value as move` | Produce the same optional type; accepted whole-optional `move` contract determines source state; no optional cleanup is scheduled by the form |
| `value as last` | Produce the same optional type; accepted whole-optional `last` contract determines terminal source state; no optional cleanup is scheduled by the form |
| `move value` | Produce the same optional type; preserve absence/presence; leave a present payload live and moved-from |
| `last value` | Produce the same optional type; mark terminal when accepted; after complete consumer, leave wrapper absent and payload dispositioned |

Whether an absent qualified optional satisfies a consumer follows that
consumer's contract. Same-type transfer can propagate absence; a future
unwrapping consumer may panic or be unavailable. General fallback and accepted
source state are defined by [Zax transfer stances](transfer-stances.md).

## References, pointers, and other boxed types

A reference has a hidden handle location, auto-follows its referent, cannot be
rebound, and accepts no qualifiers on the reference layer. It may be stored in an
optional or pointed to:

```zax
MyValue readonly & ?          // optional containing a readonly reference
MyValue? readonly &           // reference to a readonly optional wrapper
MyValue readonly & * writable // writable pointer to a readonly reference handle
```

A direct reference to a reference is rejected:

```zax
MyValue & & // error
```

Pointers, references, and optionals otherwise compose when every adjacent layer
is eligible:

```zax
MyValue & * & * &
MyValue & ? * & ?
```

There is no arbitrary depth limit.

### Optional references

An optional reference owns only a conditional reference handle:

```zax
view : MyValue readonly & ?
```

Reset ends the stored reference lifetime, not the referred-to `MyValue`.
Lifetime analysis must prove that a present stored reference never outlives its
referent. Later presence may construct a reference to another place, but that is
a new contained reference lifetime rather than rebinding the old reference.

A reference obtained through postfix access to a present optional payload has a
different target:

```zax
if ?optionalValue {
  payloadView : MyValue readonly & = optionalValue.
  reset optionalValue
  inspect(payloadView) // error: reset ended the boxed path
}
```

The optional wrapper may continue while its boxed path ends. Complete
life-path and reference-origin behavior is defined by
[lifetimes and references](lifetimes-and-references.md#optional-payloads).

### Pointer presence is not optional presence

Pointers have their own vacancy without requiring an optional wrapper.

```zax
owner : MyValue * unique

if ?owner {
  use(owner.)
}
```

For an owning pointer, `?owner` proves that this pointer currently owns a
target. For a weak pointer, `?observer` reports that a weak relationship is
stored even after ownership closes. `liveness probe observer` is the
nonacquiring momentary liveness query. Weak-to-strong `copy` performs the actual
conditional acquisition and pins the target on success.

An optional pointer adds another independent layer:

```zax
maybeOwner : MyValue * unique ?
```

It distinguishes an absent optional wrapper from a present vacant pointer and
from a present pointer owning a target. Each `?` operation or postfix access
acts on one explicit semantic layer. Complete pointer presence, Nothing
instances, weak acquisition, and ownership are defined by
[Zax Nothing instances](nothing-instances.md) and
[pointers and arenas](pointers-and-arenas.md#presence-and-weak-acquisition).

### Existing empty-like boxed states remain distinct

Optional absence never collapses a boxed type's own empty-like value:

- an optional pointer distinguishes outer absence, a present vacant pointer,
  and other present pointer states;
- an optional function distinguishes outer absence, a present function
  value that is unavailable, and a present callable;
- a nested optional distinguishes outer absence, outer-present/inner-absent, and
  both layers present.

A present weak callable remains present while its weak binding is installed,
even when receiver ownership has closed. `liveness probe` asks that separate
momentary question. Complete callable states and reset behavior belong to
[Zax lambdas and callable composition](lambdas-and-callable-composition.md#presence-liveness-and-installed-binding-kind).

Destroying a boxed pointer follows that pointer type's ownership contract. A raw
pointer may perform no pointee work; an owning or reference-counted pointer may
release or destroy another value according to its arena-backed allocation
disposition.

Even a zero-storage boxed type requires a semantic presence distinction.

## Optional interception and payload forwarding

Transfer stance attaches once to the complete optional source:

```zax
MyValue readonly? writable * immutable * varying deep
```

The optional layer owns presence and conditional payload lifetime. When its
selected contract forwards transfer:

- an absent optional performs no boxed transfer;
- a present optional forwards the accepted stance to its payload as required by
  the optional operation;
- nested wrappers intercept at each present layer;
- wrapper qualifiers do not rewrite boxed qualifications;
- and pointer or reference payloads retain their own ownership and referent
  authority.

These generic post-unary forms are current:

```zax
source as copy
source as deep
source as move
source as last
```

They need no phrase fence. This rule does not imply matching protected
pre-unary `copy` or `deep` optional adapters.

Custom containing operations own their member policy. `move` or `last` on a custom
body does not blindly stamp every projected member with destructive stance.
Complete projection and custom interception are defined by
[Zax transfer stances](transfer-stances.md#projection-and-aliases).

## Swapping optional values

Same-type optional values support the protected swap form:

```zax
left : MyValue?
right : MyValue? = value

left <<>> right
```

After completion, `left` has the optional state formerly held by `right`, and
`right` has the state formerly held by `left`. Absence and presence remain
distinct, and a present state carries its payload.

Swap requires both wrapper paths to permit the applicable change. It does not
flatten nested optional layers or collapse pointer/function empty-like payload
states. The exact internal layout and transfer strategy are not prescribed.
General swap form and association are listed by the
[operator catalog](operator-catalog.md#assignment-and-swap-forms).

## Costs and representation

The optional model requires enough information to preserve every distinct
wrapper state, but no particular layout:

- a separate tag may increase `TypeSize`, alignment, or padding;
- a valid niche may represent absence only when every boxed value remains
  available;
- `T? ?` needs at least three semantic states;
- optional pointers and functions retain their inner empty-like states;
- zero-storage values still require a presence distinction;
- presence testing reads the represented state;
- reset, transfer, replacement, and destruction perform boxed work only on
  applicable present paths;
- direct construction does not require observable default construction followed
  by assignment.

Exact layout, niche choice, discriminant location, ABI, and reflection encoding
remain future contracts. Formatting and debugging must distinguish absence,
presence, and nested states; exact text remains unsettled.

## Diagnostics and source stability

Diagnostics should identify both the failure and the deciding distinction:

- `optional = value`: fresh contained construction uses `.=`; boxed assignment
  needs postfix `.`;
- `[{ }]`: the blank packet looks like omitted intended arguments; use `[{}]`
  to acknowledge zero inputs;
- `T??`: `??` is one conditional token; use `T? ?`;
- whitespace before a type-layer `?`: attach the marker to the complete
  qualified inner type, such as `T immutable readonly final?`;
- `outer : T? ? = inner`: adding optional depth requires a packet;
- a bare packet: constructor inputs need a typed destination;
- `optional.`: prove the exact boxed lifetime present;
- `optional.---()`: direct destruction cannot update wrapper presence;
- wrapper mutation through readonly or immutable access;
- complete-wrapper replacement without varying place authority;
- use of an invalidated boxed reference;
- unsupported `deep` transfer or unavailable accepted fallback;
- ordinary use after protected terminal transfer was accepted;
- protected `last` cleanup that would outlive its complete consumer;
- swap through an ineligible wrapper path;
- a requested nested conversion or admission operation that does not exist.

Formatters and source-preserving tools must retain:

- the space between nested `?` markers;
- contiguous `[{}]`;
- packet input order;
- postfix `.` placement;
- wrapper-versus-boxed qualifier attachment;
- explicit transfer stance and any required phrase fence.

## Boundaries and maturity

This document is current conceptual design, not a formal grammar, implementation
mapping, layout or ABI contract, or conformance specification.

Later work must preserve:

- absence as a valid wrapper state with no live boxed value;
- default absence and explicit present construction;
- construction failure as error or panic rather than absence;
- the distinction among contained reconstruction with `.=`; complete-wrapper
  replacement; and boxed assignment;
- idempotent reset returning the same wrapper;
- transfer-qualified expressions having no effect until consumed;
- the optional source-state consequences of `copy`, `move`, and `last`;
- mandatory static proof before postfix access;
- explicit nested optional depth without flattening;
- wrapper and boxed qualification independence;
- per-layer pointer/reference/optional composition;
- visible costs and intent diagnostics.

Future work owns:

- complete transfer preference, `move`/`last` capability, moved-from contracts,
  and source-view syntax;
- `:=` value/reference inference;
- self-assignment and alias policy beyond the optional guarantees here;
- exact unsafe assertion categories;
- optional pattern matching and presence binding;
- async construction, transfer, and cancellation;
- exact reference-handle representation and optional pointer layout;
- nested numeric conversion/admission availability;
- exact representation, reflection, ABI, and formatting.
