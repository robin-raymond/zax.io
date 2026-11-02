# Zax `using`

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers acquiring, coordinating, shutting down, and releasing scoped resources |
| Applies To | Programmer-facing `using` resource entries, lifetime extension, structural disposal, exit behavior, ordering, costs, and diagnostics; not a formal grammar or specification |
| Implementation State | Not established by this repository |
| Owns | The complete `using` operation; resource-entry enrollment; structural recognition of the ordinary `dispose` phrase; disposal compatibility; disposal and destruction order; using-specific flow eligibility and bypass; duplicate-enrollment intent; scoped-resource costs, diagnostics, formatting, and source stability |
| Does Not Own | General result mapping ([function invocation](function-invocation.md)); cohesive [exceptional result flow](except.md); declaration behavior ([declarations and bindings](declarations-and-bindings.md)); ordinary reference semantics ([lifetimes and references](lifetimes-and-references.md)); optional presence and boxed lifetime ([optional values](optional-values.md)); operator-phrase declaration and selection ([operator phrases](operator-phrases.md)); qualification meaning ([qualifiers](qualifiers.md)); terminal destruction ([construction and destruction](construction-and-destruction.md)); general label and transfer lookup ([core flow control](core-flow-control.md)); general intent acknowledgement ([intent acknowledgements](intent-acknowledgements.md)); generic cleanup hooks; async cancellation; or compiler lowering |
| Source / Provenance | Legacy [flow-control](../flow-control.md) resource-lifetime intent, reconciled with current flow, invocation, lifetime, operator, and destruction design |

## Mental model

`using` keeps selected resources available for one body, gives each compatible
resource an orderly end-of-use operation while every enrolled resource remains
alive, and only then destroys the values it owns:

```zax
using resources: (
  connection := connect(),
  grant := connection.acquireGrant(),
  auditContract(),
  sharedService
) {
  performWork(connection, grant, sharedService)
}
```

The resource entries evaluate once in source order. When the body exits:

1. body-local values are destroyed;
2. compatible `dispose` operations run in reverse resource-entry order;
3. every disposal call finishes; and
4. values owned by `using` are destroyed in reverse caller-visible entry order.

`connection` and `grant` are named values owned by this `using`.
`auditContract()` may contribute an unnamed value whose lifetime is extended
through the body. `sharedService` is an existing place borrowed by reference;
it may be disposed but is not destroyed by `using`.

For independently evaluated entries, visible entry order normally matches
construction order. Result mapping can expose producer results in another order.
The selected visible prototype controls pre-body result construction, and the
implementation controls explicit body and return construction. `using` does not
reorder either; it disposes and destroys its destinations according to the order
visible in the caller.

Disposal and destruction are different. `dispose resource` is an ordinary
operation on a still-live instance. Destruction is the terminal end of that
instance. This separation supports orderly shutdown, coordination among several
live resources, and breaking circular `strong` relationships before
reference-counted destruction can begin.

The word *resource* is descriptive. Zax does not introduce a resource base type,
marker interface, or separate lifetime category. Any enrolled receiver with a
compatible `dispose` phrase participates; an enrolled receiver without one is
kept alive and later destroyed according to its ordinary ownership.

## Source shape

The complete conceptual form is:

```text
using [label:] (resource-entry-list) body
```

Parentheses are required even for one entry:

```zax
using (resource := acquireResource()) {
  use(resource)
}
```

The parentheses delimit a mapping-capable resource list, like a call's argument
list. They are not grouping parentheses around one expression. This distinction
matters for multiple results:

```zax
using (acquirePair()) {
  // Every result is enrolled.
}

using ((acquirePair())) {
  // Valid only if acquirePair() forms one expression value.
}
```

An empty list is invalid because it performs no resource operation:

```zax
using () { // error: use an ordinary block or explicit scope
}
```

The body follows the ordinary effective-body and brace rules.

### No initializer or post sections

`using` is not an initializer/post flow-header schema and accepts no `;;`
section:

```zax
using (prepare() ;; resource) {
} // error: using has no separate initializer section

using (resource) ;; recordCompletion(resource) {
} // error: using has no post section
```

The resource list already performs ordered acquisition and initialization.
Zero-result setup work belongs before `using`. A value needed by later entries
can be declared as an enrolled entry.

Normal-completion work before disposal belongs at the end of the body. An inner
block can end other body-local lifetimes first:

```zax
using (resource) {
  {
    temporary := prepare(resource)
    use(temporary)
  } // destroy temporary

  recordCompletion(resource)
} // dispose resource, then destroy it if using owns it
```

Work that belongs to disposal itself belongs in `dispose` or in a wrapper that
provides the required operation.

## Resource entries

Each resource entry is evaluated once in visible source order.

An entry becomes enrolled only after its producer selects success and completes
the mapped resource destination:

```zax
using (
  connection := connect(),
  grant := acquireGrant(connection) catch failure {
    return
  }
) {
  use(connection, grant)
}
```

If grant acquisition selects `failure`, no `grant` instance becomes enrolled,
disposed, or destroyed. The earlier `connection` entry is already enrolled, so
the handler's outward transfer performs ordinary reverse disposal and
destruction for `connection` before continuing.

A catch handler cannot fall through into a body that requires an unconstructed
resource. Forwarding `except` has the same partial-header cleanup consequence.
Outcome handling and forwarding are defined by
[Zax exceptional result flow](except.md#acquisition-inside-using).

### Named owned values

A declaration establishes a named value available to later entries and the
body:

```zax
using (
  connection := connect(),
  grant := connection.acquireGrant()
) {
  use(connection, grant)
}
```

These values are owned by the `using` header and destroyed after the complete
disposal phase.

### Unnamed value results

A by-value expression result becomes an unnamed owned entry:

```zax
using (
  acquireContract(),
  resource := acquireResource()
) {
  use(resource)
}
```

The contract value remains alive through the body and disposal phase even though
the body has no name for it. It is then destroyed with the other owned entries.

A zero-result operation cannot form a resource entry.

### Existing places and reference results

An existing place is borrowed by reference:

```zax
resource := acquireResource()

using (resource) {
  use(resource)
}
// using does not destroy resource
```

The borrowed reference remains bound to the same place. If the place is varying
and receives a completely established successor, disposal observes that
successor:

```zax
resource varying : Resource = acquireFirst()

using (resource) {
  resource = acquireSecond()
}
// Disposal observes the acquireSecond() successor.
```

A reference-valued expression likewise enrolls its borrowed target. `using`
keeps the returned reference value available for disposal but does not acquire
ownership of the target:

```zax
using (findSharedResource()) {
}
```

Ordinary origin and lifetime proof must establish that every borrowed place
still exists and contains a complete resident instance when disposal occurs.
Borrowing does not extend the target's owning life path.

Explicit qualification and transfer syntax keeps its ordinary meaning. A stance
such as `move` offers a transfer posture to the applicable consumer; `using`
does not reinterpret the spelling as proof that a transfer or new ownership
occurred.

### Optional values

An optional entry participates only when it contains a value:

```zax
using (resource := tryObtainResource()) {
  if ?resource
    use(resource.)
}
```

At disposal, an absent `resource` causes no call. When it is present, `using`
selects the equivalent of `dispose resource.` on the immediate boxed value.
Compatibility is checked for that possible present path even when a particular
runtime value is absent.

The optional wrapper remains the owned or borrowed entry. It undergoes ordinary
destruction after the disposal phase. `using` crosses exactly one optional layer:
an immediate optional payload can participate, but nested optional depth is not
flattened recursively. Complete presence, postfix access, and nested lifetime
behavior are defined by
[Zax optional values](optional-values.md#participation-in-using).

## Multiple results and names

A bare producer with several results contributes every result in declared result
order:

```zax
using (
  acquirePair(),
  externalResource
) {
}
```

If `acquirePair()` produces two results, the flattened enrollment order is its
first result, its second result, then `externalResource`.

A structured pair is one result and therefore one enrolled receiver. `using`
does not descend into its members automatically.

### Capture by source-result label

Named capture selects producer results by their source-facing labels:

```zax
using (
  resource:,
  grant: = acquirePair()
) {
  use(resource, grant)
}
```

This introduces same-named inferred bindings. Labels may select results in
another order, but a label must exist and each result may be selected at most
once.

A source/destination pair gives a result another body-visible name:

```zax
using (
  resource: myResource:,
  grant: myGrant: = acquirePair()
) {
  use(myResource, myGrant)
}
```

`resource:` and `grant:` select the producer results. `myResource:` and
`myGrant:` introduce the caller's destinations.

A selected result may initialize a destination with an explicit type:

```zax
using (
  resource:,
  grant: disposalGrant : MyCarryingGrant = acquirePair()
) {
  use(resource, disposalGrant)
}
```

The selected `grant` result initializes `disposalGrant`. The destination's type,
ownership, and operations determine what `using` later disposes and destroys.
General source-first routing and typed-destination behavior are defined by
[Zax function invocation](function-invocation.md#result-routing-groups).

`using` does not introduce several new names through positional typed
declarations:

```zax
using (
  myResource : Resource,
  myGrant : Grant = acquirePair()
) {
} // error: named using results require source-result labels
```

This restriction prevents result reordering or API evolution from silently
changing which resource a body name denotes.

### Anonymous enrollment and discard

A bare destination `:` enrolls a source result without introducing a
body-visible name:

```zax
using (resource: : = acquire()) {
  work()
}
// Dispose the anonymous resource, then destroy it.
```

The anonymous entry otherwise behaves like a named owned entry. In particular,
a compatible structural `dispose` participates.

Caller-side `#` also introduces no body-visible name, but explicitly suppresses
disposal for that result:

```zax
using (resource: # = acquire()) {
  work()
}
// Skip disposal, then destroy the anonymous resource.
```

Both forms construct at the same entry position, establish the same anonymous
owned lifetime, permit the same source/destination elision, and destroy at the
same caller-visible position. Their meaningful `using` difference is whether
structural disposal participates.

A typed anonymous destination remains distinct:

```zax
using (resource: : MyResource = acquire()) {
  work()
}
```

The source result initializes an anonymous `MyResource`. That type determines
construction, transfer, structural disposal, and destruction.

Bare positional `#` likewise introduces no body-visible name and suppresses
disposal for the corresponding result:

```zax
using (
  resource:,
  # = acquirePair()
) {
  use(resource)
}
// Skip disposal of the second result.
// Destroy the second result, then resource.
```

The result is not destroyed immediately. It remains an anonymous
`using`-owned value through the body and complete disposal phase, then takes its
ordinary position in reverse caller-visible entry destruction.

`#` means that the caller does not need access to the value and that `using`
does not dispose it. It does not mean that the value's lifetime is unimportant.

## Structural disposal

A type opts into automatic disposal by declaring the exact ordinary pre-unary
phrase `dispose`:

```zax
Resource :: type {
  operator pre unary 'dispose' final : ()() writable = {
    // Shut down or complete `_` while the instance remains alive.
  }
}
```

`dispose` is receiver-owned and non-protected. `using` does not impose
`writable`, `readonly`, or another special receiver qualification. Normal
selection may therefore choose a `readonly` operation:

```zax
ResourceView :: type {
  operator pre unary 'dispose' final : (
    diagnostic # : String
  )() readonly = {
    // No mutation occurs through `_`.
  }
}
```

The operation is compatible when normal operator selection can perform the
equivalent of:

```zax
dispose receiver
```

for the enrolled receiver's effective type, qualifications, and transfer state,
without leaving a result that the caller must acknowledge. The language selects
that operation directly; it does not construct and parse hidden source.

Zero results is the common form. An operation may produce one or more results
only when every result permits omission with `#`. Omitted results are still
constructed and destroyed under ordinary invocation rules, so their work and
cost do not disappear.

### Absence and incompatibility

When normal discovery finds no applicable exact pre-unary `dispose` phrase, the
entry has no disposal call. Its lifetime extension and eventual owned
destruction still apply.

When `dispose` is present but cannot satisfy the implicit bare-call contract,
the entry is a non-acknowledgeable intent error. Causes include:

- a required result;
- receiver qualification or transfer mismatch;
- ambiguous selection;
- a uniquely selected but unavailable operation; or
- another ordinary invocation failure.

```zax
Resource :: type {
  operator pre unary 'dispose' final : (
    success : Boolean
  )() writable = {
  }
}

using (resource) {
  use(resource)
}
// error: dispose resource produces required result success
```

An `intent<...>{...}` enclosure cannot make the implicit call valid or discard a
required result. The programmer must avoid `using` for that value, invoke and
handle the operation explicitly, or use a wrapper that provides a compatible
`dispose`.

`using` never searches for `close`, `shutdown`, destructor bodies, or similarly
named functions. Its one explicit wrapper integration is the immediate payload
of an optional entry described above.

## Ordering

The complete normal ordering is:

1. evaluate resource entries once in source order;
2. execute the body once;
3. destroy body-local values in reverse construction order;
4. invoke compatible `dispose` operations in reverse flattened entry order;
5. complete every disposal call; and
6. destroy owned entries in reverse caller-visible entry order.

Steps 4 and 6 walk the same caller-visible entry sequence in reverse. They can
act on different subsets: a borrowed entry can be disposed but is not destroyed
by `using`, while an absent optional entry or `#` entry is not disposed but its
owned storage is still destroyed. Entries that participate in both phases keep
the same relative order.

```zax
using (
  connection := connect(),
  grant := connection.acquireGrant()
) {
  request := prepareRequest(connection)
  send(request, grant)
}
```

The observable end order is:

```text
destroy body-local request
dispose using entry grant
dispose using entry connection
destroy using-owned grant
destroy using-owned connection
```

`request` is a body-local value, not an enrolled resource. Destroying body
locals first preserves ordinary nested-scope behavior and lets their destructors
use entries that are still live and not yet disposed. Both enrolled `dispose`
calls then finish before either owned enrolled entry is destroyed.

No enrolled receiver is destroyed between disposal calls. Every receiver
therefore remains alive throughout the complete disposal phase, including
borrowed receivers that are not eventually destroyed by `using`.

Results omitted from one `dispose` call complete and are destroyed at their
ordinary call boundary before the next disposal call. The no-destruction
guarantee covers enrolled receivers, not incidental temporaries produced by
disposal.

If one list expression produces several results, those results enter the
flattened order in declared result order. Explicit result-label routing instead
uses its visible mapped order. Reverse disposal applies to that flattened order.

Result construction and caller destruction are separate. Reordering selected
results does not reorder prototype-defined pre-body construction or the
implementation's body and return construction, but it does change the order of
newly introduced `using` destinations:

```zax
using (
  resultA: firstA:,
  resultB: secondB: = makeResults()
) {
  use(firstA, secondB)
}
// Dispose and destroy secondB before firstA.

using (
  resultB: firstB:,
  resultA: secondA: = makeResults()
) {
  use(firstB, secondA)
}
// Prototype- and implementation-defined construction is unchanged.
// Dispose and destroy secondA before firstB.
```

An existing destination keeps the lifetime established by its existing owner.
Result elision may instead unify a producer result slot with a new destination.
The unified value has the destination's lifetime and destruction position and no
separate source-slot destruction, so elision can change overall destruction
order. It does not reorder the producer's explicitly sequenced result
construction. Complete source-slot, destination, and elision behavior is defined
by [Zax function invocation](function-invocation.md#result-slots-destinations-and-elision).

## Labels and exits

`using` may have an ordinary flow label before its mandatory resource-list
parentheses:

```zax
using cleanup: (resource) {
  use(resource)
}
```

It executes its body once. It is not a loop, has no post operation, and cannot
be targeted by `next`, `continue`, or `goto`.

Normal completion and every ordinary transfer crossing the `using` boundary run
the disposal phase before continuing:

```zax
using cleanup: (resource) {
  if shouldReturn()
    return // dispose resource, destroy owned entries, then return

  if shouldContinue()
    continue outer: // dispose before reaching outer:
}
```

The only ordinary-flow disposal bypass is an explicit `break` naming that exact
`using`:

```zax
using cleanup: (resource) {
  if transferResponsibility(resource)
    break cleanup: // skip disposal, then destroy using-owned entries
}
```

The explicit target makes the changed resource responsibility visible.
Destruction is not bypassed.

### Bare `break` is a barrier

A bare `break` never targets a `using`. When `using` is the nearest break
boundary, the bare transfer is a non-acknowledgeable intent error rather than a
request to search outward:

```zax
while outer: keepWorking() {
  using cleanup: (resource) {
    if done()
      break // error: name cleanup: to bypass disposal,
            // or name outer: to perform disposal before leaving
  }
}
```

An unlabeled `using` therefore offers no disposal-bypassing transfer. A normal
inner target remains available:

```zax
using cleanup: (resource) {
  while pending() {
    break // exits the inner while; cleanup: remains active
  }
}
```

Nested `using` statements apply the target and crossing rules independently:

```zax
using outer: (first) {
  using inner: (second) {
    break outer:
    // dispose second because inner: is crossed
    // bypass dispose first because outer: is the explicit target
  }
}
```

## Repeated entries and aliases

`using` does not merge entries. Two entries mean two potential disposal calls
even when their source is repeated or their borrowed places are known to be the
same.

When required origin analysis establishes that a later entry enrolls a place
already enrolled by an earlier entry, the later entry requires
`duplicate-resource-enrollment`:

```zax
using (
  resource,
  intent<duplicate-resource-enrollment>{ resource }
) {
}
```

When a later entry repeats the same resource-producing expression but separate
evaluations may return distinct resources, it requires
`repeated-resource-expression`:

```zax
using (
  grantMeAResource(),
  intent<repeated-resource-expression>{ grantMeAResource() }
) {
}
```

An individual acknowledgement encloses the later complete resource entry or
producer group that creates the repetition. A known duplicate place uses
`duplicate-resource-enrollment` when both categories could describe the source.

Acknowledgement preserves every evaluation, enrollment, disposal call, and
destruction consequence. It does not assert that disposal is idempotent or make
an invalid alias relationship safe. Distinct source that cannot be established
as the same place remains ordinary programmer responsibility; diagnostics do
not depend on optional optimizer inference.

One surrounding acknowledgement may instead cover a complete region containing
several occurrences of the same category:

```zax
intent<repeated-resource-expression>{
  using (
    grantMeAResource(),
    grantMeAResource(),
    grantMeAResource()
  ) {
  }
}
```

Individual acknowledgement remains valid. The enclosing form must contain at
least one matching occurrence; it does not suppress another category or a
non-acknowledgeable error.

## Acquisition, panic, and completion

`using` adds no general success test. A result object or status value remains an
ordinary enrolled value, and the body handles its operations. An optional has
the one-layer conditional disposal behavior described earlier:

```zax
using (attempt := tryAcquire()) {
  if ?attempt
    use(attempt.)
}
```

A producer selecting success completes its entire ordinary result shape before
those results are enrolled. A selected exceptional outcome enrolls no success
result from that producer. Earlier entries remain enrolled and receive ordinary
cleanup when the handler or forwarding transfer exits the partially established
`using`.

An unresolved panic is fatal rather than an ordinary unwinding path. `using`
does not promise that remaining disposal calls or destruction complete after a
body operation, acquisition, or disposal hook enters an unresolved panic.

Disposal is synchronous. Suspension, cancellation, executor movement, and async
disposal remain future async design.

## Costs

`using` makes the possibility of structural disposal visible, but the exact cost
depends on its entries:

- every resource-producing expression evaluates once;
- by-value results pay their selected construction, transfer, and destruction
  costs;
- borrowed entries add no ownership but require lifetime and qualification
  proof;
- every selected `dispose` operation pays its ordinary call and body costs;
- omission-permitting disposal results are still constructed and destroyed;
- disposal calls run sequentially in reverse entry order;
- no `dispose` call occurs for a receiver with no applicable exact phrase;
- an absent optional causes no disposal call for its boxed value;
- body-local destruction retains ordinary reverse-construction cost;
- owned-entry destruction retains reverse caller-visible entry order, including
  any destruction-order change caused by result routing or elision;
  and
- no allocation, dynamic dispatch, control block, or runtime protocol test is
  implied unless a selected entry or operation independently requires it.

## Diagnostics

Diagnostics should distinguish:

- an empty resource list;
- a zero-result operation used as a resource entry;
- unknown, duplicate, or already-consumed source-result labels;
- positional introduction of several named results;
- an invalid or incompatible typed result destination;
- an expression grouped into one-value mode when it has several mandatory
  results;
- an exceptional acquisition outcome that is neither caught nor forwarded;
- a catch handler that falls through while its resource destination remains
  unconstructed;
- an optional whose possible immediate payload exposes an incompatible
  `dispose`;
- a borrowed place that cannot remain valid through disposal;
- no resident instance available to the implicit disposal call;
- an applicable `dispose` with required results;
- receiver qualification or transfer mismatch during disposal;
- ambiguous or unavailable `dispose`;
- recognizable repeated expression or duplicate-place enrollment lacking its
  acknowledgement;
- an acknowledgement that names the wrong repetition category or encloses the
  wrong source unit;
- a bare `break` stopped by a `using` boundary;
- an explicit transfer targeting `using` with an ineligible keyword;
- an attempt to use `;;` as a `using` initializer or post separator; and
- incomplete result, binding, or lifetime state on an exiting path.

Incompatible `dispose` and bare-`break` barrier errors are
non-acknowledgeable. Repeated resource expressions and duplicate resource
enrollment have the exact acknowledgement forms defined above. Exact diagnostic
identifiers and presentation remain future diagnostics work.

## Formatting

Canonical formatting:

- always preserves the mandatory resource-list parentheses;
- keeps an optional flow label between `using` and `(`;
- uses ordinary comma spacing and continuation;
- preserves producer groups, result-label adjacency, source/destination label
  pairs, anonymous `:`, `#`, grouping parentheses, qualifications, and transfer
  stances;
- keeps the body-opening `{` on the final physical header line;
- aligns the closing `}` with `using`; and
- never inserts a `;;` section.

```zax
using resources: (
  resource:,
  grant: = acquirePair(),
  sharedService
) {
  use(resource, grant, sharedService)
}
```

A formatter must not add grouping parentheses around a bare producer because
that could collapse a result sequence into one expression context.

## Source stability

The following changes are source- or behavior-visible:

- adding, removing, relabeling, or reordering producer results changes resource
  mapping;
- reordering source/destination entries changes the caller-visible disposal and
  owned-destination destruction order without changing prototype- or
  implementation-defined construction;
- changing a result between value and reference changes ownership and
  destruction;
- adding or changing an acquisition's exceptional outcomes changes which
  resource-list paths complete and which earlier entries require exit cleanup;
- changing optional depth changes which payload, if any, participates;
- changing anonymous `:` enrollment to `#` suppresses disposal without changing
  the entry lifetime or destruction position;
- adding an applicable `dispose` makes an existing `using` entry perform
  disposal;
- changing `dispose` overloads, qualifications, transfer requirements,
  availability, or result acknowledgement may change selection or make source
  invalid;
- changing entry order changes disposal and destruction order;
- adding a recognizable repeated entry requires explicit acknowledgement;
- renaming a `using` label breaks its explicit bypass; and
- changing a transfer target may change whether disposal runs.

An unrelated import cannot add an ordinary phrase to a receiver type it does not
own. Adding `dispose` to the receiver type itself remains an API compatibility
event.

## Boundaries and maturity

This document describes current conceptual design, not formal grammar, compiler
lowering, a compatibility contract, or an implementation guarantee.

It establishes synchronous scoped resource use. It does not establish:

- generalized `defer`, `finally`, scope guards, or cleanup callbacks;
- exception-like recovery or panic unwinding;
- async disposal, cancellation, or cross-thread teardown;
- a generic resource type, interface, concept, or constraint;
- automatic member exposure or lexical capture;
- compiler data structures or lowering; or
- exact diagnostic identifiers.

Future work may add those facilities only without silently changing the
resource-entry, disposal, exit, and destruction behavior defined here.
