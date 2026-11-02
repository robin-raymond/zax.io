# Zax lifetimes and references

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers reasoning about where Zax instances live and when non-owning access remains valid |
| Applies To | Programmer-facing instance lifetimes, life paths, instance places, references, reference origin, escape, and synchronous borrowing; not a formal specification |
| Implementation State | Not established by this repository |
| Owns | Life paths; instance places and resident instances; reference binding and origin; references across mutation and replacement; member and nested-place consequences; synchronous parameter and temporary borrowing; returned references, including receiver-origin `self`; reference capture and storage; reference-facing diagnostics, costs, and unsafe boundaries |
| Does Not Own | Lambda expression/capture syntax and callable composition ([lambdas and callable composition](lambdas-and-callable-composition.md)); how construction and destruction perform lifecycle transitions ([construction and destruction](construction-and-destruction.md)); cohesive [exceptional result flow](except.md); complete [variant behavior](variants.md) or unmanaged [union behavior](unions.md); composition publication, forwarding, and outer-cast forms ([Zax composition](composition.md)); `using` resource enrollment and disposal ([Zax `using`](using.md)); complete qualifier meaning ([qualifiers](qualifiers.md)); pointer ownership, arenas, and allocation disposition ([pointers and arenas](pointers-and-arenas.md)); transfer stances ([transfer stances](transfer-stances.md)); or general safety-contract behavior ([safety and analysis](safety-and-analysis.md)) |
| Source / Provenance | Legacy pointer, function-capture, scope, construction, and global-lifecycle evidence reconciled with current qualifier, invocation, optional, identity, and transfer design |
| Supersedes | Reference and lifetime teaching formerly distributed through root legacy pages |

## Start with a life path

Every Zax instance is created, lives, and is destroyed within a **life path**.
An instance never outlives its life path.

```zax
process final : ()() = {
  first : Document

  scope {
    second : Document
    inspect(first)
    inspect(second)
  } // second is destroyed as its inner life path ends
} // first is destroyed as the invocation life path ends
```

Outside an unsafe contract violation:

- each instance is constructed once within its life path;
- each instance is destroyed once within that path;
- ordinary construction follows the path's defined order;
- ordinary destruction follows the corresponding reverse order; and
- no reference, pointer, transfer, or allocation makes an instance survive the
  path that owns it.

Some operations create a separate nested life path and transfer or share
ownership of that path. The instance does not escape its path; the path itself
continues because another owner keeps it alive.

This distinction is the foundation for Zax lifetime reasoning.

## Life path, instance place, and resident instance

An **instance place** is the stable, typed position within a life path through
which a live instance is reached. After introducing the complete term, this
document also calls it a **place**.

The **resident instance** is the completely constructed instance currently
available through that place.

```text
allocation or enclosing storage
-> life path
-> instance place
-> resident instance
```

These are language-level distinctions, not promises of separate runtime
objects. A place does not inherently require a wrapper allocation, hidden state,
or extra indirection.

Allocation provides physical storage. It may exist before construction or
remain after destruction. Stable bytes or an unchanged address do not by
themselves prove that a life path, place, or resident instance remains valid.

### Final and varying places

A final place hosts one resident-instance lifetime:

```zax
document final : Document immutable final = makeDocument("one")
```

A varying place may host successive resident instances without the place itself
ending:

```zax
document varying :
  Document immutable writable varying = makeDocument("one")

document .= makeDocument("two")
```

The first immutable `Document` is destroyed and a successor is constructed in
the continuing `document` place. Ordinary access is unavailable while a
lifecycle operation has left the place without a complete resident instance.

Type-side `final` or `varying` describes this underlying place truth.
Declaration-side `final` or `varying` independently controls whether one access
path may initiate replacement. See [Zax qualifiers](qualifiers.md).

`resident instance` is distinct from Zax's established term **current
instance**, which means the instance whose type-defined body is executing
through `_`.

## Common life paths

The same model applies across Zax:

| Source | Life-path behavior |
| --- | --- |
| Global execution | Global declarations live within the process path and are destroyed in reverse of their established construction order; each generative module instance has individualized state, while exact injected namespace aliases may deliberately share another module instance; exact dependency and `once` ordering remains future work |
| Namespace | A module/namespace declaration introduces a path within global execution and ends before the containing unnamed global path; namespaces are not function-, block-, or type-local lifetime containers |
| Flow scope | Blocks, clauses, loops, and explicit `scope` create bounded paths destroyed when control leaves them |
| Type instance | Composition creates contained member places and nested paths bounded by the containing instance |
| Function invocation | Parameters, selected result slots, and invocation temporaries live through the complete synchronous call and selected-outcome mapping boundary |
| Lambda capture | A capturing lambda owns a capture path tied to the lambda instance |
| Expression temporary | A temporary path survives through the complete use or transfer that required it |
| Optional | A present optional owns one nested boxed path; reset ends that path |
| Managed variant | Zero or one named alternative path is resident; selecting or resetting ends the old path and may begin another |
| Array or collection | A collection owns one or more element paths whose stability depends on its operation contract |
| Dynamic allocation | An arena supplies storage for a separately owned path |

Module-instance identity, injection, and namespace placement are defined by
[Zax namespaces and modules](namespaces-and-modules.md). This document does not
yet define construction order across module instances, repeated/failing startup,
injected shared-state teardown, or `once` concurrency.

`using` applies these ordinary paths to one resource list. Named and unnamed
by-value entries occupy a header-owned path through the body and disposal phase.
This includes a result captured with `#`: it has no accessible name and receives
no disposal call, but its owned path is not shortened. An existing place or
reference result instead contributes a borrowed path: `using` keeps the
reference available but neither owns nor extends its target's life path.
Complete behavior is in [Zax `using`](using.md#resource-entries).

The details of pointer-owned dynamic paths are in
[Zax pointers, allocation, and arenas](pointers-and-arenas.md).

A raw pointer still owns no path, but a declaration may independently schedule
the disposition of an allocation it creates:

```zax
scheduled : Document * = @
alias : Document * = scheduled
```

The allocation path is bounded by `scheduled`'s declaration life path unless
its disposition responsibility is transferred. `alias` copies only the raw
address and cannot safely outlive that path.

Replacing or resetting `scheduled` ends the old allocation path according to its
recorded disposition and invalidates dependent aliases. A terminal transfer
leaves `scheduled` vacant; its later scheduled cleanup is therefore a no-op.

An open-ended raw allocation has no declaration-attached owner:

```zax
manual : Document * = @<
```

Its lifetime remains safe only when analysis proves another owner or a complete
manual `reset` protocol. An opaque relationship may require narrow unsafe
responsibility; a known-ended or known-interior path is rejected.

## References borrow one fixed place

A reference is a cheap, non-owning access path to an existing instance place:

```zax
source : Document
view : Document readonly & = source
```

The reference instance has its own life path. Creating or destroying it does not
create, destroy, or own `source`.

### References never rebind

A reference binds to exactly one instance place when constructed. That target
never changes during the reference's lifetime.

```text
reference target place       = permanently fixed
resident instance in place   = may change when the place is varying
```

No safe operation, unsafe assertion, transfer stance, cast, capture, parameter
mapping, or result mapping can retarget an existing reference.

Constructing another reference is not rebinding:

```zax
source : Document
first : Document & = source
second : Document & = first
```

`first` and `second` are separate reference instances bound to the same place.

Assignment through a reference acts on its referent:

```zax
source : Document
view : Document writable & = source

view = replacement
```

It does not assign another target into `view`. A pointer is different: its
pointer value may be replaced so that it targets another place or becomes
vacant.

### Non-null does not mean owning

A live reference always has one fixed referent place and has no independent
vacancy state. Ordinary reference construction starts from an ordinary live
place, but unchecked pointer dereference can mechanically bind a reference to
the pointee type's Nothing backing:

```zax
pointer : MyValue * = choosePointer()
view : MyValue & = pointer.
```

The dereference performs no ordinary vacancy check. A statically proved vacant
source is diagnosed; otherwise a runtime-vacant pointer binds `view` to the
special backing. Prepared reads, trapping access, invalid writes, and custom
containment then follow the type's
[Nothing policy](nothing-instances.md#pointer-dereference-is-unchecked).

The reference representation itself remains non-null-like and never rebinds.
Binding or destroying it does not keep an ordinary pointee life path alive.

Safe use requires proof that:

- the referenced place still exists;
- the place contains a complete resident instance, or the selected operation
  has defined Nothing-backing behavior;
- no forbidden lifecycle transition overlaps the access; and
- the access path retains the required qualifications and permissions.

When the compiler cannot prove a genuinely valid relationship, a narrow unsafe
assertion may accept responsibility. Unsafe source cannot create ownership,
extend a path, rebind a reference, or make a known-absent instance exist.

## Mutation and replacement

Content mutation does not end the resident instance:

```zax
record : MyRecord mutable
view : MyRecord readonly & = record

record.count += 1
inspect(view) // same resident instance, changed contents
```

`readonly` prevents change through `view`; it does not promise that another path
will never change what `view` observes.

### A reference to a varying place observes its successor

```zax
value varying :
  MyValue immutable writable varying = makeValue("first")

view final :
  MyValue immutable readonly varying & = value

value .= makeValue("second")
inspect(view) // observes the completely established successor
```

`view` remains bound to `value`'s stable place. It does not rebind when
replacement renews the resident instance.

Ordinary access cannot observe the place while replacement is dismantling the
old instance or establishing the successor. Constructors, replacement
constructors, and destructors receive only the lifecycle authority appropriate
to their tracked partial state.

### Direct-member references cross a renewal boundary

A direct member has its own place:

```zax
record : MyRecord
countView : Integer readonly & = record.count

record.count = 5
inspect(countView) // same member lifetime
```

Complete replacement of `record` renews every member resident instance:

```zax
record varying : MyRecord immutable writable varying
countView : Integer readonly & = record.count

record .= makeReplacement()
inspect(countView) // error: the referenced member was renewed
```

This remains a safe-language error even when:

- the member has a trivial representation;
- the address did not change; and
- low-level access to the successor would be technically possible.

A narrow unsafe permission may deliberately use the fixed member-place
reference to reach a completely established successor. It accepts the logical
renewal boundary; it does not claim that the old member instance survived.

Complete replacement and member renewal are defined by
[Zax construction, replacement, and destruction](construction-and-destruction.md).

## Nested places

Reference validity follows the exact target place, not merely its containing
allocation.

### Published paths and outer casts

An `own`-published path or place-preserving data `via` route names the same
stored member place as its physical path. It creates no additional resident
instance and no independent lifetime. Mutation, replacement, renewal, and
destruction through either path therefore affect the same target.

Publication stops at semantic indirection. A stored reference or pointer member
itself may receive a published short path, but the referenced or pointed-to
value does not:

```zax
Link :: type {
  target : Node &
}

Graph :: type {
  link own : Link
}

inspectGraph final : ()(graph : Graph readonly &) = {
  inspect(graph.target)            // published path to the reference member
  inspect(graph.link.target.value) // explicit traversal to the referent
  inspect(graph.value)             // error: publication stops before the referent
}
```

The reference member and referent keep their distinct places and lifetimes.
Complete publication and forwarding eligibility belongs to
[Zax composition](composition.md#publication-stops-at-semantic-indirection).

An outer cast travels in the opposite direction. It starts with a reference or
pointer to a stored member and names the exact physical path through which an
immediate container would hold that member:

```zax
Engine :: type outer tracked {
}

Car :: type {
  engine : Engine
}

recover final : (car : Car & ?)(engine : Engine &) = {
  return engine tracked outer cast Car.engine
}
```

A `tracked outer cast` returns an optional reference because an `Engine &` might
name a standalone engine or an engine stored somewhere other than `Car.engine`.
It explicitly uses the `outer tracked` placement capability to perform that
checked operation. `unsafe outer cast` skips the check and makes the programmer
responsible for the provenance claim. No outer-cast form can use an expired
origin or revive an ended resident.

A selected contract may instead require a site-specific exact-origin proof:

```zax
LocalEngine :: type {
}

LocalCar :: type {
  engine : LocalEngine
}

inspectCar final : ()(car : LocalCar &) = {
  engine : LocalEngine & = car.engine
  sameCar : LocalCar & = engine outer cast LocalCar.engine
  inspect(sameCar)
}
```

When every value reaching the cast operand is proved to come from exactly
`LocalCar.engine`, plain `outer cast` produces a non-optional result without
`outer tracked` or runtime work. If the selected contract does not establish
that proof, plain `outer cast` is rejected rather than silently becoming a
tracked optional operation. Same-typed sibling members, arbitrary parameters,
callbacks, raw-pointer or opaque ingress, and exported acceptance of standalone
members can defeat the proof when they can reach this site. Unrelated external
construction does not defeat a closed internal flow merely because it creates
another `LocalEngine`.

Complete forms and costs belong to
[Zax composition](composition.md#outer-casting-to-an-immediate-container).

### Optional payloads

```zax
wrapper : MyValue?
wrapperView : MyValue? readonly & = wrapper
```

`wrapperView` may remain valid while the optional wrapper remains alive.

A reference reached through a present payload instead names the payload place.
Reset ends that boxed path:

```zax
if ?wrapper {
  payloadView : MyValue readonly & = wrapper.
  reset wrapper
  inspect(payloadView) // error: the boxed path ended
}
```

Later presence creates a new boxed path. The old reference does not retarget to
it.

An optional reference stores a conditional reference instance:

```zax
view : MyValue readonly & ?
```

Reset destroys the stored reference, not the referent. Later presence may
construct a new reference bound to a different place; that is a new reference
lifetime rather than rebinding.

### Variant payloads and union lenses

A [variant](variants.md) owns one wrapper path and zero or one named payload
path. A reference obtained from an active alternative names that exact
conditional path:

```zax
choice.text .= "first"
textView : String readonly & = choice.text

choice.text .= "second"
use(textView) // error: reselection ended the first text path
```

Selecting the same name still renews its payload. Selecting another name,
resetting the wrapper, complete wrapper replacement, and destruction likewise
end the old path. The reference never follows a successor payload merely
because storage is reused.

An unmanaged [union](unions.md) is different. It has one union resident
instance and several typed lenses, not several member life paths. A reference
formed from a plain-union lens remains tied to the common backing and observes
later safe writes through another lens. In `union unsafe`, reference formation
requires lens-validity proof, and a later overlay write can invalidate typed use
even while the numeric address remains unchanged.

### Arrays and collections

A fixed array never changes how many element places it owns, so a reference to
one of those places can remain valid until that element or the array ends.
Complete array replacement or an explicit future storage transition may still
end those places; fixed describes element count, not immunity from replacing
the whole array representation.

A resizable array may have to move or remove elements. Each size-changing
operation therefore states what remains valid:

- Growing at the end within existing capacity preserves the old elements.
- Moving to new backing storage invalidates access to every old element place.
- Shrinking ends the removed suffix and preserves the earlier elements.
- Insertion preserves an unaffected prefix at most and moves or replaces later
  elements.
- Removal ends the selected elements and may move or replace later elements.

A slice borrows the particular element places selected when it is created. It
does not retarget whatever values later occupy the same numeric indexes.
Relocation invalidates every affected slice; insertion or removal invalidates
intersecting and following slices. An invalidated slice never becomes empty or
revives after later growth.

If source makes an invalid use certain, the compiler rejects it. When validity
can only be checked while running, the slice may carry origin/validity
information and the use can panic. Complete array operations, slice behavior,
capacity, and traversal are explained by
[Zax arrays and slices](arrays-and-slices.md#element-place-stability).

A reference obtained from an element remains bound to the original place. It
does not follow that element when an array moves it elsewhere.

Array storage providers participate in this decision. The provider reports
which raw slots, chunks, or mappings changed; the array translates that change
into element-place and slice invalidation. Future storage work may represent the
relationship with stability kinds/tokens and observed versions.

An unconstrained `T[N] &` can be a storage-erased array reference carrying the
common operations needed to reach inline or provider-backed elements. It still
borrows one fixed source array place and may not outlive that array.

### Pointer members and pointees

These references have different origins:

```zax
pointerView : MyValue * readonly & = owner.pointer
pointeeView : MyValue readonly & = owner.pointer.
```

`pointerView` targets the pointer member's place. Repointing that member changes
the pointer value but does not retarget `pointerView`.

`pointeeView` targets the pointee's place. Repointing `owner.pointer` does not
retarget `pointeeView` and does not prove that its old pointee remains alive.

An [interior pointer](pointers-and-arenas.md#interior-pointers) is a distinct
owning mechanism: a `strong` or `weak` pointer that keeps an enclosing
allocation alive while targeting a direct member place.

### Same-storage identity views

A same-storage identity reference creates no independent identity instance and
schedules no separate destruction. It forwards the underlying target place,
origin, and permitted access while applying the identity's nominal rules. See
[Zax identity types](identity-types.md#construction-and-transfer).

### Structurally compatible views and anchors

A structurally compatible reference likewise creates no independent destination
lifetime. It remains tied to the source place, origin, and permissions:

```zax
view : Destination & =
  source as layout Destination &
```

An unanchored whole-root view can retain varying replacement authority only when
the complete source and destination lifetime partitions, qualifications,
tracking, construction, replacement, and destruction obligations match.

An anchored interior region is different:

```zax
view final : Destination final & =
  source as layout Destination & anchor .member
```

It is a final subplace of the current containing resident instance. Complete
replacement of the container invalidates the view rather than making it observe
a successor region. A zero-size anchor remains tied to its selected conceptual
place even when another place shares the same numeric address.

Anchoring a by-value result temporary remains valid only through its complete
synchronous consumer. An anchored reference may not escape that temporary.
Complete structural conditions and conversion forms belong to
[Zax structural shapes and compatibility](structural-shapes-and-compatibility.md#same-storage-compatible-references).

## Function parameters and temporaries

A reference parameter constructs a local reference bound to caller-owned
storage:

```zax
inspect final : ()(
  input : Document readonly &
) = {
}
```

The callee receives no ownership. The caller preserves the target through the
complete synchronous call.

Explicit arguments evaluate in caller source order and bind immediately. Later
argument evaluation may mutate a referent already bound to an earlier
parameter:

```zax
observe(
  source,
  action: changeSource(source)
)
```

The first parameter is already bound when `changeSource` runs.

### Temporary extension is synchronous

```zax
inspect(makeDocument())
```

When `inspect` accepts `Document readonly &`, the temporary `Document` survives
through body execution and result mapping. It is destroyed only after its
complete synchronous consumer finishes.

Nested calls preserve the same boundary:

```zax
inspect(returnView(makeDocument()))
```

This may be valid when origin analysis proves that the reference is used only
through the complete outer call.

Suspension is not synchronous completion. Reference validity across async
suspension, cancellation, or executor movement remains future async design.

A `using` resource list supplies another explicit synchronous extension
boundary:

```zax
using (makeDocument()) {
  // The unnamed Document result remains alive through this body and disposal.
}
```

A by-value result is owned by the `using` header. A reference result keeps only
the reference value alive and still requires proof that its target survives
through disposal. Complete entry and disposal behavior is owned by
[Zax `using`](using.md#unnamed-value-results).

### Result elision joins life paths

Without elision, a producer result slot and the destination initialized from it
are distinct lifetimes. The source slot completes at the result-mapping boundary;
the destination follows the path established by its owner.

Elision may make those two logical positions one value. That value follows the
destination path and has no separate source-slot destruction. Its destruction
can therefore move later and change order relative to source slots that remain
distinct.

Suppose a producer declares results `resultA` and then `resultB`, and `resultB`
is elided into a longer-lived outer destination. `resultA` is destroyed when the
inner result mapping completes. The unified `resultB` survives until the outer
destination ends, so the observable order is `resultA` before `resultB`.

Elision does not reorder visible-prototype pre-body construction or the
implementation's explicitly sequenced body and return construction. It changes
which life path owns the unified value. Complete result-slot, destination-order,
and elision behavior is defined by
[Zax function invocation](function-invocation.md#result-slots-destinations-and-elision).

An exceptional outcome makes that unification conditional. Outer storage may be
reserved and passed through nested calls before any value lifetime begins. The
destination life path becomes caller-visible only when the selected success or
exceptional outcome publishes that result.

If another outcome is selected, raw reserved storage has no instance to destroy.
If a provisional instance was already constructed, its current owner destroys
it before propagation. Successful inner completion transfers provisional
cleanup responsibility outward until the final destination binding is
published.

This permits deep result elision without letting an exceptional path observe,
use, or destroy a success destination whose lifetime never committed. Complete
flow examples are in
[Zax exceptional result flow](except.md#conditional-result-elision).

## Returned references

A reference result owns only an access path. Result-slot destruction does not
make the referent terminal and does not extend its life path.

The same rule applies to an exceptional reference result. Selecting its named
outcome supplies the control distinction but grants no target lifetime:

```zax
failure except : MyError readonly &
```

Its origin must survive the complete handler or forwarding consumer. `catch`
does not extend the referent life path, and outcome reshape preserves rather
than erases origin.

The compiler tracks the returned target place and its origin:

```zax
returnInput final : (
  result : MyValue readonly &
)(
  input : MyValue readonly &
) = {
  return input
}
```

The result remains tied to `input`'s target.

Commonly provable origins include:

- the current instance `_`;
- an input parameter's referent;
- a direct projection from one of those places; and
- a global place.

### Receiver-origin `self`

A reference or pointer result declared `self` promises that every returning path
returns exactly the invocation receiver `_`:

```zax
tune final : (
  result self : Engine &
)(profile : Profile readonly &) = {
  apply(_, profile)
  return _
}
```

The compiler verifies the receiver origin. It does not infer `self` into an
undeclared public contract, and `self` never describes a copied result.

Composition may use this proof to map a contained receiver reference back to an
outer receiver whose relationship is already known. An arbitrary non-receiver
reference does not gain that mapping. General contracts stating that a result
originates from one particular input remain future callable-contract work. See
[Zax composition](composition.md#the-self-result-contract).

A local cannot escape its invocation path:

```zax
badView final : (
  result : MyValue readonly &
)() = {
  local : MyValue
  return local // error: local is destroyed when this call completes
}
```

Temporary extension likewise does not justify storing a returned reference:

```zax
view : MyValue readonly & = returnInput(makeValue())
// error: the reference would outlive the temporary target
```

Zax does not currently require programmer-written lifetime parameters. Concrete
bodies use static origin analysis. Opaque bodies, FFI, and separately expressed
callable contracts may eventually need inferred metadata or conservative origin
contracts, but that future need does not make an unproved result valid.

## Capture and stored references

Lambda capture defaults to `copy`, even when the captured name is a reference:

```zax
source : Document
view : Document readonly & = source

callback := [[ view ]] ()() {
  inspect(view) // observes a Document copied into the capture path
}
```

The lambda owns that copied `Document` in its capture path. If the referent is
not copyable, ordinary capture is unavailable.

An explicit reference capture instead constructs another reference bound to the
original place. It:

- does not extend or own the target path;
- must be proved valid for every invocation;
- may not silently inherit destructive `move` or `last` stance; and
- defaults ordinary nested use to `copy`.

Reference capture states its destination explicitly:

```zax
callback := [[ view: borrowed : & ]] ()() {
  inspect(borrowed)
}
```

Complete capture syntax and callable receiver behavior belong to
[Zax lambdas and callable composition](lambdas-and-callable-composition.md#capture-values-references-and-producer-results).

Storing a reference in a field, optional, callback, or result never resets its
origin. The destination must not outlive the target path unless a separate owner
keeps that path alive.

`OpaqueReferenceObserver` preserves the same fixed target and origin while
erasing its type. `OpaqueObserver` preserves one pointer target but provides no
ownership or liveness guarantee. Their complete pointer-facing behavior belongs
to
[Zax pointers, allocation, and arenas](pointers-and-arenas.md#type-erased-ownership-and-observation).

## `move`, `last`, and reference lifetime

Transfer stance and lifetime validity are independent.

A reference-shaped `move` or `last` parameter grants the selected synchronous
consumer authority over caller-owned storage:

```zax
consume final : ()(
  input : Resource mutable writable & last
) = {
  inspect(input) // named input ordinarily offers copy
  transfer(input.handle as last)
}
```

`move` and `last` change resource authority and source state. They do not rebind
the reference or by themselves end the outer place.

Explicit renewal may create another terminal-stanced reference:

```zax
stored :
  Resource mutable writable & last = (input as last)
```

That declaration is viable only when independent lifetime analysis also proves
the target outlives `stored`. Named use of `stored` again offers `copy` unless a
later consumer explicitly renews `as last`.

The legacy `lease` concept has no remaining language role. Fixed reference
binding, synchronous borrowing, pointer ownership, copy-default named use, and
explicit destructive renewal cover its useful behavior.

## Thread boundary

Ordinary references do not safely cross thread boundaries. A reference does not
own or synchronize the target path, and the resident instance may be destroyed
or changed on another thread.

Raw pointers may cross when the compiler proves the pointee path, arena,
provenance, and affinity remain valid; otherwise the boundary requires unsafe
programmer responsibility. Managed cross-thread ownership uses the applicable
unique or atomic pointer contract; that still does not make the pointee's
operations data-race safe.

## Costs and guarantees

An ordinary proved reference requires:

- no ownership transfer;
- no required control block;
- no required allocation;
- no required runtime lifetime check; and
- no rebindable handle behavior.

Those low costs depend on static restrictions. A program needing dynamic
ownership, weak observation, cross-thread lifetime accounting, or
allocation-backed continuation selects an explicit pointer strategy and pays its
visible costs.

Compiler debugging modes may instrument unsafe claims or lifecycle state. Such
checks are tooling, not language guarantees.

## Diagnostics

Diagnostics should identify:

- the reference declaration or forwarding boundary;
- the target instance place and originating life path;
- the destruction, replacement, reset, relocation, or escape that invalidates
  the use;
- the later conflicting use;
- any permission lost through an alias, parameter, result, or capture; and
- whether the operation is unproved, a defined unsafe boundary, or impossible.

Representative errors include:

- reference may outlive its target path;
- returned reference targets a local or temporary;
- member reference crosses complete replacement;
- payload reference is used after reset;
- ordinary access may observe an incomplete resident instance;
- reference capture may outlive its target;
- a borrowed `using` entry may not remain valid through its disposal phase;
- reference cannot be rebound; and
- lifetime assertion contradicts a known-ended path.

Technically valid but surprising observation through another authorized mutable
path may be a lint rather than a safety error.

## Source stability

Reference meaning does not depend on one compiler's current cleverness.

- The selected language contract determines which cases every conforming
  compiler must prove.
- A newer compiler may prove additional cases under an older contract.
- Removing an assertion made redundant only by that compiler may reduce source
  portability.
- A newer selected contract may require the proof and reject the obsolete
  assertion.

The general rule belongs to [Zax safety and analysis](safety-and-analysis.md).

Improved analysis may accept more valid source. It must not silently:

- rebind a reference;
- add ownership;
- change transfer stance;
- extend a life path;
- turn a direct member into an allocation root; or
- change which resident instance a fixed place exposes.

## Boundaries and maturity

This document is current conceptual design, not a formal lifetime calculus or
compiler algorithm.

Still deferred:

- exact compiler proof algorithms;
- opaque callable origin metadata;
- runtime-fixed array and multidimensional block-view lifetime contracts;
- formal proof algorithms for unsafe-union lens references and variant checked
  access;
- async suspension and cancellation;
- formal grammar, ABI, layout, and lowering.

Those deferrals do not weaken the fixed reference-binding model or the
programmer-visible life-path foundation defined here.
